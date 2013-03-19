Debian: apt preferences
#######################

Install some packages from testing
==================================

From: http://stackoverflow.com/questions/512906

Add the following to ``/etc/apt/preferences``::

    Package: *
    Pin: release a=testing
    Pin-Priority: 200

``200`` means that new packages in testing still override local packages that
are not in stable (local is always ``100``), but not ones that are in the stable
repo as well.

To install packages from testing::

    $ apt-get install -t testing <some_package>
