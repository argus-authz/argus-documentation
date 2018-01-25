.. _argus_bind_localhost:

Bind services to localhost
==========================

Argus services can be deployed all on the same machine.

The service that is mandatory to expose outside the machine is
only the PEP service.
So to avoid to expose PAP and PDP services,
these daemon can configured to listen on loopback interface.

To achieve this kind of deployment, change the configuration as
described below.

.. warning::
   To bind the services on the loopback interface,
   ensure that the host certificate includes ``localhost`` and
   ``127.0.0.1`` in the Subject Alternative Name.

PAP
^^^

Argus PAP service listen on all interfaces, by design.
So, it cannot be binded only to loopback interface.


PDP
^^^

Edit ``/etc/argus/pdp/pdp.ini`` specifying ``localhost`` as hostname::

  hostname = localhost
  port = 8152

Update the ``paps`` option with the endpoint of the local PAP::

  [POLICY]
  paps = https://localhost:8150/pap/services/ProvisioningService

PEP
^^^

Edit ``/etc/argus/pepd/pepd.ini`` with the local PDP endpoint::
  
  [PDP]
  pdps = https://localhost:8152/authz


Validation
^^^^^^^^^^

Restart the services; on EL7 platforms::

  $ systemctl restart argus-pap argus-pdp argus-pepd

On EL6 platforms::

  $ for svc in pap pdp pepd; do service argus-$svc restart; done

Check the ports::

  $ ss -tnpl sport ge :8150 and sport le :8155 | column -t
  State   Recv-Q  Send-Q  Local                   Address:Port  Peer                             Address:Port
  LISTEN  0       50      :::8150                 :::*          users:(("java",pid=8030,fd=63))
  LISTEN  0       50      ::ffff:127.0.0.1:8151   :::*          users:(("java",pid=8030,fd=58))
  LISTEN  0       50      ::ffff:127.0.0.1:8152   :::*          users:(("java",pid=7963,fd=67))
  LISTEN  0       50      ::ffff:127.0.0.1:8153   :::*          users:(("java",pid=7963,fd=62))
  LISTEN  0       50      ::ffff:10.0.0.173:8154  :::*          users:(("java",pid=7979,fd=66))
  LISTEN  0       50      ::ffff:127.0.0.1:8155   :::*          users:(("java",pid=7979,fd=60))

From another host, scan the Argus ports::

  $ nmap -p8150-8155 argus.cnaf.test

  Starting Nmap 7.60 ( https://nmap.org ) at 2017-12-04 15:07 CET
  Nmap scan report for argus.cnaf.test (10.0.0.173)
  Host is up (0.00015s latency).
  rDNS record for 10.0.0.173: argus

  PORT     STATE  SERVICE
  8150/tcp open   unknown
  8151/tcp closed unknown
  8152/tcp closed unknown
  8153/tcp closed quantastor
  8154/tcp open   unknown
  8155/tcp closed unknown

  Nmap done: 1 IP address (1 host up) scanned in 0.03 seconds


See also
--------

:ref:`Firewall setup <argus_firewall>`

References
----------

`GGUS ticket <https://ggus.eu/index.php?mode=ticket_info&ticket_id=131284>`_
