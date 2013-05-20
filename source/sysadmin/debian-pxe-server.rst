Debian: installing a PXE server on Wheezy
#########################################

Installing tftp server
======================

First, install the TFTP server::

    apt-get install tftpd-hpa

Default configuration should be OK::

    # cat /etc/default/tftpd-hpa
    # /etc/default/tftpd-hpa

    TFTP_USERNAME="tftp"
    TFTP_DIRECTORY="/srv/tftp"
    TFTP_ADDRESS="0.0.0.0:69"
    TFTP_OPTIONS="--secure"


Configuring the DHCP server
===========================

This is the configuration I'm using on my DHCP server.

.. note::

    You'll have to use a decent dhcp server, like a linux box or a cisco,
    those plastic things you buy at the supermarket usually don't fit, sorry.

::

    allow booting;
    allow bootp;


    ## Configuration for the lan
    subnet 192.168.0.0 netmask 255.255.0.0 {
      option routers 192.168.1.1;
      filename "pxelinux.0";
      option broadcast-address 192.168.255.255;
      next-server 192.168.1.2;

      # Unknown clients get this pool.
      pool {
        max-lease-time 600;
        range dynamic-bootp 192.168.20.20 192.168.20.253;
        allow unknown-clients;
      }

      # Known clients get this pool.
      pool {
        max-lease-time 28800;
        range dynamic-bootp 192.168.1.1 192.168.10.254;
        deny unknown-clients;
      }
    }

In short, you have to add the ``allow booting`` and ``allow bootp``
directives, specify the ``filename`` in the pool and add ``dynamic-bootp``
to your ``range`` directives in the pools (or in the subnet if you specify
it there).


Adding netboot images for installers
====================================

For example, the debian wheezy installer for amd64 is a good fit for this:

    wget http://ftp.debian.org/debian/debian/dists/wheezy/main/installer-amd64/current/images/netboot/netboot.tar.gz

To get it up & running, simply extract the tar in the ``/srv/tftp`` folder.

If you list the contents of the directory later, you'll see you now have:

    version.info
    pxelinux.cfg -> debian-installer/amd64/pxelinux.cfg
    pxelinux.0 -> debian-installer/amd64/pxelinux.0
    debian-installer/

``pxelinux.0`` is the executable that will be launched first (it's the one
referenced by the DHCP configuration entry..)

``pxelinux.cfg`` is a directory containing configuration files

``debian-installer/amd64`` contains some other boot screens, the actual
``pxelinux.0`` and ``pxelinux.cfg``, along with a kernel and initrd images.

If you're ok with only one image, that automatically gets started after
boot from PXE, you're done here. If not, keep reading.


Booting multiple images from the same PXE server
================================================

Since you'll very likely need multiple installation images (eg. for
different architectures, distros, ...), we're now going to create
some configuration suitable for allowing you to choose amongst a collection
of images to boot from.

So, start by removing the symlink to ``pxelinux.cfg``, containing
the configuration used to boot debian, and replace it with a directory.

    cd /srv/tftp
    rm pxelinux.cfg
    mkdir pxelinux.cfg

Also, copy ``debian-installer/amd64/boot-screens/vesamenu.c32`` to
the ``/srv/tftp`` folder, as we will need it later::

    cp debian-installer/amd64/boot-screens/vesamenu.c32 .

You can either keep the symlink to ``pxelinux.0`` or copy it over,
it doesn't matter.

Then, create the following files:

pxelinux.cfg/default::

    default vesamenu.c32
    prompt 0
    timeout 0

    menu title PXE Boot Menu
    menu include pxelinux.cfg/graphics.conf

    label Install Debian Wheezy amd64
          menu label ^Debian Wheezy amd64
          kernel vesamenu.c32
          append debian-installer/amd64/boot-screens/menu.cfg


pxelinux.cfg/graphics.conf::

    menu background     #ff000000
    menu color background   0   * #ff000000 *
    menu color title    1;33    #ffffff00 * *
    menu color border   32  #ff008800 * *
    menu color help     37;40   #ffdddd00 #00000000 none

    menu color tabmsg   37;40   #80ffffff #00000000 *
    menu color hotsel   30;47   #40000000 #20ffffff *
    menu color sel      30;47   #40000000 #20ffffff *
    menu color scrollbar    30;47   #40000000 #20ffffff *

    menu width 80
    menu margin 10
    menu rows 15
    menu tabmsgrow 18
    menu cmdlinerow 18
    menu endrow 24
    menu passwordrow 11
    menu timeoutrow 20
    menu vshift 6

    menu master passwd yourpassword
    menu passprompt enter password:
    menu passwordmargin 26
    menu passwordrow 12

    noescape 1
    allowoptions 0

While not strictly necessary, the latter will provide some nice theming
of the menu. Yo can specify a bunch of customizations, have a look at
http://www.syslinux.org/wiki/index.php/Comboot/menu.c32.

If everything went fine, booting from the PXE will now show you
a menu which allows you choose the (one!) image from which to boot.

Now, in order to add, eg, the image of the installer for wheezy i386,
simply extract the ``debian-installer`` directory and add an entry to
the boot menu:

    cd /srv/tftp
    wget http://ftp.debian.org/debian/dists/wheezy/main/installer-i386/current/images/netboot/netboot.tar.gz -O wheezy-i386.tar.gz
    tar xzvf wheezy-i386.tar.gz ./debian-installer/

add to ``pxelinux.cfg/default``::

    label Install Debian Wheezy i386
          menu label ^Debian Wheezy i386
          kernel vesamenu.c32
          append debian-installer/i386/boot-screens/menu.cfg

.. warning::
    Do not extract the whole archive, or you'll risk overriding
    other things, such as pxelinux.0 and pxelinux.cfg!


Adding images for squeeze
-------------------------

If you want the squeeze (old stable) installer too, you'll have to
customize things further, as the contents of both installers are named
the same.

For example::

    cd /tmp
    wget http://ftp.debian.org/debian/dists/squeeze/main/installer-amd64/current/images/netboot/netboot.tar.gz -O squeeze-amd64.tar.gz
    tar xzvf squeeze-amd64.tar.gz ./debian-installer/
    find debian-installer/ -type f -print0 | xargs -0 sed "s,debian-installer/,debian-installer/squeeze/," -i
    mkdir -p /srv/tftp/debian-installer/squeeze/
    mv debian-installer/amd64 /srv/tftp/debian-installer/squeeze/

add to ``pxelinux.cfg/default``::

    label Install Debian Squeeze amd64
          menu label ^Debian Squeeze amd64
          kernel vesamenu.c32
          append debian-installer/squeeze/amd64/boot-screens/menu.cfg


Adding ubuntu images
--------------------

For example, we want to add the ubuntu images for the LTS (12.04 precise)
and latest (13.04 raring) versions, both for i386 and amd64:

.. code-block:: bash

    TMPDIR="$( mktemp -d )"
    cd "$TMPDIR"

    wget http://archive.ubuntu.com/ubuntu/dists/precise-updates/main/installer-i386/current/images/netboot/netboot.tar.gz -O ubuntu-precise-i386.tar.gz
    wget http://archive.ubuntu.com/ubuntu/dists/precise-updates/main/installer-amd64/current/images/netboot/netboot.tar.gz -O ubuntu-precise-amd64.tar.gz
    wget http://archive.ubuntu.com/ubuntu/dists/raring/main/installer-i386/current/images/netboot/netboot.tar.gz -O ubuntu-raring-i386.tar.gz
    wget http://archive.ubuntu.com/ubuntu/dists/raring/main/installer-amd64/current/images/netboot/netboot.tar.gz -O ubuntu-raring-amd64.tar.gz

    mkdir _ubuntu-installer

    for DIST in precise raring; do
        mkdir -p "./_ubuntu-installer/${DIST}"
        for ARCH in i386 amd64; do
            tar xzvf "ubuntu-${DIST}-${ARCH}".tar.gz ./ubuntu-installer/
            find ./ubuntu-installer/ -type f -print0 | \
                xargs -0 sed "s,ubuntu-installer/,ubuntu-installer/${DIST}/," -i
            mv "./ubuntu-installer/${ARCH}/" "./_ubuntu-installer/${DIST}"
            rm -rfv ./ubuntu-installer/
        done
    done

    mkdir -p /srv/tftp/ubuntu-installer/
    mv -t /srv/tftp/ubuntu-installer/ ./_ubuntu-installer/*

    cd -
    rm -r "$TMPDIR"


Create a new menu for the Ubuntu installers,
eg. ``pxelinux.cfg/ubuntu-install.cfg``:

.. code-block:: bash

    cat > /srv/tftp/pxelinux.cfg/ubuntu-install.cfg << EOF
    default vesamenu.c32
    prompt 0
    timeout 0
    menu title Ubuntu Installers
    menu include pxelinux.cfg/graphics.conf

    EOF

    for DIST in precise raring; do
        for ARCH in i386 amd64; do
            echo "label Install Ubuntu ${DIST} ${ARCH}"
            echo "      menu label ^Ubuntu ${DIST} ${ARCH}"
            echo "      kernel vesamenu.c32"
            echo "      append ubuntu-installer/${DIST}/${ARCH}/boot-screens/menu.cfg"
            echo
        done
    done >> /srv/tftp/pxelinux.cfg/ubuntu-install.cfg


and link it from ``pxelinux.cfg/default``::

    label ubuntu_install
          menu label ^Ubuntu Installers
          kernel vesamenu.c32
          append pxelinux.cfg/ubuntu-install.cfg


**todo** write some helper application to install images to appropriate places.
It would be nice to have something, working at least for netboot images
of  the major distributions, plus some common live.


Booting a live distribution
===========================

**todo:** Debian live (build+install using ``live-build``)

**todo:** Generic ISO image (where to place kernel+initrd and rootfs)

Creating custom netboot images
==============================

**todo** write this

Custom live images
------------------

**todo** write this (mount root via nfs, use aufs, etc.)
