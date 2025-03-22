---
- name: Collect log files from the Control node
  hosts: control-node
  gather_facts: false
  tasks:
    - name: Ensure the log directory exists on the Manage node
      file:
        path: "/tmp/logs"
        state: directory
        mode: '0755'
      delegate_to: manage-node

    - name: Copy syslog file from Control node to Manage node
      fetch:
        src: /var/log/syslog
        dest: /tmp/logs/syslog
        flat: yes

    - name: Copy messages file from Control node to Manage node
      fetch:
        src: /var/log/messages
        dest: /tmp/logs/messages
        flat: yes

    - name: Copy auth.log file from Control node to Manage node
      fetch:
        src: /var/log/auth.log
        dest: /tmp/logs/auth.log
        flat: yes
