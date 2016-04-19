.. _configuration-with-yaim:

Configuration with YAIM
=======================

.. warning::

    As of UMD 4.0, YAIM is no longer supported to configure Argus.
    This page is kept for old Argus versions only.


The ``ARGUS_server`` node type is available to configure the Argus
service with YAIM.

Argus YAIM Configuration Variables
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Description of all the available Argus YAIM configuration
variables: :ref:`argus_emi_yaim_configuration`


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

See the documentation of all the supported Argus YAIM configuration variables: :ref:`argus_emi_yaim_configuration`.

Generate Argus Configuration
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Run YAIM to generate the Argus configuration for your site:
``/opt/glite/yaim/bin/yaim -c -s site-info.def -n ARGUS_server``

At this point, the Argus services (PAP, PDP and PEP Server) must be
configured, up and running.