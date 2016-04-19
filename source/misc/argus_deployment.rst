.. _argus-deployment:

Argus Service Deployment for 1.7 version
========================================

Argus Service Reference Card:
:ref:`service-reference-card`.

Requirements
------------

Platform
~~~~~~~~

The supported platforms are:

-  **EL6/x86_64** with the `EPEL <http://fedoraproject.org/wiki/EPEL>`__
   and `UMD3 <http://repository.egi.eu/category/umd_releases/distribution/umd-3/>`__
   repositories **enabled**.

-  **EL7/x86_64** with the `EPEL <http://fedoraproject.org/wiki/EPEL>`__,
   `CERN WLCG <http://linuxsoft.cern.ch/wlcg/centos7/x86_64/>`__
   and `UMD4 <http://repository.egi.eu/category/umd_releases/distribution/umd-4/>`__
   repositories **enabled**.

Host Certificate
~~~~~~~~~~~~~~~~

The Argus services require a valid host certificate. The key pair is
typically installed in ``/etc/grid-security/hostcert.pem`` and
``/etc/grid-security/hostkey.pem``

Installation with YUM
---------------------

The Argus Authorization Service is a bundle of 3 services (PAP, PDP and
PEP Server) and is available in the repository. The Argus
metapackage should be installed with YUM.

Install the EGI IGTF Bundle
~~~~~~~~~~~~~~~~~~~~~~~~~~~

If not already present on your host, install the EGI IGTF trust anchors.
More information available online:
https://wiki.egi.eu/wiki/EGI_IGTF_Release

-  `Install the EGI IGTF trust anchors with
   YUM <https://wiki.egi.eu/wiki/EGI_IGTF_Release#Using_YUM_package_management>`__

Verify fetch-crl
~~~~~~~~~~~~~~~~

The Argus metapackage install the ``fetch-crl`` cron job;
run it once, and verify that the cron job is enabled and started:

::

    # run it immediately (it can take some time...)
    /usr/sbin/fetch-crl
    # enable the periodic fetch-crl cron job
    /sbin/chkconfig fetch-crl-cron on
    /sbin/service fetch-crl-cron start


For EL7 system, enable ``fetch-crl`` cron job with:

::

   /usr/bin/systemctl start fetch-crl-cron
   /usr/bin/systemctl enable fetch-crl-cron


Install the Argus Metapackage
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The ``argus-authz`` metapackage bundles the 3 Argus services: PAP, PDP and
PEP Server

Therefore, the ``argus-authz`` metapackage is the simplest way to install
the Argus Authorization Services (PAP, PDP and PEP Server) on your host.

Use YUM to install the Argus metapackage: ``yum install argus-authz``

At this point all 3 Argus services (PAP, PDP and PEP server) are
installed on your host. You must now continue with the Argus service
configuration with YAIM.

Update with YUM
---------------

Argus and its components are packages in the UMD repositories,
and therefore automatically updated if a new version is available and
``yum update`` is executed. The updating process however is stopping the
Argus services (PAP, PEPd and PDP). To get Argus back to work after an
update it is recommended to restart the services.

.. warning::
   **Please make sure that your system does not use the automated yum updating service,
   since this may lead to a stopped Argus server in case of a unnoticed update.**


Configuration
-------------

- Version 1.7: :ref:`manual configuration <argus-manual-configuration>`.
- Older version: :ref:`configuration-with-yaim`.



Nagios Probes for Argus
=======================

A set of Nagios probes for Argus (EMI-2 and EMI-3) are available to
monitor the Argus PAP, PDP and PEP Server:

-  Argus Nagios Probes Documentation (EMI):
   :ref:`argus_emi_nagios_probes`

EMIR Publisher for Argus 1.6 (EMI-3)
====================================

You can use
`EMIR-SERP <https://twiki.cern.ch/twiki/bin/view/EMI/SERP>`__ to publish
the Argus resource information to
`EMIR <https://twiki.cern.ch/twiki/bin/view/EMI/EMIRegistry>`__.
EMIR-SERP uses the information already available in the resource BDII
and publish it to an EMIR DSR endpoint.

-  See the Argus EMIR Configuration to publish the Argus into EMIR:
   :ref:`argus-emir-configuration`

Known Issues
============

In the 1.7 version, there are no known issues.
For older versions, see :ref:`old-version-known-issues`
