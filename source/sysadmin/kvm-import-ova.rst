KVM: Importing an OVA appliance
####


First of all, the ``.ova`` format is nothing more than a ``tar`` archive,
containing an ``.ovf`` and a ``.vmdk`` files, respectively the VM
configuration and disk.

So, you can simply extract the files::

    $ tar tvf MyAppliance.ova

And convert to a format appropriate for qemu/kvm::

    # List the available formats
    $ qemu-img -h |tail -n1
    Supported formats: vvfat vpc vmdk vdi sheepdog raw host_cdrom host_floppy
    host_device file qed qcow2 qcow parallels nbd dmg tftp ftps ftp https http
    cow cloop bochs blkverify blkdebug

    # Do the actual conversion (I chose qcow2 here)
    $ qemu-img convert -O qcow2 MyAppliance-disk1.vmdk MyAppliance.qcow2

Have a look at the ``.ovf`` too, for information on expected machine
configuration / resources (eg. memory and cpu).

After the conversion, simply create a new VM and make it use the newly
created disk as the primary harddisk.


References
==========

* http://edoceo.com/notabene/ova-to-vmdk-to-qcow2
