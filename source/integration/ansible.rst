=======
Ansible
=======

Preparations
============

* Prepare virtual environment

.. code-block:: none

   $ virtualenv .venv
   $ source .venv/bin/activate
   $ pip install ansible shade

* Create a ``clouds.yml`` file (see `Authentication` chapter)

OpenStack modules
=================

* http://docs.ansible.com/ansible/latest/list_of_cloud_modules.html#openstack

* Create a ``playbook.yml`` file

.. code-block:: yaml

   ---
   - hosts: localhost
     connection: local
     gather_facts: no

     tasks:
     - name: Start instance
       os_server:
         cloud: sample
         name: sample
         flavor: 1C-1GB-10GB
         image: "Ubuntu 16.04 (Xenial Xerus)"
         key_name: samplekey
         security_groups:
           - default
         floating_ip_pools: public
         delete_fip: yes
         state: present

.. note::

   ``floating_ip_pools`` must be adjusted according to the domain used. Except in the domain ``default``,
   the public pool is ``DOMAINNAME-public``.

* Start the playbook now

.. code-block: none

   $ ansible-playbook playbook.yml -i localhost,

   PLAY [localhost] **********************************************************

   TASK [Start instance] *****************************************************
   changed: [localhost]

   PLAY RECAP ****************************************************************
   localhost                  : ok=1    changed=1    unreachable=0    failed=0

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
