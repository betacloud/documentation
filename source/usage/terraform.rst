=========
Terraform
=========

.. contents::

* https://www.terraform.io

Terraform is an open source tool from ``HashiCorp`` that can be used to create and manage 
application infrastructure. Terraform can also provide software for example with Ansible or Chef.


OpenStack Provider
==================

* https://www.terraform.io/docs/providers/openstack/index.html#example-usage

* Create a ``modules.tf`` file and define the OpenStack provider

.. code-block:: none

   provider "openstack" {
     user_name   = "USERNAME"
     password    = "PASSWORD"
     auth_url    = "https://api-1.betacloud.io:5000/v3"
     region      = "REGION"
   }

   module "sample" {
     source = "modules/sample"
   }

* Or you can use a clouds.yml file (see Authentication chapter) to provide access to the OpenStack client

.. code-block:: none

   provider "openstack" {
     cloud = "example_cloud"
   }

* Create the modules directory

.. code-block:: console

   $ mkdir -p modules/sample


OpenStack Resource
================== 

* Create a ``main.tf`` file below the sample directory

.. code-block:: none

   variable "count" {
     default = 1
   }

* Import an SSH key pair

* https://www.terraform.io/docs/providers/openstack/r/compute_keypair_v2.html

.. code-block:: none

   resource "openstack_compute_keypair_v2" "betacloud-key" {
     name       = "betacloud-key"
     public_key = "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDAjpC1hwiOCCmKEWxJ4qzTTsJbKzndLotBCz5Pcwt...."
   }

* Define the floatingip ``openstack_networking_floatingip_v2`` resource

* https://www.terraform.io/docs/providers/openstack/r/compute_floatingip_v2.html

.. code-block:: none

   resource "openstack_networking_floatingip_v2" "fip_1" {
     count = "${var.count ? 1 : 0}"
     pool  = "${var.openstack_network_floatingip_pool}"
   }

* Associate the flotingip ``openstack_compute_floatingip_associate_v2`` resource

* https://www.terraform.io/docs/providers/openstack/r/compute_floatingip_associate_v2.html

.. code-block:: none

   resource "openstack_compute_floatingip_associate_v2" "fip_1" {
     count       = "${var.count ? 1 : 0}"
     floating_ip = "${element(openstack_networking_floatingip_v2.fip_1.*.address, count.index)}"
     instance_id = "${element(openstack_compute_instance_v2.sample.*.id, count.index)}"
   }

* Manage ``openstack_compute_instance_v2`` resource

* https://www.terraform.io/docs/providers/openstack/r/compute_instance_v2.html

.. code-block:: none

   resource "openstack_compute_instance_v2" "sample" {
     count           = "${var.count ? 1 : 0}"
     name            = "${format("sample-%02d", count.index+1)}"
     image_name      = "${var.openstack_image_name}"
     flavor_name     = "${var.openstack_flavor_name}"
     key_pair        = "${openstack_compute_keypair_v2.betacloud-key.name}"
     security_groups = ["default"]
     network {
       name = "${var.openstack_network}"
     }
   }

Variables
============

* Create a ``variables.tf`` file below the sample directory

.. code-block:: none

   variable "openstack_network" {
       description = "The network to be used."
       default  = "network"
   }

   variable "openstack_network_floatingip_pool" {
       description = "The network floatingip pool to be used."
       default  = "public"
   }

   variable "openstack_flavor_name" {
       description = "The flavor name to be used."
       default  = "2C-2GB-20GB"
   }

   variable "openstack_image_name" {
       description = "The image name to be used."
       default  = "Ubuntu 18.04 (Bionic Beaver)"
   }

.. note::

   The variables must be adjusted according to the project used.

Start the deployment
====================

First we initialize the working directory with the ``terraform init`` command 
after writing a new Terraform configuration. This will ensure that Terraform has 
all the necessary components to build the template in OpenStack. If the working 
directory is initialized, we create an execution plan with ``terraform plan``. 
In this step, the required resources are compared with the state information 
stored by Terraform. After checking the plan, the configuration can be carried 
out with ``terraform apply``. The instance can easily be deleted using the 
``terraform destroy`` command.
