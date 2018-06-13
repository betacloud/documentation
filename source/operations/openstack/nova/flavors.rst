=======
Flavors
=======

Private flavors
===============

Creation
--------

.. code-block:: console

   $ openstack --os-cloud service flavor create \
      --private \
      --project-domain DOMAIN \
      --project PROJECT \
      --vcpus 32 --ram 16384 --disk 20 \
      32C-16GB-20GB
   +----------------------------+--------------------------------------+
   | Field                      | Value                                |
   +----------------------------+--------------------------------------+
   | OS-FLV-DISABLED:disabled   | False                                |
   | OS-FLV-EXT-DATA:ephemeral  | 0                                    |
   | disk                       | 20                                   |
   | id                         | b8515d99-054b-43d1-8aa8-9db801350c3d |
   | name                       | 32C-16GB-20GB                        |
   | os-flavor-access:is_public | False                                |
   | properties                 |                                      |
   | ram                        | 16384                                |
   | rxtx_factor                | 1.0                                  |
   | swap                       |                                      |
   | vcpus                      | 32                                   |
   +----------------------------+--------------------------------------+
