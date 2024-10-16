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

#### Project:
In Oracle Linux Automation Manager you can directly import the playbook repository from Github as Project. The top-level directory of the repository contains the requirements file to download the ovir.ovirt ansible collection.

#### Inventory:
Create an inventory and add one host with the details of your OLVM server, this is the target host were you run the playbook. Make sure tou have a Machine credential setup for this host so that ansible can SSH to it (run the ping Module for this host).

#### Credentials:
Besides the standard SSH credential to access the target host, an additional credential is required to use the ovirt modules in the playbooks. It's based on credential type ``Red Hat Virtualization`` and you need to fill in the OLVM FQDN, username, password and CA File. For example:

    Host (Authentication URL): 	https://OLVM-FQDN/ovirt-engine/api
    Username:			admin@internal
    Password:			<passwd used for GUI logon>

#### Templates:
Create a new job template and provide the following information:

    Inventory:		Select the inventory containing the OLVM host
    Project:		Select project from the Github repository
    Playbook:		Select playbook from Project, for example olvm_create_single_vm.yml
    Credentials:		Select Machine (SSH) credential and the Virtualization credentials
    ariables:		Enter the variables as used in the example default_vars.yml file

# Variables used in the playbooks 

| Variable | Example value | Description |
| -------- | ------------- | ----------- |
| OVIRT_URL | https://olvm-fqdn/ovirt-engine/api | The API URL of the OLVM server
| OVIRT_USERNAME | admin@internal | The name of the user, same as used for GUI login
| OVIRT_PASSWORD | your_secret_pw | The password of the user, same as used for GUI login
| olvm_cluster | Default | Name of the cluster, where VM should be created
| olvm_template | OL9U4_x86_64-olvm-b234 |Name of the template, which should be used to create VM
| vm_name | oltest | Name of the VM, will also be used as hostname
| vm_ip_address | 192.168.1.100 | Static IP address of VM, if DHCP is required cloud-init section in playbook should be changed
| vm_ram | 2048MiB | Amount of memory of the VM
| vm_root_passwd | your_secret_root_pw | Root password of the VM, used bu cloud-init
| vm_dns | 192.168.1.3 | DNS server to be used for VM
| vm_dns_domain | demo.local | DNS domainto to be used for VM
| vm_gateway | 192.168.1.1 | Default gateway to be used for VM
| vm_netmask | 255.255.255.0 | Netmask to be used for VM
| vm_timezone | Europe/Amsterdam | Timezone for VM
| vm_user | opc | Standard user for Oracle provided template, otherwise use your own or root user
| vm_user_sshpubkey | "ssh-rsa AAAA...YOUR KEY HERE...hj8= " | SSH Public key for stndard user
| src_vm | oltest | VM used as source VM for cloning operation
| src_vm_snapshot | base_snapshot | Name of snapshot of source VM, for cloning operation 
| dst_vm | oltest_cloned | Name of destination VM for cloning operation
| dst_kvmhost | KVM1 | Name (not hostname) of kvm host in OLVM cluster and destination for live-migration
| vm_id | 76c76c8b-a9ad-414e-8274-181a1ba8948b | VM ID for the VM, used for rename of VM
| vm_newname | oltest | New name for VM with vm_id, used for rename of VM


# Deploying Oracle Linux OLVM VM templates

Two playbooks are provided to deploy new virtual machines in Oracle Linux Virtualization Manager based on a pre-configured template. This may be your own template or templates downloaded from Oracle's website which can be [imported directly in Oracle Linux Virtualization Manager](https://docs.oracle.com/en/virtualization/oracle-linux-virtualization-manager/admin/admin-admin-tasks.html#templates-create):

* [Free Oracle Linux templates](https://yum.oracle.com/oracle-linux-templates.html)
* [Single Instance and Oracle Real Application Clusters (RAC) templates](https://www.oracle.com/database/technologies/rac/vm-db-templates.html)

The Oracle provided templates use cloud-init to automate the initial setup of virtual machines and cloud-init variables are included in the playbooks.

