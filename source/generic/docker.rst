======
Docker
======

.. contents::
   :local:

Network issues with Docker
==========================

In case of network issues make sure that the ``mtu`` value of your system and the one
used by docker match. Docker defaults to ``1500`` whereas instances running on OpenStack
are often using ``1450``. Use ``ip a`` to detect the value of your primary interface and
insert the value into ``/etc/docker/daemon.json``.

.. code-block:: console
   :emphasize-lines: 8

   $ ip a
   1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1
       link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
       inet 127.0.0.1/8 scope host lo
	  valid_lft forever preferred_lft forever
       inet6 ::1/128 scope host
	  valid_lft forever preferred_lft forever
   2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc pfifo_fast state UP group default qlen 1000
       link/ether fa:16:3e:7a:6d:36 brd ff:ff:ff:ff:ff:ff
       inet 10.250.5.77/26 brd 10.250.5.127 scope global eth0
	  valid_lft forever preferred_lft forever
       inet6 fe80::f816:3eff:fe7a:6d36/64 scope link
	  valid_lft forever preferred_lft forever<Paste>

.. code-block:: json

   {
         "mtu": 1450
   }

See `here <https://mlohr.com/docker-mtu/>`_ for further information
(``docker-compose``, etc.).
