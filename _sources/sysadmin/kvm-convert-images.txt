KVM: Convert disk images
########################

Export image from virtualbox::

  VBoxManage clonehd my-disk.vdi my-disk.img --format raw

Convert raw image to qcow2::

  qemu-img convert -f raw my-disk.img -O qcow2 my-disk.qcow2
