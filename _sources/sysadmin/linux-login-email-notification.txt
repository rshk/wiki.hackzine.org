Linux: getting e-mail notification on user login
################################################

Maybe I am a bit paranoid, but I want to know in real-time who logs into and
from which address to all my publicly-reachable machines.

Since logging that to file is too much insecure (if one gets the control of
the machine, he can delete every file he wants..) I wrote this script that
sends me an alert e-mail each time a user logs in.

It uses mutt to compose an e-mail and attaches the output of some commands
that give an idea of the current system status.

Here it is the source code:

.. code-block:: bash

    #!/bin/bash

    ## Login notification script
    ## 2009-11-20 00:28 Samuele ~redShadow~ Santi
    ## Under GPL

    ## Place it into /etc/profile or /root/.profile to send an e-mail on user login.
    ##  - uses mutt to send e-mail
    ##  - sends the output of some commands as attachments:
    ##    - netstat (connections/servers)
    ##    - iptables (firewall configuration)
    ##    - ps (processes list)
    ##    - who (logged in users + hostnames)
    ##  - of course, you need a configured MTA/smtp server in order to send emails.


    # --- Configuration ----------------------------------------------------

    NOTIFY_ADDR="MyUser <user@otherhost.org>"
    FROM_ADDR="Login Notify <security@myserver.net>"

    # ----------------------------------------------------------------------


    LOG_USER="$( whoami )"
    LOG_DATE="$( date "+%Y-%m-%d %H:%M:%S" )"
    OUT_WHO="$( who )"

    netstat -lnp > /tmp/netstat-listen
    netstat -np > /tmp/netstat
    ps afux > /tmp/processes
    who > /tmp/who
    (
    echo "--- Iptables: list rules"
    iptables -L
    echo
    echo "--- Iptables: show rules"
    iptables -S
    ) > /tmp/iptables-conf

    (
    cat <<EOF
    ------------------------------------------------------------------------
      LOGIN NOTIFICATION
    ------------------------------------------------------------------------

    Host:   $(hostname)
    User:   ${LOG_USER}
    Date:   ${LOG_DATE}
            $(date)
    Uptime: $(uptime)

    --- Logged in users ----------------------------------------------------
    ${OUT_WHO}

    ------------------------------------------------------------------------
    Attaching other relevant system data.

    EOF
    ) | /usr/bin/mutt -s "[LOGIN-NOTIFY] $(hostname) Login of ${LOG_USER} on ${LOG_DATE}" \
      -e "my_hdr From: ${FROM_ADDR}" \
      -a /tmp/netstat-listen -a /tmp/netstat -a /tmp/processes -a /tmp/who -a /tmp/iptables-conf \
      "${NOTIFY_ADDR}"

    rm /tmp/netstat-listen /tmp/netstat /tmp/processes /tmp/who /tmp/iptables-conf

Of course, you can modify which commands are run, where to log them and which
files you want to be attached to the e-mail. Or you can add more commands
output between the two EOF, to include their output in the message body.

Once placed the script somewhere, add it to a file executed on user login,
such as ``~/.profile`` or ``/etc/profile``.

You'll also of course need a configured and working mailserver, in order to
send e-mails..
