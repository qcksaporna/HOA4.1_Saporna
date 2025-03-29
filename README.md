---
- name: Extract Executable from PCAP and Store Locally on Control Node
  hosts: control
  gather_facts: yes
  tasks:

    - name: Check if tshark is installed
      ansible.builtin.command:
        cmd: tshark -v
      register: tshark_check
      failed_when: false

    - name: Install tshark if not present
      ansible.builtin.yum:
        name: tshark
        state: present
      when: tshark_check.rc != 0

    - name: Ensure PCAP file is present
      ansible.builtin.stat:
        path: /home/qcksaporna1/pcaps/file.pcap
      register: pcap_file

    - name: Fail if PCAP file is missing
      ansible.builtin.fail:
        msg: "PCAP file not found on control node"
      when: not pcap_file.stat.exists

    - name: Extract executable from PCAP using tshark
      ansible.builtin.command:
        cmd: "tshark -r /home/qcksaporna1/pcaps/file.pcap -T fields -e data > /tmp/extracted_executable.bin"
      register: extracted_executable
      failed_when: false

    - name: Check if the executable was extracted
      ansible.builtin.stat:
        path: /tmp/extracted_executable.bin
      register: extracted_executable_stat

    - name: Fail if extraction failed
      ansible.builtin.fail:
        msg: "Executable extraction failed"
      when: not extracted_executable_stat.stat.exists

    - name: Notify of successful extraction
      ansible.builtin.debug:
        msg: "Executable successfully extracted and saved on control node at /tmp/extracted_executable.bin"

    - name: Clean up temporary executable on control node
      ansible.builtin.file:
        path: /tmp/extracted_executable.bin
        state: absent
