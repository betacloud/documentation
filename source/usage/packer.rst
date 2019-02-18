======
Packer
======

.. contents::

* https://www.packer.io/docs/builders/openstack.html

Packer is a tool for building identical machine images for multiple
platforms from a single source configuration.

The OpenStack builder allows to build OpenStack images.

Preparations
============

* Install required packages

  .. code-block:: console

     $ sudo apt install packer

* Create a template

  .. code-block:: json

     {
       "builders": [
         {
           "type": "openstack",
           "ssh_username": "root",
           "image_name": "ubuntu1604_packer_test_1",
           "source_image": "0c944345-4b47-426f-b4cf-c5967cfd9692",
           "flavor": "2C-2GB-20GB",
           "networks": "f2e89d63-b780-47e6-9cb6-0e27194d867b",
           "security_groups": "ssh",
           "floating_ip_pool": "public",
           "identity_endpoint": "https://api-1.betacloud.io:5000/v3",
           "tenant_name": "PROJECTNAME",
           "domain_name": "DOMAIN",
           "username": "USERNAME",
           "password": "PASSWORD",
           "region": "REGION"
         }
       ]
     }


Creation
========

* Run ``packer build``

  .. code-block:: console

     $ packer build packer-example.json
