Linux: Specifying handler for file execution
############################################

The ``suexec`` binary usually only handles executables or scripts specifying
their interpreter using a hash-bang line.

Such a line is usually not specified in PHP scripts. Fortunately, on Linux,
you can specify the interpreter using ``/proc/sys/fs/binfmt_misc``::

    echo ':PHP:E::php::/usr/bin/php-cgi:' > /proc/sys/fs/binfmt_misc/register
