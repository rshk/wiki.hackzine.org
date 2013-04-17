Grabbing a whole site using wget
################################

Recently, I needed an off-line copy of some documentation, available only as
web pages. That's how I managed to clone entire parts of websites using wget.

First of all, the whole command I use::

    wget -U "Mozilla/5.0 (X11; U; Linux; en-US; rv:1.9.1.16) Gecko/20110929 Firefox/3.5.16" \
        --recursive --level=1 --no-clobber --page-requisites --html-extension \
        --convert-links --no-parent \
        --wait=3 --random-wait \
        http://www.example.com/docs/interesting-part/ --domains=www.example.com

Basic arguments
===============

These are the basic arguments needed to perform the recursive download.

``--recursive``
    Tells wget to recursively download pages, starting from the specified URL.

``--level=1``
    Tells wget to stop after one level of recursion. This can be changed to download more deeply, or set to 0 that means "no limit"

``--no-clobber``
    Skip downloads that would download to existing files

``--page-requisites``
    Tells wget to download all the resources (images, css, javascript, ...) that are needed for the page to work.

``--html-extension``
    Adds ".html" extension to downloaded files, with the double purpose of making the browser recognize them as html files and solving naming conflicts for "generated" URLs, when there are no directories with "index.html" but just a framework that responds dynamically with generated pages.

``--convert-links``
    After the download is complete, convert the links in the document to make them suitable for local viewing. This affects not only the visible hyperlinks, but any part of the document that links to external content, such as embedded images, links to style sheets, hyperlinks to non-HTML content, etc.

``--no-parent``
    Do not ever ascend to the parent directory when retrieving recursively.

``--domains=www.example.com``
    Set domains to be followed. DOMAIN-LIST is a comma-separated list of domains.


Avoiding imposed download limits
================================

Many web servers tend to limit the pages a user can download in a given amount
of time, or the user-agents that can access given pages, etc.
To avoid such limits, some extra options may be added.

``-U "Mozilla/5.0 (X11; U; Linux; en-US; rv:1.9.1.16) Gecko/20110929 Firefox/3.5.16"``
    Tells wget to use a fake user-agent, to emulate the one of a web browser (in this case, Firefox 3.5 on Linux)

``--wait=3``
    Tells wget to wait at least 3 seconds between retrievals.

``--random-wait``
    Tells wget to wait a random time between 0 and double the value specified with --wait between requests.
