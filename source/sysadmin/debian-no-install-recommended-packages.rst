Debian: prevent installation of recommended packages
####################################################

To prevent apt from suggesting/installing recommended packages add this to
``/etc/apt/apt.conf``::

    APT::Install-Recommends "false";
    APT::Install-Suggests "false";
