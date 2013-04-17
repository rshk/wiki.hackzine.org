Python: nicely formatted table
##############################

.. warning::
    although it seems to be working fine, this piece of code still lacks
    some serious unittesting. Beware!

.. note::
    This might be worth becoming a project..

.. code-block:: python

    """
    Nicely format tables for CLI output
    :author: Samuele Santi <redshadow@hackzine.org>
    :created: 2012-05-09
    """

    import os
    import textwrap

    def have_color_support():
        """Check whether the current terminal has color support"""
        TERM = os.environ.get('TERM')
        terms_with_color = ['xterm', 'xterm-color', 'xterm-256color', 'linux', 'screen', 'screen-256color', 'screen-bce']
        return  (TERM in terms_with_color) or TERM.startswith('xterm')

    def safe_unicode(text):
        try:
            return unicode(str(text), "utf8")
        except:
            return "???"

    def getTerminalSize():
        def ioctl_GWINSZ(fd):
            try:
                import fcntl, termios, struct, os
                cr = struct.unpack('hh', fcntl.ioctl(fd, termios.TIOCGWINSZ, '1234'))
            except:
                return None
            return cr
        cr = ioctl_GWINSZ(0) or ioctl_GWINSZ(1) or ioctl_GWINSZ(2)
        if not cr:
            try:
                fd = os.open(os.ctermid(), os.O_RDONLY)
                cr = ioctl_GWINSZ(fd)
                os.close(fd)
            except:
                pass
        if not cr:
            try:
                cr = (os.environ['LINES'], os.environ['COLUMNS'])
            except:
                cr = (25, 80)
        return int(cr[1]), int(cr[0])

    def data_table(data, header_rows=0, use_colors=None, min_field_width=10,
                   max_field_width=None, min_table_width=None, max_table_width=None):
        """Nicely format some data in a table

        :param data: an iterable of iterables, describing rows/columns
        :param min_field_width: minimum width for fields. Defaults to 10,
            and autoshrinks to fit max_table_width.
        :param max_field_width: DEPRECATED
        :param max_table_width: Maximum width for the table
        :param use_colors: whether to use colors or not. The default (None)
            means autodiscover color support from $TERM
        :param header_rows: how many rows should be considered as headers
        """
        ## Convert data to unicode here, as we need exact lengths to calculate
        ## field widths
        #data = [[unicode(str(col), encoding="utf8") for col in row] for row in data]
        data = [[safe_unicode(col) for col in row] for row in data]
        _columns = max([len(row) for row in data])
        _rows = len(data)

        if use_colors is None:
            use_colors = have_color_support()

        if max_table_width is None:
            termw, termh = getTerminalSize()
            max_table_width = termw

        if min_table_width is None:
            ## Try to fit the 80% of terminal width
            termw, termh = getTerminalSize()
            min_table_width = min(max_table_width, termw * 4 / 5)

        ## Make sure we can respect min_field_width
        _expected_min_table_width = min_field_width * _columns
        if use_colors:
            _expected_min_table_width += 2 * _columns
        else:
            _expected_min_table_width += (3 * _columns) + 1
        if _expected_min_table_width > max_table_width:
            if use_colors:
                min_field_width = int((max_table_width-1) / _columns) - 3
            else:
                min_field_width = int((max_table_width) / _columns) - 2
            if min_field_width < 1:
                raise ValueError("Unable to determine a large enough value for min_field_width")

        ## Compute required field lengths
        required_field_lengths = {}
        field_lengths = {}
        for row in data:
            for col_id, col in enumerate(row):
                required_field_lengths[col_id] = max(required_field_lengths.get(col_id, 0), len(col))

        ## Check what the table width will be
        _required_table_length = sum(required_field_lengths.itervalues())
        if use_colors:
            ## Only padding, for colorful tables
            _required_table_length += 2 * len(required_field_lengths)
        else:
            ## '| <value> ' ... '|'
            _required_table_length += (3 * len(required_field_lengths)) + 1

        ## Check if we need to resize the table..
        _required_delta = 0

        if _required_table_length > max_table_width:
            _required_delta = max_table_width - _required_table_length
        elif _required_table_length < min_table_width:
            _required_delta = min_table_width - _required_table_length

        if _required_delta:
            _tot_lengths = sum(required_field_lengths.itervalues())
            for k, v in required_field_lengths.items():
                _delta = int(round(_required_delta * 1.0 * v / _tot_lengths))
                field_lengths[k] = v + _delta
        else:
            field_lengths = required_field_lengths.copy()

        def row_separator():
            return u"".join([u"+" + (u"-" * (f + 2)) for k, f in field_lengths.items()]) + u"+"
            sep = ""
            for k, f in field_lengths.items():
                sep += u"+" + (u"-"*(f+2))
            sep += u"+"
            return sep

        str_table = []
        for row_id, row in enumerate(data):
            if not use_colors:
                str_table.append(row_separator())
            _wrapped_row = []
            #for col_id, col in enumerate(row):
            for col_id in range(len(field_lengths)):
                try:
                    col = row[col_id]
                    _wrapped_row.append(textwrap.wrap(col, field_lengths[col_id]))
                except IndexError:
                    _wrapped_row.append([])
            _subrows = max(*[len(c) for c in _wrapped_row])
            for _srid in range(_subrows):
                _row_str = u""
                for col_id, col in enumerate(_wrapped_row):
                    if use_colors:
                        if row_id < header_rows:
                            _row_str += u"\033[1;37;42m"
                        elif col_id % 2 == 0:
                            if row_id % 2 == 1:
                                _row_str += u"\033[48;5;238m"
                            else:
                                _row_str += u"\033[48;5;234m"
                        else:
                            if row_id % 2 == 1:
                                _row_str += u"\033[48;5;240m"
                            else:
                                _row_str += u"\033[48;5;236m"
                    else:
                        _row_str += u"|"
                    try:
                        content = u"%s" % _wrapped_row[col_id][_srid]
                    except IndexError:
                        content = u""
                    _row_str += u" %s " % unicode.ljust(content, field_lengths[col_id])
                    #_row_str += u" "
                if use_colors:
                    _row_str += "\033[0m"
                else:
                    _row_str += u"|"
                str_table.append(_row_str)
        if not use_colors:
            str_table.append(row_separator())
        return u"\n".join(str_table)


`Download source from github gist <https://gist.github.com/4338299>`_
