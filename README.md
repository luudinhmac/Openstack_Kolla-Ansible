# Openstack_Kolla-Ansible
## Deploy All-In-One OpenStack with Kolla-Ansible on Ubuntu 18.04
System Requirements
Below are the recommended minimum requirements for deploying AIO OpenStack with Kolla-Ansible:

![](/Image/1.PNG)

Install Required Packages on Ubuntu 18.04
Before you can proceed, there are a number of required packages that needs to be installed.
Update and upgrade your system packages

```sh sudo apt update
   sudo apt upgrade
```
Install the required packages;

```sh 
   sudo apt install python3-dev python3-venv libffi-dev gcc libssl-dev git

```

Create a virtual environment for deploying Kolla-ansible. To avoid conflict between system packages and Kolla-ansible packages, it is recommended that Kolla-ansible be installed in a virtual environment.

You can create a virtual environment by executing the command below. Be sure to replace the path to your virtual environment.
```sh 
python3 -m venv $HOME/kolla-openstack
```
Next, activate your virtual environment;

```sh 
source $HOME/kolla-openstack/bin/activate
```
Once you activate the Kolla-ansible virtual environment, you shell prompt should change. See my shell prompt;

(kolla-openstack) openstack@openstack:~$
Upgrade pip;

```sh
pip install -U pip
```
Install Ansible on Ubuntu 18.04
Install Ansible from the virtual environment. If you ever log out of the virtual environment, you can always source the path to activate it;
```sh
source $HOME/kolla-openstack/bin/activate
```
Next, install Ansible. Kolla requires at least Ansible 2.8 up to 2.9.
```sh
pip install 'ansible<2.10'

```
Create an ansible configuration file on your home directory with the following tunables;

```sh
vim $HOME/ansible.cfg
[defaults]
host_key_checking=False
pipelining=True
forks=100
```
Install Kolla-ansible on Ubuntu 18.04 using pip from the virtual environment above;

```sh 
pip install kolla-ansible
```
Configure Kolla-ansible for All-in-one OpenStack Deployment
Next, create Kolla configuration directory;
```sh
sudo mkdir /etc/kolla
```
Update the ownership of the Kolla config directory to the user with which you activated Koll-ansible deployment virtual environment as.

```sh
sudo chown $USER:$USER /etc/kolla
```
Copy the main Kolla configuration file, globals.yml and the OpenStack services passwords file, passwords.yml into the Kolla configuration directory above from the virtual environment.

```sh
cp $HOME/kolla-openstack/share/kolla-ansible/etc_examples/kolla/* /etc/kolla/
```
Copy Kolla-ansible deployment inventory to the current working directory. In this tutorial, we are deploying all-in-one OpenStack with Kolla-ansible. Hence, copy the all-in-one ansible inventory file.

```sh
cp $HOME/kolla-openstack/share/kolla-ansible/ansible/inventory/all-in-one .
```
Define Kolla-Ansible Global Deployment Options
Open the globals.yml configuration file and define the AIO Kolla global deployment options;

```sh
vim /etc/kolla/globals.yml
```
Below are the basic options that we enabled for our AIO OpenStack deployment.

```sh
grep -vE '^$|^#' /etc/kolla/globals.yml
---
```
config_strategy: "COPY_ALWAYS"
kolla_base_distro: "ubuntu"
kolla_install_type: "binary"
openstack_release: "ussuri"
kolla_internal_vip_address: "192.168.122.206"
kolla_internal_fqdn: "{{ kolla-openstack.kifarunix-demo.com }}"
kolla_external_vip_address: "{{ kolla_internal_vip_address }}"
kolla_external_fqdn: "{{ kolla_internal_fqdn }}"
network_interface: "enp1s0"
neutron_external_interface: "enp6s0"
neutron_plugin_agent: "openvswitch"
enable_haproxy: "yes"
enable_cinder: "yes"
enable_cinder_backend_lvm: "yes"
keystone_token_provider: 'fernet'
cinder_volume_group: "openstack_cinder"
nova_compute_virt_type: "qemu"
Note that we enabled cinder block storage for openstack and defined the name of the existing volume group.
sudo vgs
  VG               #PV #LV #SN Attr   VSize   VFree  
  openstack_cinder   1   0   0 wz--n- <53.06g <53.06g
Refer to Kolla-ansible documentation guide to learn more about the global options used above. The configuration is also highly commented. Go through the comments for each option to learn what it is about a specific option.

Generate Kolla Passwords
Kolla passwords.yml configuration file stores various OpenStack services passwords. You can automatically generate the password using the Kolla-ansible kolla-genpwd in your virtual environment.
Ensure that your virtual environment is activated
Next, generate the passwords;
```sh
kolla-genpwd
```
All generated passwords will be populated to /etc/kolla/passwords.yml file.

Configure All-in-one OpenStack deployment Inventory
You now have our deployment inventory in place. Since we are running an all-in-one deployment, we will leave all the default options defined on the all-in-one inventory file as is.

Deploy All-In-One OpenStack with Kolla-Ansible on Ubuntu 18.04
Since everything is setup, you can now start to deploy OpenStack using Kolla-ansible playbooks.

Again, ensure that your virtual environment is activated.

Bootstrap your localhost configuration before deploying containers using bootstrap-servers subcommand.
```sh 
kolla-ansible -i all-in-one bootstrap-servers
```
