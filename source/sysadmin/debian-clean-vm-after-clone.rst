Debian: VM cleanup after cloning
################################

After you clone a virtual machine, it will still contain some garbage
from the previous installation that we want to clean up.

To do so, I usually run the following commands. These are not only
debian-specific, although some modifications might be necessary for other
distributions.

Remove "persistent naming" udev rules
=====================================

These are created to uiniquely associate device names, by serial number or so,
and are responsible for things like your first vm network card being named
``eth1`` etc. So, just remove them::

    rm /etc/udev/rules.d/70-persistent-*.rules

Clean up the bash history
=========================

Usually, only the history for root will be "dirty" (although you can
avoid writing it in the template by setting ``HISTFILE=`` in the terminal
you use for administration tasks).

To clean up the bash history for all the users in the system, you can use this::

    cat /etc/passwd | cut -d: -f6 | sed 's@$@/.bash_history@' | xargs -d '\n' rm -fv


Regenerate SSH keys
===================

To rebuild the SSH keys of this machine, in order to avoid having the same
key used amongst many machines::

    rm -fv /etc/ssh/ssh_host_*
    dpkg-reconfigure openssh-server
