Python: virtualenv hacks
########################

virtualenvwrapper - custom color prompt prefix
==============================================

When using virtualenvwrapper, to get custom color prompt prefix:

File: ``~/.virtualenvs/postactivate``:

.. code-block:: bash

    PS1="\[\e[1;33;45m\] (`basename \"$VIRTUAL_ENV\"`) \[\e[0m\]$_OLD_VIRTUAL_PS1"

To disable virtualenv prompt change (useful when using a custom prompt
that already handles display of virtualenv information)::

    VIRTUAL_ENV_DISABLE_PROMPT=1


Install Node.js packages in virtualenv
======================================

File: ``~/.virtualenvs/postactivate``:

.. code-block:: bash

    ## node.js packages
    export npm_config_prefix=$VIRTUAL_ENV


Install ruby gems in virtualenv
===============================

File: ``~/.virtualenvs/postactivate``:

.. code-block:: bash

    ## ruby gems
    export GEM_HOME=$VIRTUAL_ENV
    export GEM_PATH=""
