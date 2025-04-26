---
- name: Block IP Address
  hosts: checkpoint  # Specify the Check Point firewall device or group in your inventory
  connection: httpapi  # This is specific to Check Point's API connection
  
  tasks:
    - include_role:
        name: acl_manager  # The role responsible for managing ACLs
        tasks_from: block_ip  # Specific task in the role to block an IP address
      vars:
        source_ip: 172.17.13.98  # The IP address to block
        destination_ip: 192.168.0.10  # The destination IP address
        ansible_network_os: checkpoint  # Define the network OS
