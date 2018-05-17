============
Create image
============

.. note::

   Standard images like Ubuntu or Debian are deployed globally. Should there be need for further distributions or appliances send an email to ``support@betacloud.io``.

Images are created through the manager node.

Store the image file temporarily under ``/opt/configuration/environments/openstack``.

The image must be in raw format. A conversion can be done in advance with ``qemu-img``.

.. code-block:: none

   $ qemu-img convert -f qcow2 -O raw \
       bionic-server-cloudimg-amd64.img \
       bionic-server-cloudimg-amd64.img.raw

The import is now done with the ``openstack`` client. Then remove the temporary files.

Possible values for e.g. ``os_distro`` can be found on https://docs.openstack.org/python-glanceclient/latest/cli/property-keys.html.

.. code-block:: none

   $ openstack --os-cloud service image create \
       --public \
       --container-format bare \
       --disk-format raw \
       --property hw_watchdog_action=reset \
       --property hw_disk_bus=scsi \
       --property hw_scsi_model=virtio-scsi \
       --property hw_vif_multiqueue_enabled=true \
       --property os_distro=ubuntu \
       --property os_version=18.04 \
       --file /configuration/bionic-server-cloudimg-amd64.img.raw \
       "Ubuntu 18.04 (Bionic Beaver)"
   +------------------+------------------------------------------------------+
   | Field            | Value                                                |
   +------------------+------------------------------------------------------+
   | checksum         | 328b4bce90df58123e3bb459500513d8                     |
   | container_format | bare                                                 |
   | created_at       | 2018-02-01T20:27:48Z                                 |
   | disk_format      | raw                                                  |
   | file             | /v2/images/e7bacd22-3410-4aa1-83a6-5d2c25438185/file |
   | id               | e7bacd22-3410-4aa1-83a6-5d2c25438185                 |
   | min_disk         | 0                                                    |
   | min_ram          | 0                                                    |
   | name             | Ubuntu 18.04 (Bionic Beaver)                         |
   | owner            | de8299637be6486f9dd0d51c1f544a71                     |
   | properties       | direct_url='rbd://7c5eba58-c30b-4f2a-...             |
   | protected        | False                                                |
   | schema           | /v2/schemas/image                                    |
   | size             | 2361393152                                           |
   | status           | active                                               |
   | tags             |                                                      |
   | updated_at       | 2018-02-01T21:05:24Z                                 |
   | virtual_size     | None                                                 |
   | visibility       | public                                               |
   +------------------+------------------------------------------------------+
