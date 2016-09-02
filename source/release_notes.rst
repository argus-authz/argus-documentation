.. _release_notes:

Argus Release notes
===================

.. _v1_7_0:

1.7.0 (09-02-2016) 
------------------

The Argus 1.7.0 release provides fixes for some outstanding bugs and introduces
support for Centos 7. The highlights of this release are:

- Centos7 support 
- Argus services now require Java 8
- Upgraded core dependencies to latest versions:

   * Jetty upgraded to version 9.2.13.v20150730
   * VOMS upgraded to version 3.1
   * CANL upgraded to version 2.2.0
   * OpenSAML upgraded to version 2.6.4

- Introduced a `load testing test suite`_ based on Grinder
- Introduced an `integration test suite`_ based on Robot Framework
- `Documentation`_ has been migrated to ReadTheDocs

Packages
~~~~~~~~

Packages for this release can be obtained from the Argus produt team package repository:

http://argus-authz.github.io/repo

Service configuration
~~~~~~~~~~~~~~~~~~~~~

Instructions on how to configure and run the services are provided in the in the
following documentation sections:

- :ref:`Manual configuration <argus-manual-configuration>`
- :ref:`YAIM configuration  <configuration-with-yaim>`

Configuration for the services has not changed, so existing SL6 installation
can upgrade and reconfigure services as usual (YAIM on SL6 is still supported).

YAIM configuration support is **not** provided on CENTOS 7; sites are free to use
their favourite configuration management tool (Ansbile, Puppet, Quattor) to
manage the Argus services. 

Changes in the mapping logic behaviour (`pep-7`_ , `pep-11`_), and in
particular in the handling of secondary group names, could produce different
mappings for user jobs so it's safe to drain the site before upgrading the
Argus services to version 1.7.0.

Main fixes
~~~~~~~~~~

PDP-PEP-COMMON
**************

- Update to CANL 2.2.0 to get more scalable certificate validation [`pdp-pep-common-11`_].

PEP Server
**********

- Improper synchronization may lead to corrupted mappings in the Gridmapdir [`pep-3`_].
- Incorrect mapping for secondary group names [`pep-7`_] , [`pep-11`_] .
- Incorrect handling of CNs with internal slash characters [`pep-9`_].

PDP
***

- Argus PDP should not start until policies are succesfully fetched from a PAP [`pdp-3`_].

PAP
***

- Init script fails if shutdown command is changed in configuration [`pap-7`_].
- BDII for Argus cannot check service status [`pap-8`_].
- Prevent to creation of policy rules with empty subject [`pap-6`_].

.. _pep-3: https://github.com/argus-authz/argus-pep-server/issues/3
.. _pep-7: https://github.com/argus-authz/argus-pep-server/issues/7
.. _pep-9: https://github.com/argus-authz/argus-pep-server/issues/9
.. _pep-11: https://github.com/argus-authz/argus-pep-server/issues/11

.. _pdp-pep-common-11: https://github.com/argus-authz/argus-pdp-pep-common/issues/2

.. _pdp-3: https://github.com/argus-authz/argus-pdp/issues/3

.. _pap-6: https://github.com/argus-authz/argus-pap/issues/6 
.. _pap-7: https://issues.infn.it/jira/browse/ARGUS-7
.. _pap-8: https://issues.infn.it/jira/browse/ARGUS-8

.. _load testing test suite: https://github.com/argus-authz/load-testsuit://github.com/argus-authz/load-testsuite 
.. _integration test suite: https://github.com/argus-authz/argus-robot-testsuite
.. _Documentation: http://argus-documentation.readthedocs.io
