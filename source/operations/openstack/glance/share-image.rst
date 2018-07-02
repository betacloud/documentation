===========
Share image
===========

* https://docs.openstack.org/image-guide/share-images.html

python-glanceclient
===================

The visibility of the image must be set to ``shared``: ``glance image-update --visibility shared IMAGE_ID``

In the source project:

.. code-block:: console

   $ glance member-create IMAGE_ID PROJECT_ID
   +----------+------------+---------+
   | Image ID | Member ID  | Status  |
   +----------+------------+---------+
   | IMAGE_ID | PROJECT_ID | pending |
   +----------+------------+---------+

In the target project:

.. code-block:: console

   $ glance member-update IMAGE_ID PROJECT_ID accepted
   +----------+------------+----------+
   | Image ID | Member ID  | Status   |
   +----------+------------+----------+
   | IMAGE_ID | PROJECT_ID | accepted |
   +----------+------------+----------+

To delete the member, in the source project:

.. code-block:: console

   $ glance member-delete IMAGE_ID PROJECT_ID
