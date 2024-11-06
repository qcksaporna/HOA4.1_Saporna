---
- name: Install and configure Elastic Stack, Monitoring tools, and LAMP stack
  hosts: all
  become: yes
  vars_files:
    - config.yaml

  tasks:
    - name: Install common dependencies
      package:
        name:
          - curl
          - wget
          - unzip
          - vim
          - net-tools
        state: present

# ------------------------------------------------------
# Elastic Stack Installation (Elasticsearch, Logstash, Kibana)
# ------------------------------------------------------
- name: Install Elastic Stack on CentOS
  hosts: elastic_stack
  become: yes
  tasks:
    - name: Install Elasticsearch
      when: ansible_os_family == 'RedHat'
      yum:
        name: "https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-{{ elastic_stack.elasticsearch_version }}-x86_64.rpm"
        state: present

    - name: Start Elasticsearch service
      service:
        name: elasticsearch
        state: started
        enabled: yes

    - name: Install Logstash
      when: ansible_os_family == 'RedHat'
      yum:
        name: "https://artifacts.elastic.co/downloads/logstash/logstash-{{ elastic_stack.logstash_version }}-x86_64.rpm"
        state: present

    - name: Start Logstash service
      service:
        name: logstash
        state: started
        enabled: yes

    - name: Install Kibana
      when: ansible_os_family == 'RedHat'
      yum:
        name: "https://artifacts.elastic.co/downloads/kibana/kibana-{{ elastic_stack.kibana_version }}-x86_64.rpm"
        state: present

    - name: Start Kibana service
      service:
        name: kibana
        state: started
        enabled: yes

# ------------------------------------------------------
# Monitoring Tools Installation (Prometheus, Grafana, InfluxDB)
# ------------------------------------------------------
- name: Install Monitoring Tools on Ubuntu
  hosts: monitoring
  become: yes
  tasks:
    - name: Install Prometheus
      when: ansible_os_family == 'Debian'
      apt:
        deb: "https://github.com/prometheus/prometheus/releases/download/v{{ monitoring.prometheus_version }}/prometheus-{{ monitoring.prometheus_version }}.linux-amd64.tar.gz"
        state: present
        force: yes

    - name: Install Grafana
      when: ansible_os_family == 'Debian'
      apt:
        deb: "https://dl.grafana.com/oss/release/grafana-{{ monitoring.grafana_version }}-amd64.deb"
        state: present

    - name: Install InfluxDB
      when: ansible_os_family == 'Debian'
      apt:
        deb: "https://dl.influxdata.com/influxdb/releases/influxdb-{{ monitoring.influxdb_version }}_amd64.deb"
        state: present

    - name: Start Prometheus service
      service:
        name: prometheus
        state: started
        enabled: yes

    - name: Start Grafana service
      service:
        name: grafana-server
        state: started
        enabled: yes

    - name: Start InfluxDB service
      service:
        name: influxdb
        state: started
        enabled: yes

# ------------------------------------------------------
# LAMP Stack Installation (Apache, PHP, MariaDB)
# ------------------------------------------------------
- name: Install LAMP Stack on CentOS
  hosts: lamp_stack
  become: yes
  tasks:
    - name: Install Apache
      yum:
        name: httpd
        state: present

    - name: Start Apache service
      service:
        name: httpd
        state: started
        enabled: yes

    - name: Install PHP
      yum:
        name: php
        state: present

    - name: Install MariaDB
      yum:
        name: mariadb-server
        state: present

    - name: Start MariaDB service
      service:
        name: mariadb
        state: started
        enabled: yes
