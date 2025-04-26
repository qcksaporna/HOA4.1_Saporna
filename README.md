---
- name: Block IP Address
  hosts: checkpoint  # The Check Point firewall device or group in your inventory
  connection: httpapi  # Check Point uses httpapi for communication
  
  vars:
    ansible_network_os: checkpoint  # Set the network OS for Check Point
    ansible_user: "your_username"   # Set your Check Point username
    ansible_password: "your_password"  # Set your Check Point password

  tasks:
    - include_role:
        name: acl_manager
        tasks_from: block_ip
      vars:
        source_ip: 172.17.13.98
        destination_ip: 192.168.0.10


TASK [ansible_security.acl_manager : Search source IP host object] *************
An exception occurred during task execution. To see the full traceback, use -vvv. The error was: ansible.module_utils.connection.ConnectionError: string indices must be integers, not str
fatal: [192.168.56.113]: FAILED! => {"changed": false, "module_stderr": "Traceback (most recent call last):\n  File \"/home/qcksaporna1/.ansible/tmp/ansible-local-13104sxf8Zv/ansible-tmp-1745635130.96-13201-14764545747073/AnsiballZ_checkpoint_object_facts.py\", line 102, in <module>\n    _ansiballz_main()\n  File \"/home/qcksaporna1/.ansible/tmp/ansible-local-13104sxf8Zv/ansible-tmp-1745635130.96-13201-14764545747073/AnsiballZ_checkpoint_object_facts.py\", line 94, in _ansiballz_main\n    invoke_module(zipped_mod, temp_path, ANSIBALLZ_PARAMS)\n  File \"/home/qcksaporna1/.ansible/tmp/ansible-local-13104sxf8Zv/ansible-tmp-1745635130.96-13201-14764545747073/AnsiballZ_checkpoint_object_facts.py\", line 40, in invoke_module\n    runpy.run_module(mod_name='ansible.modules.network.check_point.checkpoint_object_facts', init_globals=None, run_name='__main__', alter_sys=True)\n  File \"/usr/lib/python2.7/runpy.py\", line 188, in run_module\n    fname, loader, pkg_name)\n  File \"/usr/lib/python2.7/runpy.py\", line 82, in _run_module_code\n    mod_name, mod_fname, mod_loader, pkg_name)\n  File \"/usr/lib/python2.7/runpy.py\", line 72, in _run_code\n    exec code in run_globals\n  File \"/tmp/ansible_checkpoint_object_facts_payload_Gm1O28/ansible_checkpoint_object_facts_payload.zip/ansible/modules/network/check_point/checkpoint_object_facts.py\", line 116, in <module>\n  File \"/tmp/ansible_checkpoint_object_facts_payload_Gm1O28/ansible_checkpoint_object_facts_payload.zip/ansible/modules/network/check_point/checkpoint_object_facts.py\", line 107, in main\n  File \"/tmp/ansible_checkpoint_object_facts_payload_Gm1O28/ansible_checkpoint_object_facts_payload.zip/ansible/modules/network/check_point/checkpoint_object_facts.py\", line 91, in get_object\n  File \"/tmp/ansible_checkpoint_object_facts_payload_Gm1O28/ansible_checkpoint_object_facts_payload.zip/ansible/module_utils/connection.py\", line 190, in __rpc__\nansible.module_utils.connection.ConnectionError: string indices must be integers, not str\n", "module_stdout": "", "msg": "MODULE FAILURE\nSee stdout/stderr for the exact error", "rc": 1}
