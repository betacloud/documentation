===================
Importing RBD image
===================

Some images are very large after the conversion into the ``raw`` format.

.. code-block:: console

   $ qemu-img info testing.qcow2 
   image: testing.qcow2
   file format: qcow2
   virtual size: 40G (42949672960 bytes)
   disk size: 1.4G
   cluster_size: 65536
   Format specific information:
       compat: 1.1
       lazy refcounts: false
       refcount bits: 16
       corrupt: false

If adjusting the size with ``virt-resize`` is not an option, the following approach is an alternative to uploading via the Glance API.

.. note::

   Make sure beforehand that ``image_size_cap`` and/or ``user_storage_quota`` are set high enough.

.. code-block:: console

   $ IMAGE_ID=$(uuidgen)
   $ echo $IMAGE_ID
   2264ffb1-78f0-4f2f-b027-86af353cc54d
   $ qemu-img convert -f qcow2 -O raw testing.qcow2 rbd:images/$IMAGE_ID

Of course, the image can already be in RAW format.

.. code-block:: console

   $ qemu-img convert -f raw -O raw testing.raw rbd:images/$IMAGE_ID

.. code-block:: console

   $ rbd info images/$IMAGE_ID
   rbd image '2264ffb1-78f0-4f2f-b027-86af353cc54d':
       size 40960 MB in 10240 objects
       order 22 (4096 kB objects)
       block_name_prefix: rbd_data.386b082ae8944a
       format: 2
       features: layering, exclusive-lock, object-map, fast-diff, deep-flatten
       flags: 
       create_timestamp: Mon Feb  5 20:29:37 2018

.. code-block:: console

   $ rbd snap create images/$IMAGE_ID@snap
   $ rbd snap protect images/$IMAGE_ID@snap

.. note::

   .. code-block:: console

      $ ceph fsid
      7c5eba58-c30b-4f2a-90c1-73d8aa9b7162

.. code-block:: console

   $ IMAGE_ID=2264ffb1-78f0-4f2f-b027-86af353cc54d
   $ CLUSTER_ID=7c5eba58-c30b-4f2a-90c1-73d8aa9b7162

.. note::

   The ``--location`` parameter can only be used with Glance API v1, which is deprecated.

   Therefore you have to work with the `` --os-image-api-version 1`` parameter.

   ..code-block:: none

     ERROR: --location was given, which is an Image v1 option that is no longer supported in Image v2

.. code-block:: console

   $ openstack --os-cloud service image --os-image-api-version 1 create \
       --private \
       --container-format bare \
       --disk-format raw \
       --id $IMAGE_ID \
       --location rbd://$CLUSTER_ID/images/$IMAGE_ID/snap \
       testing

.. note::

   In a newer environment, Glance API v1 may already be disabled.
   Therefore you have to work with the ``--property`` parameter.

   .. code-block:: none

     HTTPMultipleChoices (HTTP 300) Requested version of OpenStack Images API is not available.

.. todo::

   Document use of ``--property``.

References
==========

* https://www.hastexo.com/resources/hints-and-kinks/importing-rbd-into-glance/
