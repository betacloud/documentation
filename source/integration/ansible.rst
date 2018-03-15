=======
Ansible
=======

* Prepare virtual environment

.. code-block:: none

   $ virtualenv .venv
   $ source .venv/bin/activate
   $ pip install ansible shade

* Create a ``clouds.yml`` file

.. code-block:: yaml

   ---
   clouds:
     PROJECT:
       auth:
         auth_url: https://api-1.betacloud.io:5000/v3
         username: USERNAME
         password: PASSWORD
         project_name: PROJECT
         project_domain_name: default
         user_domain_name: default
       interface: public
       identity_api_version: 3
       volume_api_version: 3

.. note::

   ``project_domain_name`` and ``user_domain_name`` must be adjusted accordingly.

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

References
==========

* http://docs.ansible.com/ansible/latest/list_of_cloud_modules.html#openstack
