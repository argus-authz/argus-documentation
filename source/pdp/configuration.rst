.. _argus_pdp_configuration:

Argus Policy Decision Point (PDP): Configuration
================================================

Configuration Files
-------------------
The PDP is configured through the use of the ``pdp.ini`` file,
located in ``/etc/argus/pdp`` directory.

There is also an :ref:`environment file <argus-pdp-conf-env-file>`,
located in ``/etc/sysconfig/argus-pdp``,
that contains environment variables used by the init script that manage the
service.


Configuration File Syntax
-------------------------

The ``pdp.ini`` file is a standard INI file with three defined sections.
The ``SERVICE`` section contains properties related the PDP service as a
whole and how it listens for incoming requests. The ``POLICY`` section
contains properties for the retrieval of policies from the Policy
Administration Point (PAP). The final section, ``SECURITY``, contains
properties that related to various security aspects of the service, the
services private key and certificate, for example.

Basic Configuration Options
---------------------------

SERVICE section
~~~~~~~~~~~~~~~

+---------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------+--------------------------------------------------------------------------------------------------------------+
| Property      | Description                                                                                                                                                                                                                         | Required? | Default Value                                                                                                |
+===============+=====================================================================================================================================================================================================================================+===========+==============================================================================================================+
| entityID      | This is a unique identifier for the PDP. It must be a URI (URL or URN) and the same entity ID should be used for all PDP instances that make up a single logical PDP. If a URL is used it need not resolve to any specific webpage. | Y         | None. Recommended value is a URL corresponding to the logical PDP service (e.g. ``http://pdp.example.org``). |
+---------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------+--------------------------------------------------------------------------------------------------------------+
| hostname      | This is the hostname or IP address to which the service will bind.                                                                                                                                                                  | Y         | None.                                                                                                        |
+---------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------+--------------------------------------------------------------------------------------------------------------+
| port          | This is the port to which the service will bind.                                                                                                                                                                                    | N         | 8152                                                                                                         |
+---------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------+--------------------------------------------------------------------------------------------------------------+
| adminHost     | The hostname upon which the service will listen for admin commands.                                                                                                                                                                 | N         | 127.0.0.1                                                                                                    |
+---------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------+--------------------------------------------------------------------------------------------------------------+
| adminPort     | This is the port upon which the service will listen for admin command.                                                                                                                                                              | N         | 8153                                                                                                         |
+---------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------+--------------------------------------------------------------------------------------------------------------+
| adminPassword | This is the password required to accompany admin commands. If unspecified than no password is required to run admin commands.                                                                                                       | N         | None                                                                                                         |
+---------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------+--------------------------------------------------------------------------------------------------------------+

POLICY section
~~~~~~~~~~~~~~

+-------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------+---------------+
| Property          | Description                                                                                                                                                                                                                                                                                         | Required? | Default Value |
+===================+=====================================================================================================================================================================================================================================================================================================+===========+===============+
| paps              | A space separated list of PAP endpoint URLs. Endpoints will be tried in turn until one returns a successful response. This provides limited failover support. If more intelligent failover is necessary or load balancing is required, a dedicated load-balancer/failover appliance should be used. | Y         | None          |
+-------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------+---------------+
| retentionInterval | The number of minutes the PDP will retain (cache) a policy retrieved from the PAP. After this time is passed the PDP will again call out to the PAP and retrieve the policy.                                                                                                                        | N         | 240 (4 hours) |
+-------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------+---------------+

SECURITY section
~~~~~~~~~~~~~~~~

.. list-table::
    :header-rows: 1

    *
        - Property
        - Description
        - Required
        - Default Value

    *
        - servicePrivateKey
        - An absolute path to the file containing the
          unencrypted, PEM-encoded, private key used by this service. All PDPs
          instances within a single logical PDP should use the same key
        - Yes
        - None

    *
        - serviceCertificate
        - An absolute path to the file containing the unencrypted, PEM-encoded, certificate used by this
          service. All PDPs instances within a single logical PDP should use the same key
        - Yes
        - None

    *
        - trustInfoDir
        - An absolute path to the directory that contains standard X.509 trust information, such as the
          IGTF Trust Anchor Distribution
        - Yes
        - None

    *
        - enableSSL
        - Indicates whether the service port should use SSL/TLS or not
        - No
        - false

    *
        - tlsProtocol
        - Which TLS protocol should be used whent HTTPS is enabled. Available values: TLS (default), TLSv1.2, TLSv1.1.
        - No
        - TLS

    *
        - enabledProtocols
        - Specifies the TLS protocol versions to be enabled for use on the connection. The standard names that can be passed are, for example: TLSv1.2, TLSv1.1 and TLSv1.
        - No
        - None

    *
        - requireClientCertAuthentication
        - Indicates whether the client must use a valid client certificate to authenticate to the PDP
        - No
        - false


Example pdp.ini files
~~~~~~~~~~~~~~~~~~~~~

The following example file contain the bare minimum required for a valid
PDP configuration file.

::

    [SERVER]
    entityID = http://argus.example.org/pdp
    hostname = argus.example.org

    [POLICY]
    paps = https://argus.example.org:8150/pap/services/ProvisioningService

    [SECURITY]
    servicePrivateKey = /etc/grid-security/hostkey.pem
    serviceCertificate = /etc/grid-security/hostcert.pem
    trustInfoDir = /etc/grid-security/certificates
    # HTTPS enabled
    enableSSL = true
    tlsProtocol = TLS

Advanced Configuration Options
------------------------------

The following advanced options are available but are unlikely to ever be
used by deployers. They are mostly for performing very fine-grained
tuning of request/response handling parameters. Incorrectly configuring
these can have a very negative impact on performance so deployers should
not change these unless they are very sure they understand what the
impact will be.

SERVICE section
~~~~~~~~~~~~~~~

+-------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------+---------------+
| Property          | Description                                                                                                                                                                                                           | Required? | Default Value |
+===================+=======================================================================================================================================================================================================================+===========+===============+
| maximumRequests   | The maximum number of requests that will be processed simultaneously. Additional requests will be queued.                                                                                                             | N         | 200           |
+-------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------+---------------+
| requestQueueSize  | The maximum number of requests that will be queued up when all the processing threads are busy. Incoming requests received when all processing threads are busy and the queue is full will receive an HTTP 503 error. | N         | 500           |
+-------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------+---------------+
| connectionTimeout | This is the length of time, in seconds, the service will wait for the client to send information before it considers the request timed out.                                                                           | N         | 30 seconds    |
+-------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------+---------------+
| receiveBufferSize | This is the size, in bytes, that will be allocated to the HTTP request buffer.                                                                                                                                        | N         | 16384 (16kb)  |
+-------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------+---------------+
| sendBufferSize    | This is the size, in bytes, that will be allocated to the HTTP response buffer.                                                                                                                                       | N         | 16384 (16kb)  |
+-------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------+---------------+

POLICY section
~~~~~~~~~~~~~~

.. list-table::
    :header-rows: 1

    *
        - Property
        - Description
        - Required
        - Default Value

    *
        - policySetId
        - The ID of the policy to fetch from the PAP
        - No
        - -1

    *
        - connectionTimeout
        - This is the length of time, in seconds,  the PAP client will wait for the PAP to send information before it
          considers the request timed out
        - None
        - 30

    *
        - receiveBufferSize
        - This is the size, in bytes, that will be allocated to the PAP client send buffer
        - No
        - 16384 (16KB)

    *
        - sendBufferSize
        - This is the size, in bytes, that will be allocated to the PAP client request buffer
        - No
        - 16384 (16KB)


SECURITY section
~~~~~~~~~~~~~~~~

+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------+------------------+
| Property              | Description                                                                                                                                                                       | Required? | Default Value    |
+=======================+===================================================================================================================================================================================+===========+==================+
| trustInfoRefresh      | The frequency, in minutes, that the trust material specified by ``trustInfoDir`` will be checked for updates.                                                                     | N         | 60 (1 hour)      |
+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------+------------------+
| messageValidityPeriod | The number of seconds, from the time a message is issued, until it is considered expired.                                                                                         | N         | 300s (5 minutes) |
+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------+------------------+
| clockSkew             | The allowance, in seconds, used when computing validity periods.                                                                                                                  | N         | 30s              |
+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------+------------------+
| enableSSL             | Enable HTTPS on the service port (SSL/TLS). The ``serviceCertificate``, ``servicePrivateKey``, and ``trustInfoDir`` properties must also be defined in order to use this setting. | N         | false            |
+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------+------------------+
| tlsProtocol           | Which TLS protocol should be used whent HTTPS is enabled. Available values: TLS (default), TLSv1.2, TLSv1.1.                                                                      | N         | TLS              |
+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------+------------------+
| enabledProtocols      | Specifies the TLS protocol versions to be enabled for use on the connection. The standard names that can be passed are, for example: TLSv1.2, TLSv1.1 and TLSv1                   | N         | None             |
+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------+------------------+


.. _argus-pdp-conf-env-file:

Environment file
----------------

In the ``/etc/sysconfig/argus-pdp`` file are defined Argus PDP environment variables,
described in the table below.

   ===================   ============================================   =========
   Variable              Default value                                  Meaning
   ===================   ============================================   =========
   ``JAVACMD``           ``/usr/bin/java``                              Absolute path of the JVM executable.
   ``PDP_JOPTS``         ``-Xmx256M -Djdk.tls.trustNameService=true``   Optional parameters to pass to the JVM when PDP is started/stopped
   ``PDP_START_JOPTS``   empty                                          Optional parameters to pass to the JVM only when PDP is started; useful to enable JMX or remote debug
   ``PDP_HOME``          ``/usr/share/argus/pdp``                       Absolute path of PDP installation directory
   ``PDP_CONF``          ``/etc/argus/pdp/pdp.ini``                     Absolute path of PDP configuration file.
   ``PDP_CONFDIR``       ``/etc/argus/pdp``                             Absolute path of PDP configuration directory.
   ``PDP_LOGDIR``        ``/var/log/argus/pdp``                         Absolute path of PDP logs directory.
   ``PDP_LIBDIR``        ``/var/lib/argus/pdp/lib``                     Absolute path of PDP libraries.
   ``PDP_ENDORSEDDIR``   ``/var/lib/argus/pdp/lib/endorsed``            Absolute path of PDP endorsed libraries.
   ``PDP_PROVIDEDDIR``   ``/var/lib/argus/pdp/lib/provided``            Absolute path of PDP provided libraries.
   ``PDP_PID``           ``/var/run/argus-pdp.pid``                     Absolute path of PDP PID file.
   ===================   ============================================   =========

.. attention::
   | The option ``-Djdk.tls.trustNameService=true`` is mandatory in Argus version 1.7 with TLS turned on.
   | This system property enforce the host name check to avoid JDK bug https://bugs.openjdk.java.net/browse/JDK-8133196.

.. attention::
   | The Argus version 1.7 provides an environment file with the default values described above.
   | In case of update from the previous version, a new file named ``/etc/sysconfig/argus-pdp.rpmnew`` will be created.
   | Manually overwrite the file and restart the service.


