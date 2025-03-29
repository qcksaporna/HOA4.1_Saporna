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

    - name: Create directory for CA and server files
      file:
        path: "/etc/ssl/my_ca"
        state: directory
        mode: '0755'

    - name: Generate CA private key (2048 bits, no encryption)
      command:
        cmd: openssl genpkey -algorithm RSA -out /etc/ssl/my_ca/ca.key -pkeyopt rsa_keygen_bits:2048
        creates: /etc/ssl/my_ca/ca.key

    - name: Debug: Check if the private key file exists
      stat:
        path: /etc/ssl/my_ca/ca.key
      register: ca_key_status

    - name: Show status of CA private key file
      debug:
        var: ca_key_status

    - name: Generate self-signed CA certificate
      command:
        cmd: openssl req -new -x509 -key /etc/ssl/my_ca/ca.key -out /etc/ssl/my_ca/ca.crt -days 3650 -subj "/CN=My Custom CA"
        creates: /etc/ssl/my_ca/ca.crt

    - name: Debug: Check if the server private key exists
      stat:
        path: /etc/ssl/my_ca/server.key
      register: server_key_status

    - name: Show status of server private key
      debug:
        var: server_key_status

    - name: Generate SSL certificate for web server (private key and CSR)
      command:
        cmd: openssl req -newkey rsa:2048 -nodes -keyout /etc/ssl/my_ca/server.key -out /etc/ssl/my_ca/server.csr -subj "/CN=localhost"
        creates: /etc/ssl/my_ca/server.key

    - name: Debug: Check if the server CSR file exists
      stat:
        path: /etc/ssl/my_ca/server.csr
      register: server_csr_status

    - name: Show status of server CSR
      debug:
        var: server_csr_status

    - name: Sign server certificate with CA
      command:
        cmd: openssl x509 -req -in /etc/ssl/my_ca/server.csr -CA /etc/ssl/my_ca/ca.crt -CAkey /etc/ssl/my_ca/ca.key -CAcreateserial -out /etc/ssl/my_ca/server.crt -days 3650
        creates: /etc/ssl/my_ca/server.crt

    - name: Debug: Check if the server certificate file exists
      stat:
        path: /etc/ssl/my_ca/server.crt
      register: server_crt_status

    - name: Show status of server certificate
      debug:
        var: server_crt_status
