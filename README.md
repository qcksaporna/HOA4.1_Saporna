---
- name: Capture Network Traffic from Multiple Interfaces and Create PCAP Files
  hosts: control
  become: true
  gather_facts: no
  tasks:

    - name: Install tshark
      ansible.builtin.yum:
        name: tshark
        state: present
      when: ansible_facts.packages['tshark'] is not defined

    - name: Capture traffic from enp0s3 using tshark
      ansible.builtin.command:
        cmd: "tshark -i enp0s3 -w /home/qcksaporna1/pcaps/file_enp0s3.pcap -c 100"
      async: 300
      poll: 0

    - name: Capture traffic from enp0s8 using tshark
      ansible.builtin.command:
        cmd: "tshark -i enp0s8 -w /home/qcksaporna1/pcaps/file_enp0s8.pcap -c 100"
      async: 300
      poll: 0

    - name: Wait for the PCAP files to be created (enp0s3)
      ansible.builtin.wait_for:
        path: "/home/qcksaporna1/pcaps/file_enp0s3.pcap"
        state: present
        timeout: 600

    - name: Wait for the second PCAP file to be created (enp0s8)
      ansible.builtin.wait_for:
        path: "/home/qcksaporna1/pcaps/file_enp0s8.pcap"
        state: present
        timeout: 600

    - name: Extract executable files from the first PCAP (enp0s3)
      ansible.builtin.command:
        cmd: |
          tshark -r /home/qcksaporna1/pcaps/file_enp0s3.pcap -Y 'http.content_type contains "application/octet-stream"' -T fields -e frame.time -e ip.src -e ip.dst -e http.file_data > /home/qcksaporna1/pcaps/extracted_files_enp0s3.txt
      register: extracted_files_enp0s3
      ignore_errors: yes

    - name: Extract executable files from the second PCAP (enp0s8)
      ansible.builtin.command:
        cmd: |
          tshark -r /home/qcksaporna1/pcaps/file_enp0s8.pcap -Y 'http.content_type contains "application/octet-stream"' -T fields -e frame.time -e ip.src -e ip.dst -e http.file_data > /home/qcksaporna1/pcaps/extracted_files_enp0s8.txt
      register: extracted_files_enp0s8
      ignore_errors: yes

    - name: Notify if executable files are extracted from enp0s3
      ansible.builtin.debug:
        msg: "Extracted executable files from enp0s3 saved to /home/qcksaporna1/pcaps/extracted_files_enp0s3.txt"
      when: extracted_files_enp0s3 is not skipped

    - name: Notify if executable files are extracted from enp0s8
      ansible.builtin.debug:
        msg: "Extracted executable files from enp0s8 saved to /home/qcksaporna1/pcaps/extracted_files_enp0s8.txt"
      when: extracted_files_enp0s8 is not skipped

    - name: Notify that the PCAP files have been created
      ansible.builtin.debug:
        msg: "PCAP files successfully created and executable files extracted at /home/qcksaporna1/pcaps/"
