---
- name: Install Neutron on the Controller Node
  hosts: controller
  tasks:
    - name: Install required packages for Neutron
      apt:
        name: 
          - neutron-server
          - neutron-plugin-ml2
          - neutron-linuxbridge-agent
        state: present
        update_cache: yes

    - name: Configure Neutron - Update neutron.conf
      lineinfile:
        path: /etc/neutron/neutron.conf
        regexp: '^auth_strategy='
        line: 'auth_strategy = keystone'

    - name: Configure Neutron - Set database connection
      lineinfile:
        path: /etc/neutron/neutron.conf
        regexp: '^connection='
        line: 'connection = mysql+pymysql://neutron:NEUTRON_DBPASS@controller/neutron'

    - name: Configure Modular Layer 2 (ML2)
      blockinfile:
        path: /etc/neutron/plugins/ml2/ml2_conf.ini
        block: |
          [ml2]
          type_drivers = flat,vlan,vxlan
          tenant_network_types = vxlan
          mechanism_drivers = linuxbridge,l2population

          [ml2_type_vxlan]
          vni_ranges = 1:1000

          [securitygroup]
          enable_security_group = true
          firewall_driver = neutron.agent.linux.iptables_firewall.IptablesFirewallDriver

    - name: Start and enable Neutron services
      systemd:
        name: "{{ item }}"
        state: started
        enabled: true
      loop:
        - neutron-server
        - neutron-linuxbridge-agent
        - neutron-dhcp-agent
        - neutron-metadata-agent

- name: Install Horizon on the Controller Node
  hosts: controller
  tasks:
    - name: Install Horizon package
      apt:
        name: openstack-dashboard
        state: present
        update_cache: yes

    - name: Configure Horizon - Update local_settings
      lineinfile:
        path: /etc/openstack-dashboard/local_settings.py
        regexp: '^ALLOWED_HOSTS ='
        line: "ALLOWED_HOSTS = ['*']"

    - name: Restart Apache
      systemd:
        name: apache2
        state: restarted
        enabled: true

- name: Install Cinder on the Controller Node
  hosts: controller
  tasks:
    - name: Install Cinder packages
      apt:
        name:
          - cinder-api
          - cinder-scheduler
        state: present
        update_cache: yes

    - name: Configure Cinder
      lineinfile:
        path: /etc/cinder/cinder.conf
        regexp: '^auth_strategy='
        line: 'auth_strategy = keystone'

    - name: Start and enable Cinder services
      systemd:
        name: "{{ item }}"
        state: started
        enabled: true
      loop:
        - cinder-api
        - cinder-scheduler

- name: Configure Neutron on Compute Node
  hosts: compute
  tasks:
    - name: Install Neutron packages
      apt:
        name:
          - neutron-linuxbridge-agent
        state: present
        update_cache: yes

    - name: Configure Neutron on Compute Node
      lineinfile:
        path: /etc/neutron/neutron.conf
        regexp: '^auth_strategy='
        line: 'auth_strategy = keystone'

    - name: Start and enable Neutron services
      systemd:
        name: "{{ item }}"
        state: started
        enabled: true
      loop:
        - neutron-linuxbridge-agent
