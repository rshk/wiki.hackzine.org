###################################
Dart: development environment setup
###################################

As of 2012-05-28, there's very little support for running Darts editor on 64-bit Linux (and in general on non-ubuntu linux).

In order to run the modified version of chromium shipped with the editor, I had to do a little hack.

Luckily, I already have a 32-bit squeeze chroot on my system (using schroot),
but nevertheless I wasn't able to run the whole IDE in the chroot; instead I
had to substitute the actual ``chrome`` executable with a wrapper script that
runs it through the chroot.

First of all, download the `DartEditor <http://www.dartlang.org/downloads.html>`_
from `<http://www.dartlang.org/downloads.html>`_.

Then, unzip the package and go in the ``dart-sdk/chromium`` directory.

Rename ``chrome`` to ``the-actual-chrome``, then create a script named ``chrome`` with the following content:


.. code-block:: bash

    #!/bin/bash
    schroot -c squeeze32 -- env DISPLAY=:0 $( dirname "$0" )/the-actual-chrome "$@"

.. warning:: For this to work, you'll need a working Debian 32bit chroot, named squeeze32.
