=========
Terraform
=========

OpenStack Provider
==================

* https://www.terraform.io/docs/providers/openstack/index.html

* Create a ``modules.tf`` file

.. code-block:: json

   provider "openstack" {
     version = "~> 1.6"
   }

   terraform {
     required_version = "= 0.11.7"
   }

   module "sample" {
     source = "modules/sample"
   }

.. note::

   ``provider:`` Before we can use the OpenStack provider, we configured the proper credentials.
   Download the ``OpenStack RC File v3/v2`` from the horizon dashboard and evaluate the file with 
   the command ``source``. Enter your OpenStack Password for youre project.

   ``terraform:`` The terraform block configures the required Terraform version. 

   ``module:`` We use child modules to defining resources.


* Create the modules directory

.. code-block: none

   $ mkdir -p modules/sample


OpenStack Resource
================== 

* Create a ``main.tf`` file below the sample directory

.. code-block:: json

   variable "count" {
     default = 1
   }

* Define the floatingip ``openstack_networking_floatingip_v2`` resource

* https://www.terraform.io/docs/providers/openstack/r/compute_floatingip_v2.html

.. code-block:: json

   resource "openstack_networking_floatingip_v2" "fip_1" {
     count = "${var.count ? 1 : 0}"
     pool  = "${var.openstack_network_floatingip_pool}"
   }

* Associate the flotingip ``openstack_compute_floatingip_associate_v2`` resource

* https://www.terraform.io/docs/providers/openstack/r/compute_floatingip_associate_v2.html

.. code-block:: json

   resource "openstack_compute_floatingip_associate_v2" "fip_1" {
     count       = "${var.count ? 1 : 0}"
     floating_ip = "${element(openstack_networking_floatingip_v2.fip_1.*.address, count.index)}"
     instance_id = "${element(openstack_compute_instance_v2.sample.*.id, count.index)}"
   }

* Manage ``openstack_compute_instance_v2`` resource

* https://www.terraform.io/docs/providers/openstack/r/compute_instance_v2.html

.. code-block:: json

   resource "openstack_compute_instance_v2" "sample" {
     count           = "${var.count ? 1 : 0}"
     name            = "${format("sample-%02d", count.index+1)}"
     image_name      = "${var.openstack_image_name}"
     flavor_id       = "${var.openstack_flavor_id}"
     key_pair        = "${var.openstack_keypair}"
     security_groups = ["default"]
     network {
       name = "${var.openstack_network}"
     }
   }

Variables
============

* Create a ``variables.tf`` file below the sample directory

.. code-block:: json

   variable "openstack_keypair" {
       description = "The keypair to be used."
       default  = "authentication_key"
   }

   variable "openstack_network" {
       description = "The network to be used."
       default  = "net-to-DOMAINNAME-public"
   }

   variable "openstack_network_floatingip_pool" {
       description = "The network floatingip pool to be used."
       default  = "DOMAINNAME-public"
   }

   variable "openstack_flavor_id" {
       description = "The flavor id to be used."
       default  = "122"
   }

   variable "openstack_image_name" {
       description = "The image name to be used."
       default  = "Ubuntu 18.04 (Bionic Beaver)"
   }


.. note::

The variables must be adjusted according to the project used.

* Start the deployment now

Terraform commands
==================

* https://www.terraform.io/docs/commands/index.html

* Initialize a working directory

.. code-block:: none

   $ terraform init

* Create an execution plan

.. code-block:: none

   $ terraform plan

* Apply the changes

.. code-block:: none

   $ terraform apply
