---
- name: Extract Executable from PCAP on Control Node
  hosts: control-node
  remote_user: qcksaporna1
  become: true
  vars:
    pcap_file_path: "/home/qcksaporna1/pcaps/sample.pcap"

  tasks:
    - name: Ensure tshark is installed on the control node
      package:
        name: tshark
        state: present

    - name: Extract executable from the PCAP file using tshark
      shell: |
        tshark -r {{ pcap_file_path }} -T fields -e data > /tmp/extracted_data.bin
      args:
        chdir: /tmp

    - name: Identify if the extracted data is an executable
      shell: |
        file /tmp/extracted_data.bin
      register: file_type

    - name: Move extracted file if it is an executable
      when: "'executable' in file_type.stdout"
      command: mv /tmp/extracted_data.bin /tmp/extracted_executable

    - name: Ensure correct permissions for the extracted executable
      file:
        path: /tmp/extracted_executable
        mode: '0755'
        state: file
