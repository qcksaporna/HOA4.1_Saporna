---
- name: Install NTP on Controller Node
  hosts: controller
  become: true
  tasks:
    - name: Install NTP
      apt:
        name: ntp
        state: present

- name: Install OpenStack Packages on Controller Node
  hosts: controller
  become: true
  tasks:
    - name: Install OpenStack packages (controller services)
      apt:
        name: "{{ item }}"
        state: present
      loop:
        - python3-openstackclient
        - nova-api
        - nova-scheduler
        - nova-conductor
        - openstack-dashboard
        - rabbitmq-server
        - memcached
        - mysql-server
        - apache2
        - libapache2-mod-wsgi
        - neutron-server
        - keystone
        - glance
        - cinder
      when: ansible_os_family == 'Debian'

- name: Install SQL Database (MySQL) on Controller Node
  hosts: controller
  become: true
  tasks:
    - name: Install MySQL Server
      apt:
        name: mysql-server
        state: present

    - name: Start MySQL Service
      service:
        name: mysql
        state: started
        enabled: yes

- name: Install Message Queue (RabbitMQ) on Controller Node
  hosts: controller
  become: true
  tasks:
    - name: Install RabbitMQ
      apt:
        name: rabbitmq-server
        state: present

    - name: Start RabbitMQ Service
      service:
        name: rabbitmq-server
        state: started
        enabled: yes

- name: Install Memcached on Controller Node
  hosts: controller
  become: true
  tasks:
    - name: Install Memcached
      apt:
        name: memcached
        state: present

    - name: Start Memcached Service
      service:
        name: memcached
        state: started
        enabled: yes

- name: Install and Configure Etcd on Controller Node
  hosts: controller
  become: true
  tasks:
    - name: Install Etcd
      apt:
        name: etcd
        state: present

    - name: Start Etcd Service
      service:
        name: etcd
        state: started
        enabled: yes
