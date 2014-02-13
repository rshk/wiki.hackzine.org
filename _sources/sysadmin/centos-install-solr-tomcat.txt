CentOS: Install Solr with Tomcat6
#################################

.. warning::
    Although this method seems to "just work", it is not to be considered
    "the right way" to install Solr or be used as-is in production deployments.

    Anyways, it should probably work just fine for development environments.


Multiple Solr versions
======================

In my case, I needed to install two Solr versions (1.4 and 4.6) on the same machine,
each one with multiple cores.

To accomplish that, I separated all the configuration / data directories
by prefixing them wiht the version number.


Downloading tarballs
====================

For example

* http://archive.apache.org/dist/lucene/solr/1.4.1/apache-solr-1.4.1.tgz
* http://mirror.nohup.it/apache/lucene/solr/4.6.1/solr-4.6.1.tgz

(use mirrors close to you!)

.. code-block:: console

    mkdir -p /opt/solr
    cd /opt/solr

    wget http://archive.apache.org/dist/lucene/solr/1.4.1/apache-solr-1.4.1.tgz
    tar xzvf apache-solr-1.4.1.tgz
    ln -s apache-solr-1.4.1 1.4

    wget http://mirror.nohup.it/apache/lucene/solr/4.6.1/solr-4.6.1.tgz
    tar xzvf solr-4.6.1.tgz
    ln -s solr-4.6.1 4.6


Create Solr configuration
=========================

.. code-block:: bash

    VERSIONS="1.4 4.6"

    mkdir -f /etc/solr
    for version in $VERSIONS; do
        # Prepare directories to hold configuration and data
        mkdir -fp /usr/share/solr/${version}
        mkdir -fp /var/lib/solr/${version}/data

        # This directory should hold the data (although, apparently,
        # data gets actually stored in /usr/share/solr/..../data)
        chown -R tomcat:tomcat /var/lib/solr/${version}/

        # Apparently there are things looking for configuration
        # in /etc/ too -- better symlink!
        ln -s /usr/share/solr/${version} /etc/solr/${version}

        # Let's copy example configuration for multicore solr
        cp -r /opt/solr/${version}/example/multicore/* /usr/share/solr/${version}
    done


Install Solr applications in Tomcat
===================================

.. code-block:: console

    ln -s /opt/solr/1.4/dist/apache-solr-1.4.1.war /usr/share/tomcat6/webapps/solr-1.4.war
    ln -s /opt/solr/4.6/dist/solr-4.6.1.war /usr/share/tomcat6/webapps/solr-4.6.war

And create appropriate configuration files:

``/usr/share/tomcat6/conf/Catalina/localhost/solr-1.4.xml``:

.. code-block:: xml

    <Context docBase="/usr/share/tomcat6/webapps/solr-1.4.war" debug="0" privileged="true" allowLinking="true" crossContext="true">
      <Environment name="solr/home" type="java.lang.String" value="/usr/share/solr/1.4" override="true" />
    </Context>

``/usr/share/tomcat6/conf/Catalina/localhost/solr-4.6.xml``:

.. code-block:: xml

    <Context docBase="/usr/share/tomcat6/webapps/solr-4.6.war" debug="0" privileged="true" allowLinking="true" crossContext="true">
      <Environment name="solr/home" type="java.lang.String" value="/usr/share/solr/4.6" override="true" />
    </Context>


Testing installation
====================

Restart tomcat::

    service tomcat6 restart


Check whether applications are there and do not display errors:

* http://localhost:8080/solr-1.4/
* http://localhost:8080/solr-4.6/
