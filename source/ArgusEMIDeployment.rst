.. _ArgusEMIDeployment:

Argus Service Deployment for EMI
================================

Argus Service Reference Card:
https://twiki.cern.ch/twiki/bin/view/EMI/ArgusSRC

Requirements
------------

Platform
~~~~~~~~

-  For **EMI-3** the supported platforms are **Debian6/x86\_64**, and
   **SL5/x86\_64** and **SL6/x86\_64** with the
   `EPEL <http://fedoraproject.org/wiki/EPEL>`__ repository **enabled**.
-  For **EMI-2** the supported platforms are **SL5/x86\_64** and
   **SL6/x86\_64** with the
   `EPEL <http://fedoraproject.org/wiki/EPEL>`__ repository **enabled**.
-  For **EMI-1** the supported platform is **SL5/x86\_64** with the
   `EPEL <http://fedoraproject.org/wiki/EPEL>`__ repository **enabled**.

Host Certificate
~~~~~~~~~~~~~~~~

The Argus services require a valid host certificate. The key pair is
typically installed in ``/etc/grid-security/hostcert.pem`` and
``/etc/grid-security/hostkey.pem``

Installation with YUM
---------------------

The Argus Authorization Service is a bundle of 3 services (PAP, PDP and
PEP Server) and is available in the EMI repository. The Argus
metapackage should be installed with YUM.

Install the EGI IGTF Bundle
~~~~~~~~~~~~~~~~~~~~~~~~~~~

If not already present on your host, install the EGI IGTF trust anchors.
More information available online:
https://wiki.egi.eu/wiki/EGI_IGTF_Release

-  `Install the EGI IGTF trust anchors with
   YUM <https://wiki.egi.eu/wiki/EGI_IGTF_Release#Using_YUM_package_management>`__

Install fetch-crl
~~~~~~~~~~~~~~~~~

If not already present on your host, install the ``fetch-crl`` cron job
(from the `EPEL <http://fedoraproject.org/wiki/EPEL>`__ repository), run
it once, and enable the cron job:

::

    # install
    yum install fetch-crl
    # run it immediately (it can take some time...)
    /usr/sbin/fetch-crl
    # enable the periodic fetch-crl cron job
    /sbin/chkconfig fetch-crl-cron on
    /sbin/service fetch-crl-cron start

Install the EMI Repository
~~~~~~~~~~~~~~~~~~~~~~~~~~

If not already configured on your host, install the EMI repositories
package as described in the EMI generic installation and configuration
guide for `EMI
1 <https://twiki.cern.ch/twiki/bin/view/EMI/GenericInstallationConfigurationEMI1>`__
or `EMI
2 <https://twiki.cern.ch/twiki/bin/view/EMI/GenericInstallationConfigurationEMI2>`__,
or `EMI
3 <https://twiki.cern.ch/twiki/bin/view/EMI/GenericInstallationConfigurationEMI3>`__.

And update the YUM cache: ``yum makecache``

Install the Argus Metapackage
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The ``emi-argus`` metapackage bundles the 3 Argus services: PAP, PDP and
PEP Server

Therefore, the ``emi-argus`` metapackage is the simplest way to install
the Argus Authorization Services (PAP, PDP and PEP Server) on your host.

Use YUM to install the Argus metapackage: ``yum install emi-argus``

At this point all 3 Argus services (PAP, PDP and PEP server) are
installed on your host. You must now continue with the Argus service
configuration with YAIM.

Update with YUM
---------------

Argus and its components are packages in the EMI and UMD repositories,
and therefore automatically updated if a new version is available and
``yum update`` is executed. The updating process however is stopping the
Argus services (PAP, PEPd and PDP). To get Argus back to work after an
update it is recommended to rerun Yaim and/or restart the services.

**Please make sure that your system <font color="red">does not use the
automated yum updating service</font>, since this may lead to a stopped
Argus server in case of a unnoticed update.**

Configuration with YAIM
-----------------------

The ``ARGUS_server`` node type is available to configure the Argus
service with YAIM.

Argus YAIM Configuration Variables
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Description of all the available Argus YAIM configuration variables:
https://twiki.cern.ch/twiki/bin/view/EGEE/ArgusEMIYaimConfiguration

Mandatory YAIM Variables
~~~~~~~~~~~~~~~~~~~~~~~~

-  ``ARGUS_HOST`` Fully qualified host name (FQHN) of the Argus host
-  ``PAP_ADMIN_DN`` Certificate distinguished name (DN) of the
   administrator, allowed to use the ``pap-admin`` command
-  ``SITE_NAME`` BDII site name
-  ``USERS_CONF`` Absolute location of the users configuration file
-  ``GROUPS_CONF`` Absolute location of the groups configuration file
-  ``VOS`` List of supported VO names
-  ``VO_<vo-name>_VOMS_CA_DN`` VOMS CA DN for each VO name listed in
   ``VOS``
-  ``VO_<vo-name>_VOMSES`` VOMS definition for each VO name listed in
   ``VOS``

The ``USERS_CONF`` and ``GROUPS_CONF`` configuration files **MUST** be
the same on the Argus host as on the client host (CREAM, WMS, gLExec,
...). On successful authorization, the Argus PEP Server is configured to
determine the user/group mapping (pool account) for this authorization
and send it the client. Therefore, the client must be able to map the
resulting user mapping received with the authorization decision.

Argus ``site-info.def`` Configuration
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Your ``site-info.def`` for Argus must contain at least the following
variable:

::

    # BDII site name
    SITE_NAME=MySiteName

    # Argus service hostname
    ARGUS_HOST=argus.example.org

    # PAP administrator DN allowed to use 'pap-admin' command
    PAP_ADMIN_DN="/DC=org/DC=acme/CN=John Doe"

    # Users and Groups definition for grid and group mapfile
    USERS_CONF=/opt/glite/yaim/examples/users.conf
    GROUPS_CONF=/opt/glite/yaim/examples/groups.conf

    # Supported VOs
    VOS="dteam"

    VO_DTEAM_VOMSES="'dteam voms.hellasgrid.gr 15004 /C=GR/O=HellasGrid/OU=hellasgrid.gr/CN=voms.hellasgrid.gr dteam' 'dteam voms2.hellasgrid.gr 15004 /C=GR/O=HellasGrid/OU=hellasgrid.gr/CN=voms2.hellasgrid.gr dteam'"
    VO_DTEAM_VOMS_CA_DN="'/C=GR/O=HellasGrid/OU=Certification Authorities/CN=HellasGrid CA 2006' '/C=GR/O=HellasGrid/OU=Certification Authorities/CN=HellasGrid CA 2006'"

Documentation of all the `Argus YAIM Configuration
Variables <ArgusEMIYaimConfiguration>`__.

Generate Argus Configuration
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Run YAIM to generate the Argus configuration for your site:
``/opt/glite/yaim/bin/yaim -c -s site-info.def -n ARGUS_server``

At this point, the Argus services (PAP, PDP and PEP Server) must be
configured, up and running.

Nagios Probes for Argus
=======================

A set of Nagios probes for Argus (EMI-2 and EMI-3) are available to
monitor the Argus PAP, PDP and PEP Server:

-  Argus Nagios Probes Documentation (EMI):
   https://twiki.cern.ch/twiki/bin/view/EGEE/ArgusEMINagiosProbes

EMIR Publisher for Argus 1.6 (EMI-3)
====================================

You can use
`EMIR-SERP <https://twiki.cern.ch/twiki/bin/view/EMI/SERP>`__ to publish
the Argus resource information to
`EMIR <https://twiki.cern.ch/twiki/bin/view/EMI/EMIRegistry>`__.
EMIR-SERP uses the information already available in the resource BDII
and publish it to an EMIR DSR endpoint.

-  See the Argus EMIR Configuration to publish the Argus into EMIR:
   https://twiki.cern.ch/twiki/bin/view/EGEE/ArgusEMIEmirConfiguration

Known Issues
============

Timeouts for certificates from CAs that use OCSP
------------------------------------------------

As of late 2013 a few CAs (e.g. the CERN CA) have started using the
`Online Certificate Status
Protocol <http://en.wikipedia.org/wiki/Online_Certificate_Status_Protocol>`__
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
officially released in `Argus
v1.6.3 <http://www.eu-emi.eu/releases/emi-3-monte-bianco/updates/-/asset_publisher/5Na8/content/update-27-10-06-2015-v-3-15-3-1#ARGUS_v_1_6_3>`__
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

#. edit the ``/etc/argus/pepd/pepd.ini`` file 2. add the parameter
   =maximumCachedResponses = 0= in the ``[PDP]`` section (see example)
   3. restart PEP Server: ``/etc/init.d/argus-pepd restart`` (or
   increase the memory settings, then restart)

Example:

::

    [PDP]
    pdps = https://chaos.switch.ch:8152/authz
    # disabling the cache
    maximumCachedResponses = 0

**Increasing the default memory**

Allocating 1GB of memory for the Argus PEP Server solves the performance
issue. To increase the default memory:

#. edit the ``/etc/sysconfig/argus-pepd`` file 2. set the line
   ``PEPD_JOPTS="-Xmx1024M"`` 3. restart PEP Server:
   ``/etc/init.d/argus-pepd restart``

Problem the EMI-3 update and Argus PEP Server v.1.6.1
-----------------------------------------------------

Updating the Argus PEP Server to the last EMI-3 update
(``argus-pep-server-1.6.1``) will not always restart the Argus PEP
Server

Workaround
~~~~~~~~~~

After the update (``yum update``), restart the Argus PEP Server by hand:

::

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

::

    root# yum install fetch-crl
    root# fetch-crl -v

If you have **already** installed and configured Argus, just install the
``fetch-crl`` package by hand, run it at least once, and restart the
Argus services:

::

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

::

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

#. edit ``/etc/argus/pepd/pepd.ini`` 2. add the parameter
   =maximumCachedResponses = 0= in the ``[PDP]`` section (see example
   below) 3. restart PEP Server: ``/etc/init.d/argus-pepd restart``

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

::

    yum upgrade
    (argus is upgraded...)
    yum reinstall argus-pap argus-pdp argus-pep-server
