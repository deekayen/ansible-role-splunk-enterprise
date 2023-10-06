Splunk Enterprise
=========

[![Project Status: Inactive – The project has reached a stable, usable state but is no longer being actively developed; support/maintenance will be provided as time allows.](https://www.repostatus.org/badges/latest/inactive.svg)](https://www.repostatus.org/#inactive)

This repository contains Ansible which could be used to deploy Distributed Splunk Enterprise environment on Windows Server via WinRM.

Requirements
------------

Enable WinRM.

1. Ansible controller
1. A couple of VMs with Windows Server onboard
1. SMTP server (for notifications)

Role Variables
--------------

    splunk_download_url: https://download.splunk.com/products/splunk/releases/9.1.1/windows/splunk-9.1.1-64e843ea36b1-x64-release.msi

    splunk_home_dir: 'C:\Program Files\Splunk'
    splunk_admin_user: ''
    splunk_admin_pass: ''

Dependencies
------------

[Ansible.Windows](https://docs.ansible.com/ansible/latest/collections/ansible/windows/index.html)

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: spunk
      roles:
         - role: deekayen.splunk_enterprise
           vars:
             splunk_admin_user: admin
             splunk_admin_pass: encryptme_with_ansible-vault


Author Information
------------------

Forked from original work by Po-temkin. Conversion to a generic role by David Norman, [deekayen](https://github.com/deekayen/ansible-splunk-enterprise).
