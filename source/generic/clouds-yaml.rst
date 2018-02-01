===========
clouds.yaml
===========

* https://docs.openstack.org/os-client-config/latest/
* https://docs.openstack.org/os-client-config/latest/user/vendor-support.html#betacloud

.. code-block:: yaml

   ---
   clouds:
     PROJECT:
       profile: betacloud
       auth:
         username: USERNAME
         password: PASSWORD
         project_name: PROJECT
         project_domain_name: default
         user_domain_name: default

The vendor preset ``betacloud`` has recently been added to ``os-client-config`` and may not be available. Alternatively, this file can be used.

.. code-block:: yaml

   ---
   clouds:
     PROJECT:
       auth:
         auth_url: https://api-1.betacloud.io:5000/v3
         username: USERNAME
         password: PASSWORD
         project_name: PROJECT
         project_domain_name: default
         user_domain_name: default
       interface: public
       identity_api_version: 3
       volume_api_version: 3
