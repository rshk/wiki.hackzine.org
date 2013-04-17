Python: Mechanize hacks
####

Stuff about Python `mechanize`_ module.

Forcing all connections to localhost
====

This code snippet came from a script I wrote to perform some installation tasks on a web application, freshly installed on the machine.
As the installation is usually fresh, can be the case that the DNS have not been properly refreshed, and so they're not returning the correct IP for the domain name, thus the script execution would fail.

To fix that, I used this "hack", that forces all the HTTP Connections to use a socket on the server ip, no matter which the domain name is.

.. code-block:: python

    #!highlight python
    import httplib
    import mechanize
    from mechanize._urllib2 import HTTPHandler

    def get_local_browser(server_ip, server_port=None):
        """Returns a mechanize browser that always connects to a given server
        ip and port, no matter what the """

        class MyHTTPConnection(httplib.HTTPConnection):
            """Custom http connection class that always connects to localhost"""
            def connect(self):
                self.sock = create_connection((server_ip, server_port or self.port), self.timeout)

        class MyHTTPHandler(HTTPHandler):
            """Custom HTTP handler that uses MyHTTPConnection to open connections"""
            def http_open(self,req):
                return self.do_open(MyHTTPConnection, req)

        MyBrowser = mechanize.Browser
        MyBrowser.handler_classes['http'] = MyHTTPHandler
        return MyBrowser

Sample usage would be:

.. code-block:: python

    LocalBrowser = get_local_browser('127.0.0.1')
    browser = LocalBrowser()
    # from now on, treat as normal mechanize.Browser()

.. _mechanize: http://wwwsearch.sourceforge.net/mechanize/
