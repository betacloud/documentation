.. _Authentication:

==============
Authentication
==============

.. contents::
   :local:

clouds.yaml
===========

.. note::

   Identifiers such as ``USERNAME``, ``PASSWORD``, ``PROJECT`` are placeholders and should
   be replaced accordingly.

The file ``clouds.yaml`` is used to provide access to the OpenStack client. This eliminates
the need to use environment variables or ``openrc`` files. The used Secrets can be stored
in a separate file ``secure.yaml``.

* https://docs.openstack.org/os-client-config/latest/

The file ``clouds.yaml`` must be stored at one of the following locations. The first file
found wins.

* Current directory
* ``~/.config/openstack``
* ``/etc/openstack``



With vendor profile
-------------------

.. note::

   The vendor preset ``betacloud`` has recently been added to the ``os-client-config`` library
   and may not be available.

   * https://docs.openstack.org/openstacksdk/latest/user/config/vendor-support.html#betacloud

Below is a ``clouds.yaml`` example file to access project PROJECT with user USERNAME.

It is possible to define multiple accesses in one file. Therefore every entry is assigned a
name like ``betacloud``.

To use access ``betacloud`` with the OpenStack client, it is called with ``openstack --os-cloud betacloud``.
If only one entry is listed in the file you can omit the parameter.

* ``clouds.yaml``

  .. code-block:: yaml

     ---
     clouds:
       betacloud:
         profile: betacloud
         auth:
           username: USERNAME
           project_name: PROJECT
           project_domain_name: default
           user_domain_name: default

* ``secure.yaml``

  .. code-block:: yaml

     clouds:
       betacloud:
         auth:
           password: PASSWORD

.. note::

   ``project_domain_name`` and ``user_domain_name`` must be adjusted accordingly.

   If you login to Horizon with the domain ``COMPANY`` you have to use ``COMPANY``
   instead of ``default``.

Without vendor profile
----------------------

Alternatively, this file can be used. No vendor profile is used here.

* ``clouds.yaml``

  .. code-block:: yaml

     ---
     clouds:
       betacloud:
         auth:
           auth_url: https://api-1.betacloud.io:5000/v3
           username: USERNAME
           project_name: PROJECT
           project_domain_name: default
           user_domain_name: default
         interface: public
         identity_api_version: 3
         volume_api_version: 3

* ``secure.yaml``

  .. code-block:: yaml

     ---
     clouds:
       betacloud:
         auth:
           password: PASSWORD

.. note::

   ``project_domain_name`` and ``user_domain_name`` must be adjusted accordingly.

   If you login to Horizon with the domain ``COMPANY`` you have to use ``COMPANY``
   instead of ``default``.

With a token
------------

You can get a token from keystone for authentication instead of using username and password. Tokens are valid for 24h.

Below is an example ``clouds.yaml`` with authentication using a token.

* ``clouds.yaml``

  .. code-block:: yaml

    ---
    clouds:
      betacloud-token:
        auth_type: token
        auth:
          auth_url: https://api-1.betacloud.io:5000/v3
          preoject_id: ID
          project_name: PROJECT
          token: TOKEN

You can generate the token with ``openstack --os-cloud betacloud token issue``.

.. note::

   Apart from the OpenStackClient, other clients do not yet support ``clouds.yaml``
   comprehensively.    Wherever possible, always use the OpenStackClient. If not
   possible use an ``openrc`` file.

   https://specs.openstack.org/openstack/openstack-specs/specs/clouds-yaml-support.html

openrc
======

Below is a ``openrc`` example file to access project PROJECT with user USERNAME.

.. code-block:: shell

   export OS_PROJECT_DOMAIN_NAME=default
   export OS_USER_DOMAIN_NAME=default
   export OS_PROJECT_NAME=PROJECT
   export OS_USERNAME=USERNAME
   export OS_PASSWORD=PASWORD
   export OS_AUTH_URL=https://api-1.betacloud.io:5000/v3
   export OS_IDENTITY_API_VERSION=3
   export OS_VOLUME_API_VERSION=3

.. note::

   ``OS_PROJECT_DOMAIN_NAME`` and ``OS_USER_DOMAIN_NAME`` must be adjusted accordingly.

   If you login to Horizon with the domain ``COMPANY`` you have to use ``COMPANY``
   instead of ``default``.
