=================
Quota out of sync
=================

.. note::

   Only affects Nova in version Ocata or before.

   Starting at Pike, the quota is calculated by Nova on-the-fly and no longer stored in the database.

.. code-block:: shell

   $ openstack --os-cloud service limits show --absolute --project testbed    
   +--------------------------+-------+
   | Name                     | Value |
   +--------------------------+-------+
   [...]
   | totalCoresUsed           |    10 |
   | totalRAMUsed             | 13312 |
   [...]
   +--------------------------+-------+

This quota is not correct, in the project runs only one instance with 1 vCPU and 4 GB of RAM (``openstack --os-cloud service server list --project testbed``).

With ``nova-manage project quota_usage_refresh``, the quota can be recalculated on one of the controller nodes.

.. code-block:: shell

   $ docker exec -it nova_api bash
   (nova-api)[nova@20-10 /]$ nova-manage project quota_usage_refresh --project 676... --user 609...

References
==========

* https://bugs.launchpad.net/nova/+bug/1742826
