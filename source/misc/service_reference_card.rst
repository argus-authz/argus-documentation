.. _service-reference-card:

Argus Service Reference Card
============================

Definitions
-----------

**PAP**
   The Policy Administration Point manages and publishes the authorization policies.

**PDP**
   The Policy Decision Point evaluates the authorization requests.

**PEP Server**
   The Policy Enforcement Point server processes the PEP clients requests and responses.

**PEP client**
   Lightweight PEP client library available for Java and C. Enforces the authorization decision locally.


Service Reference Card
----------------------

**Functional description:**
   Render authorization decisions based on XACML policies: *"Can user X performs action Y on resource Z ?"* or *"Is user X banned for any action on any resource ?"*

**Services running:**
   * PAP: (Java application) ``org.glite.authz.pap.server.standalone.PAPServer``
   * PDP: (Java application) ``org.glite.authz.pdp.server.PDPDaemon``
   * PEP Server: (Java application) ``org.glite.authz.pep.server.PEPDaemon``

**Init scripts and options:**
   * PAP: ``/etc/init.d/argus-pap {start|stop|status|restart}``
   * PDP: ``/etc/init.d/argus-pdp {start|stop|restart|status|info|version|reloadpolicy}``
   * PEP Server: ``/etc/init.d/argus-pepd {start|stop|restart|status|info|version|clearcache}``

**Configuration files location with example:**
   * PAP:
      * *Config directory:* ``/etc/argus/pap``
      * *Config files:* `pap_configuration.ini <https://raw.github.com/argus-authz/argus-pap/master/src/main/config/pap_configuration.ini>`_ and `pap_authorization.ini <https://raw.github.com/argus-authz/argus-pap/master/src/main/config/pap_authorization.ini>`_
   * PDP:
      * *Config directory:* ``/etc/argus/pdp``
      * *Config file:* `pdp.ini <https://raw.github.com/argus-authz/argus-pdp/EMI-3/src/main/config/pdp.ini>`_
   * PEP Server:
      * *Config directory:* ``/etc/argus/pepd``
      * *Config file:* `pepd.ini <https://raw.github.com/argus-authz/argus-pep-server/EMI-3/src/main/config/pepd.ini>`_

**Logfile locations (and management) and other useful audit information:**
   * PAP:
      * *Logging directory:* ``/var/log/argus/pap``
      * *Logging configuration:* ``/etc/argus/pap/logging/standalone/logback.xml``
      * *Example:* `PAP logback.xml <https://raw.github.com/argus-authz/argus-pap/master/src/main/config/logging/standalone/logback.xml>`_
      * *Status handler:* ``https://HOSTNAME:8150/pap/status``
   * PDP:
      * *Logging directory:* ``/var/log/argus/pdp``
      * *Logging configuration:* ``/etc/argus/pdp/logging.xml``
      * *Example:* `PDP logging.xml <https://raw.github.com/argus-authz/argus-pdp/EMI-3/src/main/config/logging.xml>`_
      * *Status handler:* ``https://HOSTNAME:8152/status``
   * PEP Server:
      * *Logging directory:* ``/var/log/argus/pepd``
      * *Logging configuration:* ``/etc/argus/pepd/logging.xml``
      * *Example:* `PEP logging.xml <https://raw.github.com/argus-authz/argus-pep-server/EMI-3/src/main/config/logging.xml>`_
      * *Status handler:* ``https://HOSTNAME:8154/status``

**Open ports:**
   * PAP:
      * *Service port:* ``*:8150``
      * *Admin port:*  ``localhost:8151``
   * PDP:
      * *Service port:* ``*:8152``
      * *Admin port:*  ``localhost:8153``
   * PEP Server:
      * *Service port:* ``*:8154``
      * *Admin port:*  ``localhost:8155``

**Possible unit test of the service:**
   Nagios probes are available to monitor the services, see :ref:`argus_emi_nagios_probes`.

**Where is service state held (and can it be rebuilt):**
   The services (PAP, PDP, PEP Server) are stateless. However:

      * PAP: The XACML policies are stored locally in the ``/usr/share/argus/pap/repository`` directory.
      * PEP Server: The user pool account mapping leases are kept in the ``/etc/grid-security/gridmapdir`` directory.

**Cron jobs:**
   None

**Security information**
   * Access control mechanism (authentication & authorization):
      * Authentication: SSL/TLS client authentication on the service ports
      * Authorization: PAP uses access control list
   * How to block/ban a user
   * Network Usage
   * Firewall configuration
   * Security recommendations
   * Security incompatibilities
   * List of externals (packages are NOT maintained by Red Hat)
   * Other security relevant comments

**Utility scripts:**
   * ``/usr/bin/pap-admin`` CLI to manage the PAP policies
   * ``/etc/init.d/argus-pdp reloadpolicy`` forces the PDP to reload the policies from the PAP
   * ``/etc/init.d/argus-pepd clearcache`` clears the PEP daemon response cache

**Location of reference documentation for users:**
   Not applicable

**Location of reference documentation for administrators:**
   * *General documentation:* :ref:`argus_introduction`
   * *PAP admin CLI:* :ref:`argus_pap_cli`
   * *Simplified Policy Language:* :ref:`argus_simplified_policy_language`

