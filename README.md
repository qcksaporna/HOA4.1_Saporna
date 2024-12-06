---
- name: Install OpenStack Keystone (Identity Service) on controller
  hosts: controller
  become: true
  tasks:
    - name: Install Keystone and dependencies
      apt:
        name:
          - keystone
          - python3-keystoneclient
          - apache2
          - libapache2-mod-wsgi
        state: present
        update_cache: yes

    - name: Configure Keystone database
      command: keystone-manage db_sync

    - name: Start and enable Apache and Keystone
      service:
        name: apache2
        state: started
        enabled: true

    - name: Configure Keystone API endpoint
      template:
        src: keystone_api_endpoint.j2
        dest: /etc/keystone/keystone.conf

    - name: Restart Apache to apply Keystone settings
      service:
        name: apache2
        state: restarted

- name: Install OpenStack Glance (Imaging Service) on controller
  hosts: controller
  become: true
  tasks:
    - name: Install Glance and dependencies
      apt:
        name:
          - glance
          - python3-glanceclient
        state: present
        update_cache: yes

    - name: Configure Glance database
      command: glance-manage db_sync

    - name: Start and enable Glance services
      service:
        name: glance-api
        state: started
        enabled: true

    - name: Configure Glance API endpoint
      template:
        src: glance_api_endpoint.j2
        dest: /etc/glance/glance-api.conf

    - name: Restart Glance services
      service:
        name: glance-api
        state: restarted

- name: Install OpenStack Nova (Compute Service) on controller
  hosts: controller
  become: true
  tasks:
    - name: Install Nova and dependencies
      apt:
        name:
          - nova-api
          - nova-conductor
          - nova-scheduler
          - nova-compute
          - python3-novaclient
        state: present
        update_cache: yes

    - name: Configure Nova database
      command: nova-manage db_sync

    - name: Start and enable Nova services
      service:
        name: nova-api
        state: started
        enabled: true

    - name: Configure Nova API endpoint
      template:
        src: nova_api_endpoint.j2
        dest: /etc/nova/nova.conf

    - name: Restart Nova services
      service:
        name: nova-api
        state: restarted

- name: Install OpenStack Nova (Compute Service) on compute node
  hosts: compute
  become: true
  tasks:
    - name: Install Nova and dependencies
      apt:
        name:
          - nova-compute
        state: present
        update_cache: yes

    - name: Configure Nova compute service
      command: nova-compute --config-file /etc/nova/nova.conf

    - name: Start and enable Nova compute service
      service:
        name: nova-compute
        state: started
        enabled: true
