======
Magnum
======

* https://docs.openstack.org/magnum/latest/user/

Magnum is an OpenStack API service developed by the OpenStack Containers Team
making container orchestration engines (COE) such as Docker Swarm, Kubernetes
and Apache Mesos available as the first class resources in OpenStack.

Preparations
============

* You need to have the ``heat_stack_owner`` role assigned to create Magnum
  clusters.

* You need a Glance image that is supported by Magnum. The ``os_distro``
  property has to be set properly on the image. We recommend the Fedora Atomic
  Image, with the ``os_distro`` property set to ``fedora-atomic``.

.. note:: 
  
   Be careful to pick an image version that is compatible with the current
   Magnum version. Currently Betacloud is running OpenStack Ocata, so the
   ``Fedora Atomic Host 25 for Magnum (Ocata)`` image works fine.

Create Cluster Template and Cluster
===================================

* Create a cluster template. You have to specify the flavors to be used for k8s
  masters and k8s nodes seperately.

  .. code-block:: console

     $ magnum cluster-template-create --image <image>  --keypair <keypair> \
         --external-network <external-network> --dns-nameserver <nameserver> \
         --network-driver flannel --coe kubernetes --flavor <flavor> \
         --master-flavor <master-flavor>

* Create a cluster from your cluster template.

  .. code-block:: console

     $ magnum cluster-create --cluster-template <template> \
         --node-count <node-count> <name>

* For additional parameters have a look at the Magnum user documentation:
  https://docs.openstack.org/magnum/latest/user/#kubernetes

Usage
=====

* Save cluster config to a local directory and add it to environment

  .. code-block:: console

     $ magnum cluster-config --dir <dir> <cluster>
     $ export KUBECONFIG=<dir>/config

* Now you can use ``kubectl`` as usually.

  .. code-block:: console

     $ kubectl cluster-info

.. note::

   You can obtain ``kubectl`` at
   https://kubernetes.io/docs/tasks/tools/install-kubectl/

Troubleshooting
===============

* If you receive HTTP 400 erros on cluster creation, try to use the ``magnum``
  command instead of the ``openstack`` command.

* Investigate your cluster

  .. code-block:: console

     $ magnum cluster-list
     $ magnum cluster-show <cluster>

* Investigate the Heat stack of your cluster

  .. code-block:: console

     $ magnum cluster-list --fields uuid,name,stack_id
     $ openstack stack show <stack_id>
     $ openstack stack event list <stack_id>

* Investigate the virtual machines of your cluster

  .. code-block:: console

     $ openstack stack resource list <stack_id> -n 2 \
         --filter type=OS::Nova::Server
     $ openstack console log show <server>

