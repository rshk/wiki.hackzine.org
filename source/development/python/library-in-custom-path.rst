Python: build extension module against library in non-standard path
###################################################################

I just installed a library (with a ``.so`` and a ``.h``) in a custom ``$PREFIX``
(eg, ``$HOME/.local`` or ``$VIRTUAL_ENV``).

Use this to pass custom ``-I`` and ``-L`` flags to the compiler::

    python setup.py build_ext -I $PREFIX/include -L $PREFIX/lib install --user
