Linux: Resizing a LUKS volume on LVM
####################################

I have the following partitioning layout::

                    +---------------+
                    |     ext4      |
    +---------------+---------------+
    |     ext4      |     LUKS      |
    +---------------+---------------+
    |     LV-0      |     LV-1      |
    +---------------+---------------+----- ... --+
    |             VG-0                           |
    +------------------------------------- ... --+
    |             PV-0                           |
    +------------------------------------- ... --+


Now, I need to extend the two ext4 filesystems, to get
some more space.


Resizing ext4 on LVM
====================

The first one is easy:

.. code-block:: console

    # lvextend -L +50G -r /dev/VG-0/LV-0

Will add 50GB to the volume and resize the contained
filesystem. It works even when the filesystem is mounted.


Resizing ext4 on LUKS on LVM
============================

The next one is trickier, as LVM doesn't automatically
support resizing.

**Step 1:** resize the logical volume:

.. code-block:: console

    # lvextend -L +50G /dev/VG-0/LV-1


**Step 2:** open the LUKS volume:

.. code-block:: console

    # cryptsetup luksOpen /dev/VG-0/LV-1 crypt_LV-1


**Step 3:** resize the inner filesytem (extend to fit space):

.. code-block:: console

    # e2fsck -f /dev/mapper/crypt_LV-1
    # resize2fs /dev/mapper/crypt_LV-1


(the ``e2fsck`` is a good practice, and it is enforced
by ``resize2fs`` anyways..)
