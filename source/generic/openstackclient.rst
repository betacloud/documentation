===============
OpenStackClient
===============

.. contents::
   :local:

OpenStackClient (aka OSC) is a command-line client for OpenStack that brings the command set for
Compute, Identity, Image, Object Store and Block Storage APIs together in a single shell with a
uniform command structure. [#s1]_

* https://docs.openstack.org/python-openstackclient/

.. note::

   Possibly release names (e.g. ``pike``) are already obsolete. Then just use the current release.

   An overview of OpenStack releases can be found at https://releases.openstack.org/.

Docker
======

We provide Ubuntu based Docker Images with the OpenStack client.

* Use of the integrated shell

  .. code-block:: console

     $ docker run -it osism/openstackclient:rocky -v $(pwd):/configuration --os-cloud sample
     (openstack) token issue
     [...]
     (openstack)

* Direct call of commands

  .. code-block:: console

     $ docker run -it osism/openstackclient:rocky -v $(pwd):/configuration --os-cloud sample token issue
     [...]

Ubuntu
======

1. Add latest Ubuntu Cloud Archive (UCA) repository: ``sudo add-apt-repository cloud-archive:pike``
   (``add-apt-repository`` is part of the package ``software-properties-common``)
2. Install ``python-openstackclient``: ``sudo apt-get install python-openstackclient``

.. note::

   The package ``python-openstackclient`` does not install all OpenStack Python client libraries as
   dependencies. Install these manually if plugins are missing/not usable in ``python-openstackclient``,
   e.g.```sudo apt-get install python-heatclient`` to be able to use the Heat plugin.

Pip
===

1. Create and activate a virtual environment: ``virtualenv -p python3 .venv && source .venv/bin/activate``
   (``virtualenv`` is part of the package ``virtualenv``)
2. Install the latest released version of ``python-openstackclient``: ``pip install python-openstackclient``

.. note::

   The installed dependencies require the installation of additional development packages, such as
   ``pyhton-dev`` on Ubuntu.

.. note::

   An installation of the master branch is also possible: ``pip install git+https://github.com/openstack/python-openstackclient``

.. [#s1] source: https://github.com/openstack/python-openstackclient/blob/master/README.rst
