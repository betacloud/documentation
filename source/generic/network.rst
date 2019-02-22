=======
Network
=======

.. contents::
   :local:

sshuttle - poor man’s VPN 
=========================

* https://github.com/sshuttle/sshuttle
* https://sshuttle.readthedocs.org/

sshuttle - referred to by developers as "a poor man's VPN" - provides the ability to tunnel
entire subnets over an SSH connection with a simple command. For example, if you only have
access to subnets ``192.168.100.0/24`` (FIRST_CIDR) and ``192.168.200.0/24`` (SECOND_CIDR)
via jumphost ``185.136.140.45`` (BASTION_ADDRESS), the following command is sufficient to
make them available on the local host:

.. code-block:: console

   $ sshuttle -e 'ssh -i id_rsa.sample' -r ubuntu@BASTION_ADDRESS FIRST_CIDR SECOND_CIDR ...
