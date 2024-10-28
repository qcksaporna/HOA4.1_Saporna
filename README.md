# HOA4.1_Saporna
---
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

- name: Install Elasticsearch from the repository
  yum:
    name: elasticsearch
    state: present

- name: Install Elasticsearch from RPM link
  yum:
    name: https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.10.2-x86_64.rpm
    state: present

- name: Start and enable Elasticsearch service
  systemd:
    name: elasticsearch
    enabled: yes
    state: started




