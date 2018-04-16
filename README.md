ECMP role
=========

This role facilitates the configuration of equal cost multi-path (ECMP). It supports the configuration of ECMP for IPv4. This role is abstracted for dellos9 and dellos10.

The ECMP role requires an SSH connection for connectivity to a Dell EMC Networking device. You can use any of the built-in OS connection variables or the *provider* dictionary.

Installation
------------

    ansible-galaxy install Dell-Networking.dellos-ecmp

Role variables
--------------

- Role is abstracted using the *ansible_net_os_name* variable that can take the dellos9 value
- If *dellos_cfg_generate* is set to true, the variable generates the role configuration commands in a file
- Any role variable with a corresponding state variable set to absent negates the configuration of that variable
- Setting an empty value for any variable negates the corresponding configuration
- Variables and values are case-sensitive

**dellos_ecmp keys**

| Key        | Type                      | Description                                             | Support               |
|------------|---------------------------|---------------------------------------------------------|-----------------------|
| ``weighted_ecmp`` | boolean: true, false           | Configures weighted ECMP | dellos9         |
| ``ecmp_group_max_paths`` | integer        | Configures the number of maximum-paths per ecmp-group                 | dellos9, dellos10 |
| ``trigger_threshold`` | integer        | Configures the number of link bundle utilization trigger-threshold | dellos10 |
| ``ecmp_group_path_fallback`` | boolean: true, false          | Configures ECMP group path management | dellos9  |
| ``ecmp <group id>`` | dictionary          | Configures ECMP group (see ``ecmp <group id>.*``) | dellos9 |
| ``ecmp <group id>.interface`` | list           | Configures interface into ECMP group                        | dellos9 |
| ``ecmp <group id>.link_bundle_monitor`` | boolean: true,false           | Configures link-bundle monitoring   | dellos9 |
| ``ecmp <group id>.state`` | string:present\*,absent           | Removes the ECMP group if set to absent           |  dellos9 |

Connection variables
--------------------

Ansible Dell EMC Networking roles require connection information to establish communication with the nodes in your inventory. This information can exist in the Ansible *group_vars* or *host_vars* directories, or in the playbook itself.

| Key         | Required | Choices    | Description                                         |
|-------------|----------|------------|-----------------------------------------------------|
| ``host`` | yes      |            | Specifies the hostname or address for connecting to the remote device over the specified transport. |
| ``port`` | no       |            | Specifies the port used to build the connection to the remote device; if unspecified, the value defaults to 22 |
| ``username`` | no       |            | Specifies the username that authenticates the CLI login for the connection to the remote device; if value is unspecified, the ANSIBLE_NET_USERNAME environment variable value is used  |
| ``password`` | no       |            | Specifies the password that authenticates the connection to the remote device; if value is unspecified, the ANSIBLE_NET_PASSWORD environment variable value is used  |
| ``authorize`` | no       | yes, no\*   | Instructs the module to enter privileged mode on the remote device before sending any commands; if value is unspecified, the ANSIBLE_NET_AUTHORIZE environment variable value is used, and the device attempts to execute all commands in non-privileged mode . This key is supported only in dellos9 and dellos6. |
| ``auth_pass`` | no       |            | Specifies the password to use if required to enter privileged mode on the remote device; if ``authorize`` is set to no, this key is not applicable; if value is unspecified, the ANSIBLE_NET_AUTH_PASS environment variable value is used . This key is supported only in dellos9 and dellos6. |
| ``provider`` | no       |            | Passes all connection arguments as a dictionary object; all constraints (such as required or choices) must be met either by individual arguments or values in this dictionary |

> **NOTE**: Asterisk (\*) denotes the default value if none is specified.

Dependencies
------------

The *dellos-ecmp* role is built on modules included in the core Ansible code. These modules were added in Ansible version 2.2.0.

Example playbook
----------------

This example uses the *dellos-ecmp* role to configure ECMP for IPv4. The example creates a *hosts* file with the switch details and corresponding variables. The hosts file should define the *ansible_net_os_name* variable with corresponding Dell EMC networking OS name.

When *dellos_cfg_generate* is set to true, the variable generates the configuration commands as a .part file in *build_dir* path. By default, the variable is set to false. The example writes a simple playbook that only references the *dellos-ecmp* role. The sample host_vars given below is for dellos9.

**Sample hosts file**

    leaf1 ansible_host= <ip_address> ansible_net_os_name= <OS name(dellos9,dellos10)>

**Sample host_vars/leaf1**

    hostname: leaf1
    provider:
      host: "{{ hostname }}"
      username: xxxxx 
      password: xxxxx
      authorize: yes
      auth_pass: xxxxx 
    build_dir: ../temp/dellos9
    dellos_ecmp:
      ecmp 1:
        interface:
          - fortyGigE 1/49
          - fortyGigE 1/51
        link_bundle_monitor: true
        state: present
      weighted_ecmp: true
      ecmp_group_max_paths: 3
      ecmp_group_path_fallback: true
            
**Simple playbook to setup system - leaf.yaml**

    - hosts: leaf1
      roles:
         - Dell-Networking.dellos-ecmp

**Run**

    ansible-playbook -i hosts leaf.yaml
    
(c) 2017 Dell EMC
