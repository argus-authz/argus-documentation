.. _argus_pepd_configuration:

Argus PEP Server: Configuration
===============================

Configuration File
------------------

The PEP is configured through the user of the ``pepd.ini`` file. This
file is a standard INI file with different defined
sections. The ``SERVICE`` section contains properties related the PEP
Server service as a whole and how it listens for incoming requests. The
``PDP`` section that control how callouts to the PDP are made. The final
section, ``SECURITY``, contains properties that related to various
security aspects of the service, the services private key and
certificate, for example.

PIP Configuration
~~~~~~~~~~~~~~~~~

Each Policy Information Point (PIP) is configured in its own INI
section, referenced in the ``SERVICE`` section under the ``pips``
property.

The configuration properties needed by each PIP is specific to that
given PIP. Some will not require any properties beyond the standard set
while other will need addition information.

-  See the :ref:`Policy Information Point <argus_pep_pip>` documentation for
   more information:

   - :ref:`Request Validator PIP <argus_pep_pip_request_validator>`
   - :ref:`OpenSSL Subject Converter PIP <argus_pep_pip_subject_converter>`
   - :ref:`gLite Grid Authorization Profile PIP <argus_pep_pip_grid_authz_profile>`
   - :ref:`Common XACML Authorization Profile PIP <argus_pep_pip_common_xacml_authz_profile>`

Obligation Handler Configuration
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Each :ref:`Obligation Handler <argus_pep_obligation_handlers>` (OH) is configured in its own INI section,
referenced in the ``SERVICE`` section under the ``obligationHandlers``
property.

The configuration properties needed by each Obligation Handler is
specific to that given OH. Some will not require any properties beyond
the standard set while other will need addition information.

See the :ref:`Obligation Handler <argus_pep_obligation_handlers>` documentation for more
information, in particular the :ref:`Gridmap Account Mapping Obligation Handler <argus_pep_gridmap_obligation_handler>`.

Basic Configuration Options
---------------------------

SERVICE section
~~~~~~~~~~~~~~~

+----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------+----------------------------------------------------------------------------------------------------------------+
| Property             | Description                                                                                                                                                                                                                                                                                                         | Required?   | Default Value                                                                                                  |
+======================+=====================================================================================================================================================================================================================================================================================================================+=============+================================================================================================================+
| entityID             | This is a unique identifier for the PEP. It must be a URI (URL or URN) and the same entity ID should be used for all PEP instances that make up a single logical PEP. If a URL is used it need not resolve to any specific webpage.                                                                                 | Y           | None. Recommended value is a URL corresponding to the logical PEP service (e.g. ``http://pep.example.org``).   |
+----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------+----------------------------------------------------------------------------------------------------------------+
| hostname             | This is the hostname or IP address to which the service will bind.                                                                                                                                                                                                                                                  | Y           | None                                                                                                           |
+----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------+----------------------------------------------------------------------------------------------------------------+
| port                 | This is the port to which the service will bind.                                                                                                                                                                                                                                                                    | N           | 8154                                                                                                           |
+----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------+----------------------------------------------------------------------------------------------------------------+
| adminHost            | The hostname upon which the service will listen for admin commands.                                                                                                                                                                                                                                                 | N           | 127.0.0.1                                                                                                      |
+----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------+----------------------------------------------------------------------------------------------------------------+
| adminPort            | This is the port upon which the service will listen for admin commands. This port is only available on the localhost (127.0.0.1).                                                                                                                                                                                   | N           | 8155                                                                                                           |
+----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------+----------------------------------------------------------------------------------------------------------------+
| adminPassword        | This is the password required to accompany admin commands. If unspecified than no password is required to run admin commands.                                                                                                                                                                                       | N           | None                                                                                                           |
+----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------+----------------------------------------------------------------------------------------------------------------+
| pips                 | This is a space separated list of the INI section names that configure policy information points (PIP) that the PEP will invoke upon the arrival of every request. PIPs are executed in the order listed by this property. See the `policy information point <AuthZPEPPIP>`__ documentation for more information.   | N           | None                                                                                                           |
+----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------+----------------------------------------------------------------------------------------------------------------+
| obligationHandlers   | This is a space separated list of the INI section names that configure obligations handlers that the PEP will use to fulfill obligation requirements sent back by the PDP. See the :ref:`obligation handler <argus_pep_obligation_handlers>` documentation for more information.                                    | N           | None                                                                                                           |
+----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------+----------------------------------------------------------------------------------------------------------------+

PDP section
~~~~~~~~~~~

+--------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------+-----------------+
| Property                 | Description                                                                                                                                                                                                                                                                                           | Required?   | Default Value   |
+==========================+=======================================================================================================================================================================================================================================================================================================+=============+=================+
| pdps                     | A space separated list of PDP endpoint URLs. Endpoints will be tried in turn until one returns a successful response. This provides limited failover support. If more intelligent failover is necessary or load balancing is required, a dedicated load-balancer/failover appliance should be used.   | Y           | None            |
+--------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------+-----------------+
| maximumCachedResponses   | The maximum number of responses from any PDP that will be cached. Setting this value to 0 (zero) will disable caching. The maximum amount of time a single response is cached is controlled by the ``cachedResponseTTL`` property described below.                                                    | N           | 500             |
+--------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------+-----------------+

SECURITY section
~~~~~~~~~~~~~~~~

+-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------+-----------------+
| Property                          | Description                                                                                                                                                                         | Required?                                                         | Default Value   |
+===================================+=====================================================================================================================================================================================+===================================================================+=================+
| servicePrivateKey                 | An absolute path to the file containing the unencrypted, PEM-encoded, private key used by this service.                                                                             | Yes, if requests from the PEP client should be done over HTTPS.   | None.           |
+-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------+-----------------+
| serviceCertificate                | An absolute path to the file containing the unencrypted, PEM-encoded, certificate used by this service.                                                                             | Yes, if requests from the PEP client should be done over HTTPS.   | None.           |
+-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------+-----------------+
| trustInfoDir                      | An absolute path to the directory that contains standard X.509 trust information, such as the IGTF Trust Anchor Distribution.                                                       | Required when connecting to PDPs over HTTPS.                      | None            |
+-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------+-----------------+
| enableSSL                         | Enable HTTPS on the service port (SSL/TLS). The ``serviceCertificate``, ``servicePrivateKey``, and ``trustInfoDir`` properties must also be defined in order to use this setting.   | N                                                                 | false           |
+-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------+-----------------+
| requireClientCertAuthentication   | The client must have a valid X.509 client certificate to authenticate to the PEP Server                                                                                             | N                                                                 | true            |
+-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------+-----------------+

Example pepd.ini files
~~~~~~~~~~~~~~~~~~~~~~

The following example file contain the bare minimum required for a valid
PEP configuration file.

::

    [SERVICE]
    entityId = https://argus.example.org/pep
    hostname = argus.example.org

    [PDP]
    pdps = https://argus.example.org:8152/authz

    [SECURITY]

The following example file contains the bare minimum required for a
valid PEP configuration plus the configuration of a couple PIPs. Note
how each element in the list ``pips`` list of the ``SERVER`` section
matches the name section configuring the PIP. Also note that the
``REQVALIDATOR_PIP`` takes a few additional configuration parameters.

::

    [SERVICE]
    entityID = http://argus.example.org/pep
    hostname = argus.example.org
    pips = REQVALIDATOR_PIP 

    [PDP]
    pdps = https://argus.example.org:8152/authz https://pdp2.example.org:8152/authz

    [SECURITY]
    servicePrivateKey = /etc/grid-security/hostkey.pem
    serviceCertificate = /etc/grid-security/hostcert.pem
    trustInfoDir = /etc/grid-security/certificates
    enableSSL = true
    requireClientCertAuthentication = true

    [REQVALIDATOR_PIP]
    parserClass = org.glite.authz.pep.pip.provider.RequestValidatorPIPIniConfigurationParser
    validateRequestSubjects = true
    validateRequestResources = true
    validateRequestAction = true
    validateRequestEnvironment = false

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

+---------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------+-----------------+
| Property            | Description                                                                                                                                                                                                             | Required?   | Default Value   |
+=====================+=========================================================================================================================================================================================================================+=============+=================+
| maximumRequests     | The maximum number of requests that will be processed simultaneously. Additional requests will be queued.                                                                                                               | N           | 200             |
+---------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------+-----------------+
| requestQueueSize    | The maximum number of requests that will be queued up when all the processing threads are busy. Incoming requests received when all processing threads are busy and the queue is full will receive an HTTP 503 error.   | N           | 500             |
+---------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------+-----------------+
| connectionTimeout   | This is the length of time, in seconds, the service will wait for the client to send information before it considers the request timed out.                                                                             | N           | 30 seconds      |
+---------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------+-----------------+
| receiveBufferSize   | This is the size, in bytes, that will be allocated to the HTTP request buffer.                                                                                                                                          | N           | 16384 (16kb)    |
+---------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------+-----------------+
| sendBufferSize      | This is the size, in bytes, that will be allocated to the HTTP response buffer.                                                                                                                                         | N           | 16384 (16kb)    |
+---------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------+-----------------+

PDP section
~~~~~~~~~~~

.. list-table::
    :header-rows: 1

    *
        - Property
        - Description
        - Required
        - Default Value

    *
        - maximumRequests
        - The maximum number of simultaneous requests that will be made to the PDP. Additional requests will wait
          until a free request slot becomes available
        - No
        - 200

    *
        - cachedResponseTTL
        - The length of time, in seconds, for which a response will be cached
        - No
        - 600 (10 minutes)

    *
        - connectionTimeout
        - This is the length of time, in seconds, the PDP client will wait for the PDP to send
          information before it considers the request timed out
        - No
        - 30

    *
        - receiveBufferSize
        - This is the size, in bytes, that will be allocated to the PDP client send buffer
        - None
        - 16384 (16 KB)

    *
        - sendBufferSize
        - This is the size, in bytes, that will be allocated to the PDP client request buffer
        - None
        - 16384 (16 KB)

SECURITY section
~~~~~~~~~~~~~~~~

+--------------------+-----------------------------------------------------------------------------------------------------------------+-------------+-----------------+
| Property           | Description                                                                                                     | Required?   | Default Value   |
+====================+=================================================================================================================+=============+=================+
| trustInfoRefresh   | The frequency, in minutes, that the trust material specified by ``trustInfoDir`` will be checked for updates.   | N           | 60 (1 hour)     |
+--------------------+-----------------------------------------------------------------------------------------------------------------+-------------+-----------------+
