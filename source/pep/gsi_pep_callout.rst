.. _gsi_pep_callout:

Argus GSI PEP Callout
=====================

Module Description
------------------

The Globus Toolkit version 3.2 and later have the ability to customize
the authorization and gridmap lookup (currently available in the GridFTP
and Gatekeeper servers). The `Globus Authorization
Callouts <http://www.globus.org/toolkit/security/callouts/>`__ framework
allows to plug in authorization and mapping modules.

The GSI PEP Callout module implements the functionality to authorize and
map the user by calling out to the :ref:`Argus PEP Server <argus_introduction_pep>`.

Authorization and Mapping
+++++++++++++++++++++++++

Based on the Grid credientials, typically a proxy certificate, the GSI
PEP Callout module will send an authorization request to the
:ref:`Argus PEP Server <argus_introduction_pep>`, and then
parse the authorization response decision to authorize the user and the
obligations to map him to a local account.

XACML Profile
+++++++++++++

The GSI PEP Callout module implements the `XACML Grid Worker Node
Authorization Profile 1.0 <https://edms.cern.ch/document/1058175>`__,
and by default uses the identifiers described in the profile.

XACML Request
+++++++++++++

The GSI PEP Callout module sends a request to the PEP Daemon with the
following elements:

-  XACML subject with the attribute element:

   -  AttributeId: ``urn:oasis:names:tc:xacml:1.0:subject:key-info``
   -  Value: The PEM encoded Grid credentials provided by the calling
      service

-  XACML resource with the attribute element:

   -  AttributeId: ``urn:oasis:names:tc:xacml:1.0:resource:resource-id``
   -  Value: The value of the GSI PEP Callout :ref:`configuration directive <pep_gsi_callout_conf_directives>`
      ``xacml_resourceid``.

-  XACML action with the attribute element:

   -  AttributeId: ``urn:oasis:names:tc:xacml:1.0:action:action-id``
   -  Value: The service being requested by the client (e.g. ``file``
      for GridFTP) or the name of the service passed to the gatekeeper.

-  XACML environment with the attribute element:

   -  AttributeId: ``http://glite.org/xacml/attribute/profile-id``
   -  Value: ``http://glite.org/xacml/profile/grid-wn/1.0`` (Default)

XACML Response
^^^^^^^^^^^^^^

The PEP Daemon sends back a response to the GSI PEP Callout module. The
following response elements are parsed to authorize and map the user:

-  XACML decision element: Contains the **authorization decision**
   ``Permit``, ``Deny``, ``Indeterminate`` or ``NotApplicable``
-  XACML obligation, ObligationId:
   ``http://glite.org/xacml/obligation/local-environment-map/posix``,
   with the attribute assignment element:

   -  AttributeId: ``http://glite.org/xacml/attribute/user-id``
   -  Value: Contains the **local identity mapping** of the user.

The local identity mapping will only succeed if the authorization
decision is ``Permit``.

.. _pep_gsi_callout_installation:

Argus GSI PEP Callout: Installation
===================================

Manual Installation
===================

The GSI PEP Callout module requires the following RPM:

.. code-block:: bash

   yum install argus-gsi-pep-callout

The package includes the shared library implementing the GSI
authorization and mapping module.


.. _pep_gsi_callout_configuration:

Configuration
-------------

Manual Configuration
++++++++++++++++++++

To configure the GSI PEP Callout module, you first have to configure the
Globus Authorization Callouts framework to use the GSI PEP Callout
library to do the authorization and mapping. Then the GSI PEP Callout
module must be configured.

Globus Authorization Callouts Configuration
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Configuration file and configuration directives for the
`Globus Authorization Callouts <http://www.globus.org/toolkit/security/callouts/>`__ to enable
the GSI PEP Callout module.

Configuration File
~~~~~~~~~~~~~~~~~~

The Globus Authorization Callouts framework uses the following locations
(in order) for the callout configurations file:

-  ``$GSI_AUTHZ_CONF`` (Environment variable)
-  ``/etc/grid-security/gsi-authz.conf``
-  ``$GLOBUS_LOCATION/etc/gsi-authz.conf``
-  ``$HOME/.gsi-authz.conf``

EMI-1 Configuration Directives
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Content of the Globus Authorization Callouts configuration file to
enable the GSI Argus PEP Callout function ``argus_pep_callout`` for EMI:

::

    # Globus authorization and mapping callout to the ARGUS GSI PEP Callout module
    # format: globus_mapping <library_path> <function_name>
    globus_mapping /usr/lib64/libgsi_pep_callout.so argus_pep_callout

For EMI the Argus PEP GSI callout library is installed in the
``/usr/lib64`` directory.

GSI PEP Callout Configuration
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Configuration file and configuration directives for the GSI PEP Callout
module.

Configuration File
~~~~~~~~~~~~~~~~~~

The GSI PEP Callout module uses the following locations (in order) for
the configurations file:

-  ``$GSI_PEP_CALLOUT_CONF`` (Environment variable)
-  ``/etc/grid-security/gsi-pep-callout.conf``

.. _pep_gsi_callout_conf_directives:

Configuration Directives
~~~~~~~~~~~~~~~~~~~~~~~~

The configuration directives for the GSI PEP Callout are single ``name``
``value`` lines. Lines with comments ``#`` are allowed.

+------------------------------+------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------+------------------------------------------------+--------------------------------------------------------------------+-------+
| Directive                    | Description                                                                                                                  | Mandatory?                                  | Default Value                                  | Example                                                            | Since |
+==============================+==============================================================================================================================+=============================================+================================================+====================================================================+=======+
| ``pep_url``                  | The endpoint URL of the PEP daemon.                                                                                          | Yes                                         |                                                | ``pep_url`` ``https://pepd.example.org:8154/authz``                | 1.0   |
+------------------------------+------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------+------------------------------------------------+--------------------------------------------------------------------+-------+
| ``xacml_resourceid``         | XACML request resource-id value                                                                                              | Yes                                         |                                                | ``xacml_resourceid`` ``x-urn:example.org:resource:ce:gridftp``     | 1.0   |
+------------------------------+------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------+------------------------------------------------+--------------------------------------------------------------------+-------+
| ``xacml_actionid``           | XACML request action-id value. Define this parameter to overwrite the *service* name passed to the module by the application | No                                          |                                                | ``xacml_actionid`` ``http://glite.org/xacml/action/access``        | 1.0   |
+------------------------------+------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------+------------------------------------------------+--------------------------------------------------------------------+-------+
| ``xacml_profileid``          | XACML request profile-id value. Define this parameter to overwrite the default profile id                                    | No                                          | ``http://glite.org/xacml/profile/grid-wn/1.0`` | ``xacml_profileid`` ``http://glite.org/xacml/profile/grid-ce/1.0`` | 1.2   |
+------------------------------+------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------+------------------------------------------------+--------------------------------------------------------------------+-------+
| ``pep_timeout``              | Connection timeout in seconds                                                                                                | No                                          | ``30``                                         | ``pep_timeout`` ``60``                                             | 1.0   |
+------------------------------+------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------+------------------------------------------------+--------------------------------------------------------------------+-------+
| ``pep_ssl_validation``       | Enable SSL validation of the PEP daemon endpoint URL (HTTPS)                                                                 | No                                          | ``true``                                       | ``pep_ssl_validation`` ``false``                                   | 1.0   |
+------------------------------+------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------+------------------------------------------------+--------------------------------------------------------------------+-------+
| ``pep_ssl_server_capath``    | CA directory path for the HTTPS validation of the PEP daemon endpoint URL                                                    | No                                          | ``/etc/grid-security/certificates``            | ``pep_ssl_server_capath`` ``/etc/grid-security/certificates``      | 1.0   |
+------------------------------+------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------+------------------------------------------------+--------------------------------------------------------------------+-------+
| ``pep_ssl_server_cert``      | Certificate file for the HTTPS validation of the PEP daemon endpoint URL                                                     | No                                          |                                                | ``pep_ssl_server_cert`` ``/etc/grid-security/pepdcert.pem``        | 1.0   |
+------------------------------+------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------+------------------------------------------------+--------------------------------------------------------------------+-------+
| ``pep_ssl_client_cert``      | Client certificate file for the TLS client authentication on the PEP daemon endpoint URL                                     | No                                          | ``/etc/grid-security/hostcert.pem``            | ``pep_ssl_client_cert`` ``/etc/ssl/mycert.pem``                    | 1.0   |
+------------------------------+------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------+------------------------------------------------+--------------------------------------------------------------------+-------+
| ``pep_ssl_client_key``       | Client private key file for the TLS client authentication on the PEP daemon endpoint URL                                     | No                                          | ``/etc/grid-security/hostkey.pem``             | ``pep_ssl_server_key`` ``/etc/ssl/mykey.pem``                      | 1.0   |
+------------------------------+------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------+------------------------------------------------+--------------------------------------------------------------------+-------+
| ``pep_ssl_client_keypasswd`` | Client private key password                                                                                                  | Only if ``pep_ssl_client_key`` is encrypted |                                                | ``pep_ssl_server_keypasswd`` ``mykeypassword``                     | 1.0   |
+------------------------------+------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------+------------------------------------------------+--------------------------------------------------------------------+-------+

Configuration Example
~~~~~~~~~~~~~~~~~~~~~

Example of a valid configuration file for the GSI PEP Callout module:

::

    #
    # GSI PEP Callout configuration example
    #
    pep_url   https://chaos.switch.ch:8154/authz
    xacml_resourceid http://ce.example.org/cream/gridftp

YAIM Configuration
++++++++++++++++++

The yaim-core (>= 4.0.12) the function ``config_lcas_lcmaps_gt4`` is now
able to configure the Argus GSI PEP callout module.

In your ``site-info.def`` set the following variables:

::

    USE_ARGUS=yes
    ARGUS_PEPD_ENDPOINTS="<Argus_URL> ..."
    CREAM_PEPC_RESOURCEID=<CreamCE_XACML_resouce_id>

where ``Argus_URL`` is the Argus PEP daemon endpoint URL. e.g.
``ARGUS_PEPD_ENDPOINTS=https://argus.example.org:8154/authz``

where ``CreamCE_XACML_resouce_id`` is the XACML resource identifier for
this cream CE. e.g.
``CREAM_PEPC_RESOURCEID=http://glite.org/xacml/resource/cream-ce``


.. _pep_gsi_callout_troubleshooting:

Troubleshooting
---------------

Syslog
++++++

By default the GSI PEP Callout module logs info and error via syslog.

The syslog facility used is ``local5`` and the identifier is
``gsi_pep_callout``. These log messages are typically in
``/var/log/messages``

Enabling Debug Information
++++++++++++++++++++++++++

You can enable the debugging mode of the GSI PEP Callout module to
troubleshoot your problem.

Environment Variables
^^^^^^^^^^^^^^^^^^^^^

You can set the following environment variables to enable debug mode:

-  ``GSI_PEP_CALLOUT_DEBUG_LEVEL`` Set the debug level from ``0`` (none)
   to ``9`` (lot of info). Default is ``0``.
-  ``GSI_PEP_CALLOUT_DEBUG_FILE`` Set the file to log the debugging
   information. Default is ``stderr``.

Example
^^^^^^^

This example shows how to start the GridFTP server in debug mode. The
configuration files ``gsi-authz.conf`` and ``gsi-pep-callout.conf`` must
be correctly configured as :ref:`previously described<pep_gsi_callout_configuration>`.

.. code-block:: bash

    export GLOBUS_CALLOUT_DEBUG_LEVEL=5
    # set the gsi-authz config to use (default /etc/grid-security/gsi-authz.conf)
    export GSI_AUTHZ_CONF=/etc/grid-security/gsi-authz.conf
    # set the gsi-pep-callout config to use (default /etc/grid-security/gsi-pep-callout.conf)
    export GSI_PEP_CALLOUT_CONF=/etc/grid-security/gsi-pep-callout.conf
    export GSI_PEP_CALLOUT_DEBUG_LEVEL=5

    globus-gridftp-server -d 255 -p 9999 -debug


The GridFTP server is now running and listening on port ``9999``. Use
the ``uberftp`` client or ``globus-url-copy`` to connect to the server
with your Grid credentials and obtain debugging information from the
server:

.. code-block:: bash

   uberftp -P 9999 HOSTNAME
   globus-url-copy file:///etc/passwd gsiftp://HOSTNAME:9999/tmp/e33
