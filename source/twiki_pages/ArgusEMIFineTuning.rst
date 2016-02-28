
Argus Services (EMI-1) Fine Tuning
==================================

Optimizing Argus Memory Usage
-----------------------------

The **Argus 1.3** services (PAP, PDP and PEP Server) don't have fine
tuned memory limits. For production sites it is therefore recommended to
manually set a memory limit for each service.

**NOTICE:** Starting with **Argus 1.4**, the memory limits are already
set, but you can adapt them for your special need.

Argus PAP Memory
~~~~~~~~~~~~~~~~

In the PAP, the memory usage of the PAP depends directly on the number
of policies.

-  Default memory limit: 512 MB (v.1.4), 256MB (v.1.5)

   -  **No** default memory limit set for version lower than v.1.4

-  The default memory limit is typically enough for:

   -  ~2'000 policies (resource - action - obligation - rule)
   -  ~20'000 rules in one policy (~20'000 DN's or FQAN's)

**PAP Memory Setting for Production**

For production sites, which typically have less than 500 policies, we
recommend to set the PAP memory limit to 256 MB

-  **Recommended** memory limit (assuming less than 500 policies): 256
   MB
-  The value can be changed by setting the variable
   ``PAP_JAVA_OPTS="-Xmx256m"`` in the ``/etc/sysconfig/argus-pap``
   configuration file

You have to restart the Argus PAP service to apply the new memory limit
settings.

Argus PDP Memory
~~~~~~~~~~~~~~~~

The PDP memory usage depends on the number of policies it has to import
from the PAP.

-  Default memory limit: 256 MB (v.1.4)

   -  **No** default memory limit set for version lower than v.1.4

-  The needed memory is comparable with the memory needed by the PAP

**PDP Memory Setting for Production**

For production sites, it is highly recommended to set a memory limit, to
avoid a growing of the used memory (up to 1 GB) by the PDP.

-  **Recommended** memory limit (assuming less than 500 policies in the
   PAP): 256 MB
-  The value can be changed by setting the variable
   ``PDP_JOPTS="-Xmx256M"`` in the ``/etc/sysconfig/argus-pdp``
   configuration file

You have to restart the Argus PDP service to apply the new memory limit
settings.

Argus PEP Server Memory
~~~~~~~~~~~~~~~~~~~~~~~

The PEP Server memory usage depends on the cache-size.

-  Default memory limit: 512MB (v.1.3.1) and 128MB (v.1.4), and 256MB
   (v.1.5)

   -  **No** default memory limit set for version lower than v.1.3.1

-  Default value for the cache size: max. 500 cached PDP responses

The value for the cache size (``maximumCachedResponses``) can be changed
following the `PEP Server Configuration <AuthZPEPDConfig#PDP_section>`__
documentation.

**PEP Server Memory Setting for Production**

The default memory limit set for the PEP Server v1.3.1 is still rather
high, It can safely be lowered without any degradation of performance,
and as long as the cache-size is not increased. For PEP Server version
lower than v1.3.1, it is highly recommended to set a memory limit, to
avoid a growing of the used memory (up to 1 GB).

-  **Recommended** memory limit: 256 MB

   -  The previous memory limit of 128MB in Argus 1.4.0 was too low for
      site with a lot of pool accounts.

-  The value can be changed by setting the variable
   ``PEPD_JOPTS="-Xmx256M"`` in the ``/etc/sysconfig/argus-pepd``
   configuration file

You have to restart the Argus PEP Server service to apply the new memory
limit settings.

Production Logging Settings
---------------------------

By default the Argus 1.3 (EMI-1) services don't have production specific
logging configuration, therefore, for production sites it is recommended
to manually configure the logging for each service.

**NOTICE:** Starting with **Argus 1.4**, the services already use these
production logging configuration.

For production sites, we have define the following logging policy:

-  Log files rotation:

   -  daily rotating (at midnight)
   -  or when the file size >= 100MB

-  Rotated log files are gzipped
-  History of log files keep for 90 days

The Argus services uses the `Logback Logging
Framework <http://logback.qos.ch/manual/index.html>`__ to do their
logging. The logback framework can be configured to implement the
production logging policy.

Argus PAP Logging
~~~~~~~~~~~~~~~~~

The Argus PAP service logging configuration is configured with the
``/etc/argus/pap/logging/standalone/logback.xml`` file.

**PAP Logging Configuration for Production**

To configure the Argus PAP service for production logging, please
download the attached `configuration
file <%ATTACHURL%/pap-logback.xml>`__ and store it as
``/etc/argus/pap/logging/standalone/logback.xml``:

``wget --no-check-certificate`` %ATTACHURL%/pap-logback.xml
``-O /etc/argus/pap/logging/standalone/logback.xml``

You have to restart the PAP service to apply the new logging
configuration.

Argus PDP Logging
~~~~~~~~~~~~~~~~~

The Argus PDP service logging configuration is configured with the
``/etc/argus/pdp/logging.xml`` file.

**PDP Logging Configuration for Production**

To configure the Argus PDP service for production logging, please
download the attached `configuration
file <%ATTACHURL%/pdp-logging.xml>`__ and store it as
``/etc/argus/pdp/logging.xml``:

``wget --no-check-certificate`` %ATTACHURL%/pdp-logging.xml
``-O /etc/argus/pdp/logging.xml``

The PDP will automatically reload its logging configuration, no need to
restart the service.

Argus PEP Server Logging
~~~~~~~~~~~~~~~~~~~~~~~~

The Argus PEP Server service logging configuration is configured with
the ``/etc/argus/pepd/logging.xml`` file.

**PEP Server Logging Configuration for Production**

To configure the Argus PEP Server service for production logging, please
download the attached `configuration
file <%ATTACHURL%/pepd-logging.xml>`__ and store it as
``/etc/argus/pepd/logging.xml``:

``wget --no-check-certificate`` %ATTACHURL%/pepd-logging.xml
``-O /etc/argus/pepd/logging.xml``

The PEP Server will automatically reload its logging configuration, no
need to restart the service.

%META:FILEATTACHMENT{name="pap-logback.xml" attachment="pap-logback.xml"
attr="" comment="PAP logging config" date="1311669537"
path="pap-logback.xml" size="2157" stream="pap-logback.xml"
tmpFilename="/usr/tmp/CGItemp26785" user="ad968f62f612332eff6b"
version="3"}% %META:FILEATTACHMENT{name="pdp-logging.xml"
attachment="pdp-logging.xml" attr="" comment="PDP logging config"
date="1311669557" path="pdp-logging.xml" size="5541"
stream="pdp-logging.xml" tmpFilename="/usr/tmp/CGItemp26834"
user="ad968f62f612332eff6b" version="3"}%
%META:FILEATTACHMENT{name="pepd-logging.xml"
attachment="pepd-logging.xml" attr="" comment="PEP Server logging
config" date="1311669570" path="pepd-logging.xml" size="5067"
stream="pepd-logging.xml" tmpFilename="/usr/tmp/CGItemp26858"
user="ad968f62f612332eff6b" version="3"}%
