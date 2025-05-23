---
- name: Configure Cisco Router
  hosts: cisco_routers
  gather_facts: no

  tasks:
    - name: Configure hostname
      cisco.ios.ios_config:
        lines:
          - hostname AnsibleRouter

    - name: Configure interface IP
      cisco.ios.ios_config:
        lines:
          - interface GigabitEthernet0/1
          - ip address 192.168.1.1 255.255.255.0
          - no shutdown

    - name: Save running config
      cisco.ios.ios_config:
        save_when: always
