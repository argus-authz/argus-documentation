.. _old-version-known-issues:

Known Issues for older version
==============================

Timeouts for certificates from CAs that use OCSP
------------------------------------------------

As of late 2013 a few CAs (e.g. the CERN CA) have started using the
`Online Certificate Status Protocol <http://en.wikipedia.org/wiki/Online_Certificate_Status_Protocol>`__
(OCSP) in addition to the CRL mechanism to advertise which certificates
have been revoked. Some security libraries will then by default contact
the OCSP responder of the CA in real time when a certificate from such a
CA needs to be validated. If that outgoing traffic happens to be
blocked, or if the responder is slow or even unreachable, the operation
will eventually time out (see GGUS:105666). Depending on the case at
hand, such a timeout need not be a fatal error in itself, but will at
least slow down operations and may therefore cause timeouts downstream
(e.g. for CREAM or gLExec).

Workaround
~~~~~~~~~~

In EGI and WLCG we do not need OCSP to work at this time, as the CRL
mechanism is still deemed sufficient. Therefore it is reasonable to
disable OCSP for the time being and version 1.4.1 of the
``argus-pdp-pep-common`` rpm does that by default. That version was
officially released in `Argus v1.6.3 <http://www.eu-emi.eu/releases/emi-3-monte-bianco/updates/-/asset_publisher/5Na8/content/update-27-10-06-2015-v-3-15-3-1#ARGUS_v_1_6_3>`__
as part of EMI-3 Update 27 (2015-06-10).

Performance issue with Argus PEP Server (EMI-2, EMI-3, all versions)
--------------------------------------------------------------------

After a large number of authorization requests, the PDP responses
caching mechanism in the PEP Server becomes unstable and the performance
of the service deteriorates. It is recommended to completely disable the
PDP responses caching mechanism in the Argus PEP Server.

Additionally, the default memory settings for the Argus PEP Server could
be too low for production site. It is recommended to allocate at least
1GB memory.

Workaround
~~~~~~~~~~

**Disabling the caching mechanism**

Completely disabling the PDP responses caching mechanism in the PEP
Server configuration solves the performance issue. To disable the cache:

1. edit the ``/etc/argus/pepd/pepd.ini`` file
2. add the parameter ``maximumCachedResponses = 0`` in the ``[PDP]`` section (see example)
3. restart PEP Server: ``/etc/init.d/argus-pepd restart`` (or increase the memory settings, then restart)

Example:

::

    [PDP]
    pdps = https://chaos.switch.ch:8152/authz
    # disabling the cache
    maximumCachedResponses = 0

**Increasing the default memory**

Allocating 1GB of memory for the Argus PEP Server solves the performance
issue. To increase the default memory:

1. edit the ``/etc/sysconfig/argus-pepd`` file
2. set the line ``PEPD_JOPTS="-Xmx1024M"``
3. restart PEP Server: ``/etc/init.d/argus-pepd restart``


Problem the EMI-3 update and Argus PEP Server v.1.6.1
-----------------------------------------------------

Updating the Argus PEP Server to the last EMI-3 update
(``argus-pep-server-1.6.1``) will not always restart the Argus PEP
Server

Workaround
~~~~~~~~~~

After the update (``yum update``), restart the Argus PEP Server by hand:

.. code-block:: bash

    root# /sbin/service argus-pepd restart


Problem with Argus 1.6 (EMI-3) and ``fetch-crl``
------------------------------------------------

The Argus metapackage ``emi-argus`` have no dependency on the
``fetch-crl`` cron job. Installing and starting the Argus services,
without having ``fetch-crl`` installed, will cause SSL errors when
trying to connect to the services.

Workaround
~~~~~~~~~~

**Prior** to installing the ``emi-argus`` metapackage, install the the
``fetch-crl`` package by hand, and run it at least once:

.. code-block:: bash

    root# yum install fetch-crl
    root# fetch-crl -v


If you have **already** installed and configured Argus, just install the
``fetch-crl`` package by hand, run it at least once, and restart the
Argus services:

.. code-block:: bash

    root# yum install fetch-crl
    root# fetch-crl -v
    root# service argus-pap restart
    root# service argus-pdp restart
    root# service argus-pepd restart


Problem with Nagios plugins for Argus and TMP directory permission
------------------------------------------------------------------

The ``nagios-plugins-argus`` package do not set the correct ownership
for the ``/var/lib/grid-monitoring/nagios-plugins-argus`` temp
directory. The plugins will throw errors (``Permission denied``) because
the ownership of the directory is not ``nagios``.

Workaround
~~~~~~~~~~

To fix the temp directory ownership to ``nagios``, please do:

.. code-block:: bash

    root# yum install nagios-plugins-argus
    root# chown -R nagios:nagios  /var/lib/grid-monitoring/nagios-plugins-argus


Problem with Argus 1.5 (EMI-2) and CREAM
----------------------------------------

Under heavy load the Argus PEP Server (v1.5.1) does not always return a
user mapping for a permitted operation, causing CREAM to throw an error
and abort the job. This typically occurs for 10% of the jobs submitted
by CREAM.

Workaround
~~~~~~~~~~

Disabling the PDP responses caching mechanism in the PEP Server
configuration solve this issue. To disable the cache:

1. edit ``/etc/argus/pepd/pepd.ini``
2. add the parameter ``maximumCachedResponses = 0`` in the ``[PDP]`` section (see example below)
3. restart PEP Server: ``/etc/init.d/argus-pepd restart``

Example:

::

    [PDP]
    pdps = https://chaos.switch.ch:8152/authz
    # disabling the cache
    maximumCachedResponses = 0


Problem with upgrade from Argus 1.4 (EMI-1) to Argus 1.5 (EMI-2)
----------------------------------------------------------------

When upgrading an previous Argus 1.4 (EMI-1) installation on SL5, you
need to re-install Argus 1.5 (EMI-2). This is due to an error in the
Argus 1.4 post uninstall script.

Workaround
~~~~~~~~~~

Simply reinstalling the components with YUM just after the upgrade
solves the issue:

.. code-block:: bash

    yum upgrade
    (argus is upgraded...)
    yum reinstall argus-pap argus-pdp argus-pep-server