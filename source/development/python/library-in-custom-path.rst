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
    python setup.py build_ext \
        -I "$VIRTUAL_ENV"/include/ \
        -L "$VIRTUAL_ENV"/lib/ \
        -R "$VIRTUAL_ENV"/lib/ \
        install

The trick here is to use ``-I`` to tell te compiler where to look for
header files, ``-L`` to tell the linker where to find libraries
and ``-R`` to tell the linker where to search for libraries at runtime::

    --include-dirs (-I)  list of directories to search for header files
                         (separated by ':')
    --library-dirs (-L)  directories to search for external C libraries
                         (separated by ':')
    --rpath (-R)         directories to search for shared C libraries at runtime

(from ``python setup.py build_ext --help``)


Example: kyotocabinet
=====================

As a real-world use-case example, I used this method to install `Kyoto Cabinet`_
inside a virtualenv.

.. _Kyoto Cabinet: http://fallabs.com/kyotocabinet/

This is the complete procedure I followed:

.. code-block:: bash

    mkdir -p "$VIRTUAL_ENV"/tmp

    cd "$VIRTUAL_ENV"/tmp
    wget http://fallabs.com/kyotocabinet/pkg/kyotocabinet-1.2.76.tar.gz
    tar xzvf kyotocabinet-1.2.76.tar.gz
    cd kyotocabinet-1.2.76/
    ./configure --prefix="$VIRTUAL_ENV"
    make
    make install

    cd "$VIRTUAL_ENV"/tmp
    wget http://fallabs.com/kyotocabinet/pythonlegacypkg/kyotocabinet-python-legacy-1.18.tar.gz
    tar xzvf kyotocabinet-python-legacy-1.18.tar.gz
    cd kyotocabinet-python-legacy-1.18/
    python setup.py build_ext \
        -I "$VIRTUAL_ENV"/include/ -L "$VIRTUAL_ENV"/lib/ \
        -R "$VIRTUAL_ENV"/lib/ install

**todo:** put all this in the setup.py for the extension module, as eg. pyzmq does
