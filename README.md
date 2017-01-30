Role Name
=========

Role that configures nagios and sincronizes configuration for nagios from git repositories.
This role is prepared and compatible with other ansiblecoffee nagios roles plugins, thruk interface, pnp4nagios, etc.

Requirements
------------

No requirements, just keep your system updated.
It's tested only on Ubuntu 14.04 for now.

Role Distribution support
------------------------

Ubuntu: ok
RedHat: Not ready

Role Variables
--------------

Add to your host_vars or group_vars:

### Extra Nagios configuration options

And add to readonly: 

nagios_cgi_authorized_for_read_only: user1, user2

Or change the permissions specified: 

    nagios_cgi_authorized_for_system_information: nagiosadmin
    nagios_cgi_authorized_for_configuration_information: nagiosadmin
    nagios_cgi_authorized_for_system_commands: nagiosadmin
    nagios_cgi_authorized_for_all_services: nagiosadmin,user1,user2
    nagios_cgi_authorized_for_all_hosts: nagiosadmin,user1,user2
    nagios_cgi_authorized_for_all_service_commands: nagiosadmin
    nagios_cgi_authorized_for_all_host_commands: nagiosadmin

*Disable notifications on hosts not required using host_vars/hostname*

nagios_config_enable_notifications: 0

### Clone repositories feature
If you want you can setup some git repository to clone configuration on subdir specified, this is excelent feature from this role.
You can have multiple repositories for example:

* Network repository, dir: "net"
* servers repository, dir: "servers"
* servers_services repository, dir: "services"

You can then reuse just few of these or add extra different configuration for this var to other hosts like:
* Remote host without services check
* Testing server with testing repository, dir: "testing" and all the previous repos.

You can then use *tag*: "config_nagios" on your ansible-playbook task to sincronize all your servers each time you update the repositories.

Even better: Use something like jenkins githook to automatically run the task that calls the tag: config_nagios and all your servers will be automatically sincronized each time you push something new to your nagios repositories with new config.

### Example:

    nagios_config_cfg_dirs_git:
      - { repo: "http://server/user/repo.git", version: "master", dir: "repo"}
      - { repo: "http://users:password@server/user/repo2.git", version: "master", dir: "repo2"}

### You can also use another var with ssh key: 

First create the directory for keys on remote server for ansible remote user

    cd
    mkdir keys

Then generate the keys and save as nagios_key_rsa 

    nagios_config_cfg_dirs_git_rsa:
      - { repo: "git@server:user/repo.git", version: "master", dir: "repo"}

When nagios_config_cfg_dirs_git_rsa var exists, the role will look for /home/remoteuser/keys/nagios_key_rsa to use it. 



Dependencies
------------

A list of other roles hosted on Galaxy should go here, plus any details in regards to parameters that may need to be set for other roles, or variables that are used from other roles.

Example Playbook
----------------



### Full list of roles:

``` yaml
- name: apply Nagios settings
  hosts: nagios4_servers
  become: yes
  become_method: sudo
  roles:
    - { role: nagios4_server, tags: ["install", "nagios4_server_all", "nagios4_server"] }
    - { role: nagios4_server_plugins, tags: ["install", "nagios4_server_all", "nagios4_server_plugins"] }
    - { role: nagios4_server_pnp4nagios, tags: ["install", "nagios4_server_all", "nagios4_server_pnp4nagios"] }
    - { role: nagios4_server_snmptrap, tags: ["install", "nagios4_server_all", "nagios4_server_snmptrap"] }
    - { role: ANXS.mysql, tags: ["install", "nagios4_server_all", "nagios4_server_thruk", "ANXS.mysql"] }
    - { role: nagios4_server_thruk, tags: ["install", "nagios4_server_all", "nagios4_server_thruk"] }
    - { role: postfix_client, tags: ["install", "nagios4_server_all", "postfix_client"] }
# Additional tags: role/tag
# nagios4_server             - config_nagios
# nagios4_server             - nagios4_server_main_config
# nagios4_server             - config_nagios_cron
# nagios4_server_plugins     - config_nagios_plugins
# nagios4_server_plugins     - test_nagios_plugins
# nagios4_server_pnp4nagios  - test_nagios_pnp4nagios
# nagios4_server_thruk       - config_nagios_thruk_cron
# nagios4_server_thruk       - test_nagios_thruk
# nagios4_server_thruk_git   - config_nagios_thruk_git_cron
```


tags:

    nagios4_server_main_config
    nagios_config
    config_nagios_cron

Behind Firewalls
---------

For clients to firewalled hosts you must open these ports: 

TCP: 
* Ping: host-alive
* 5666: nrpe     (windows and linux)
* 1248: nsclient (windows only)
* 9666: for custom nrpe when 5666 is used
* 22: ssh checks
* 80,443: http, https checks

UDP: 
* 161: SNMP checks
		
License
-------

BSD

Author Information
------------------

Main authors: Diego Daguerre, Pablo Estigarribia.
Site: https://github.com/ansiblecoffee


