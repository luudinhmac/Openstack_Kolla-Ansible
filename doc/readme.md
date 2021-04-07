# Openstack_Kolla-Ansible
## Deploy All-In-One OpenStack with Kolla-Ansible on Ubuntu 18.04
System Requirements
Below are the recommended minimum requirements for deploying AIO OpenStack with Kolla-Ansible:

![](../Image/1.png)



2 (or more) network interfaces.
At least 8gb main memory
At least 40gb disk space
Below are our deployment system specifics;

Interfaces	2 network interfaces:
enp1s0: 192.168.122.216/24
enp6s0: no assigned IP address
RAM	8 GB
vCPUs	2
Storage	/dev/vda (root filesystem), /: 50G
/dev/vda5 (Volume group, openstack_cinder): 50G
Virtualization Platform	KVM
Operating System	Ubuntu 18.04 LTS
User	non root user with sudo rights
You can provide as much resources since the more resources you have the better the performance of the stack.
