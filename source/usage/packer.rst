======
Packer
======

.. contents::
   :local:

* https://www.packer.io/docs/builders/openstack.html

Packer is a tool for building identical machine images for multiple
platforms from a single source configuration.

The OpenStack builder allows to build OpenStack images.

Preparations
============

* Install required packages

  .. code-block:: console

     $ sudo apt install packer

* Create a ``clouds.yaml`` and ``secure.yaml`` file (see :ref:`Authentication` chapter)

* Find out the ID of the network to which the instance should be assigned during construction (``networks``)

* Create a security group ``packer`` and allow access to SSH (TCP/22)

* Create a ``packer-sample.json`` template file

  .. code-block:: json

     {
       "builders": [
         {
           "type": "openstack",
           "ssh_username": "ubuntu",
           "image_name": "packer-sample",
           "source_image_name": "Ubuntu 18.04",
           "flavor": "1C-1GB-10GB",
           "networks": "874f343d-bc41-4210-ae1c-12d08078a122",
           "security_groups": "packer",
           "floating_ip_network": "public",
           "cloud": "sample"
         }
       ]
     }


Usage
=====

* Run ``packer build packer-example.json``

  .. code-block:: console

     $ packer build packer-example.json
     openstack output will be in this color.

     ==> openstack: Loading flavor: 1C-1GB-10GB
         openstack: Verified flavor. ID: 30
     ==> openstack: Creating temporary keypair: packer_5c6b5f2a-8fe4-5b46-2cdf-6173e4eb9400 ...
     ==> openstack: Created temporary keypair: packer_5c6b5f2a-8fe4-5b46-2cdf-6173e4eb9400
     ==> openstack: Launching server...
     ==> openstack: Launching server...
         openstack: Server ID: 86e21e55-2ec6-412f-b2e6-925c71ad6d71
     ==> openstack: Waiting for server to become ready...
     ==> openstack: Creating floating IP using network 0647c0a0-862c-4c7e-9433-4558fcc5573b ...
         openstack: Created floating IP: '361d9c02-86f5-476e-b060-1254c850a78e' (185.136.140.45)
     ==> openstack: Associating floating IP '361d9c02-86f5-476e-b060-1254c850a78e' (185.136.140.45) with instance port...
         openstack: Added floating IP '361d9c02-86f5-476e-b060-1254c850a78e' (185.136.140.45) to instance!
     ==> openstack: Using ssh communicator to connect: 185.136.140.45
     ==> openstack: Waiting for SSH to become available...
     ==> openstack: Creating the image: packer-sample
         openstack: Image: c040e75c-1730-44b5-a01c-f3a3a1dae709
     ==> openstack: Waiting for image packer-sample (image id: c040e75c-1730-44b5-a01c-f3a3a1dae709) to become ready...
     ==> openstack: Deleted temporary floating IP '361d9c02-86f5-476e-b060-1254c850a78e' (185.136.140.45)
     ==> openstack: Terminating the source server: 86e21e55-2ec6-412f-b2e6-925c71ad6d71 ...
     ==> openstack: Deleting temporary keypair: packer_5c6b5f2a-8fe4-5b46-2cdf-6173e4eb9400 ...
     Build 'openstack' finished.

     ==> Builds finished. The artifacts of successful builds are:
     --> openstack: An image was created: c040e75c-1730-44b5-a01c-f3a3a1dae709
