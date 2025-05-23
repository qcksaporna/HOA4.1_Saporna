---
- name: Comprehensive Security Hardening of Managed Node
  hosts: managed
  become: true
  vars:
    admin_user: qcksaporna
    ssh_public_key: "{{ lookup('file', '/home/your_user/.ssh/id_rsa.pub') }}"  # Update path as needed

  tasks:
    - name: Install essential security packages
      apt:
        name:
          - ufw
          - fail2ban
          - snort
          - logwatch
          - unattended-upgrades
        state: present
        update_cache: yes

    - name: Enable UFW firewall
      ufw:
        state: enabled

    - name: Allow essential services through UFW
      ufw:
        rule: allow
        name: "{{ item }}"
      loop:
        - OpenSSH

    - name: Disable unused services
      apt:
        name: "{{ item }}"
        state: absent
      loop:
        - telnet
        - ftp
        - rsh-client

    - name: Disable root login via SSH
      lineinfile:
        path: /etc/ssh/sshd_config
        regexp: '^PermitRootLogin'
        line: 'PermitRootLogin no'
        backup: yes

    - name: Enforce SSH key authentication for admin
      authorized_key:
        user: qcksaporna
        key: "{{ lookup('file', '/home/qcksaporna/.ssh/id_rsa.pub') }}"

    - name: Disable SSH password authentication
      lineinfile:
        path: /etc/ssh/sshd_config
        regexp: '^PasswordAuthentication'
        line: 'PasswordAuthentication no'
        backup: yes

    - name: Restart SSH service
      service:
        name: ssh
        state: restarted

    - name: Ensure unattended-upgrades is enabled
      copy:
        dest: /etc/apt/apt.conf.d/20auto-upgrades
        content: |
          APT::Periodic::Update-Package-Lists "1";
          APT::Periodic::Unattended-Upgrade "1";

    - name: Setup basic logwatch configuration
      lineinfile:
        path: /etc/cron.daily/00logwatch
        create: yes
        line: "/usr/sbin/logwatch --output mail --mailto root --detail high"

    - name: Ensure fail2ban is running
      service:
        name: fail2ban
        state: started
        enabled: true

    - name: User Access Audit Script
      copy:
        dest: /usr/local/bin/user_audit.sh
        mode: '0755'
        content: |
          #!/bin/bash
          echo "Sudo Users:"
          grep '^sudo:.*$' /etc/group
          echo "Login History:"
          last -n 5

    - name: Ensure audit script is executable and can be run
      file:
        path: /usr/local/bin/user_audit.sh
        mode: '0755'
        state: file
