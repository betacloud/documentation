==========
Migrations
==========

Live migrate all instances of node
==================================

.. code-block:: console

   $ nova host-evacuate-live --target-host 50-10 --max-servers 5 50-12
   +--------------------------------------+-------------------------+---------------+                                     
   | Server UUID                          | Live Migration Accepted | Error Message |                                     
   +--------------------------------------+-------------------------+---------------+                                     
   | f326290c-f0f7-4a9c-94fd-5faa45af36bf | True                    |               |                                     
   +--------------------------------------+-------------------------+---------------+                                     
