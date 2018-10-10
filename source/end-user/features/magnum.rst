======
Magnum
======

Magnum is an OpenStack API service developed by the OpenStack Containers Team
making container orchestration engines (COE) such as Docker Swarm, Kubernetes
and Apache Mesos available as the first class resources in OpenStack.

Documentation
=============

* https://docs.openstack.org/magnum/latest/user/

Preparations
============

* You need to have the ``heat_stack_owner`` role assigned to create Magnum
  clusters.

Create a cluster template
=========================

You need a Glance image that is supported by Magnum. The ``os_distro``
property has to be set properly on the image. We recommend the Fedora Atomic
image, with the ``os_distro`` property set to ``fedora-atomic``.

.. code-block:: console

   $ openstack coe cluster template create \
       --coe kubernetes \
       --dns-nameserver 9.9.9.9 \
       --docker-volume-size 10 \
       --external-network public \
       --flavor 1C-1GB-10GB \
       --image 'Fedora Atomic Host 27' \
       --labels docker_volume_type=ceph-1,prometheus_monitoring=True \
       --master-flavor 1C-1GB-10GB \
       --master-lb-enabled \
       --network-driver flannel \
       'Fedora Atomic Host 27 - Kubernetes'

.. note::

   For additional parameters have a look at the Magnum user documentation:
   https://docs.openstack.org/magnum/latest/user/#kubernetes

Create a cluster
================

.. code-block:: console

   $ openstack coe cluster template list
   +--------------------------------------+------------------------------------+
   | uuid                                 | name                               |
   +--------------------------------------+------------------------------------+
   | 52edf7fb-d61a-4311-a99e-6d2f5c005a03 | Fedora Atomic Host 27 - Kubernetes |
   +--------------------------------------+------------------------------------+

.. code-block:: console

   $ openstack coe cluster create \
       --cluster-template 'Fedora Atomic Host 27 - Kubernetes' \
       --keypair KEYPAIR \
       --master-count 1 \
       --node-count 2 \
       testing
   Request to create cluster 2548f2b3-64bd-487f-b5b5-003e631c8909 accepted

Usage
=====

* Save cluster config to a local directory and add it to environment

  .. code-block:: console

     $ mkdir magnum-testing
     $ openstack coe cluster config --dir magnum-testing testing
     $ export KUBECONFIG=magnum-testing/config

* Now you can use ``kubectl`` as usually.

  .. code-block:: console

     $ kubectl cluster-info
     Kubernetes master is running at https://a.b.c.d:6443
     CoreDNS is running at https://a.b.c.d:6443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

     To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.

.. note::

   You can obtain ``kubectl`` at https://kubernetes.io/docs/tasks/tools/install-kubectl/.

Troubleshooting
===============

* If you receive HTTP 400 erros on cluster creation, try to use the ``magnum``
  command instead of the ``openstack`` command.

* Investigate your cluster

  .. code-block:: console

     $ openstack coe cluster list
     +--------------------------------------+---------+---------+------------+--------------+-----------------+
     | uuid                                 | name    | keypair | node_count | master_count | status          |
     +--------------------------------------+---------+---------+------------+--------------+-----------------+
     | 3ed5bc1d-6b08-4b58-ac7d-1410027ea574 | testing | KEYPAIR | 2          | 1            | CREATE_COMPLETE |
     +--------------------------------------+---------+---------+------------+--------------+-----------------+

  .. code-block:: console

     $ openstack coe cluster -show <cluster>

* Investigate the Heat stack of your cluster

  .. code-block:: console

     $ openstack coe cluster show testing -c stack_id
     $ openstack stack show <stack_id>
     $ openstack stack event list <stack_id>

* Investigate the virtual machines of your cluster

  .. code-block:: console

     $ openstack stack resource list <stack_id> -n 2 \
         --filter type=OS::Nova::Server
     $ openstack console log show <server>
