Python: Rot13 text transformation
#################################

If GPG isn't enough, you can use ROT13 for your encryption needs! ;)

.. code-block:: python

    def rotate_string(s, r=0):
        s2=""
        for c in s:
            if c >= 'a' and c <= 'z':
                c2 = chr(((ord(c)-ord('a')) + r) % 26 + ord('a'))
            elif c >= 'A' and c <= 'Z':
                c2 = chr(((ord(c)-ord('A')) + r) % 26 + ord('A'))
            else:
                c2 = c
            s2 += c2
        return s2

`Download source from github gist <https://gist.github.com/4338365>`_
