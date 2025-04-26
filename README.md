- name: block IP address
  hosts: checkpoint
  connection: httpapi
  vars_files:
    - vars/checkpoint_credentials.yml  # Make sure this path is correct
  vars:
    ansible_network_os: checkpoint

  tasks:
    - include_role:
        name: ansible_security.acl_manager
        tasks_from: block_ip
      vars:
        source_ip: 172.17.13.98
        destination_ip: 192.168.0.10
        checkpoint_server: "{{ checkpoint_server }}"
        checkpoint_username: "{{ checkpoint_username }}"
        checkpoint_password: "{{ checkpoint_password }}"
