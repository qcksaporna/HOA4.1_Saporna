---
- name: Collect logs from the Control node using journalctl
  hosts: control-node
  gather_facts: false
  become: yes
  tasks:
    - name: Ensure the log directory exists on the Manage node
      file:
        path: "/tmp/logs"
        state: directory
        mode: '0755'
      delegate_to: manage-node

    - name: Collect system logs using journalctl
      command: journalctl --since "2025-03-22" --until "2025-03-23"  # Adjust the date/time range as needed
      register: journal_logs

    - name: Save journal logs to file on Manage node
      copy:
        content: "{{ journal_logs.stdout }}"
        dest: "/tmp/logs/journal_logs.txt"
      delegate_to: manage-node
