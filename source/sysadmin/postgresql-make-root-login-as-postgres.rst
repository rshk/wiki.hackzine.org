PostgreSQL: Make root log in as postgres user
#############################################

The goal is allowing root (or another user) to login as a certain
other user without the need of entering a password.

PostgreSQL 9.3
==============

``pg_hba.conf``::

    # TYPE  DATABASE USER   ADDRESS         METHOD
    local   all     all                     peer map=root_as_others
    host    all     all     127.0.0.1/32    ident map=root_as_others

``pg_ident.conf``::

    # MAPNAME       SYSTEM-USERNAME PG-USERNAME
    root_as_others  root            postgres
    root_as_others  root            gis

PostgreSQL 8.3
==============

In ``/etc/postgresql/8.3/main/pg_hba.conf``::

    # TYPE          DATABASE        USER            CIDR-ADDRESS            METHOD
    local           all             postgres                                ident rootaspg
    local           all             all                                     ident sameuser
    host            all             all             127.0.0.1/32            md5
    host            all             all             ::1/128                 md5

In ``/etc/postgresql/8.3/main/pg_ident.conf``::

    # MAPNAME     IDENT-USERNAME    PG-USERNAME
    rootaspg                root                            postgres
    rootaspg                root                            drupal
    rootaspg                postgres                        postgres
