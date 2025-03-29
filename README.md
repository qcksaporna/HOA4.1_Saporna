---
- name: Generate Certificate Authority with SSL
  hosts: control
  become: yes
  tasks:
    - name: Install OpenSSL
      apt:
        name: openssl
        state: present
        update_cache: yes

    - name: Create directory for CA
      file:
        path: "/etc/ssl/my_ca"
        state: directory
        mode: '0755'

    - name: Generate CA private key
      command:
        cmd: openssl genpkey -algorithm RSA -out /etc/ssl/my_ca/ca.key -aes256
        creates: /etc/ssl/my_ca/ca.key

    - name: Generate self-signed CA certificate
      command:
        cmd: openssl req -new -x509 -key /etc/ssl/my_ca/ca.key -out /etc/ssl/my_ca/ca.crt -days 3650 -subj "/CN=My Custom CA"
        creates: /etc/ssl/my_ca/ca.crt

    - name: Create SSL certificate for web server
      command:
        cmd: openssl req -newkey rsa:2048 -nodes -keyout /etc/ssl/my_ca/server.key -out /etc/ssl/my_ca/server.csr -subj "/CN=localhost"
        creates: /etc/ssl/my_ca/server.key

    - name: Sign server certificate with CA
      command:
        cmd: openssl x509 -req -in /etc/ssl/my_ca/server.csr -CA /etc/ssl/my_ca/ca.crt -CAkey /etc/ssl/my_ca/ca.key -CAcreateserial -out /etc/ssl/my_ca/server.crt -days 3650
        creates: /etc/ssl/my_ca/server.crt
