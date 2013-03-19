Debian: installation from chroot
################################

**See also:** `Installing Debian GNU/Linux from a Unix/Linux System`_

.. _Installing Debian GNU/Linux from a Unix/Linux System:
    http://www.debian.org/releases/stable/i386/apds03.html.en

Partitioning
============

Use ``fdisk`` to create partitions on the device, then create filesystems /
swap space / ...

Example::

    fdisk /dev/sdx

And create the following partitions:

* ``/dev/sdx1`` -> the root directory
* ``/dev/sdx2`` -> the swaps space
* ``/dev/sdx3`` -> the /home directory

Create filesystems
------------------

::

    mke2fs /dev/sdx1      #--> for ext2 filesystems
    mke2fs -j /dev/sdx1   #--> for ext3 filesystems
    mkfs.ext4 /dev/sdx1   #--> for ext4 filesystems
    mkswap /dev/sdx2      #--> for swap space


Activate swap
-------------

It is recommended to sync before activating swap::

    sync ; sync ; sync
    swapon /dev/sdx2

Mount the root filesystem
-------------------------

::

    mkdir /mnt/debinst
    mount /dev/sdx1 /mnt/debinst


Install the system using debootstrap
====================================

Installing debootstrap
----------------------

The utility used by the Debian installer, and recognized as the official way
to install a Debian base system, is **debootstrap**.
It uses **wget** and **ar**, but otherwise depends only on ``/bin/sh`` and
basic Unix/Linux tools. Install **wget** and **ar** if they aren't already on
your current system, then download and install **debootstrap**.

Or, you can use the following procedure to install it manually.
Make a work folder for extracting the .deb into::

    mkdir work
    cd work

The debootstrap binary is located in the Debian archive (be sure to select
the proper file for your architecture). Download the debootstrap .deb from
the `pool <http://ftp.debian.org/debian/pool/main/d/debootstrap/>`_,
copy the package to the work folder, and extract the files from it.
You will need to have root privileges to install the files.

::

    ar -x debootstrap_0.X.X_all.deb
    cd /
    zcat /full-path-to-work/work/data.tar.gz | tar xv


Running debootstrap
-------------------

debootstrap can download the needed files directly from the archive when you
run it. You can substitute any Debian archive mirror for http.us.debian.org/debian
in the command example below, preferably a mirror close to you network-wise.
Mirrors are listed at http://www.debian.org/mirror/list.

If you have a squeeze Debian GNU/Linux CD mounted at ``/cdrom``,
you could substitute a file URL instead of the http URL: ``file:/cdrom/debian/``

Substitute one of the following for ''ARCH'' in the debootstrap command:
alpha, amd64, arm, armel, hppa, i386, ia64, m68k, mips, mipsel, powerpc, s390,
or sparc.

::

    /usr/sbin/debootstrap --arch ARCH squeeze /mnt/debinst http://ftp.us.debian.org/debian


**TODO:** Finish this guide

**TODO:** Add information on how to install on UsbKey (how did I do that, btw?)
