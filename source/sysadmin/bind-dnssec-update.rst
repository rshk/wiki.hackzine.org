Bind: dnssec update
###################

.. note:: This is just a quick reference guide, have a look at more
          complete guides if you don't know much about this..

Let's assume we want to allow updating entries in the ``mydomain.tld`` zone.

The procedure has been successfully used on Debian 6 "squeeze".


Generate keys
=============

Generate keypair for authenticating the DNSSEC update::

    dnssec-keygen -a HMAC-SHA512 -b 512 -n USER admin.mydomain.tld


Configure Bind
==============

File ``/etc/bind/keys.conf``::

    key admin.mydomain.tld. {
        algorithm HMAC-SHA512;
        secret "PEMxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx yyyyyyyyyyyyyyyyyyyyyyyyyyyyyy==";
    };

File ``/etc/bind/named.conf.local``::

    include "/etc/bind/keys.conf";

    zone "mydomain.tld" {
        type master;
        file "/etc/bind/db.mydomain.tld";
        allow-update {
            key admin.mydomain.tld.;
        };
    };

Issue DNSSEC updates
====================

File ``/tmp/dnssec-update.txt``::

    server localhost
    zone r3dbool.local
    update delete test-dnssec.mydomain.tld A
    update add test-dnssec.mydomain.tld 3600 A 10.11.12.13
    show
    send

Issue the update::

    nsupdate -k Kadmin.mydomain.tld.+157+46947.private -v /tmp/dnssec-update.txt

Check::

    # host test-dnssec.mydomain.tld 127.0.0.1
    Using domain server:
    Name: 127.0.0.1
    Address: 127.0.0.1#53
    Aliases:

    test-dnssec.mydomain.tld has address 10.11.12.13


Credits
=======

- http://linux.yyz.us/nsupdate/
- http://linux.yyz.us/dns/ddns-server.html
