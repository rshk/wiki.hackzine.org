Debian: registering an apt repository key
#########################################

**See also:** http://www.hackzine.org/register-apt-repository-key.html

Here is an useful script to register APT keys, to avoid the "There are no
public key available for the following key IDs" warning message.

.. code-block:: bash

    #!/bin/bash

    # samu 2010-01-01
    # (c) 2010 Samuele ~redShadow~ Santi
    # redshadow&hackzine.org - http://www.hackzine.org
    # Under Gnu GPL v3

    KEYID="$1"

    echo "Registering APT key $1..."
    gpg --keyserver pgp.mit.edu --recv-keys "$KEYID"
    gpg --export --armor "$KEYID" | apt-key add -

Alternative: debian-archive-keyring
===================================

From: http://www.debian-administration.org/users/dkg/weblog/11

::

    apt-get install debian-archive-keyring

or, if you have unstable repositories::

    apt-get install debian-archive-keyring/unstable

apt-key update
==============

Another method, is just to run::

    apt-key update

"This will obtain the necessary keys and import them. No need to go through gpg
directly".

Anyways, this method will not always work.


