# Oracle RAC setup

Requires 9Gb of RAM 

* DNS server - 1 Gb
* nodes - 4 Gb each

## Setup
**Start VMs:**
~~~
vagrant up 
~~~

**Run ansible playbook**
~~~
cd ansible
ansible-playbook -i inventory/inventory.yml playbook/setup-ora-nodes.yml -v
~~~


