Python: logging related stuff
####

Colorful formatter for console messages
====

.. code-block:: python

    import logging, string

    LOG_FORMAT_CONSOLE_COLOR = (
        "\033[1;36;44m%(asctime)s,%(msecs)03d\033[0m "
        "\033[0m%(c_levelname)s\033[0m "
        "\033[1;34m[%(name)s]\033[0m "
        "\033[1;33m%(module)s.%(funcName)s\033[0m\n"
        "%(message)s", "%T")

    LOG_DATEFMT_CONSOLE_COLOR = "%F %T"

    class ConsoleColorFormatter(logging.Formatter):
        def __init__(self, fmt=None, datefmt=None):
            if fmt is None:
                fmt = LOG_FORMAT_CONSOLE_COLOR
            if datefmt is None:
                datefmt = LOG_DATEFMT_CONSOLE_COLOR
            logging.Formatter.__init__(self, fmt=fmt, datefmt=datefmt)

        def format(self, record):
            record.message = record.getMessage()

            if string.find(self._fmt,"%(asctime)") >= 0:
                record.asctime = self.formatTime(record, self.datefmt)

            colors = {
                logging.DEBUG : '1;37;46',
                logging.INFO : '1;33;42',
                logging.WARNING : '1;37;43',
                logging.ERROR : '1;37;41',
                logging.CRITICAL : '1;33;41',
            }

            c = colors.get(record.levelno, '7')

            record.c_levelname = "\033[" + c + "m " + record.levelname + " \033[0m"

            s = self._fmt % record.__dict__

            if record.exc_info:
                # Cache the traceback text to avoid converting it multiple times
                # (it's constant anyway)
                if not record.exc_text:
                    record.exc_text = self.formatException(record.exc_info)
            if record.exc_text:
                if s[-1:] != "\n":
                    s = s + "\n"
                s = s + record.exc_text
            return s

    LOGFMT_CONSOLE_COLOR = ConsoleColorFormatter(
        "\033[1;36;44m%(asctime)s,%(msecs)03d\033[0m "
        "\033[0m%(c_levelname)s\033[0m "
        "\033[1;34m[%(name)s]\033[0m "
        "\033[1;33m%(module)s.%(funcName)s\033[0m\n"
        "%(message)s", "%T")


Formatter that prefixes lines with level name
====

.. code-block:: python

    class ConsolePrefixedFormatter(logging.Formatter):
        def __init__(self, fmt=None, datefmt=None, use_color=True):
            self.use_color = use_color
            logging.Formatter.__init__(self, fmt=fmt, datefmt=datefmt)

        def format(self, record):
            colors = {
                logging.DEBUG : '1;36',
                logging.INFO : '1;32',
                logging.WARNING : '1;33',
                logging.ERROR : '1;31',
                logging.CRITICAL : '1;31',
            }
            if self.use_color:
                c = colors.get(record.levelno, '7')
                levelname = "\033[" + c + "m[" + record.levelname + "]\033[0m"
            else:
                levelname = "[" + record.levelname + "]"
            s = "".join(["%s %s" % (levelname, l) for l in record.getMessage().splitlines()])
            if record.exc_info:
                # Cache the traceback text to avoid converting it multiple times
                # (it's constant anyway)
                if not record.exc_text:
                    record.exc_text = self.formatException(record.exc_info)
            if record.exc_text:
                if s[-1:] != "\n":
                    s = s + "\n"
                s = s + record.exc_text
            return s

    LOGFMT_CONSOLE = ConsolePrefixedFormatter(use_color=(os.environ.get('TERM', '').startswith('xterm')))
