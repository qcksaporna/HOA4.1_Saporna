# HOA4.1_Saporna
Activity 4: Running Elevated Ad hoc Commands

- name: Create Prometheus configuration directory
  file:
    path: /etc/prometheus
    state: directory

- name: Move example configuration file
  copy:
    src: /usr/local/bin/prometheus-2.42.0.linux-amd64/prometheus.yml
    dest: /etc/prometheus/prometheus.yml

- name: Create Prometheus systemd service file
  copy:
    dest: /etc/systemd/system/prometheus.service
    content: |
      [Unit]
      Description=Prometheus
      Wants=network-online.target
      After=network-online.target

      [Service]
      User=root
      ExecStart=/usr/local/bin/prometheus \
        --config.file=/etc/prometheus/prometheus.yml \
        --storage.tsdb.path=/var/lib/prometheus/ \
        --web.listen-address="0.0.0.0:9090"

      [Install]
      WantedBy=multi-user.target

- name: Create Prometheus storage directory
  file:
    path: /var/lib/prometheus
    state: directory

- name: Reload systemd daemon
  command: systemctl daemon-reload

- name: Start Prometheus service
  systemd:
    name: prometheus
    state: started
    enabled: yes
