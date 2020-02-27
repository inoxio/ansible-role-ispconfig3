Ansible Role: Installs and customizes ISPConfig 3 on Ubuntu Bionic
=========

The inoxio.ispconfig3 role will help you to install ISPConfig on your Ubuntu 18.04 machine.
It follows the instructions of the howtoforge.com [tutorial](https://www.howtoforge.com/tutorial/perfect-server-ubuntu-18.04-with-apache-php-myqsl-pureftpd-bind-postfix-doveot-and-ispconfig/)
and allows customizations which will be explained in the following segments.
It also implements Molecule as a test environment and contains a few tips if you want use Molecule for yourself.

Requirements
------------

- Machine with Ubuntu 18.04

Dependencies
------------

- oefenweb.apt

Role Variables
--------------

Mail, PHPMyAdmin and Roundcube will set mostly the admin passwords of the named application.
Mail itself will also need an email with which it can send mails to.

ISPConfig is here the most important part, it contains a lot of different variables. 
Most of them can be translated into the autoinstall.php from ISPConfig.
It is also important to notice that the mysql_root_password will be used for the setup 
of the MariaDB module.  

**Playbook** variables:
The following variables are required in the playbook (except the passwords, that will be generated if left out).
There are checks that verify that these variables are stated in the playbook. The [default](defaults/main.yml) variables
can be overwritten in the playbook.

`Mail`: This will set all needed variables to setup a new mailing list. It will
automatically create a mailing list with the given Parameters.
* `mail_admin_email`: 
    Mail which will send/receive updates from Mailman.
* `mail_admin_password`: 
    (Optional) Password for Mailman admin. If no password is set a password will be generated and printed at 
    the end of role execution.
* `mail_base_domain`: Base domain for Mailman.

* `phpmyadmin_hostname`: Base domain for phpmyadmin.

* `roundcube_admin_password`: (Optional) Password for Admin Login in Roundcube. If no password is set a password
    will be created and printed at the end of role execution.

`ispconfig`: This contains the most configurations for your ISPConfig setup. 
Please have a look at the example provided by ISPConfig itself. 
You can find it [here](https://git.ISPConfig.org/ISPConfig/ISPConfig3/blob/master/docs/autoinstall_samples/autoinstall.ini.sample).
* `ispconfig_admin_password`: (Optional) Password for the ISPConfig Admin Login. If no password is set a password will be 
generated and printed at the end of role execution.
* `ispconfig_mysql_root_password`: (Optional) Password of the root user, please choose a secure one. Even if your database 
isn't exposed. If no password is set a password will be generated and printed at the end of role execution.   
* `ispconfig_mysql_ispconfig_password`: (Optional) Login for MySQL. If no password is set a password will be generated and 
printed at the end of role execution.    
* `ispconfig_mysql_master_root_password`: (Optional) Password for the root user of the master data. If no password is set a 
password will be generated and printed at the end of role execution.
* `ispconfig_hostname`: Name of the ISPConfig host.

* `roundcube_hostname`: Name of the roundcube host

* `quota_mounts`: Is a list of all directories to be remounted for quota. See section "Quota".
 
   
[**Default**](defaults/main.yml) variables:
* `language`: System wide language

* `mailing_list_name`:
    Sets the mailman List. It is recommended to set it as Mailman (see example playbook).
    * Default: `Mailman` 
    
* `ispconfig_install_mode`: Set the mode you want to have if you just want to install it regularly it is recommended to use the default.
    * Default: `standard`
* `ispconfig_hostname`: This is used for your FQDN where you can set your sitename.
* `ispconfig_mysql_hostname`: Location where the MySQL Database is running, if you just use this role the default is sufficient.
    * Default: `localhost` 
* `ispconfig_mysql_root_user`: Name of the root user. In most cases this will be just 'root'. If you customize your
    ISPConfig set it as you wish. It will also set the name of the root user in the MariaDB Setup. 
    * Default: `root`
* `ispconfig_mysql_database`: Database which will be initialized in MariaDB setup and used from ISPConfig. 
    * Default: `dbISPConfig`
* `ispconfig_mysql_ispconfig_user`: User which will be created inside the MySQL database.
    * Default: `ISPConfig`
* `ispconfig_mysql_port`: Port which is used by MariaDB, it is also used for the database initialization.  
    * Default: `3306`
* `ispconfig_mysql_charset`: Charset of the database.  
    * Default: `utf8`
* `ispconfig_http_server`: Sets the HTTP server which is running behind the ISPConfig site.
    * Default: `apache`
* `ispconfig_ispconfig_port`: Port for the Website to use.
    * Default: `8080`
* `ispconfig_ispconfig_use_ssl`:  Use SSL to connect to ISPConfig.
    * Default: `y`  
* `ispconfig_join_multiserver_setup`: Joins multiple servers. 
    * Default: `n`
* `ispconfig_mysql_master_hostname`: Location of the master MySQL database, for most cases 'localhost' is sufficient.
    * Default: `localhost`  
* `ispconfig_configure_mail`: Mailman setup for ISPConfig.
    * Default: `y`
* `ispconfig_configure_jailkit`: Jailkit setup for ISPConfig.
    * Default: `y`
* `ispconfig_configure_ftp`: PureFTP setup for ISPConfig.
    * Default: `y`
* `ispconfig_configure_dns`: DNS setup for ISPConfig.
    * Default: `y`
* `ispconfig_configure_nginx`: NGINX setup for ISPConfig.
    * Default: `n`
* `ispconfig_configure_apache`: Apache setup for ISPConfig.
    * Default: `y`
* `ispconfig_configure_firewall`: Firewall setup for ISPConfig.
    * Default: `y`
* `ispconfig_install_ispconfig_web_interface`: Webinterface setup for ISPConfig.
    * Default: `y`
* `ispconfig_do_backup`: Backup setup for ISPConfig.
    * Default: `yes`
* `ispconfig_mysql_master_database`: Database which contains the information for ISPConfig.
    * Default: `dbISPConfig`
* `ispconfig_reconfigure_permissions_in_master_database`: Sets the permissions needed for ISPConfig in the database. 
    * Default: `no`
* `ispconfig_reconfigure_services`: Reconfigures all services.
    * Default: `yes`
* `ispconfig_create_new_ispconfig_ssl_cert`: Creates new certificate (will not be needed). 
    * Default: `no`
* `ispconfig_reconfigure_crontab`: Configures cronjob.
    * Default: `yes`
* `ispconfig_configure_webserver`: Configures the webserver.
    
         
* `quota_mounts`:  List of directories that quota will watch over.
    * Default: `/`
          
Quota 
------------
The list `quota_mounts` in [defaults/main.yml](defaults/main.yml) contains all directories that will be edited in the fstab file to enable quota on
them. If the list is empty quota will not be enabled. You can overwrite this list by adding the quota variable in the playbook 
(See section "Role Variables"). If the kernel on your machine does not contain the quota
modules, you have to keep the list empty. This is the case for AWS machines that use the 
[linux-aws kernel](https://bugs.launchpad.net/ubuntu/+source/linux-aws/+bug/1773172).

Example Playbook
----------------
This shows an example how you could configure your playbook.

    - role: inoxio.ispconfig3
      mail_admin_email: email@your-company.com
      mail_base_domain: your-company.com
      ispconfig_hostname: ispconfig.your-company.com
      roundcube_hostname: mail.your-company.de
     
Everything else mentioned in role variables can be found in the defaults/main.yml.  
All settings for the ISPConfig role are taken from the config file for the Apache2 setup.
See [this link](https://git.ISPConfig.org/ISPConfig/ISPConfig3/blob/master/docs/autoinstall_samples/autoinstall.ini.sample) 
for more information. 
You can find example settings under autoinstall.php for ISPConfig.

Start as VM and run tests with Molecule
--------------

Everything here is taken from https://Molecule.readthedocs.io/en/latest/. It's convenient to have everything in 
one place.

This will help you to run Ansible roles on a real virtual machine without caring about the state of the VM.  

To start the role and run the test environment you will require Molecule. 
The following will explain how to setup everything and run the tests. 

1. Install virtualenv
    * Install virtualenv `sudo apt install virtualenv`
    * Create virtualenv inside your folder `virtualenv --no-site-packages .venv ` 
    This will contain all installed packages. This is the cleanest way to prevent your system from package cluttering.
2. Start virtualenv 
    * You will run this command every time you restart your console, or enter a new session.
    * `source  .venv/bin/activate`
3. Install the Molecule package
    * `pip3 install Molecule ansible`
    * You will need to install the Python API for your driver which you will use, in the case of this role it will be Vagrant.
    * Run this command if you see the `(.venv)`  at the beginning of your console line.
    * `pip install python-vagrant`
    
These are the requirements you have to fulfill before starting Molecule. In the following it will be explained 
how you can setup roles for testing. 

Run Test
------------

Running tests is fairly easy, just go into your venv as seen in step 2 above and run: 

`Molecule test`

This will automatically setup your driver (Docker or Vagrant in most cases) and run different test scenarios 
such as idempotence tests or your own written tests which can test all kind of things.


New Role
------------
`Molecule init role -r new-role -d vagrant` 

This will initialize a new Vagrant role with the name `new-role` and 
it will contain the standard setup for Vagrant. As alternative you can set your driver to Docker or other 
supported methods of virtualization. 

Upgrade Molecule
------------
Note: Molecule is relatively new so there will be updates, that is why it is recommended to run this from time to time.

`pip install --upgrade Molecule`

Example Molecule and Playbook
------------

Here you are going to see an example how the `Molecule.yml` and the `Playbook.yml` is structured 
for this test scenario.

`Molecule.yml`

    ---
    dependency:
      name: galaxy
    driver:
      name: vagrant
      provider:
        name: virtualbox
    lint:
      name: yamllint
    platforms:
      - name: instance
        box: ubuntu/bionic64
        instance_raw_config_args:
          - "vm.hostname = 'web.my.net'"
        memory: 4096
    provisioner:
      name: ansible
      lint:
        name: ansible-lint
        enabled: false
    scenario:
      name: default
      test_sequence:
        # - lint
        - destroy
        # - dependency
        - syntax
        - create
        - prepare
        - converge
        - idempotence
        # - side_effect
        - verify
        - destroy
    verifier:
      name: testinfra
      lint:
        name: flake8
        
 Here you can see the used `Molecule.yml` for this project you can see the used driver
 and its settings, such as memory. Important Note: It was required for 
 a task in the role to set the hostname so you can see that it is set with the help
 of `instance_raw_config_args`. 
 You can use different boxes directly from Vagrant or load them from a link. 
 For this type of change please consult the documentation of Molecule. 
 
 
 If you take a look at the `Playbook.yml` you will see it is similar to the normal
 role call but the name. See the full example below in the example playbook.
 
 `Playbook.yml`
    
    ---
    - name: Converge
      hosts: all
      roles:
         - role: inoxio.ispconfig3
             ***
             ***


Molecule
------------------

This section will contain a conclusion about Molecule because this was the first setup of it. 

The first run with Molecule was with Docker as the driver. With this used method there were roadblocks really fast. 
Because of the nature of Docker and the complexity of this role. 
This started with services which aren't loaded inside a container and went on to PID which are different. 
So we had come to the conclusion that it isn't pleasant to adapt your code to your testing framework. 

This left the choice of using Vagrant as the driver. This will help us with the problem of no full existing
virtual environment and running everything smoothly. 

This setup went without any major problems and tested all the wanted keypoints. 

If you want so include Molecule in your role please follow this little list: 
* Make sure you start with a blank slate and create your role in Molecule so you can test it every now and then
* If you want to use Docker make sure your role isn't too complex and you don't have to adapt your role to the Docker

