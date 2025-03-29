---
- name: Extract executable from PCAP on control node
  hosts: control-node
  become: false
  tasks:
    - name: Check if PCAP file exists
      stat:
        path: "/home/qcksaporna1/pcaps/sample.pcap"
      register: pcap_file_status

    - name: Fail if PCAP file doesn't exist
      fail:
        msg: "PCAP file does not exist!"
      when: pcap_file_status.stat.exists == false

    - name: Create output directory if it does not exist
      file:
        path: "/tmp"
        state: directory
        mode: '0755'

    - name: Extract data from PCAP using tshark
      shell: |
        tshark -r /home/qcksaporna1/pcaps/sample.pcap -Y "http" -T fields -e http.file_data > /tmp/extracted_data.bin
      register: tshark_output
      failed_when: tshark_output.rc != 0

    - name: Check if the extracted file exists
      stat:
        path: "/tmp/extracted_data.bin"
      register: extracted_file_status

    - name: Fail if no executable data was extracted
      fail:
        msg: "No executable data extracted from the PCAP!"
      when: extracted_file_status.stat.size == 0

    - name: Display file info about extracted data
      command: file /tmp/extracted_data.bin
      register: file_info

    - name: Show extracted file details
      debug:
        msg: "Extracted file info: {{ file_info.stdout }}"
