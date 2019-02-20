=========
Kubespray
=========

.. contents::

* https://github.com/kubernetes-sigs/kubespray

Preparations
============

* Clone kubespray repository

  .. code-block:: console

     $ git clone https://github.com/kubernetes-sigs/kubespray

* Prepare sample inventory directory

  .. code-block:: console

     $ cp -LRp contrib/terraform/openstack/sample-inventory inventory/mycluster
     $ ln -s ../../contrib/terraform/openstack/hosts inventory/mycluster
     $ ln -s ../../contrib inventory/mycluster

* Create a ``clouds.yaml`` and ``secure.yaml`` file (see :ref:`Authentication` chapter)

* Create a ssh keypair with ``ssh-keygen -t rsa -b 2048 -N "" -C "" -f inventory/mycluster/id_rsa.mycluster``

Terraform
---------

* Modify the ``cluster.tf`` configuration file in ``inventory/mycluster``

  .. code-block:: none

     az_list = ["south-1"]

     cluster_name = "mycluster"
     public_key_path = "id_rsa.mycluster.pub"
     image = "Ubuntu 18.04"
     ssh_user = "ubuntu"

     number_of_etcd = 0
     number_of_k8s_masters = 0
     number_of_k8s_masters_no_etcd = 0
     number_of_k8s_masters_no_floating_ip = 1
     number_of_k8s_masters_no_floating_ip_no_etcd = 0
     flavor_k8s_master = "140"

     number_of_k8s_nodes = 0
     number_of_k8s_nodes_no_floating_ip = 4
     flavor_k8s_node = "140"

     network_name = "mycluster"
     external_net = "0647c0a0-862c-4c7e-9433-4558fcc5573b"
     subnet_cidr = "192.168.0.0/24"
     floatingip_pool = "public"

     number_of_bastions = 1
     flavor_bastion = "30"
     bastion_allowed_remote_ips = ["0.0.0.0/0"]

.. note::

   * Remove none of the ``number_of`` variables.
   * The ID of the flavor must be used. Use ``openstack --os-cloud betacloud flavor list``.
   * At least 1024 MByte memory for nodes. We recommend a flavor with at least 2 GByte memory and 2 vCPUs.
   * At least 1500 MByte memory for masters. We recommend a flavor with at least 2 GByte memory and 2 vCPUs.

Ansible
-------

* Prepare mitogen (optional step)

  .. code-block:: console

     $ ansible-playbook mitogen.yaml

* Adjust ``inventory/mycluster/group_vars/k8s-cluster/k8s-cluster.yml``

  .. code-block:: yaml

     kubeconfig_localhost: true
     kubectl_localhost: true

     kube_network_plugin: flannel

* Adjust ``inventory/mycluster/group_vars/all/all.yml`` if necessary

* Add a ``log_path`` to the ``ansible.cfg`` configuration file

  .. code-block:: ini

     [defaults]
     log_path = ansible.log

Usage
=====

Terraform
---------

* Prepare the infrastructure (execute commands in ``inventory/mycluster``)

  .. code-block:: console

     $ export OS_CLOUD=betacloud
     $ terraform init contrib/terraform/openstack
     $ terraform apply -var-file=cluster.tf contrib/terraform/openstack

* Move the ``no-floating.yml`` file to the correct location (that's a bug)

  .. code-block:: console

     $ cp inventory/sample/group_vars/no-floating.yml inventory/mycluster/group_vars/no-floating.yml

Ansible
-------

.. note::

   Ansible v2.7.0 is failing and/or produce unexpected results due to an issue.

.. note::

   Depending on the environment, the shebang of ``inventory/mycluster/hosts`` must be adapted.

   .. code-block:: console

      [WARNING]:  * Failed to parse [...]/inventory/mycluster/hosts with script plugin:
      Inventory script ([...]/inventory/mycluster/hosts) had an execution error:
      env: python2: No such file or directory

Deployment
~~~~~~~~~~

.. code-block:: console

   $ ansible-playbook \
       --become \
       -i inventory/mycluster/hosts \
       -e ansible_ssh_private_key_file=inventory/mycluster/id_rsa.mycluster \
       cluster.yml

Upgrade
~~~~~~~

* Set e.g. the new ``kube_version`` in ``inventory/mycluster/group_vars/k8s-cluster/k8s-cluster.yml``

.. code-block:: console

   $ ansible-playbook \
      -i inventory/mycluster/hosts \
      -e ansible_ssh_private_key_file=inventory/mycluster/id_rsa.mycluster \
      upgrade-cluster.yml

Scale up (worker, master or etcd nodes)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

* Adjust ``inventory/mycluster/cluster.tf`` and apply changes with
  ``terraform apply -var-file=cluster.tf contrib/terraform/openstack``

.. code-block:: console

   $ ansible-playbook \
       --become \
       -i inventory/mycluster/hosts \
       -e ansible_ssh_private_key_file=inventory/mycluster/id_rsa.mycluster \
       scale.yml

Scale down (worker nodes only)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

* Adjust ``inventory/mycluster/cluster.tf`` and apply changes with
  ``terraform apply -var-file=cluster.tf contrib/terraform/openstack``

.. code-block:: console

   $ ansible-playbook \
       --become \
       -i inventory/mycluster/hosts \
       -e ansible_ssh_private_key_file=inventory/mycluster/id_rsa.mycluster \
       remove-node.yml

Kubernetes
----------

.. code-block:: console

   $ sshuttle -e 'ssh -i inventory/mycluster/id_rsa.mycluster' -r ubuntu@BASTION_ADDRESS 192.168.0.0/24

.. code-block:: console

   $ cd inventory/mycluster/artifacts
   $ ./kubectl.sh get nodes
   NAME                        STATUS    ROLES     AGE       VERSION
   mycluster-k8s-master-nf-1   Ready     master    121m      v1.13.3
   mycluster-k8s-node-nf-1     Ready     node      120m      v1.13.3
   mycluster-k8s-node-nf-2     Ready     node      120m      v1.13.3
   mycluster-k8s-node-nf-3     Ready     node      120m      v1.13.3
   mycluster-k8s-node-nf-4     Ready     node      120m      v1.13.3

.. code-block:: console

   $ ./kubectl.sh run hello-world \
       --replicas=2 \
       --labels="run=load-balancer-example" \
       --image=gcr.io/google-samples/node-hello:1.0 \
       --port=8080
   deployment.apps/hello-world created

.. code-block:: console

   $ ./kubectl.sh expose deployment hello-world \
       --type=NodePort \
       --name=example-service
   service/example-service exposed

.. code-block:: console

   $ ./kubectl.sh get pods
   NAME                           READY     STATUS    RESTARTS   AGE
   hello-world-696b6b59bd-7d8md   1/1       Running   0          3m24s
   hello-world-696b6b59bd-bz64c   1/1       Running   0          3m24s

.. code-block:: console

   $ ./kubectl.sh describe services example-service
   Name:                     example-service
   Namespace:                default
   Labels:                   run=load-balancer-example
   Annotations:              <none>
   Selector:                 run=load-balancer-example
   Type:                     NodePort
   IP:                       10.233.1.98
   Port:                     <unset>  8080/TCP
   TargetPort:               8080/TCP
   NodePort:                 <unset>  32732/TCP
   Endpoints:                <none>
   Session Affinity:         None
   External Traffic Policy:  Cluster
   Events:                   <none>

.. code-block:: console

   $ curl MASTER_ADDRESS:32732
   Hello Kubernetes!
