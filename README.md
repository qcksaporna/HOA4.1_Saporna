---
- name: Setup firewall on Ubuntu and CentOS
  hosts: all
  become: yes

  tasks:
    # Install UFW on Ubuntu
    - name: Install UFW on Ubuntu
      ansible.builtin.apt:
        name: ufw
        state: present
      when: ansible_facts.os_family == "Debian"
      tags: firewall

    # Install Firewalld on CentOS
    - name: Install Firewalld on CentOS
      ansible.builtin.yum:
        name: firewalld
        state: present
      when: ansible_facts.os_family == "RedHat"
      tags: firewall

    # Enable and start UFW on Ubuntu
    - name: Enable and start UFW on Ubuntu
      ansible.builtin.systemd:
        name: ufw
        state: started
        enabled: yes
      when: ansible_facts.os_family == "Debian"
      tags: firewall

    # Enable and start Firewalld on CentOS
    - name: Enable and start Firewalld on CentOS
      ansible.builtin.systemd:
        name: firewalld
        state: started
        enabled: yes
      when: ansible_facts.os_family == "RedHat"
      tags: firewall

    # Allow SSH through the firewall on Ubuntu
    - name: Allow SSH through UFW on Ubuntu
      ansible.builtin.ufw:
        rule: allow
        name: OpenSSH
      when: ansible_facts.os_family == "Debian"
      tags: firewall

    # Allow SSH through the firewall on CentOS
    - name: Allow SSH through Firewalld on CentOS
      ansible.builtin.firewalld:
        service: ssh
        permanent: yes
        state: enabled
        immediate: yes
      when: ansible_facts.os_family == "RedHat"
      tags: firewall

    # Reload Firewalld on CentOS (if applicable)
    - name: Reload Firewalld on CentOS
      ansible.builtin.command:
        cmd: firewall-cmd --reload
      when: ansible_facts.os_family == "RedHat"
      tags: firewall
