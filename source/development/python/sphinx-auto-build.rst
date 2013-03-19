Sphinx: automatically build documentation
#########################################

This is the small bash script I use to automatically rebuild the Sphinx
documentation upon change detected from inotify filesystem watch.

.. code-block:: bash

    #!/bin/bash

    cd "$( dirname "$0" )"

    while :; do
        inotifywait -r -e modify,close_write,moved_to,moved_from,move,create,delete ../
        make html
    done

**Update:** A more generic and improved version can be found here: https://github.com/rshk/CommonScripts/blob/master/Linux/Generic/autobuild.sh

.. note::
    We need a way to prevent Gnome3 from stacking up the notifications,
    or the notifications pane will become full quickly!

    How can we make the notifications only be shown on screen and
    disappear after they expire / are dismissed?

