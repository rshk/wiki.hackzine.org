KVM: Configure libvirt network
##############################

List available networks::

    % virsh net-list
    Name                 State      Autostart
    -----------------------------------------
    InternalNetwork      active     yes
    NattedNetwork        active     yes


Edit the selected network::

    % EDITOR="emacs -nw" virsh net-edit InternalNetwork


Add host configuration to XML file:

.. code-block:: xml

    <network>
      <name>InternalNetwork</name>
      <uuid>aaaabbbb-cccc-dddd-eeee-ffff00001111</uuid>
      <bridge name='virbr1' stp='on' delay='0' />
      <mac address='52:54:00:00:11:22'/>
      <ip address='10.50.1.1' netmask='255.255.255.0'>
        <dhcp>
          <range start='10.50.1.10' end='10.50.1.254' />

          <host mac="52:54:00:20:00:00" ip="10.50.2.10"  name="vm-0.local" />
          <host mac="52:54:00:20:00:01" ip="10.50.2.11"  name="vm-1.local" />
          <host mac="52:54:00:20:00:02" ip="10.50.2.12"  name="vm-2.local" />
          <host mac="52:54:00:20:00:03" ip="10.50.2.13"  name="vm-3.local" />
          <host mac="52:54:00:20:00:04" ip="10.50.2.14"  name="vm-4.local" />

        </dhcp>
      </ip>
    </network>

**Restart network** for changes to take effect::

    virsh net-destroy  InternalNetwork
    virsh net-start InternalNetwork

.. note::
    If you run net-edit again before restarting network, you'll see
    the old configuration as it was before changes..


Troubleshooting: changes not taking effect
==========================================

I tried this on Debian wheezy 7.4, libvirt 0.9.12.3-1 and changes seem not
to have effect.

Launching net-edit a second time shows the unchanged file as it was before
the changes.

Further investigation revealed that there are in fact **two** places in which
network configuration is stored:

- ``/var/lib/libvirt/network/mynetwork.xml``
  This is the actual configuration file used by libvirt to configure dnsmasq

- ``/etc/libvirt/qemu/networks/mynetwork.xml``
  This is the file in which net-edit writes configuration

**Workaround:**

Just symlink the folders::

    rm -rf /etc/libvirt/qemu/networks
    ln -s /var/lib/libvirt/network /etc/libvirt/qemu/networks

Now everything should work fine.

.. warning::

   You cannot symlink just the files, as they get both overwritten
   each time configuration is saved by ``net-edit``..
