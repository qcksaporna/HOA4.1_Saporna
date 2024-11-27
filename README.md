---
- name: Install NTP on Controller Node
  hosts: controller
  become: true
  tasks:
    - name: Install NTP
      apt:
        name: ntp
        state: present
      tags:
        - ntp  # Tag this task as 'ntp'

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
      when: ansible_os_family == 'Debian'
      tags:
        - openstack_packages  # Tag for the OpenStack packages

- name: Install SQL Database (MySQL) on Controller Node
  hosts: controller
  become: true
  tasks:
    - name: Install MySQL Server
      apt:
        name: mysql-server
        state: present
      tags:
        - mysql  # Tag for MySQL installation

    - name: Start MySQL Service
      service:
        name: mysql
        state: started
        enabled: yes
      tags:
        - mysql_service  # Tag for MySQL service

- name: Install Message Queue (RabbitMQ) on Controller Node
  hosts: controller
  become: true
  tasks:
    - name: Install RabbitMQ
      apt:
        name: rabbitmq-server
        state: present
      tags:
        - rabbitmq  # Tag for RabbitMQ installation

    - name: Start RabbitMQ Service
      service:
        name: rabbitmq-server
        state: started
        enabled: yes
      tags:
        - rabbitmq_service  # Tag for RabbitMQ service

- name: Install Memcached on Controller Node
  hosts: controller
  become: true
  tasks:
    - name: Install Memcached
      apt:
        name: memcached
        state: present
      tags:
        - memcached  # Tag for Memcached installation

    - name: Start Memcached Service
      service:
        name: memcached
        state: started
        enabled: yes
      tags:
        - memcached_service  # Tag for Memcached service

- name: Install and Configure Etcd on Controller Node
  hosts: controller
  become: true
  tasks:
    - name: Install Etcd
      apt:
        name: etcd
        state: present
      tags:
        - etcd  # Tag for Etcd installation

    - name: Start Etcd Service
      service:
        name: etcd
        state: started
        enabled: yes
      tags:
        - etcd_service  # Tag for Etcd service
