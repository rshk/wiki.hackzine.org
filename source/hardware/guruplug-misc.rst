GuruPlug: misc stuff
####################

* See the `official GuruPlug wiki page`_
* **Default password:** ``nosoup4u``

Connecting to serial console
============================

**See:** http://www.plugcomputer.org/plugwiki/index.php/Serial_terminal_program

1. Connect the JTAG board "UART" cable to the port on the GuruPlug
2. Set the mode to UART (not RS232)
3. Power up the plug
4. Connect using ``screen /dev/ttyUSB0 115200``

Accessing U-Boot console
------------------------

Do as above, but connect to the serial port **before** powering up the plug.
Then, as soon as you see serial output, press a key to interrupt normal
boot and access the U-Boot console::

    U-Boot 2009.11-rc1-00602-g28a9c08-dirty (Feb 09 2010 - 18:15:21)
    Marvell-Plug2L

    SoC:   Kirkwood 88F6281_A0
    DRAM:  512 MB
    NAND:  512 MiB
    In:    serial
    Out:   serial
    Err:   serial
    Net:   egiga0, egiga1
    88E1121 Initialized on egiga0
    88E1121 Initialized on egiga1
    Hit any key to stop autoboot:  0
    Marvell>>

Debian installation
===================

As of 2012-04-13, I successfully followed these guides in order to get debian
running on a usbkey

* `Upgrading SheevaPlug's U-Boot`_ (:doc:`mirror </hardware/guruplug-uboot-upgrade-guide>`)
    The instructions on how to upgrade the uBoot bootloader in order to do something that would otherwise fail, during the installation :)

* `Installing Debian on Plug Computers`_
    About installing Debian on the Plug

* `Installing Debian on the guruplug server plus`_
    Although that guide is a bit old, and some issues it's talking about have been fixed in the meanwhile (luckily), I found some useful advices there too.

* `Installing Debian To Flash`_
    The guide on how to copy the installation from the USB stick to the internal NAND memory of the plug

.. _Upgrading SheevaPlug's U-Boot: http://www.cyrius.com/debian/kirkwood/sheevaplug/uboot-upgrade.html
.. _Installing Debian on Plug Computers: http://www.cyrius.com/debian/kirkwood/sheevaplug/install.html
.. _Installing Debian on the guruplug server plus: http://bzed.de/posts/2010/05/installing_debian_on_the_guruplug_server_plus/
.. _Installing Debian To Flash: http://www.plugcomputer.org/plugwiki/index.php/Installing_Debian_To_Flash


Upgrading uBoot
---------------

Follow the guide here: `Upgrading SheevaPlug's U-Boot`_

(mirrored here: :doc:`/hardware/guruplug-uboot-upgrade-guide`)


Installing debian on the usb stick, booting, etc.
-------------------------------------------------

Just follow the guide here: `Installing Debian on Plug Computers`_

(mirrored here: :doc:`/hardware/guruplug-installing-debian`)


Copy to flash
-------------

**Step 1** Flash the kernel from the usb stick on the NAND::

    usb start
    ext2load usb 0:1 0x2000000 /uImage
    iminfo
    nand erase 0x100000 0x400000
    nand write 0x2000000 0x100000 0x400000

    setenv mainlineLinux yes
    setenv arcNumber 2097
    saveenv
    reset

**Step 2** Boot from the usb stick::

    setenv bootargs_console console=ttyS0,115200
    setenv bootcmd_usb 'usb start; ext2load usb 0:1 0x00800000 /uImage; ext2load usb 0:1 0x01100000 /uInitrd'
    setenv bootcmd 'setenv bootargs $(bootargs_console); run bootcmd_usb; bootm 0x00800000 0x01100000'
    saveenv
    run bootcmd

**Step 3** Clone USB rootfs to internal flash

When we make the copy, we don't want mounted filesystems (dev, tmpfs and so on), though do want anything under any mount points (e.g. /dev/console is always handy), so bind-mount / somewhere, and copy that.

::

    mkdir /tmp/rootfs
    mount -o bind / /tmp/rootfs/
    cd /tmp/rootfs
    sync
    cp -av . /mnt/

Fix the fstab in /mnt/etc/fstab::

    /dev/root  /               ubifs   defaults,noatime,rw                      0 0
    tmpfs      /var/run        tmpfs   size=1M,rw,nosuid,mode=0755              0 0
    tmpfs      /var/lock       tmpfs   size=1M,rw,noexec,nosuid,nodev,mode=1777 0 0
    tmpfs      /tmp            tmpfs   defaults,nosuid,nodev                    0 0

After that, reboot and re-enter the uBoot console. Also, detach the USB stick to make sure we are booting from flash.

**Step 4** Restore boot process from internal flash

To boot from the NAND again after copying the rootfs to NAND, I used these commands at the uBoot prompt::

    setenv x_bootcmd_usb 'usb start'
    setenv x_bootcmd_kernel 'nand read.e 0x6400000 0x100000 0x400000'
    setenv x_bootargs_root 'ubi.mtd=2 root=ubi0:rootfs rootfstype=ubifs'
    setenv x_bootargs 'console=ttyS0,115200'
    setenv x_bootcmd '$(x_bootcmd_usb); $(x_bootcmd_kernel); setenv bootargs $(x_bootargs) $(x_bootargs_root) ;bootm 0x6400000;'
    setenv bootcmd 'run x_bootcmd'
    saveenv
    reset


Kernel upgrade
==============

I recently had an issue during a kernel upgrade (3.2.0-2-kirkwood): dpkg was getting stuck after this::

    # dpkg --configure -a
    Setting up linux-image-3.2.0-2-kirkwood (3.2.15-1) ...
    Running depmod.
    Examining /etc/kernel/postinst.d.
    run-parts: executing /etc/kernel/postinst.d/initramfs-tools 3.2.0-2-kirkwood /boot/vmlinuz-3.2.0-2-kirkwood
    update-initramfs: Generating /boot/initrd.img-3.2.0-2-kirkwood
    Warning: root device /dev/root does not exist

    Press Ctrl-C to abort build, or Enter to continue

I pressed Enter but nothing happened for a quite long time.

The process that got stuck is::

    /usr/sbin/mkinitramfs -v -o /boot/initrd.img-3.2.0-2-kirkwood.new 3.2.0-2-kirkwood

but was running just fine when launched manually. To investigate further I tried this::

    mv /usr/sbin/mkinitramfs /usr/sbin/mkinitramfs_orig
    echo -e '#!/bin/sh\n/usr/sbin/mkinitramfs_orig -v "$@"' > /usr/sbin/mkinitramfs
    chmod +x /usr/sbin/mkinitramfs
    dpkg --configure -a

but still, the process was getting stuck for no apparent reason.

At last, I solved by fixing the "/dev/root does not exist" thing using this::

    ln -s /dev/ubi0_0  /dev/root

After that, the upgrade just went fine::

    # dpkg --configure -a
    Setting up linux-image-3.2.0-2-kirkwood (3.2.15-1) ...
    Running depmod.
    Examining /etc/kernel/postinst.d.
    run-parts: executing /etc/kernel/postinst.d/initramfs-tools 3.2.0-2-kirkwood /boot/vmlinuz-3.2.0-2-kirkwood
    update-initramfs: Generating /boot/initrd.img-3.2.0-2-kirkwood

    ... verbose output from mkinitramfs ...

    Building cpio /boot/initrd.img-3.2.0-2-kirkwood.new initramfs
    flash-kernel: deferring update (trigger activated)
    run-parts: executing /etc/kernel/postinst.d/zz-flash-kernel 3.2.0-2-kirkwood /boot/vmlinuz-3.2.0-2-kirkwood
    flash-kernel: deferring update (trigger activated)
    Processing triggers for flash-kernel ...
    flash-kernel: installing version 3.2.0-2-kirkwood
    Generating kernel u-boot image... done.
    Taking backup of uImage.
    Installing new uImage.
    Generating initramfs u-boot image... done.
    Taking backup of uInitrd.
    Installing new uInitrd.


.. _official GuruPlug wiki page: http://plugcomputer.org/plugwiki/index.php/GuruPlug
