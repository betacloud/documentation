===========
clouds.yaml
===========

The file ``clouds.yaml`` is used to provide access to the OpenStack client. This eliminates the need to use environment variables or ``openrc`` files.

The file ``clouds.yaml`` must be stored at one of the following locations The first file found wins

* Current directory
* ``~/.config/openstack``
* ``/etc/openstack``

Below is a ``clouds.yaml`` example file to access project PROJECT with user USERNAME. It is possible to define multiple accesses in one file.

To use access ``PROJECT`` with the OpenStack client, it is called with ``openstack --os-cloud PROJECT``.

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

.. note::

   ``project_domain_name`` and ``user_domain_name`` must be adjusted accordingly.

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

.. note::

   ``project_domain_name`` and ``user_domain_name`` must be adjusted accordingly.

.. note::

   Apart from the OpenStackClient, other clients do not yet support clouds.yaml comprehensively.

   https://specs.openstack.org/openstack/openstack-specs/specs/clouds-yaml-support.html

References
==========

* https://docs.openstack.org/os-client-config/latest/
* https://docs.openstack.org/os-client-config/latest/user/vendor-support.html#betacloud
