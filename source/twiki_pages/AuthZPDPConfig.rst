%META:TOPICINFO{author="litmaath" date="1423676969" format="1.1"
version="1.9"}% %META:TOPICPARENT{name="AuthorizationFramework"}%

Argus Policy Decision Point (PDP): Configuration
================================================

Configuration File Syntax
-------------------------

The PDP is configured through the use of the ``pdp.ini`` file. This file
is a `standard INI file <AuthZINIFile>`__ with three defined sections.
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

+-----------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------+----------------------------------------------------------------------------------------------------------------+
| Property        | Description                                                                                                                                                                                                                           | Required?   | Default Value                                                                                                  |
+=================+=======================================================================================================================================================================================================================================+=============+================================================================================================================+
| entityID        | This is a unique identifier for the PDP. It must be a URI (URL or URN) and the same entity ID should be used for all PDP instances that make up a single logical PDP. If a URL is used it need not resolve to any specific webpage.   | Y           | None. Recommended value is a URL corresponding to the logical PDP service (e.g. ``http://pdp.example.org``).   |
+-----------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------+----------------------------------------------------------------------------------------------------------------+
| hostname        | This is the hostname or IP address to which the service will bind.                                                                                                                                                                    | Y           | None.                                                                                                          |
+-----------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------+----------------------------------------------------------------------------------------------------------------+
| port            | This is the port to which the service will bind.                                                                                                                                                                                      | N           | 8152                                                                                                           |
+-----------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------+----------------------------------------------------------------------------------------------------------------+
| adminHost       | The hostname upon which the service will listen for admin commands.                                                                                                                                                                   | N           | 127.0.0.1                                                                                                      |
+-----------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------+----------------------------------------------------------------------------------------------------------------+
| adminPort       | This is the port upon which the service will listen for admin command.                                                                                                                                                                | N           | 8153                                                                                                           |
+-----------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------+----------------------------------------------------------------------------------------------------------------+
| adminPassword   | This is the password required to accompany admin commands. If unspecified than no password is required to run admin commands.                                                                                                         | N           | None                                                                                                           |
+-----------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------+----------------------------------------------------------------------------------------------------------------+

POLICY section
~~~~~~~~~~~~~~

+---------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------+-----------------+
| Property            | Description                                                                                                                                                                                                                                                                                           | Required?   | Default Value   |
+=====================+=======================================================================================================================================================================================================================================================================================================+=============+=================+
| paps                | A space separated list of PAP endpoint URLs. Endpoints will be tried in turn until one returns a successful response. This provides limited failover support. If more intelligent failover is necessary or load balancing is required, a dedicated load-balancer/failover appliance should be used.   | Y           | None            |
+---------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------+-----------------+
| retentionInterval   | The number of minutes the PDP will retain (cache) a policy retrieved from the PAP. After this time is passed the PDP will again call out to the PAP and retrieve the policy.                                                                                                                          | N           | 240 (4 hours)   |
+---------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------+-----------------+

SECURITY section
~~~~~~~~~~~~~~~~

\| **Property** \| **Description** \| **Required?** \| **Default Value**
\| \| servicePrivateKey \| An absolute path to the file containing the
unencrypted, PEM-encoded, private key used by this service. All PDPs
instances within a single logical PDP should use the same key. \| Y \|
None. \| \| serviceCertificate \| An absolute path to the file
containing the unencrypted, PEM-encoded, certifcate used by this
service. All PDPs instances within a single logical PDP should use the
same key. \| Y \| None. \| \| trustInfoDir \| An absolute path to the
directory that contains standard X.509 trust information, such as the
IGTF Trust Anchor Distribution. \| Y \| None \| \| enableSSL \|
Indicates whether the service port should use SSL/TLS or not. \| N \|
false \| \| requireClientCertAuthentication \| Indicates whether the
client must use a valid client certificate to authenticate to the PDP \|
N \| false \|

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

POLICY section
~~~~~~~~~~~~~~

\| **Property** \| **Description** \| **Required?** \| **Default Value**
\| \| policySetId \| The ID of the policy to fetch from the PAP. \| N \|
-1 \| \| connectionTimeout \| This is the length of time, in seconds,
the PAP client will wait for the PAP to send information before it
considers the request timed out. \| N \| 30 seconds \| \|
receiveBufferSize \| This is the size, in bytes, that will be allocated
to the PAP client send buffer. \| N \| 16384 (16kb) \| \| sendBufferSize
\| This is the size, in bytes, that will be allocated to the PAP client
request buffer. \| N \| 16384 (16kb) \|

SECURITY section
~~~~~~~~~~~~~~~~

+-------------------------+-----------------------------------------------------------------------------------------------------------------+-------------+--------------------+
| Property                | Description                                                                                                     | Required?   | Default Value      |
+=========================+=================================================================================================================+=============+====================+
| trustInfoRefresh        | The frequency, in minutes, that the trust material specified by ``trustInfoDir`` will be checked for updates.   | N           | 60 (1 hour)        |
+-------------------------+-----------------------------------------------------------------------------------------------------------------+-------------+--------------------+
| messageValidityPeriod   | The number of seconds, from the time a message is issued, until it is considered expired.                       | N           | 300s (5 minutes)   |
+-------------------------+-----------------------------------------------------------------------------------------------------------------+-------------+--------------------+
| clockSkew               | The allowance, in seconds, used when computing validity periods.                                                | N           | 30s                |
+-------------------------+-----------------------------------------------------------------------------------------------------------------+-------------+--------------------+
