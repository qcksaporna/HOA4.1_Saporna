# HOA4.1_Saporna
---
- name: Ensure EPEL repository is installed
  yum:
    name: epel-release
    state: latest
  when: ansible_distribution == "CentOS"

- name: Import GPG key for Elasticsearch
  rpm_key:
    state: present
    key: https://artifacts.elastic.co/GPG-KEY-elasticsearch
  when: ansible_distribution == "CentOS"

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
  when: ansible_distribution == "CentOS"

- name: Install Elasticsearch
  yum:
    name: elasticsearch
    state: present
  when: ansible_distribution == "CentOS"

- name: Start and enable Elasticsearch service
  systemd:
    name: elasticsearch
    enabled: yes
    state: started
  when: ansible_distribution == "CentOS"

- name: Wait for Elasticsearch to start
  wait_for:
    port: 9200
    delay: 10
    timeout: 60
  when: ansible_distribution == "CentOS"





