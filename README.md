---
- name: Install and configure NGINX on Debian and CentOS
  hosts: all
  become: yes

  tasks:
    # Install NGINX on Debian/Ubuntu
    - name: Install NGINX on Debian or Ubuntu
      apt:
        name: nginx
        state: present
      when: ansible_os_family == "Debian"

    # Install NGINX on CentOS
    - name: Install NGINX on CentOS
      yum:
        name: nginx
        state: present
      when: ansible_os_family == "RedHat"

    # Ensure NGINX service is running
    - name: Ensure NGINX is running
      service:
        name: nginx
        state: started
        enabled: yes

    # Create a custom NGINX welcome page
    - name: Create a custom index.html for NGINX
      copy:
        content: "Welcome to NGINX managed by Ansible!"
        dest: "/usr/share/nginx/html/index.html"

    # Open HTTP port in the firewall for Debian-based systems
    - name: Open firewall for HTTP (Debian)
      ufw:
        rule: allow
        name: 'nginx HTTP'
      when: ansible_os_family == "Debian"

    # Open HTTP port in the firewall for CentOS-based systems
    - name: Open firewall for HTTP (CentOS)
      firewalld:
        service: http
        permanent: true
        state: enabled
      when: ansible_os_family == "RedHat"

    # Install Netdata for monitoring
    - name: Install Netdata on Debian or Ubuntu
      apt:
        name: netdata
        state: present
      when: ansible_os_family == "Debian"

    - name: Install Netdata on CentOS
      yum:
        name: netdata
        state: present
      when: ansible_os_family == "RedHat"

    # Ensure Netdata service is running
    - name: Ensure Netdata is running
      service:
        name: netdata
        state: started
        enabled: yes

    # Modify MOTD (Message of the Day)
    - name: Change MOTD
      copy:
        content: "Ansible Managed by {{ ansible_user }}"
        dest: "/etc/motd"
      become: yes
