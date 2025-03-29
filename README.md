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

    - name: Wait for the PCAP files to be created
      ansible.builtin.wait_for:
        path: "/home/qcksaporna1/pcaps/file_enp0s3.pcap"
        state: present
        timeout: 600

    - name: Wait for the second PCAP file to be created
      ansible.builtin.wait_for:
        path: "/home/qcksaporna1/pcaps/file_enp0s8.pcap"
        state: present
        timeout: 600

    - name: Notify that the PCAP files have been created
      ansible.builtin.debug:
        msg: "PCAP files successfully created at /home/qcksaporna1/pcaps/"
