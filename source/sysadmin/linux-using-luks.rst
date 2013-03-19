Linux: Basic LUKS usage
#######################

**See also:** http://www.hackzine.org/minimal-luks-guide.html

These are just the basic commands I usually use to create encrypted partitions
using LUKS.

The commands
============

::

    fdisk /dev/sdx
    cryptsetup -y --cipher aes-cbc-essiv:sha256 --key-size 256 luksFormat /dev/sdx1
    cryptsetup luksOpen /dev/sdx1 crypt_sdx1
    mkfs.ext3 -m 0 -j -O dir_index,filetype,sparse_super /dev/mapper/crypt_sdx1
    tune2fs -m 0 -c 0 -i 0 -L label /dev/mapper/crypt_sdx1
    mount /dev/mapper/crypt_sdx1 /mnt/crypt_sdx1
    umount /dev/mapper/crypt_sdx1
    cryptsetup luksClose crypt_sdx1


Commands explained
==================

This part includes just some output of the commands and some comments,
not really more. But I promise I will write a full guide one day..
I wrote this just as a reminder.

First of all, setup the partitioning using fdisk::

    # fdisk /dev/sdx
    Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
    Building a new DOS disklabel with disk identifier 0x87cd9031.
    Changes will remain in memory only, until you decide to write them.
    After that, of course, the previous content won't be recoverable.


    The number of cylinders for this disk is set to 182401.
    There is nothing wrong with that, but this is larger than 1024,
    and could in certain setups cause problems with:
    1) software that runs at boot time (e.g., old versions of LILO)
    2) booting and partitioning software from other OSs
       (e.g., DOS FDISK, OS/2 FDISK)
    Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

    Command (m for help): p

    Disk /dev/sdx: 1500.3 GB, 1500301910016 bytes
    255 heads, 63 sectors/track, 182401 cylinders
    Units = cylinders of 16065 * 512 = 8225280 bytes
    Disk identifier: 0x87cd9031

       Device Boot      Start         End      Blocks   Id  System

    Command (m for help): o
    Building a new DOS disklabel with disk identifier 0x84732589.
    Changes will remain in memory only, until you decide to write them.
    After that, of course, the previous content won't be recoverable.


    The number of cylinders for this disk is set to 182401.
    There is nothing wrong with that, but this is larger than 1024,
    and could in certain setups cause problems with:
    1) software that runs at boot time (e.g., old versions of LILO)
    2) booting and partitioning software from other OSs
       (e.g., DOS FDISK, OS/2 FDISK)
    Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

    Command (m for help): n
    Command action
       e   extended
       p   primary partition (1-4)
    p
    Partition number (1-4): 1
    First cylinder (1-182401, default 1): 1
    Last cylinder, +cylinders or +size{K,M,G} (1-182401, default 182401): 182401

    Command (m for help): p

    Disk /dev/sdx: 1500.3 GB, 1500301910016 bytes
    255 heads, 63 sectors/track, 182401 cylinders
    Units = cylinders of 16065 * 512 = 8225280 bytes
    Disk identifier: 0x84732589

       Device Boot      Start         End      Blocks   Id  System
    /dev/sdx1               1      182401  1465136001   83  Linux

    Command (m for help): w
    The partition table has been altered!

    Calling ioctl() to re-read partition table.
    Syncing disks.

Once the partitioning is done, we proceed setting up the encryption for the
selected partition::

    # cryptsetup -y --cipher aes-cbc-essiv:sha256 --key-size 256 luksFormat /dev/sdx1
    WARNING!
    ========
    This will overwrite data on /dev/sdx1 irrevocably.

    Are you sure? (Type uppercase yes): YES
    Enter LUKS passphrase:
    Verify passphrase:
    Command successful.

Decrypt the partition to use it as a normal device::

    # cryptsetup luksOpen /dev/sdx1 crypt_sdx1
    Enter LUKS passphrase:
    key slot 0 unlocked.
    Command successful.

Create an ext3 filesystem on the partition::

    # mkfs.ext3 -m 0 -j -O dir_index,filetype,sparse_super /dev/mapper/crypt_sdx1
    mke2fs 1.41.9 (22-Aug-2009)
    Filesystem label=
    OS type: Linux
    Block size=4096 (log=2)
    Fragment size=4096 (log=2)
    91578368 inodes, 366283743 blocks
    0 blocks (0.00%) reserved for the super user
    First data block=0
    Maximum filesystem blocks=4294967296
    11179 block groups
    32768 blocks per group, 32768 fragments per group
    8192 inodes per group
    Superblock backups stored on blocks:
        32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208,
        4096000, 7962624, 11239424, 20480000, 23887872, 71663616, 78675968,
        102400000, 214990848

    Writing inode tables: done
    Creating journal (32768 blocks): done
    Writing superblocks and filesystem accounting information: done

    This filesystem will be automatically checked every 20 mounts or
    180 days, whichever comes first.  Use tune2fs -c or -i to override.

I usually change some options on the filesystem, such as disable automatick
fsck and remove reserved blocks for non-root filesystems::

    # tune2fs -m 0 -c 0 -i 0 -L label /dev/mapper/crypt_sdx1
    tune2fs 1.41.9 (22-Aug-2009)
    Setting maximal mount count to -1
    Setting interval between checks to 0 seconds
    Setting reserved blocks percentage to 0% (0 blocks)

Mount the filesystem somewhere::

    # mount /dev/mapper/crypt_sdXN /mnt/crypt_sdx1

To umount and close it::

    # umount /dev/mapper/crypt_sdx1
    # cryptsetup luksClose crypt_sdx1
