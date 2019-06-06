=======
Neutron
=======

.. contents::
   :local:

Shared VIPs for clustered services
==================================

* https://docs.catalystcloud.io/tutorials/deploying-highly-available-instances-with-keepalived.html

To create a shared VIP to use e.g. within a pacemaker cluster, do the following.


1. Find the ID of the ports to assign the VIP to (repeat this step for each server)

   .. code-block:: console

      $ openstack port list --server myserver1
      +--------------------------------------+------+-------------------+----------------------------------------------------------------------------+--------+
      | ID                                   | Name | MAC Address       | Fixed IP Addresses                                                         | Status |
      +--------------------------------------+------+-------------------+----------------------------------------------------------------------------+--------+
      | 3db1151a-0d5e-4eab-a460-aeebe6d8af10 |      | fa:16:3e:e8:4e:31 | ip_address='10.250.7.17', subnet_id='d1e2aab6-d15d-466e-bb75-93873b8a6776' | ACTIVE |
      +--------------------------------------+------+-------------------+----------------------------------------------------------------------------+--------+

   .. code-block:: console

      $ openstack port list --server myserver2
      +--------------------------------------+------+-------------------+----------------------------------------------------------------------------+--------+
      | ID                                   | Name | MAC Address       | Fixed IP Addresses                                                         | Status |
      +--------------------------------------+------+-------------------+----------------------------------------------------------------------------+--------+
      | 949d8d35-2e59-4e04-9264-2b7ac9f258ac |      | fa:16:3e:f8:4a:a8 | ip_address='10.250.7.18', subnet_id='d1e2aab6-d15d-466e-bb75-93873b8a6776' | ACTIVE |
      +--------------------------------------+------+-------------------+----------------------------------------------------------------------------+--------+


2. Use the ID of the subnet to get the ID of the network

   .. code-block:: console

      $ openstack subnet show d1e2aab6-d15d-466e-bb75-93873b8a6776 | grep network_id
      | network_id        | 90f3c4d8-daf3-4fce-a46a-902f6dfcdbd1 |

3. Create a new port to block the VIP

   .. code-block:: console

      $ openstack port create --network=90f3c4d8-daf3-4fce-a46a-902f6dfcdbd1 --fixed-ip ip-address=10.250.7.19 subnet=d1e2aab6-d15d-466e-bb75-93873b8a6776

4. Assign the new VIP to the ports you found out in the first step

   .. code-block:: console

      $ openstack port set 8b41d841-1602-4e73-b63b-3ad9d6062de5 --allowed-address ip-address=10.250.7.19

FWaaS
=====

LBaaS
=====

VPNaaS
======

Routing foreign IPs (e.g. VPN)
==============================

If you have a VPN connection that you would like to route into your local
network, you'll have to add the IP (or network) to the 'allowed addresses' of
the port corresponding to your instance.

If you do not allow the address, it will be blocked, as OpenStack by default
only allows the recognized MAC address plus any configured subnets.

   .. code-block:: console

      $ openstack port set <PORT-ID> --allowed-address ip-address=10.1.2.0/24
