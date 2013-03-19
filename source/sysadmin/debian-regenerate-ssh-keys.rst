Debian: regenerate SSH keys
###########################

To regenerate the OpenSSH server keys on a debian system (especially
useful after cloning a VM template), just remove them and
reconfigure the package::

	# rm /etc/ssh/ssh_host_*
	# dpkg-reconfigure openssh-server

Then, of course, remember to delete the old key from the ``known_hosts``
of any client that already connected to this machine before key regeneration.
