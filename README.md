---
- name: Extract Executable from PCAP on Control Node
  hosts: control-node
  remote_user: qcksaporna1
  become: true
  tasks:

    - name: Ensure tshark is installed on the control node
      package:
        name: tshark
        state: present

    - name: Extract data from the PCAP file using tshark
      shell: |
        tshark -r /tmp/sample.pcap -T fields -e data > /tmp/extracted_data.bin
      args:
        chdir: /tmp

    - name: Check if the extracted file is an executable
      shell: |
        file /tmp/extracted_data.bin
      register: file_type
      ignore_errors: true  # Continue even if this step fails

    - name: Debug file type of the extracted data
      debug:
        msg: "The file type is: {{ file_type.stdout }}"

    - name: Check if the file is executable before moving
      command: mv /tmp/extracted_data.bin /tmp/extracted_executable
      when: "'executable' in file_type.stdout"

    - name: Ensure correct permissions for the extracted executable
      file:
        path: /tmp/extracted_executable
        mode: '0755'
        state: file
      when: "'executable' in file_type.stdout"

    - name: Notify if no executable found in the data
      debug:
        msg: "No executable found in the extracted data."
      when: "'executable' not in file_type.stdout"
