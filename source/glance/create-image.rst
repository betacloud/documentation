============
Create image
============

.. note::

   Standard images like Ubuntu or Debian are deployed globally. Should there be need for further distributions or appliances send an email to ``support@betacloud.io``.

The image must be in raw format. A conversion can be done in advance with ``qemu-img`` (part of the ``qemu-utils`` package).

.. code-block:: console

   $ qemu-img convert -f qcow2 -O raw \
       bionic-server-cloudimg-amd64.img \
       bionic-server-cloudimg-amd64.img.raw

.. note::

   Possible properties and values can be found on https://docs.openstack.org/glance/latest/admin/useful-image-properties.html.

.. note::

   For normal users, it is only possible to create private images. Public images can only be created by privileged users.

.. code-block:: console

   $ openstack --os-cloud images image create \
       --container-format bare \
       --disk-format raw \
       --property hw_watchdog_action=reset \
       --property hw_disk_bus=scsi \
       --property hw_scsi_model=virtio-scsi \
       --property hw_vif_multiqueue_enabled=true \
       --property os_distro=ubuntu \
       --property os_version=18.04 \
       --file bionic-server-cloudimg-amd64.img.raw \
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
   | visibility       | private                                              |
   +------------------+------------------------------------------------------+

.. note::

   Instead of a general value for ``os_distro`` like ``ubuntu``, a specific so-called ``Short ID`` can also be used.

   Possible short IDs can be looked up in the libosinfo database (https://libosinfo.org).

   .. code-block:: console

      $ osinfo-query os vendor="SUSE"
       Short ID             | Name                                               | Version  | ID
      ----------------------+----------------------------------------------------+----------+-----------------------------------------
       sled10               | SUSE Linux Enterprise Desktop 10                   | 10       | http://suse.com/sled/10
       sled11               | SUSE Linux Enterprise Desktop 11                   | 11       | http://suse.com/sled/11
       sled11sp1            | SUSE Linux Enterprise Desktop 11 SP1               | 11.1     | http://suse.com/sled/11.1
       sled11sp2            | SUSE Linux Enterprise Desktop 11 SP2               | 11.2     | http://suse.com/sled/11.2
       sled11sp3            | SUSE Linux Enterprise Desktop 11 SP3               | 11.3     | http://suse.com/sled/11.3
       sled11sp4            | SUSE Linux Enterprise Desktop 11 SP4               | 11.4     | http://suse.com/sled/11.4
       sled12               | SUSE Linux Enterprise Desktop 12                   | 12       | http://suse.com/sled/12
       sled9                | SUSE Linux Enterprise Desktop 9                    | 9        | http://suse.com/sled/9
       sles10               | SUSE Linux Enterprise Server 10                    | 10       | http://suse.com/sles/10
       sles11               | SUSE Linux Enterprise Server 11                    | 11       | http://suse.com/sles/11
       sles11sp1            | SUSE Linux Enterprise Server 11 SP1                | 11.1     | http://suse.com/sles/11.1
       sles11sp2            | SUSE Linux Enterprise Server 11 SP2                | 11.2     | http://suse.com/sles/11.2
       sles11sp3            | SUSE Linux Enterprise Server 11 SP3                | 11.3     | http://suse.com/sles/11.3
       sles11sp4            | SUSE Linux Enterprise Server 11 SP4                | 11.4     | http://suse.com/sles/11.4
       sles12               | SUSE Linux Enterprise Server 12                    | 12       | http://suse.com/sles/12
       sles9                | SUSE Linux Enterprise Server 9                     | 9        | http://suse.com/sles/9
