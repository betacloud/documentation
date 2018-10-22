==========
cloud-init
==========

* http://cloudinit.readthedocs.io/en/latest/

With ``cloud-init`` instances can be configured at boot-time. Arbitrary scripts
and configuration settings can be passed as user-data via the meta-data service.

- Scripts need to start with shebang. Example: ``#!/usr/bin/env python``
- Configuration settings start with the header ``#cloud-config``

Scripts and config settings are passed at instance creation time as following:

.. code-block:: console

   $ openstack server create --user-data userdata.txt ...

Set hostname
============

By passing ``cloud-init`` config via user-data, the hostname and fully qualified
name can be set for an instance at creation time.

.. code-block:: yaml

   #cloud-config
   hostname: myhost
   fqdn: myhost.mydomain.name
   manage_etc_hosts: yes

Add users
=========

It's possible to easily create users or set the root password with ``cloud-init``.

Configuration files for ``cloud-init`` will need to be pasted to the **Customization Script** under the **Configuration** header when deploying an instance. The configuration is in YAML format and documented at cloudinitdoc_.

A sample code block to set the root password and allow SSH password login for the instance. The password hash can be created with ``mkpasswd --method=SHA-512 --rounds=4096``.

.. code-block:: yaml

   #cloud-config
   password: $6$rounds=4096$123456789...
   chpasswd: { expire: False }
   ssh_pwauth: True

Creating users while preserving default users (remove ``default`` to remove default users created by the image):

.. code-block:: yaml

   #cloud-config
   users:
     - default
     - name: foo
       shell: /bin/bash
       sudo: ALL=(ALL) NOPASSWD:ALL
       password: $6$rounds=4096$123456789...
       chpasswd: { expire: False }
       ssh_authorized_keys:
         - some_pubkey_content

The password hash can be created with ``mkpasswd --method=SHA-512 --rounds=4096``.

.. _cloudinitdoc: https://cloudinit.readthedocs.io/en/latest/

Assignment of multiple SSH keys
===============================

* https://bugs.launchpad.net/nova/+bug/1499751 (OpenStack (nova boot exactly) allows only one SSH key)
* https://docs.openstack.org/ocata/user-guide/cli-provide-user-data-to-instances.html
* http://cloudinit.readthedocs.io/en/latest/topics/examples.html#configure-instances-ssh-keys
* http://cloudinit.readthedocs.io/en/latest/topics/modules.html#ssh

OpenStack allows to specify only one SSH key when instance is created.
To specify several keypairs when creating an instance, ``cloud-config`` can be used.

* Create a file ``userdata.sample``.

.. code-block:: yaml

   #cloud-config
   ssh_authorized_keys:
     - ssh-rsa ... 001
     - ssh-rsa ... 002
     - ssh-rsa ... 003
     - ssh-rsa ... 004

* Create an instance ``testing``.

.. code-block:: console

   $ openstack --os-cloud PROJECT server create \
       --image "Ubuntu 16.04 (Xenial Xerus)" \
       --flavor 1C-1GB-10GB \
       --user-data userdata.sample \
       testing

* When the instance is initialized, the keys are now assigned.

.. code-block:: console

   $ openstack --os-cloud PROJECT console log show testing
   [...]
   +++++Authorized keys from /home/ubuntu/.ssh/authorized_keys for user ubuntu++++++
   ci-info: +---------+-------------------------------------------------+---------+---------+
   ci-info: | Keytype |                Fingerprint (md5)                | Options | Comment |
   ci-info: +---------+-------------------------------------------------+---------+---------+
   ci-info: | ssh-rsa | e6:67:2b:9a:b3:50:33:53:28:f7:92:1c:27:13:92:0e |    -    |   003   |
   ci-info: | ssh-rsa | 47:3d:4a:5e:9f:07:75:ae:18:91:48:77:a7:6b:4f:f7 |    -    |   002   |
   ci-info: | ssh-rsa | a0:42:ee:70:ad:97:f2:59:9a:0a:70:11:02:99:8b:d0 |    -    |   001   |
   ci-info: | ssh-rsa | 31:2b:c2:2c:c9:cc:27:38:c9:90:28:df:82:c4:1f:eb |    -    |   004   |
   ci-info: +---------+-------------------------------------------------+---------+---------+
   [...]
