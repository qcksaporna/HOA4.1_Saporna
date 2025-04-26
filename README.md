- name: block IP address
  hosts: checkpoint
  connection: httpapi

  tasks:
    - include_role:
        name: acl_manager
        tasks_from: block_ip
      vars:
        source_ip: 172.17.13.98
        destination_ip: 192.168.0.10
        ansible_network_os: checkpoint
TASK [include_role : acl_manager] **********************************************
ERROR! the role 'acl_manager' was not found in /home/qcksaporna1/HOA13.1_saporna/roles:/home/qcksaporna1/.ansible/roles:/usr/share/ansible/roles:/etc/ansible/roles:/home/qcksaporna1/HOA13.1_saporna

The error appears to be in '/home/qcksaporna1/HOA13.1_saporna/playbook.yml': line 7, column 15, but may
be elsewhere in the file depending on the exact syntax problem.

The offending line appears to be:

    - include_role:
        name: acl_manager
              ^ here
TASK [ansible_security.acl_manager : Search source IP host object] *************
fatal: [192.168.56.113]: FAILED! => {"changed": false, "msg": "Check Point device returned error 404 with message Username and password are required for login"}
