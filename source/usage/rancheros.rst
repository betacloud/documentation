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


Cloud-config
============
With cloud-init instances can be configured at boot-time. Arbitrary scripts and configuration settings can be passed as user-data via the meta-data service.

Configuration settings start with the header ``#cloud-config``

* https://rancher.com/docs/os/v1.x/en/installation/configuration/#cloud-config
* https://cloudinit.readthedocs.io/en/latest/
* https://cloudinit.readthedocs.io/en/latest/topics/modules.html


Openstack configuration drive
=============================
If we change the settings from the ``RancherOS``, we need to enable the config drive parameter. Pass ``--config-drive`` to the ``openstack server create`` command (https://docs.openstack.org/nova/train/user/metadata.html#using-the-config-drive).

  .. code-block:: console
     
     openstack server create --config-drive true ....


Examples
========

* Create a ``rancher.txt`` file and define the ``hostname``, ``etc hosts`` and ``ssh authorized keys``. Try somthing like this:
  
  .. code-block:: yaml

     #cloud-config

     hostname: testinstance

     manage_etc_hosts: yes

     ssh_authorized_keys:
       - ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDoI70hvzGAiwxUqQQIfypVCSUxfx0zFZKgzBUkjIznUeDl6+rSwUtphS.....


* Execute the ``openstack server create`` command:

  .. code-block:: console

     openstack server create \
         --config-drive true \
         --image "RancherOS 1.5.4" \
         --flavor 2C-4GB-40GB \
         --nic net-id=55bd2e08-428d-484b-9ac3-8ce5882e1c68 \ 
         --security-group linux \
         --max 1 --user-data /home/user/rancher.txt testinstance
