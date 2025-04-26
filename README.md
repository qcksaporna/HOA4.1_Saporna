---
- name: Block IP Address
  hosts: checkpoint  # The Check Point firewall device or group in your inventory
  connection: httpapi  # Check Point uses httpapi for communication
  
  vars:
    ansible_network_os: checkpoint  # Set the network OS for Check Point
    ansible_user: "your_username"   # Set your Check Point username
    ansible_password: "your_password"  # Set your Check Point password

  tasks:
    - include_role:
        name: acl_manager
        tasks_from: block_ip
      vars:
        source_ip: 172.17.13.98
        destination_ip: 192.168.0.10
