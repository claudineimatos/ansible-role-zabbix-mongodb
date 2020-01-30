Role Name
=========

A role to install and configure mikoomi-mongodb-plugin to monitor MongoDB on Zabbix

Requirements
------------

This role depend's on mikoomi-mongodb-plugin fork (https://github.com/nightw/mikoomi-zabbix-mongodb-monitoring) which is write in PHP, and as such needs PHP with Mongo library from PECL. For RedHat and CentOS it does use PHP 5.6 from Remi repository as early versions deprecated Mongo library in favor of MongoDB.

In case of a Replicaset, it's needed to create a user with rights to read Oplog and ReplicaSet Statistics.
A role should be created as well as a dedicated user:

    db.runCommand({ createRole: "oplogger", 
               privileges: [{ resource: { db: 'local', collection: 'oplog.rs'},
                               actions: ['find']}, ],
               roles: [{role: 'read', db: 'local'}] })
    db.createUser ( { user: "zabbixStats", pwd: "zabbixStats", roles: [ "clusterMonitor", "oplogger"] } )

The Zabbix Template from files/mikoomi-zabbix-mongodb-monitoring/mongodb-[2.2, 2.4, 3.2] should be imported on Zabbix and associated to the target hosts.


Role Variables
--------------

This role make use of the following variables and they default values:

    zabbix_mongodb_mongodb_version: 3.2
    zabbix_mongodb_plugin_dir: "/usr/local/zabbix/mongodb/"
    zabbix_mongodb_hostname: "`hostname -s`" 
    zabbix_mongodb_username: "zabbixStats"
    zabbix_mongodb_password: "zabbixStats"
    zabbix_mongodb_mongodb_port: 27017
    zabbix_mongodb_install_zabbix_agent: False

Where:

- *zabbix_mongodb_mongodb_version* is for the MongoDB version ( mikoomi-zabbix-mongodb-monitoring supports 2.2, 2.4 and 3.2)
- *zabbix_mongodb_plugin_dir* is the folder where the scripts will be saved
- *zabbix_mongodb_hostname* is the hostname of the monitored host as it's configured on and will be validated by Zabbix Server
- *zabbix_mongodb_username* is the username to connect on MongoDB
- *zabbix_mongodb_password* is the password to connect on MongoDB
- *zabbix_mongodb_mongodb_port* is the MongoDB port
- *zabbix_mongodb_install_zabbix_agent* instruct this role to also execute the role dj-wasabi.zabbix-agent; defaults to False

Note that both username and password variables can and should be changed, as well as they should be hold on an encrypted file.
Take a look on http://docs.ansible.com/ansible/playbooks_vault.html for more information


Dependencies
------------

This role depends on:

- geerlingguy.repo-remi, to install YUM Remi repositories
- dj-wasabi.zabbix-agent, to install YUM Zabbix repositories and also zabbix agent (if it's not already installed)

Example Playbook
----------------
    - hosts: servers
      roles:
         - { role: claudineimatos.ansible-role-zabbix-mongodb }

License
-------

BSD


Notes
-----

Tested on CentOS 6 and 7 but should work on any other RedHat based distro.


Author Information
------------------

Claudinei Matos <claudineimatos@gmail.com>
