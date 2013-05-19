Python: build extension module against library in non-standard path
###################################################################

I just installed a library (with a ``.so`` and a ``.h``) in a custom ``$PREFIX``
(eg, ``$HOME/.local`` or ``$VIRTUAL_ENV``).

Use this to pass custom ``-I`` and ``-L`` flags to the compiler::

    python setup.py build_ext -I $PREFIX/include -L $PREFIX/lib install

Using virtualenv
================

If you're using virtualenv (you should!), you can do the whole build
of the library + python bindings with something like this:

.. code-block:: bash

    cd /path/to/library-dir/
    ./configure --prefix="$VIRTUAL_ENV"
    make && make install

    cd /path/to/python-module-dir/
    python setup.py build_ext -I "$VIRTUAL_ENV"/include/ -L "$VIRTUAL_ENV"/lib/ install
