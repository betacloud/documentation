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

OpenStackClient
===============

In the source project:

.. code-block:: console

   $ openstack images image add project IMAGE_ID PROJECT_ID
   +------------+----------------------+
   | Field      | Value                |
   +------------+----------------------+
   | created_at | 2018-07-02T14:41:08Z |
   | image_id   | IMAGE_ID             |
   | member_id  | PROJECT_ID           |
   | schema     | /v2/schemas/member   |
   | status     | pending              |
   | updated_at | 2018-07-02T14:41:08Z |
   +------------+----------------------+

In the target project:

.. code-block:: console

   $ openstack image set --accept IMAGE_ID
