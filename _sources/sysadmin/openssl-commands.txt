OpenSSL Basics and commands
###########################

Creating own CA
===============

If we're dealing with self-signed certificates (eg. for certificates used internally in an organization),
we usually need to create our own Certification Authority::

    # Create the CA Key and Certificate for signing Client Certs
    openssl genrsa -des3 -out ca.key 4096
    openssl req -new -x509 -days 365 -key ca.key -out ca.crt

Create a Server Key and Certificate
===================================

For example, for web servers (remember to put the domain name in the CN field)::

    # Create the Server Key, CSR, and Certificate
    openssl genrsa -des3 -out server.key 4096
    openssl req -new -key server.key -out server.csr

    # Self-sign the certificate
    openssl x509 -req -days 365 \
        -in server.csr -CA ca.crt -CAkey ca.key -CAserial ./caserial \
	-CAcreateserial -out server.crt

    # -or-
    echo 01 > ca.srl
    openssl x509 -req -days 365 \
        -in server.csr -CA ca.crt -CAkey ca.key \
	-out server.crt

    # Remove the password from the key (needed when it will be used
    # automatically eg. from a web server).
    openssl rsa -in server.key -out server-np.key


Create a Client Key
===================

Pretty much the same as with the server::

    openssl genrsa -des3 -out server.key 4096
    openssl req -new -key server.key -out server.csr

    openssl x509 -req -days 365 \
        -in server.csr -CA ca.crt -CAkey ca.key -CAserial ./caserial \
	-CAcreateserial -out server.crt


Useful commands
===============

Checking
--------

Check a Certificate Signing Request (CSR)::

  openssl req -text -noout -verify -in CSR.csr

Check a private key::

  openssl rsa -in privateKey.key -check

Check a certificate::

  openssl x509 -in certificate.crt -text -noout

Check a PKCS#12 file (.pfx or .p12)::

  openssl pkcs12 -info -in keyStore.p12



Services configuration
======================

NGINX, with client-side certificate authentication
--------------------------------------------------

This is a basic configuration I used for enforcing
certificate authentication on NGINX::

    server {
        listen 443;
        ssl on;
        
        ssl_certificate /etc/ssl/certs/ssl-cert.pem;
        ssl_certificate_key /etc/ssl/certs/ssl-cert.pem;
        ssl_client_certificate /etc/ssl/certs/ca.crt;
        
        ssl_ciphers ALL:-RC4+SSLv2;
        ssl_verify_client on;
        ssl_verify_depth 2;
        
        set $ssl_client_s_dn_cn "";
        
	# Put your location directives here...
    }

You'll also need PKSC12 certificate bundles to import in browsers::

    openssl pkcs12 -export \
        -in client-s.crt -inkey client-s.key \
	-certfile ca.crt \
	-name "Certificate for client X" \
	-out client-s.p12


External links
==============

* http://blog.nategood.com/client-side-certificate-authentication-in-ngi
* http://www.sslshopper.com/article-most-common-openssl-commands.html
