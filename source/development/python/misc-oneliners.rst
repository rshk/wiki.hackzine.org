Python: misc one-liners and snippets
####

Formatted date to datetime object
====

.. code-block:: python

    import time, datetime
    timestring = "2005-09-01 12:30:09"
    time_format = "%Y-%m-%d %H:%M:%S"
    datetime.datetime.strptime(timestring, time_format)

Punycode URLs
====

.. code-block:: python

    domain = '\xd8\xa7\xd9\x84\xd8\xa7\xd8\xb9\xd9\x84\xd9\x8a-\xd9\x84\xd9\x84\xd8\xa7\xd8\xaa\xd8\xb5\xd8\xa7\xd9\x84\xd8\xa7\xd8\xaa.\xd9\x82\xd8\xb7\xd8\xb1'
    domain_unicode = unicode(domain, "utf8")
    domain_idna = domain_unicode.encode("idna")

or

.. code-block:: python

    domain = u'\u0627\u0644\u0627\u0639\u0644\u064a-\u0644\u0644\u0627\u062a\u0635\u0627\u0644\u0627\u062a.\u0642\u0637\u0631'
    domain_idna = domain.encode("idna")

Save Python shell history
====

To save all the commands history of the current Python shell, use the readline_ module as follows:

.. code-block:: python

    import readline
    readline.write_history_file('/tmp/my-history-file')


.. _readline: http://docs.python.org/library/readline.html
