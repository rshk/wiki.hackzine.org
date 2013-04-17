############################
GuruPlug: installing Debian
############################

See also the full guide here: :doc:`guruplug-misc`

**Note:** this is a mirror copy of `Installing Debian on Plug Computers`_

.. _Installing Debian on Plug Computers: http://www.cyrius.com/debian/kirkwood/sheevaplug/install.html

This page describes how to install Debian 6.0 (squeeze) on plug computers,
such as the SheevaPlug and GuruPlug.

**Note:** Actually, as of April 2012, the images are for Wheezy (7.0).

The following devices are currently supported:

* **SheevaPlug** (the SheevaPlug Development Kit)
* **eSATA SheevaPlug**
* **GuruPlug Server Standard**
* **GuruPlug Server Plus**
* **Seagate FreeAgent DockStar**
  (`limited support <http://www.cyrius.com/debian/kirkwood/sheevaplug/plugs.html#limited>`_:
  only if you added a serial console)

Please read the `plug variants`_ page to find out about the status of other plug computers.

.. _plug variants: http://www.cyrius.com/debian/kirkwood/sheevaplug/plugs.html

The Debian installer doesn't currently support installations to on-board flash
storage, but you can use it to install to USB, SD or eSATA.

In order to proceed, you will therefore need either a USB stick (or disk),
an SD card or an external disk with an eSATA port.

Preparation
===========

Make sure to connect an Ethernet cable to your plug computer (if you haven't
already) because the installer will download files from the Internet for the
installation.

Upgrading U-Boot
================

You have to upgrade the u-boot boot loader before you can install Debian.

Please visit the page describing the `u-boot upgrade process`_ (mirrored
in :doc:`guruplug-uboot-upgrade-guide`) to ensure that you have the right
version of u-boot before proceeding with the installation of Debian.

.. _u-boot upgrade process: http://www.cyrius.com/debian/kirkwood/sheevaplug/uboot-upgrade.html

Starting the Installer
======================

First of all, you have to download the installer. Download the  uImage_ and
uInitrd_ and store them either on a USB stick, MMC/SD card or a TFTP server.

.. _uImage: ftp://ftp.debian.org/debian/dists/stable/main/installer-armel/current/images/kirkwood/netboot/marvell/sheevaplug/uImage
.. _uInitrd: ftp://ftp.debian.org/debian/dists/stable/main/installer-armel/current/images/kirkwood/netboot/marvell/sheevaplug/uInitrd

Now connect the install medium (USB stick, SD card or eSATA disk) to your plug
computer and connect a mini-USB connector in order to access the serial console.

Start your plug computer and a few seconds later you should be able to connect
to ``/dev/ttyUSB1`` with 115200 baud. If you need help accessing the serial
console, check out `this guide <http://www.plugcomputer.org/plugwiki/index.php/Serial_terminal_program>`_
on the Plug Computer wiki. When you get serial output, press a key to interrupt
the boot process so you can load the installer.

The instructions for loading the installer depend on where you want to load the
installer from. Also note that you may have to replace fatload with ext2load in
case you used the ext2 or ext3 filesystem on your USB stick or MMC card.

.. note::
    For GuruPlug users: on the GuruPlug, MMC/SD cards show up as USB devices.
    Therefore, if you're using a MMC/SD card, make sure to follow the
    instructions for USB devices and not for MMC/SD.

**USB:** If you stored the installer on a USB stick, please use::

    usb start
    fatload usb 0:1 0x00800000 /uImage
    fatload usb 0:1 0x01100000 /uInitrd

**SD card:** for MMC/SD cards, use::

    mmc init
    fatload mmc 0:1 0x00800000 /uImage
    fatload mmc 0:1 0x01100000 /uInitrd

**TFTP:** if you want to load the installer via the network from a TFTP server,
use this::

    setenv serverip 192.168.1.2
    setenv ipaddr 192.168.1.147
    tftpboot 0x00800000 uImage
    tftpboot 0x01100000 uInitrd

Of course, you have to replace 192.168.1.2 with the IP address of your TFTP server.

Finally, start the installer::

    setenv bootargs console=ttyS0,115200n8 base-installer/initramfs-tools/driver-policy=most
    bootm 0x00800000 0x01100000

The Installation
================

The installation itself should be pretty standard and you can follow the
`installation guide <http://www.debian.org/releases/stable/armel>`_.

The installer knows about all supported plug computers and will create a
bootable kernel and ramdisk at the end of the installation.
The installer will also offer a partition layout that is known to work.
If you want to choose a different layout, make sure that you create a small
(ca. 150 MB) ``/boot`` partition with the ext2 filesystem.

When the installation is done, you have to configure u-boot so it will
automatically boot Debian.
Interrupt the boot process of u-boot and enter the following commands.

For **USB**, use this::

    setenv bootargs_console console=ttyS0,115200
    setenv bootcmd_usb 'usb start; ext2load usb 0:1 0x00800000 /uImage; ext2load usb 0:1 0x01100000 /uInitrd'
    setenv bootcmd 'setenv bootargs $(bootargs_console); run bootcmd_usb; bootm 0x00800000 0x01100000'
    saveenv

If you're using an **SD card**, use these commands instead::

    setenv bootargs_console console=ttyS0,115200
    setenv bootcmd_mmc 'mmc init; ext2load mmc 0:1 0x00800000 /uImage; ext2load mmc 0:1 0x01100000 /uInitrd'
    setenv bootcmd 'setenv bootargs $(bootargs_console); run bootcmd_mmc; bootm 0x00800000 0x01100000'
    saveenv

Finally, use these commands to boot from **eSATA**::

    setenv bootargs_console console=ttyS0,115200
    setenv bootcmd_sata 'ide reset; ext2load ide 0:1 0x00800000 /uImage; ext2load ide 0:1 0x01100000 /uInitrd'
    setenv bootcmd 'setenv bootargs $(bootargs_console); run bootcmd_sata; bootm 0x00800000 0x01100000'
    saveenv

The commands above use 0:1 to refer to your boot partition.
This indicates device 0 and partition 1.
Depending on your configuration and device, you may have to specify a different
boot partition.
Please refer to the explanation on `how to find out your boot partition`_ in
case your device does not boot with 0:1.

.. _how to find out your boot partition: http://www.cyrius.com/debian/kirkwood/sheevaplug/troubleshooting.html#dev-part

Your plug computer is now ready to boot Debian from USB, SD or eSATA and it
will automatically do so whenever you turn on the plug computer.

You can now type the following command to boot::

    run bootcmd

Go back to my `Debian on Plug Computer`_ page.

.. _Debian on Plug Computer: http://www.cyrius.com/debian/kirkwood/sheevaplug/index.html
