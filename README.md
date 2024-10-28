# HOA4.1_Saporna
---
- name: Install Elasticsearch on CentOS
  hosts: all
  become: true
  tasks:
    - name: Ensure necessary packages are installed
      yum:
        name: 
          - wget
          - curl
        state: present

    - name: Import GPG key
      rpm_key:
        state: present
        key: https://artifacts.elastic.co/GPG-KEY-elasticsearch

    - name: Create Elasticsearch repository
      copy:
        dest: /etc/yum.repos.d/elasticsearch.repo
        content: |
          [elasticsearch-8.x]
          name=Elasticsearch repository for 8.x packages
          baseurl=https://artifacts.elastic.co/packages/8.x/yum
          gpgcheck=1
          gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
          enabled=1
          autorefresh=1
          type=rpm-md

    - name: Install Elasticsearch
      yum:
        name: elasticsearch
        state: present

    - name: Start and enable Elasticsearch service
      systemd:
        name: elasticsearch
        state: started
        enabled: yes




