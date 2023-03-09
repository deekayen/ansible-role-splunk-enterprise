# splunk-enterprise
This repository contains set of playbooks which could be used to deploy Splunk Enterprise on Windows Server via WinRM.
I used this playbooks to deploy Splunk in my company.

## Table of content
1. Description
1. Getting Started
1. List of playbooks

#### Desctiption
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

#### Getting Started
## Getting started with this playbooks will requires you to:
1. Ansible controller
1. Couple of VMs with Windows Server onboard
1. Splunk Enterprise installer
1. Powershell module Psini in ZIP archive
1. LDAP and SMTP server (if you need it)
1. Correct inventory file
1. 'Master' playbook (spl_deploy.yml) with defined variables and all needed tasks

#### Ansible
