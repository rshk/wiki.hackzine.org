Python: scale conversion
########################

Let's suppose we have two  different scales, one ranging from ``A`` to ``B``
and the other from ``C`` to ``D``.

Now, we have a value ``X`` in the range ``A <= X <= B`` and we want to
convert it into the other scale.

Here it is a function to do that.

.. code-block:: python

    def rescale(X,A,B,C,D,force_float=False):
        retval = ((float(X - A) / (B - A)) * (D - C)) + C
        if not force_float and all(map(lambda x: type(x) == int, [X,A,B,C,D])):
            return int(round(retval))
        return retval

Source code: [[attachment:rescale.py]]

Example: Celsius <-> Fahrenheit conversion
==========================================

An example application of this function, is conversion between Celsius
and Fahrenheit degrees, assuming we don't know the conversion
ratio / delta, but just two fixed points in the scale.

Knowing, for example, that ``0 °C = 32 °F`` and ``100 °C = 212 °F``, we
can do some conversions:

°C to °F
--------

.. code-block:: python

    >>> rescale(20,0,100,32,212,True)
    68.0
    >>> rescale(200,0,100,32,212,True)
    392.0
    >>> rescale(-5,0,100,32,212,True)
    23.0

°F to °C
--------

.. code-block:: python

    >>> rescale(0,32,212,0,100,True)
    -17.777777777777779
    >>> rescale(23,32,212,0,100,True)
    -5.0
    >>> rescale(392,32,212,0,100,True)
    200.0
    >>> rescale(68,32,212,0,100,True)
    20.0
