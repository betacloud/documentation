=========
RancherOS
=========

.. contents::
   :local:

* https://rancher.com/rancher-os/

RancherOS, a lightweight, secure Linux distribution, built from containers to run containers well.

Preparations
============
Create a ``clouds.yaml`` and ``secure.yaml`` file (see :ref:`Authentication` chapter).


Cloud-init
==========
Cloud-init is the industry standard multi-distribution method for cross-platform cloud instance initialization. Cloud-init will identify the cloud it is running on during boot, read any provided metadata from the cloud and initialize the system accordingly. This may involve setting up the network and storage devices to configuring SSH access key and many other aspects of a system. Later on the cloud-init will also parse and process any optional user or vendor data that was passed to the instance.

* https://cloudinit.readthedocs.io/en/latest/

* https://cloudinit.readthedocs.io/en/latest/topics/modules.html


Openstack Config drive
======================
If we change the settings from the ``RancherOS``, we need to enable the config drive parameter. Pass ``--config-drive`` to the ``openstack server create`` command (https://docs.openstack.org/nova/queens/user/config-drive.html).

  .. code-block:: none
     
     openstack server create --config-drive true ....


Examples
========
* Create a ``rancher.txt`` file and define the ``hostname``, ``etc hosts`` and ``ssh authorized keys``. Try somthing like this:
  
  .. code-block:: none

     #cloud-config

     hostname: testinstance

     manage_etc_hosts: yes

     ssh_authorized_keys:
       - ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDoI70hvzGAiwxUqQQIfypVCSUxfx0zFZKgzBUkjIznUeDl6+rSwUtphS.....


* Execute the ``openstack server create`` command:

  .. code-block:: none

     openstack server create --config-drive true --image "RancherOS 1.5.4" --flavor 2C-4GB-40GB --nic net-id=55bd2e08-428d-484b-9ac3-8ce5882e1c68 --security-group linux --max 1 --user-data /home/user/rancher.txt testinstance
