=========
RancherOS
=========

.. contents::
   :local:

* https://rancher.com/rancher-os/

RancherOS, a lightweight, secure Linux distribution, built from containers to run containers well.


Cloud-init
==========
* Create a ``rancher.txt`` file and define the ``hostname``, ``etc hosts`` and ``authorized keys`` modules (https://cloudinit.readthedocs.io/en/latest/topics/modules.html).

  .. code-block:: none

     #cloud-config

     hostname: testinstance

     manage_etc_hosts: yes

     ssh_authorized_keys:
       - ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDoI70hvzGAiwxUqQQIfypVCSUxfx0zFZKgzBUkjIznUeDl6+rSwUtphS.....


Openstack Config drive
======================
If we change the settings from the ``RancherOS``, we need to enable the config drive parameter. Pass ``--config-drive`` to the ``openstack server create`` command.

  .. code-block:: none
     
     openstack server create --config-drive true ....


Examples
========



