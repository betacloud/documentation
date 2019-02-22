================
k8s-on-openstack
================

.. contents::
   :local:

* https://github.com/infraly/k8s-on-openstack/

An opinionated way to deploy a Kubernetes cluster on top of an OpenStack cloud.

Preparations
============

* Install required packages

  .. code-block:: console

     $ sudo apt install ansible git python-shade

* Clone the ``k8s-on-openstack`` repository

  .. code-block:: console

     $ git clone https://github.com/infraly/k8s-on-openstack.git
     $ cd k8s-on-openstack

* Create an environment file, e.g. ``k8s-on-openstack.env``

  .. code-block:: shell

     export OS_PROJECT_DOMAIN_NAME=default
     export OS_USER_DOMAIN_NAME=default
     export OS_PROJECT_NAME=PROJECTNAME
     export OS_USERNAME=USERNAME
     export OS_PASSWORD=PASSWORD
     export OS_AUTH_URL=https://api-1.betacloud.io:5000/v3
     export OS_IDENTITY_API_VERSION=3
     export OS_IMAGE_API_VERSION=2

     export EXTERNAL_NETWORK=public
     export FLOATING_IP_POOL=public
     export IMAGE="Ubuntu 16.04 (Xenial Xerus)"
     export KEY=k8s-testbed
     export MASTER_BOOT_FROM_VOLUME=False
     export NAME=k8s-testbed
     export NODE_AUTO_IP=True

* If it does not exist, create a ``k8s-testbed`` keypair and add
  ``private_key_file = k8s-testbed.pem`` to the ``defaults``
  section of the ``ansible.cfg`` configuration file

  .. code-block:: console

     $ chmod 600 k8s-testbed.pem

Creation
========

* Run the ``site.yaml`` playbook

  .. code-block:: console

     $ source k8s-on-openstack.env
     $ ansible-playbook site.yaml

Usage
=====

Destruction
===========

* Run the ``destroy.yaml`` playbook

  .. code-block:: console

     $ source k8s-on-openstack.env
     $ ansible-playbook destroy.yaml
