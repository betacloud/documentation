=========
Terraform
=========

.. contents::
   :local:

* https://www.terraform.io

Terraform is an open source tool from ``HashiCorp`` that can be used to create and manage 
application infrastructure. Terraform can also provide software for example with Ansible or Chef.

Provider
========

* Create a ``modules.tf`` file and define the OpenStack provider
  (https://www.terraform.io/docs/providers/openstack/index.html)

  .. code-block:: none

     provider "openstack" {
       user_name   = "USERNAME"
       password    = "PASSWORD"
       auth_url    = "https://api-1.betacloud.io:5000/v3"
       region      = "betacloud-1"
     }

     module "sample" {
       source = "modules/sample"
     }

* Or create a ``clouds.yaml`` and ``secure.yaml`` file (see :ref:`Authentication` chapter)

  .. code-block:: none

     provider "openstack" {
       cloud = "sample"
     }

     module "sample" {
       source = "modules/sample"
     }

* Create a modules directory

  .. code-block:: console

     $ mkdir -p modules/sample

Variables
=========

* Create a ``variables.tf`` file below the sample module directory

  .. code-block:: none

     variable "network" {
       description = "The default name for the new network"
       default     = "sample"
     }

     variable "pool" {
       description = "The default pool where the floating IPs are taken from"
       default     = "public"
     }

     variable "flavor" {
       description = "The default flavor of the new instance"
       default     = "1C-1GB-10GB"
     }

     variable "image" {
       description = "The default image used to create the new instance"
       default     = "Ubuntu 18.04"
     }

Resources
========= 

* Create a ``main.tf`` file below the sample module directory

* Create a SSH key pair (https://www.terraform.io/docs/providers/openstack/r/compute_keypair_v2.html)

  .. code-block:: none

     resource "openstack_compute_keypair_v2" "sample" {
       name = "sample"
     }

* Define a floating IP address resource (https://www.terraform.io/docs/providers/openstack/d/networking_floatingip_v2.html)

  .. code-block:: none

     resource "openstack_networking_floatingip_v2" "sample" {
       pool  = "${var.pool}"
     }

* Create an instance resource (https://www.terraform.io/docs/providers/openstack/r/compute_instance_v2.html)

  .. code-block:: none

     resource "openstack_compute_instance_v2" "sample" {
       name            = "sample"
       image_name      = "${var.image}"
       flavor_name     = "${var.flavor}"
       key_pair        = "${openstack_compute_keypair_v2.sample.name}"
       security_groups = ["default"]
       network {
         name = "${var.openstack_network}"
       }
     }

* Associate the floating IP address resource (https://www.terraform.io/docs/providers/openstack/r/compute_floatingip_associate_v2.html)

  .. code-block:: none

     resource "openstack_compute_floatingip_associate_v2" "sample" {
       floating_ip = "${openstack_networking_floatingip_v2.sample.address}"
       instance_id = "${openstack_compute_instance_v2.sample.id}"
     }

Start the deployment
====================

First we initialize the working directory with the ``terraform init`` command 
after writing a new Terraform configuration. This will ensure that Terraform has 
all the necessary components to build the template in OpenStack.

If the working  directory is initialized, we create an execution plan with
``terraform plan``.  In this step, the required resources are compared with the
state information  stored by Terraform.

After checking the plan, the configuration can be carried  out with
``terraform apply``. The instance can easily be deleted using the
``terraform destroy`` command.

Adjust the defaults
====================

In the ``variables.tf`` file you defined variables, all of which have a default value. You can override these defaults by creating a ``terraform.tfvars`` file, setting one or more of the variables to a different value:

  .. code-block:: none

     # use a larger flavor
     flavor = "2C-2GB-20GB"

Terraform will automatically use your new value and create a larger instance. If you remove the ``terraform.tfvars`` file again and create another instance, it will again use the default set in ``variables.tf``.

Regarding the tfstate files
===========================

After you have successfully created your resources, you will notice a ``terraform.tfstate`` file (and some others of the same kind) in your working directory. Those files are where terraform keeps track of which resources you actually have. This file will be refreshed at each start of a terraform run, but nevertheless should *never* be deleted.
