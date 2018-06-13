========
RabbitMQ
========

Delete all messages from a single queue
=======================================

.. code-block:: console

   $ docker exec -it rabbitmq rabbitmqctl list_queues | tail -n +2 | sort -k 2 -n
   [...]
   notifications.info    1
   notifications.sample  1
   versioned_notifications.error  4
   versioned_notifications.info   3324

.. code-block:: console

   $ docker exec -it rabbitmq rabbitmqctl purge_queue versioned_notifications.error
   Purging queue 'versioned_notifications.error' in vhost '/'
   $ docker exec -it rabbitmq rabbitmqctl purge_queue versioned_notifications.info
   Purging queue 'versioned_notifications.info' in vhost '/'
