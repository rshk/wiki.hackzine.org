Python: generate "slightly" random values
####

Useful to generate plausible graphs, etc.

.. code-block:: python

    def slrgen(start=None,mindelta=0,maxdelta=5,minval=0,maxval=1000):
        if start is not None:
            s = int(start)
        else:
            s = randint(minval,maxval)
        while True:
            yield s
            s += choice([1,-1]) * randint(mindelta,maxdelta)
            s = min(maxval, max(minval, s))

`Download source from github gist <https://gist.github.com/4338243>`_
