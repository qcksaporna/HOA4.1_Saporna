# HOA4.1_Saporna
Activity 4: Running Elevated Ad hoc Commands

---
- name: Install necessary dependencies
  yum:
    name: 
      - wget
      - tar
    state: present

- name: Create Prometheus user
  user:
    name: prometheus
    shell: /sbin/nologin

- name: Create directories for Prometheus
  file:
    path: /etc/prometheus
    state: directory
    owner: prometheus
    group: prometheus

- name: Download Prometheus tarball
  get_url:
    url: https://github.com/prometheus/prometheus/releases/download/v2.44.0/prometheus-2.44.0.linux-amd64.tar.gz
    dest: /tmp/prometheus.tar.gz

- name: Extract Prometheus
  unarchive:
    src: /tmp/prometheus.tar.gz
    dest: /usr/local/bin/
    remote_src: yes

- name: Move Prometheus binaries to /usr/local/bin
  command: mv /usr/local/bin/prometheus-2.44.0.linux-amd64/{prometheus,promtool} /usr/local/bin/

- name: Move Prometheus configuration files
  command: mv /usr/local/bin/prometheus-2.44.0.linux-amd64/{consoles,console_libraries,prometheus.yml} /etc/prometheus/

- name: Set permissions for Prometheus files
  file:
    path: /usr/local/bin/{{ item }}
    state: file
    owner: prometheus
    group: prometheus
  loop:
    - prometheus
    - promtool

- name: Create Prometheus systemd service
  copy:
    dest: /etc/systemd/system/prometheus.service
    content: |
      [Unit]
      Description=Prometheus
      Wants=network-online.target
      After=network-online.target

      [Service]
      User=prometheus
      ExecStart=/usr/local/bin/prometheus --config.file /etc/prometheus/prometheus.yml --storage.tsdb.path /var/lib/prometheus/
      Restart=always

      [Install]
      WantedBy=multi-user.target

- name: Reload systemd daemon
  systemd:
    daemon_reload: yes

- name: Enable and start Prometheus service
  systemd:
    name: prometheus
    enabled: yes
    state: started

- name: Open Prometheus port in the firewall
  firewalld:
    port: 9090/tcp
    permanent: yes
    state: enabled

- name: Reload firewall to apply changes
  firewalld:
    immediate: yes
    state: reloaded






