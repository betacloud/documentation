==========
Libguestfs
==========

.. contents::
   :local:

* http://libguestfs.org
* https://github.com/libguestfs/libguestfs

libguestfs is a set of tools for accessing and modifying virtual machine (VM) disk images.

.. code-block:: console

   $ sudo apt-get install libguestfs-tools qemu-utils

Shrink a virtual machine disk
=============================

* http://libguestfs.org/guestfish.1.html
* http://libguestfs.org/virt-df.1.html
* http://libguestfs.org/virt-make-fs.1.html
* http://libguestfs.org/virt-resize.1.html
* http://libguestfs.org/virt-tar-in.1.html
* http://libguestfs.org/virt-tar-out.1.html

ext4
----

.. code-block:: console

   $ qemu-img info source.qcow2
   image: source.qcow2
   file format: qcow2
   virtual size: 100G (107374182400 bytes)
   disk size: 4.7G
   cluster_size: 65536
   Format specific information:
       compat: 1.1
       lazy refcounts: false
       refcount bits: 16
       corrupt: false

.. code-block:: console

   $ sudo virt-df source.qcow2
   Filesystem                          1K-blocks       Used  Available  Use%
   source.qcow2:/dev/sda2              101009824    4859576   95106332    5%

.. code-block:: console

   $ sudo guestfish -a source.qcow2

   Welcome to guestfish, the guest filesystem shell for
   editing virtual machine filesystems and disk images.

   Type: 'help' for help on commands
	 'man' to read the manual
	 'quit' to quit the shell

   ><fs> run
    100% ⟦▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒⟧ 00:00
   ><fs> resize2fs-size /dev/sda2 7G
   ><fs> e2fsck-f /dev/sda2
   ><fs> quit

.. code-block:: console

   $ truncate -s 10G target.img
   $ sudo virt-resize --shrink /dev/sda2 source.qcow2 target.img
   [   0.0] Examining testing.qcow2
    100% ⟦▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒⟧ 00:00
   **********

   Summary of changes:

   /dev/sda1: This partition will be left alone.

   /dev/sda2: This partition will be resized from 98.0G to 8.0G.  The 
   filesystem ext4 on /dev/sda2 will be expanded using the 'resize2fs' method.

   **********
   [  44.9] Setting up initial partition table on target.img
   [  60.0] Copying /dev/sda1
    100% ⟦▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒⟧ 00:00
   [  98.2] Copying /dev/sda2
    100% ⟦▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒⟧ --:--
    100% ⟦▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒⟧ 00:00
   [ 398.9] Expanding /dev/sda2 using the 'resize2fs' method

   Resize operation completed with no errors.  Before deleting the old disk, 
   carefully check that the resized disk boots and works correctly.

.. code-block:: console

   $ qemu-img info target.img
   image: target.qcow2
   file format: raw
   virtual size: 10G (10737418240 bytes)
   disk size: 4.7G
   $ qemu-img convert -O qcow2 target.img target.qcow2
   $ qemu-img info target.qcow2
   image: target.qcow2
   file format: qcow2
   virtual size: 10G (10737418240 bytes)
   disk size: 4.7G
   cluster_size: 65536
   Format specific information:
       compat: 1.1
       lazy refcounts: false
       refcount bits: 16
       corrupt: false

xfs
---

.. code-block:: console

   $ qemu-img info source.qcow2
   image: source.qcow2
   file format: qcow2
   virtual size: 150G (161061273600 bytes)
   disk size: 10.0G
   cluster_size: 65536
   Format specific information:
       compat: 1.1
       lazy refcounts: false
       refcount bits: 16
       corrupt: false

.. code-block:: console

   $ sudo virt-df source.qcow2
   Filesystem                           1K-blocks       Used  Available  Use%
   source.qcow2:/dev/sda1               157208580   10433884  146774696    7%

.. code-block:: console

   $ sudo guestfish -a source.qcow2

   Welcome to guestfish, the guest filesystem shell for
   editing virtual machine filesystems and disk images.

   Type: 'help' for help on commands
	 'man' to read the manual
	 'quit' to quit the shell

   ><fs> run
    100% ⟦▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒⟧ 00:00
   ><fs> mount-ro /dev/sda1 /
   ><fs> tar-out / source.tar
   ><fs> exit

.. code-block:: console

   $ sudo virt-make-fs --size 12G --type=xfs source.tar target.img

.. code-block:: console

   $ qemu-img info target.img
   image: target.img
   file format: raw
   virtual size: 12G (12884901888 bytes)
   disk size: 10.0G
   $ qemu-img convert -O qcow2 target.img target.qcow2
   $ qemu-img info target.qcow2
   image: target.qcow2
   file format: qcow2
   virtual size: 12G (12884901888 bytes)
   disk size: 10.0G
   cluster_size: 65536
   Format specific information:
       compat: 1.1
       lazy refcounts: false
       refcount bits: 16
       corrupt: false

Boot stuck at "grub"
====================

* http://manpages.ubuntu.com/manpages/cosmic/man1/guestfs-recipes.1.html

.. code-block:: console

   $ sudo apt-get install -y syslinux

.. code-block:: none
   :caption: syslinux.cfg

   DEFAULT linux
   LABEL linux
     SAY Booting the kernel
     KERNEL /boot/vmlinuz
     INITRD /boot/initrd
     APPEND ro root=/dev/sda2

.. code-block:: console

   $ sudo guestfish -a target.img

   Welcome to guestfish, the guest filesystem shell for
   editing virtual machine filesystems and disk images.

   Type: 'help' for help on commands
	 'man' to read the manual
	 'quit' to quit the shell

   ><fs> run
    100% ⟦▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒⟧ 00:00
   ><fs> mount /dev/sda2 /
   ><fs> upload /usr/lib/SYSLINUX/mbr.bin /boot/mbr.bin
   ><fs> upload syslinux.cfg /boot/syslinux.cfg
   ><fs> copy-file-to-device /boot/mbr.bin /dev/sda
   ><fs> extlinux /boot
   ><fs> part-set-bootable /dev/sda 2 true
   ><fs> umount /
