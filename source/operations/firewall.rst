.. _argus_firewall:

Firewall setup
==============

You can use a firewall to restrict access to Argus services.

A simple setup is to open only the Argus PEP port.

Edit the iptables configuration file ``/etc/sysconfig/iptables``
to open the PEP port 8154 in the ``INPUT`` chain and
drop network traffic on all other ports (except SSH)::
 
  *filter
  :INPUT DROP [0:0]
  :FORWARD DROP [0:0]
  :OUTPUT ACCEPT [0:0]
  -A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT
  -A INPUT -p icmp -j ACCEPT
  -A INPUT -i lo -j ACCEPT
  -A INPUT -p tcp -m state --state NEW -m tcp --dport 22 -j ACCEPT
  -A INPUT -p tcp -m state --state NEW -m tcp --dport 8154 -j ACCEPT
  COMMIT

Save the changes and restart iptables. On EL7 platforms::

  $ systemctl restart iptables

On EL6 platforms::

  $ service iptables restart


Validation
^^^^^^^^^^

From another host, scan the Argus ports::

  $ nmap -Pn -p8150-8155 argus.cnaf.test

  Starting Nmap 7.60 ( https://nmap.org ) at 2017-12-04 15:07 CET
  Nmap scan report for argus.cnaf.test (10.0.0.173)
  Host is up (0.00015s latency).
  rDNS record for 10.0.0.173: argus

  PORT     STATE    SERVICE
  8150/tcp filtered unknown
  8151/tcp filtered unknown
  8152/tcp filtered unknown
  8153/tcp filtered quantastor
  8154/tcp open     unknown
  8155/tcp filtered unknown

  Nmap done: 1 IP address (1 host up) scanned in 2.14 seconds

