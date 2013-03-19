Linux: Common configuration files
#################################

.. note::
    Code snippets in this page have now been aggregated and can be found
    here: https://github.com/rshk/CommonScripts

Cool colored prompt
===================

.. code-block:: bash

    PS1='\[\e[1;31m\]${debian_chroot:+($debian_chroot)}\[\e[1;36m\]\u\[\e[1;34m\]@\h\[\e[0m\] : \[\e[1;32m\]\w\n\[\e[1;31m\]\$\[\e[0m\] '
    PS1='\[\e[1;31m\]${debian_chroot:+($debian_chroot)}\[\e[1;42;33m\] \u\[\e[1;44;36m\]@\h \[\e[0m\] \[\e[1;32m\]\w\n\[\e[1;31m\]\$\[\e[0m\] '

Prompt additions
================

To show information on the current chroot / add prefix if working via ssh:

.. code-block:: bash

    PS1="${debian_chroot:+\[\e[41m\] (${debian_chroot}) \[\e[0m\]}${PS1}"
    PS1="${SCHROOT_SESSION_ID:+\[\e[41m\](${SCHROOT_SESSION_ID}) \[\e[0m\]}${PS1}"
    PS1="${SSH_CONNECTION:+\[\e[44m\] (ssh) \[\e[0m\]}${PS1}"

.. note::
    Here it is my even-nicer brand-new bash prompt!
    https://github.com/rshk/CommonScripts/blob/master/Configs/bash/bash_prompt


Useful aliases
==============

Have a look at the up-to-date aliases file here:
https://github.com/rshk/CommonScripts/blob/master/Configs/bash/bash_aliases
