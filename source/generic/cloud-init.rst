==========
cloud-init
==========

Creating users with cloud-init
==============================

It's possible to easily create users or set the root password with ``cloud-init``.

Configuration files for ``cloud-init`` will need to be pasted to the **Customization Script** under the **Configuration** header when deploying an instance. The configuration is in YAML format and documented at cloudinitdoc_.

A sample code block to set the root password and allow SSH password login for the instance:

.. code-block:: yaml

   #cloud-config
   password: AVeryGoodPassword
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
