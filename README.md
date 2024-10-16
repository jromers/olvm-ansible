# OLVM - Ansible Playbooks for Oracle Linux Virtualization 


A collection of Ansible playbooks to use with Oracle Linux Virtualization Manager. Playbooks are tested with Ansible CLI commands and with Oracle Linux Automation Manager.

The playbooks uses modules from the [ovirt.ovirt Ansible collection](https://docs.ansible.com/ansible/latest/collections/ovirt/ovirt/index.html) which should be downloaded before using the playbooks. Read the collection documentation page for additional explanation or for extending the functionality of the playbooks.

# How to use the playbooks

## Ansible CLI

First step is the configuration of the playbook variables which are mostly configured in ``default_vars.yml`` file. Some of the variables may be used in the command line when not configured in the default variables file. Variables are required to configure your infrastructure settings for the OLVM server, VM configuration and cloud-init. See below table for explanation of the variables. 

The playbooks can be used like this:

    $ git clone https://github.com/jromers/olvm-lab.git olplaybooks
    $ cd olplaybooks
    $ ansible-galaxy collection install -r collections/requirements.yml
    $ vi default_vars.yml
    $ export "OVIRT_URL=https://OLVM-FQDN/ovirt-engine/api"
    $ export "OVIRT_USERNAME=admin@internal"
    $ export "OVIRT_PASSWORD=<YOUR PASSWD>"
    $ ansible-playbook -i OLVM-FQDN, -u opc --key-file ~/.ssh/id_rsa \
        -e "vm_name=oltest" -e "vm_ip_address=192.168.1.100" \
        olvm_create_single_vm.yml

Note 1: using the OLVM server FQDN, appended with a comma, is a quick-way to not use a inventory file.

Note 2: as it includes clear-text password, for better security you may want to encrypt the ``default_vars.yml`` file with the ansible-vault command. When running the playbook, ansible asks for a secret to decrypt the yml-file.

    $ ansible-vault encrypt default_vars.yml
    $ ansible-playbook -i OLVM-FQDN, -u opc --key-file ~/.ssh/id_rsa \
        -e "vm_name=oltest" -e "vm_ip_address=192.168.1.100" \
        --ask-vault-pass olvm_create_single_vm.yml

## Oracle Linux Automation Manager

### Project
In Oracle Linux Automation Manager you can directly import the playbook repository from Github as Project. The top-level directory of the repository contains the requirements file to download the ovir.ovirt ansible collection.

### Inventory
Create an inventory and add one host with the details of your OLVM server, this is the target host were you run the playbook. Make sure tou have a Machine credential setup for this host so that ansible can SSH to it (run the ping Module for this host).

### Credentials
Besides the standard SSH credential to access the target host, an additional credential is required to use the ovirt modules in the playbooks. It's based on credential type ``Red Hat Virtualization`` and you need to fill in the OLVM FQDN, username, password and CA File. For example:

    Host (Authentication URL): 	https://OLVM-FQDN/ovirt-engine/api
    Username:			admin@internal
    Password:			<passwd used for GUI logon>

### Templates
Create a new job template 

# Deploying OLVM VM instances

Two playbooks are provided to deploy new virtual machines in Oracle Linux Virtualization Manager based on a pre-configured template. This may be your own template or templates downloaded from Oracle's website which can be [imported directly in Oracle Linux Virtualization Manager](https://docs.oracle.com/en/virtualization/oracle-linux-virtualization-manager/admin/admin-admin-tasks.html#templates-create):

* [Free Oracle Linux templates](https://yum.oracle.com/oracle-linux-templates.html)
* [Single Instance and Oracle Real Application Clusters (RAC) templates](https://www.oracle.com/database/technologies/rac/vm-db-templates.html)

The Oracle provided templates use cloud-init to automate the initial setup of virtual machines and cloud-init variables are included in the playbooks.

# Variables used in the playbooks 

| Variable | Description |
| -------- | ----------- |


