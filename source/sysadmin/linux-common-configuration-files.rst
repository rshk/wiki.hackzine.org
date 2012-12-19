Linux: Common configuration files
####

.. note::
    Code snippets in this page have now been aggregated and can be found
    here: https://github.com/rshk/CommonScripts

Cool colored prompt
====

.. code-block:: bash

    PS1='\[\e[1;31m\]${debian_chroot:+($debian_chroot)}\[\e[1;36m\]\u\[\e[1;34m\]@\h\[\e[0m\] : \[\e[1;32m\]\w\n\[\e[1;31m\]\$\[\e[0m\] '
    PS1='\[\e[1;31m\]${debian_chroot:+($debian_chroot)}\[\e[1;42;33m\] \u\[\e[1;44;36m\]@\h \[\e[0m\] \[\e[1;32m\]\w\n\[\e[1;31m\]\$\[\e[0m\] '

Prompt additions
====

To show information on the current chroot / add prefix if working via ssh:

.. code-block:: bash

    PS1="${debian_chroot:+\[\e[41m\] (${debian_chroot}) \[\e[0m\]}${PS1}"
    PS1="${SCHROOT_SESSION_ID:+\[\e[41m\](${SCHROOT_SESSION_ID}) \[\e[0m\]}${PS1}"
    PS1="${SSH_CONNECTION:+\[\e[44m\] (ssh) \[\e[0m\]}${PS1}"


Useful aliases
====

**TODO:** Move this thing to git.

.. code-block:: bash
    :linenos:

    ############################################################
    ## Bash aliases
    ############################################################

    export LS_OPTIONS='--color=auto --time-style=long-iso'
    eval "`dircolors`"
    alias ls='ls $LS_OPTIONS'
    alias ll='ls $LS_OPTIONS -l'
    alias la='ls $LS_OPTIONS -A'
    alias l='ls $LS_OPTIONS -lA'
    alias grep='grep --color=auto'

    ##------------------------------------------------------------
    ## Correction alias for mispelled commands
    ## Prints a warning, then runs the actual command.
    ##------------------------------------------------------------
    correction_alias() {
        alias $1="echo 'Did you mean $2?' >&2;$2"
    }

    correction_alias sl ls
    correction_alias mdkir mkdir

    ##------------------------------------------------------------
    ## Other utility functions
    ##------------------------------------------------------------
    alias md='mkdir'
    function mdcd() { mkdir "$1" && cd "$1"; }
    alias tmp='cd $( mktemp -d )'
    function urlextract(){
      grep -o "http://[^ '\"<>]*"
    }
    function decolorize(){
      sed "s/$( echo -e "\033" )\[[0-9;]*m//g" "$@"
    }
    alias whatismyip='wget -q -O - http://tnld.org/tools/ip.php ; echo'
    alias 1337='tr "etioaszb" "37104528"'
    alias emx='emacs -nw'

    ##------------------------------------------------------------
    ## FAKE_FIREFOX WGET
    ##------------------------------------------------------------
    alias ffwg='wget -U "Mozilla/5.0 (X11; U; Linux x86_64; it-IT; rv:1.9.0.10) Gecko/2009050604 Gentoo Firefox/3.0.10"'

    ##------------------------------------------------------------
    ## Highlight source code
    ##------------------------------------------------------------
    function hl() {
        pygmentize "$@" | less -RN
    }
    #alias hl='source-highlight -fesc -o STDOUT'

    ##------------------------------------------------------------
    ## System utilities
    ##------------------------------------------------------------
    alias ssh_show_known_hosts='ssh-keygen -l -f ~/.ssh/known_hosts'
    alias udevenv="udevadm info --query=env --name"
    alias ls='ls --color=auto --time-style=long-iso'
    function cleancfg() {
      if [ -n "$1" ]; then
         cat "$1"
      else
         cat
      fi |  grep -v '^#\|^\s*$'
    }

