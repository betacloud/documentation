=======
Ansible
=======

Preparations
============

* Prepare a virtual environment

.. code-block:: none

   $ virtualenv -p python3 .venv
   $ source .venv/bin/activate
   $ pip install ansible openstacksdk

* Create a ``clouds.yml`` and ``secure.yml`` file (see :ref:`Authentication` chapter)

Modules
=======

* http://docs.ansible.com/ansible/latest/list_of_cloud_modules.html#openstack

* Create a ``playbook.yml`` file

.. code-block:: yaml

   ---
   - hosts: localhost
     connection: local
     gather_facts: no

     tasks:

   # NOTE: Ansible >= 2.8
   #  - name: Generate local keypair
   #    openssh_keypair:
   #      path: id_rsa.sample
   #      size: 2048
   #      comment: ""

   # NOTE: Ansible <= 2.7
     - name: Generate local keypair
       command : ssh-keygen -t rsa -b 2048 -N "" -C "" -f id_rsa.sample
       args:
	 creates: id_rsa.sample

     - name: Create keypair
       os_keypair:
	 cloud: sample
	 name: sample
	 public_key_file: id_rsa.sample.pub
	 state: present

     - name: Create security group
       os_security_group:
	 cloud: sample
	 name: sample
	 state: present

     - name: Create security group rule - ICMP
       os_security_group_rule:
	 cloud: sample
	 security_group: sample
	 protocol: icmp
	 port_range_min: -1
	 port_range_max: -1
	 remote_ip_prefix: 0.0.0.0/0

     - name: Create security group rule - SSH
       os_security_group_rule:
	 cloud: sample
	 security_group: sample
	 protocol: tcp
	 port_range_min: 22
	 port_range_max: 22
	 remote_ip_prefix: 0.0.0.0/0

     - name: Create network
       os_network:
	 cloud: sample
	 name: sample
	 state: present

     - name: Create sub network
       os_subnet:
	 cloud: sample
	 network_name: sample
	 name: sample
	 cidr: 192.168.0.0/24
	 state: present

     - name: Create router
       os_router:
	 cloud: sample
	 name: sample
	 network: public
	 interfaces:
	   - sample
	 state: present

     - name: Create instance
       os_server:
	 cloud: sample
	 name: sample
	 flavor: 1C-1GB-10GB
	 image: "Ubuntu 18.04"
	 key_name: sample
	 network: sample
	 security_groups:
	   - sample
	 floating_ip_pools: public
	 delete_fip: yes
	 state: present

     - name: Create volume
       os_volume:
	 cloud: sample
	 size: 5
	 display_name: sample
	 state: present

     - name: Attach volume
       os_server_volume:
	 cloud: sample
	 server: sample
	 volume: sample
	 state: present

.. note::

   ``floating_ip_pools`` must be adjusted according to the domain used. Except
   in the domain ``default``, the public pool is ``DOMAINNAME-public``.

* Run the playbook with ``ansible-playbook playbook.yml``

Inventory
=========

External inventory script
-------------------------

* https://docs.ansible.com/ansible/latest/user_guide/intro_dynamic_inventory.html

.. code-block:: none

   $ wget https://raw.githubusercontent.com/ansible/ansible/stable-2.5/contrib/inventory/openstack.py
   $ chmod +x openstack.py

* View the inventory with ``ansible-inventory``

.. code-block:: none

   $ ansible-inventory -i openstack.py --list

* Use the inventory with ``ansible`` & ``ansible-playbook``

.. code-block:: none

   $ ansible-playbook -i openstack.py [...]

* Alternatively add an entry in the ``ansible.cfg``

.. code-block:: ini

   [defaults]
   inventory = openstack.py

Inventory plugin
-----------------

* https://docs.ansible.com/ansible/latest/plugins/inventory.html
* https://docs.ansible.com/ansible/latest/plugins/inventory/openstack.html
