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


      TASK [Generate self-signed CA certificate] *************************************
fatal: [control-node]: FAILED! => {"changed": true, "cmd": ["openssl", "req", "-new", "-x509", "-key", "/etc/ssl/my_ca/ca.key", "-out", "/etc/ssl/my_ca/ca.crt", "-days", "3650", "-subj", "/CN=My Custom CA"], "delta": "0:00:00.004306", "end": "2025-03-29 10:31:00.654675", "msg": "non-zero return code", "rc": 1, "start": "2025-03-29 10:31:00.650369", "stderr": "unable to load Private Key\n140709633118656:error:0909006C:PEM routines:get_name:no start line:../crypto/pem/pem_lib.c:745:Expecting: ANY PRIVATE KEY", "stderr_lines": ["unable to load Private Key", "140709633118656:error:0909006C:PEM routines:get_name:no start line:../crypto/pem/pem_lib.c:745:Expecting: ANY PRIVATE KEY"], "stdout": "", "stdout_lines": []}

PLAY RECAP *********************************************************************
control-node               : ok=6    changed=0    unreachable=0    failed=1    skipped=0    rescued=0    ignored=0  
