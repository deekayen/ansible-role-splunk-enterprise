# splunk-enterprise
This repository contains set of playbooks which could be used to deploy Distributed Splunk Enterprise environment on Windows Server via WinRM.
I used this playbooks to deploy Splunk in my company.

## Table of content
1. [Description](#description)
1. [Getting Started](#getting-started)
1. [List of files and description](#list-of-files-and-description)

## Desctiption
In each playbook you can find some usable information in header-comment.
Header contains the following sections:
* Tested on:
    * Version of environment in which playbook tested
* Description:
    * Desctiption of playbook
* Input: 
    * List of required variables and some information about each of it
* Output: 
    * List of output variables if they exist
* Requirements
    * Required additional information 

## Getting Started
#### Getting started with this playbooks will requires you to:
1. Ansible controller
1. A couple of VMs with Windows Server onboard
1. Splunk Enterprise installer
1. Powershell module Psini in ZIP archive
1. LDAP and SMTP server (if you need it)
1. Correct inventory file
1. 'Master' playbook (spl_deploy.yml) with defined variables and all needed tasks
1. A couple of playbooks from another my repository

#### Ansible
In our company we using Ansible 2.9 for some reasons. I can't test this set on Ansible > 2.9 so be carefully when yoi run it on different Ansible version.

#### VMs
I could be wrong but the minimum amount of servers according to documentation are:
* 1 server for manager node role
* 1 server for search head deployer role
* 3 servers for indexer cluster
* 3 servers for serch head cluser
* If you have small production environment you can run first roles on one VM

#### Splunk Installer
Download Splunk Installer (*.exe) and put it in place that ansible controller have access to.

#### Psini module
Powershell Psini module is used to manage Splunk ini configuration files.
Because servers in our company haven't access to PS Galery i am download module on PC with desired access and then archive it.
Put Psini *.zip file in place that ansible controller have access to if you have the same situation or use another ways to install it.

#### LDAP and SMTP server
You need this servers if you want to authenticate in Splunk via LDAP and send notification.

#### Inventory
Determine varibles which represented in example host.ini file.
Aslo determine role of each VM using the following groups:
* spl_idx - Members of indexer cluster
* spl_mn - Manager node server
* spl_sh - Members of search head cluster
* spl_shd - Search head deployer server

#### Master Playbook
To run all set of playbooks you need to set up spl_deploy correctly. In this file you determine most of variables and select which playbooks you need to run. Example spl_deploy.yml file contain all playbooks but you can delete some if you understand that you don't need it.

#### Another playbooks
I put some playbooks in another repository because they aren't related with splunk directly. Despite this, they are necessary to deployment process. You need:
* [powershell_install_psini.yml](https://github.com/Po-temkin/windows/blob/main/ansible/powershell/powershell_install_psini.yml)
* [windows_edit_ini.yml](https://github.com/Po-temkin/windows/blob/main/ansible/windows_edit_ini.yml)

## List of files and description
Here you can find all playbooks with their description. Playbooks are ordered in order of intended launch:
* [spl_install_spl.yml](https://github.com/Po-temkin/splunk-enterprise/blob/main/ansible/spl_install_spl.yml) - Installing Splunk Enterprise;
* [spl_add_path.yml](https://github.com/Po-temkin/splunk-enterprise/blob/main/ansible/spl_add_path.yml) - Adding Splunk executable dir to path. After this you can call splunk from CMD or Powershell;
* [powershell_install_psini.yml](https://github.com/Po-temkin/windows/blob/main/ansible/powershell/powershell_install_psini.yml) - Installing Psini module from archive;
* [spl_change_index_store.yml](https://github.com/Po-temkin/splunk-enterprise/blob/main/ansible/spl_change_index_store.yml) - Changing index store location;
* [spl_configure_manager_node.yml](https://github.com/Po-temkin/splunk-enterprise/blob/main/ansible/spl_configure_manager_node.yml) - Configuring Splunk Manager node role and performing some 'best practice' settings;
* [spl_configure_idxc_member.yml](https://github.com/Po-temkin/splunk-enterprise/blob/main/ansible/spl_configure_idxc_member.yml) - Configuring Indexer cluster members;
* [spl_configure_deployer.yml](https://github.com/Po-temkin/splunk-enterprise/blob/main/ansible/spl_configure_deployer.yml) - Configuring Splunk Deployer role;
* [spl_configure_shc_member.yml](https://github.com/Po-temkin/splunk-enterprise/blob/main/ansible/spl_configure_shc_member.yml) - Configuring Indexer cluster members;
* [spl_bring_shc_captain.yml](https://github.com/Po-temkin/splunk-enterprise/blob/main/ansible/spl_bring_shc_captain.yml) - Bringing up Search head cluster captain;
* [spl_shc_to_idxc.yml](https://github.com/Po-temkin/splunk-enterprise/blob/main/ansible/spl_shc_to_idxc.yml) - Connecting Search head cluster member to Indexer cluster;
* [spl_disable_default_app.yml](https://github.com/Po-temkin/splunk-enterprise/blob/main/ansible/spl_disable_default_app.yml) - Disabling default Splunk app(s) by adding 'state = disabled' in app.conf;
* [spl_create_health_check_role.yml](https://github.com/Po-temkin/splunk-enterprise/blob/main/ansible/spl_create_health_check_role.yml) - Creating health check role. This role have rights to REST endpoint https://example.server.com:8089/services/server/info which contain 'health_info' property.You can use this information to check indexer status which may be used on hardware load balancer;
* [spl_create_local_user.yml](https://github.com/Po-temkin/splunk-enterprise/blob/main/ansible/spl_create_local_user.yml) - Creating local user;
* [spl_ldap_add_auth.yml](https://github.com/Po-temkin/splunk-enterprise/blob/main/ansible/spl_ldap_add_auth.yml) - Adding LDAP authentication type to authentication.conf;
* [spl_ldap_map_roles.yml](https://github.com/Po-temkin/splunk-enterprise/blob/main/ansible/spl_ldap_map_roles.yml) - Mapping built-in Splunk groups with LDAP groups;
* [spl_ldap_enable_auth.yml](https://github.com/Po-temkin/splunk-enterprise/blob/main/ansible/spl_ldap_enable_auth.yml) - Enabling added LDAP stanza(s);
* [spl_change_admin_email.yml](https://github.com/Po-temkin/splunk-enterprise/blob/main/ansible/spl_change_admin_email.yml) - Changing buil-in admin email;
* [spl_configure_smtp.yml](https://github.com/Po-temkin/splunk-enterprise/blob/main/ansible/spl_configure_smtp.yml) - Adding SMTP configuration;
* [spl_change_builtin_indexes.yml](https://github.com/Po-temkin/splunk-enterprise/blob/main/ansible/spl_change_builtin_indexes.yml) - Changing frozenTimePeriodInSecs option of each built-in index;
* [spl_idxc_change_builtin_indexes.yml](https://github.com/Po-temkin/splunk-enterprise/blob/main/ansible/spl_idxc_change_builtin_indexes.yml) - Changing frozenTimePeriodInSecs option of each built-in index in cluser