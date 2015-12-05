Argus Policy Administration Point (PAP): Configuration
======================================================

Configuration Files
-------------------

The PAP is configured through the use of two files:
``pap_configuration.ini`` and ``pap_authorization.ini``, located in the
``/etc/argus/pap`` (EMI), or ``$PAP_HOME/conf`` (gLite), directory. Most
of the information contained in these files can also be set through the
command line interface (which is the **recommended** way to do
configuration on the PAP).

Service configuration file
--------------------------

The service is primarily configured through the
``pap_configuration.ini`` configuration file. This file is a `standard
INI file <AuthZINIFile>`__ with five defined sections.

Section: [paps]
~~~~~~~~~~~~~~~

This section contains configuration about paps. The information in this
section should be set via the command line interface (see the
`#Pap\_management\_commands <#Pap_management_commands>`__ commands
group).

A paps can be defined by providing the following information (the R
value in the Required? column indicates information that is required
only for remote paps):

+--------------------+---------------------------------------------------------------------------------------------------+-------------+-----------------+
| Property           | Description                                                                                       | Required?   | Default Value   |
+====================+===================================================================================================+=============+=================+
| *alias*.type       | Defines a pap as ``local`` or ``remote`` .                                                        | Y           | None            |
+--------------------+---------------------------------------------------------------------------------------------------+-------------+-----------------+
| *alias*.public     | Visibility of the pap: ``true`` or ``false``. If false its policies are not sent to other paps.   | N           | false           |
+--------------------+---------------------------------------------------------------------------------------------------+-------------+-----------------+
| *alias*.enabled    | ``true`` or ``false``. If false its policies are not sent to PDPs.                                | N           | false           |
+--------------------+---------------------------------------------------------------------------------------------------+-------------+-----------------+
| *alias*.dn         | DN of the PAP to get policies from.                                                               | R           | None            |
+--------------------+---------------------------------------------------------------------------------------------------+-------------+-----------------+
| *alias*.hostname   | Hostname of the PAP to get policies from.                                                         | R           | None            |
+--------------------+---------------------------------------------------------------------------------------------------+-------------+-----------------+
| *alias*.port       | Port of the PAP to get policies from.                                                             | N           | 8150            |
+--------------------+---------------------------------------------------------------------------------------------------+-------------+-----------------+
| *alias*.path       | Path of the services exposed by the PAP to get policies from.                                     | N           | /pap/services   |
+--------------------+---------------------------------------------------------------------------------------------------+-------------+-----------------+
| *alias*.protocol   | Protocol to use to contact the remote PAP.                                                        | N           | https           |
+--------------------+---------------------------------------------------------------------------------------------------+-------------+-----------------+

Section: [paps:properties]
~~~~~~~~~~~~~~~~~~~~~~~~~~

This section contains information about policy distribution and pap
ordering.

+------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------+-----------------------------------------------------------------+
| Property         | Description                                                                                                                                                                                                                                                       | Required?   | Default Value                                                   |
+==================+===================================================================================================================================================================================================================================================================+=============+=================================================================+
| poll\_interval   | The polling interval (in seconds) for retrieving remote policies.                                                                                                                                                                                                 | Y           | None. Recommended value is 14400 (4 hours).                     |
+------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------+-----------------------------------------------------------------+
| ordering         | Comma separated list of pap aliases. Example: alias-1, alias-2, ..., alias-n. Defines the order of evaluation of the policies of the paps, that means that the policies of pap "alias-1" are evaluated for first, then the policies of pap "alias-2" and so on.   | N           | If not specified the ``default`` pap is always the first one.   |
+------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------+-----------------------------------------------------------------+

Section: [repository]
~~~~~~~~~~~~~~~~~~~~~

This section contains information about the PAP policy repository.

+-----------------------------+------------------------------------------------------------------------------------------------------------------------------------+-------------+-------------------------+
| Property                    | Description                                                                                                                        | Required?   | Default Value           |
+=============================+====================================================================================================================================+=============+=========================+
| location                    | Path to the repository directory.                                                                                                  | N           | $PAP\_HOME/repository   |
+-----------------------------+------------------------------------------------------------------------------------------------------------------------------------+-------------+-------------------------+
| consistency\_check          | Forces a consistency check of the repository at startup.                                                                           | N           | false                   |
+-----------------------------+------------------------------------------------------------------------------------------------------------------------------------+-------------+-------------------------+
| consistency\_check.repair   | if set to *true* automatically fixes problems detected by the consistency check (usually means deleting the corrupted policies).   | N           | false                   |
+-----------------------------+------------------------------------------------------------------------------------------------------------------------------------+-------------+-------------------------+

Section: [standalone-service]
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

This section contains information about the PAP standalone service.

+---------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------+--------------------------------------------------------------------------------------------+
| Property            | Description                                                                                                                                                                                                                                                                                                                                                             | Required?   | Default Value                                                                              |
+=====================+=========================================================================================================================================================================================================================================================================================================================================================================+=============+============================================================================================+
| hostname            | The hostname or IP address the service will bind to                                                                                                                                                                                                                                                                                                                     | N           | 127.0.0.1                                                                                  |
+---------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------+--------------------------------------------------------------------------------------------+
| port                | The service port number                                                                                                                                                                                                                                                                                                                                                 | N           | 8150                                                                                       |
+---------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------+--------------------------------------------------------------------------------------------+
| shutdown\_port      | The service shutdown port number                                                                                                                                                                                                                                                                                                                                        | N           | 8151                                                                                       |
+---------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------+--------------------------------------------------------------------------------------------+
| shutdown\_command   | The command string that must be received on the shutdown port in order to shutdown the service. The command is needed in order to prevent unauthorized shutdown commands coming from localhost. This is effective only if the pap\_configuration.ini file is not world-readable. If the option is not present in configuration, no check on the command will be made.   | N           | shutdown                                                                                   |
+---------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------+--------------------------------------------------------------------------------------------+
| entity\_id          | This is a unique identifier for the PAP. It must be a URI (URL or URN). If a URL is used it need not resolve to any specific webpage.                                                                                                                                                                                                                                   | N           | The service endpoint, e.g. ``https://pap.cern.ch:8150/pap/services/ProvisioningService``   |
+---------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------+--------------------------------------------------------------------------------------------+

Section: [security]
~~~~~~~~~~~~~~~~~~~

This sections contains information about PAP security configuration.

+-------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------+-----------------------------------+
| Property                | Description                                                                                                                                                                                                                                 | Required?   | Default Value                     |
+=========================+=============================================================================================================================================================================================================================================+=============+===================================+
| certificate             | The X.509 pem-econded service certificate                                                                                                                                                                                                   | Y           | /etc/grid-security/hostcert.pem   |
+-------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------+-----------------------------------+
| private\_key            | The unencrypted private key bound to the certificate                                                                                                                                                                                        | Y           | /etc/grid-security/hostkey.pem    |
+-------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------+-----------------------------------+
| trust\_store\_dir       | The directory where CA files and CRL are looked for                                                                                                                                                                                         | N           | /etc/grid-security/certificates   |
+-------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------+-----------------------------------+
| crl\_update\_interval   | How frequently the PAP should update CRLs, CAs and namespaces from the filesystem. The interval is defined as a string with the following format: ``N{s,m,h,d}`` where N in the number of either (s=seconds, m=minutes, h=hours, d=days).   | N           | 30m                               |
+-------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------+-----------------------------------+

Service Access Control
----------------------

Access control rules are configured through the
``pap_authorization.ini`` configuration file. Authorization is based on
off of the Subject DN or VOMS attribute within the client certificate
used to authenticate to the PAP.

PAP permissions
~~~~~~~~~~~~~~~

The authorization layer is based on an Access Control List (ACL),
composed of several Access Control Entries (ACEs). Each ACE defines the
actions that an administrator is allowed to execute on the PAP.
Administrators' privileges are defined in terms of PAP permission flags,
whose meaning is described in the table below:

+---------------------------+------------------------------------------------------------+
| Permission Flag           | Meaning                                                    |
+===========================+============================================================+
| ``POLICY_READ_LOCAL``     | Allows read access to locally defined policies             |
+---------------------------+------------------------------------------------------------+
| ``POLICY_READ_REMOTE``    | Allows read access to policies imported from remote PAPs   |
+---------------------------+------------------------------------------------------------+
| ``POLICY_WRITE``          | Allows write access to locally defined policies            |
+---------------------------+------------------------------------------------------------+
| ``CONFIGURATION_READ``    | Allows read access to PAP configuration                    |
+---------------------------+------------------------------------------------------------+
| ``CONFIGURATION_WRITE``   | Allows write access to PAP configuration                   |
+---------------------------+------------------------------------------------------------+
| ``ALL``                   | All of the above permissions                               |
+---------------------------+------------------------------------------------------------+

A set of permission flags can be assigned to an administrator by
defining an ACE in the ``pap_authorization.ini`` configuration file or
by using the authorization management commands provided by the
``pap-admin`` command line interface.

ACEs are expressed as

::

    <principal> : <permission>

couples.

The ``principal`` part of the ACE is either:

-  ``ANYONE``, to assign privileges to any authenticated user (i.e., any
   user that presents a trusted certificate).
-  a VOMS FQAN, e.g., ``/atlas/Role=VO-Admin``
-  a **quoted** X509 certificate subject, e.g.,
   ``"/C=IT/O=INFN/OU=Personal Certificate/L=CNAF/CN=Andrea Ceccanti"``

The ``permission`` part of the ACE is either:

-  a single PAP permission flag, e.g ``CONFIGURATION_READ``
-  a ``|`` separated list of PAP permission flags, e.g.
   ``POLICY_READ_LOCAL|CONFIGURATION_READ``, to grant a set of
   permissions.

So, for example, to grant ``POLICY_READ_LOCAL`` and
``POLICY_READ_REMOTE`` permissions to a user identified by an x509
certificate with
``/C=IT/O=INFN/OU=Personal Certificate/L=CNAF/CN=Andrea Ceccanti``
subject, one should write:

::

    "/C=IT/O=INFN/OU=Personal Certificate/L=CNAF/CN=Andrea Ceccanti" : POLICY_READ_LOCAL|POLICY_READ_REMOTE

Note that the subject has been put into quotes! For VOMS FQANs this is
not needed (FQAN syntax does not allow whitespaces inside the FQAN), so
one could write:

::

    /atlas/Role=PAP-Admin : ALL

Authorization entries are loaded at PAP service startup time so any
``pap_authorization.ini`` modifications done "by hand" while the PAP
service is running do not take effect until the PAP service is
restarted.

To modify the PAP authorization configuration at runtime, use the
authorization management commands provided by the ``pap-admin`` CLI.
Changes made to the PAP ACL by these commands are immediately reflected
on the ``pap_authorization.ini`` file.

Configuration File Syntax
~~~~~~~~~~~~~~~~~~~~~~~~~

In the ``pap_authorization.ini`` file, ACEs are grouped in two *stanzas*
according to the type of the principal. Currently, two stanzas are
supported:

-  ``[dn]``, that lists ACEs defined for principals identified by an
   X509 certificate subject.
-  ``[fqan]``, that lists ACEs defined for principals identified by VOMS
   fqans.

An example of configuration file is given below:

::

    [dn]

    "/C=IT/O=INFN/OU=Personal Certificate/L=CNAF/CN=Andrea Ceccanti" : ALL

    ANYONE : CONFIGURATION_READ|CONFIGURATION_WRITE

    [fqan]

    /voms-ws/Role=PAP-Admin : ALL
