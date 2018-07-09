========
Projects
========

Quotas
======

* https://github.com/betacloud/contrib/blob/master/generic/manage-project-resources.py
* https://github.com/betacloud/contrib/blob/master/etc/quotaclasses.yml

.. code-block:: console

   $ openstack --os-cloud domain-betacloud project set --property quotaclass=basic testbed
   $ openstack --os-cloud domain-betacloud project set --property quotamultiplier=8 testbed

.. note::

   The cron job for adjusting the quotas runs every hour.
