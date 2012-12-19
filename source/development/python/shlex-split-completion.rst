Python: shlex splitting for completion
####

This is a function I used to split commands using shlex even in presence of
unterminated tokens, raising ``ValueError``s.

.. code-block:: python

    def completion_split(s):
        """
        Splits a string using shell lexer, and returns a completion-friendly
        result: ``([<prev-tokens>], <last-token>)``, where [last token] may
        be taken from an uncomplete parsing.

        :param s: The string to be splitted
        :return: a two-tuple: ``([<prev-tokens>], <last-token>)``

        >>> completion_split("")
        ([], '')
        >>> completion_split("a b c")
        (['a', 'b'], 'c')
        >>> completion_split("a b 'c")
        (['a', 'b'], 'c')
        >>> completion_split("a 'b c' d")
        (['a', 'b c'], 'd')
        >>> completion_split("a 'b c' 'd e f")
        (['a', 'b c'], 'd e f')

        """
        lex = shlex.shlex(s, posix=True)
        lex.whitespace_split = True
        lex.commenters = ''
        tokens = []
        while True:
            try:
                token = lex.get_token()
            except:
                return (tokens, lex.token)
            else:
                if token == lex.eof:
                    try:
                        ## We finished without parsing errors
                        ltok = tokens.pop()
                        return (tokens, ltok)
                    except IndexError:
                        ## Nothing here to see..
                        return ([], '')
                else:
                    tokens.append(token)
