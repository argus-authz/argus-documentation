%META:TOPICINFO{author="joel\_2ecasutt\_40cern\_2ech" date="1311164000"
format="1.1" reprev="1.17" version="1.17"}%
%META:TOPICPARENT{name="AuthorizationFramework"}%

Quick Start: glite 3.2 Argus Installation
=========================================

Installation and Configuration
------------------------------

The PAP, PDP, and PEPd are bundled together as the gLite node type
``glite-ARGUS``.

YUM Repository
~~~~~~~~~~~~~~

Copy the
http://grid-deployment.web.cern.ch/grid-deployment/glite/repos/3.2/glite-ARGUS.repo
repository file into your local ``/etc/yum.repos.d`` directory.

Packages Installation
~~~~~~~~~~~~~~~~~~~~~

To install and initially configure the service do the following: 1
Install the EUGridPMA trust anchors bundle:<br> ``yum install lcg-CA`` 1
If not already installed, install the Java VM OpenJDK 1.6:<br>
``yum install java-1.6.0-openjdk`` 1 Install the glite-ARGUS
metapackage:<br> ``yum install glite-ARGUS`` 1 In your YAIM
``site-info.def`` configuration set at least the following variables:

-  the ``ARGUS_HOST`` property to the hostname of the machine on which
   you are installing
-  the ``PAP_ADMIN_DN`` property to the subject DN of your user
   certificate. Allow the use the ``pap-admin`` command
-  the ``USERS_CONF``, ``GROUP_CONF``, and ``VOS`` property to their
   appropriate values for your site 1 Configure the ``ARGUS_server``
   node type with YAIM:<br> ``yaim -c -s site-info.def -n ARGUS_server``

Example Configuration
~~~~~~~~~~~~~~~~~~~~~

Example ``site-info.def`` for the ``ARGUS_server`` node type:

::

    #
    # glite-ARGUS hostname
    #
    ARGUS_HOST=vesta.switch.ch

    # users and groups
    USERS_CONF=/opt/glite/yaim/etc/users.conf
    GROUPS_CONF=/opt/glite/yaim/etc/groups.conf

    # Supported VOs
    VOS="dteam"

    VO_DTEAM_VOMS_CA_DN="'/DC=ch/DC=cern/CN=CERN Trusted Certification Authority' '/DC=ch/DC=cern/CN=CERN Trusted Certification Authority' '/DC=ch/DC=cern/CN=CERN Trusted Certification Authority'"
    VO_DTEAM_VOMSES="'dteam lcg-voms.cern.ch 15004 /DC=ch/DC=cern/OU=computers/CN=lcg-voms.cern.ch dteam 24' 'dteam voms.cern.ch 15004 /DC=ch/DC=cern/OU=computers/CN=voms.cern.ch dteam 24' 'dteam lxbra2309.cern.ch 15002 /DC=ch/DC=cern/OU=computers/CN=lxbra2309.cern.ch dteam 24'"

    # PAP administrator DN
    PAP_ADMIN_DN="/DC=org/DC=acme/CN=John Doe"

At this point, you should have a running PAP, PDP, and PEP daemon.

Operation Environment
---------------------

You should now have Argus installed.

Directories
~~~~~~~~~~~

There a few directories that you should be aware of:

-  ``/opt/argus/pap`` contains the PAP component of Argus
-  ``/opt/argus/pdp`` contains the PDP component of Argus
-   ``/opt/argus/pepd`` contains the PEPd component of Argus
-  ``/etc/grid-security/certificates`` contains the CAs trusted by Argus
-  ``/etc/grid-security/grid-mapfile`` contains the mappings from
   DN/FQANs to local user accounts
-  ``/etc/grid-security/groupmapfile`` contains the mappings from FQANs
   to local groups
-  ``/etc/grid-security/gridmapdir`` contains grid user to local user
   account mappings
-  ``/etc/grid-security/vomsdir`` contains the VOMS servers trusted by
   Argus

Note, if you have an existing site and wish to use your existing account
mappings you can move your existing mappings over to the Argus host.
Simply tar your existing mappings
(``tar -cf gridmap.tar /etc/grid-security/gridmapdir``) and then
transfer them over the Argus host and untar them
(``tar -xf gridmap.tar``).

Endpoint URL
~~~~~~~~~~~~

Argus is a network service. Like other network services you should
ensure that your network settings only allow anticipated clients
communicate with the service.

-  Argus 1.1 enables by default client authentication on the service
   endpoint, therefore the endpoint URL scheme is now **HTTPS**.
-  Client applications must be able to contact the service port **8154**
-  Example Argus 1.1 endpoint URL (with client authN):
   ``https://argus.example.org:8154/authz``
-  All other ports (8150-8153) should only be accessible within the
   Argus service host itself.

Starting/Stopping the Services
------------------------------

To start/stop the PAP you can use the command
``/etc/init.d/pap-standalone start|stop``.

To start/stop the PDP you can use the command
``/etc/init.d/pdp start|stop``. You can also force a reload of the
policies, retrieved from the PAP, with ``/etc/init.d/pdp reloadpolicy``.

To start/stop the PEPd you can use the command
``/etc/init.d/pepd start|stop``. You can also clear the responses cache
with ``/etc/init.d/pepd clearcache``.

You should always start the PAP before the PDP or else the PDP will not
be to retrieve its policy from the PAP.

--------------

Installation is now complete, proceed to `setting up your site
policies <AuthzQSPolicySetup>`__.

Global Banning Configuration
----------------------------

The following are the required steps to make your Argus server import
the global grid banning policies. These policies are maintained by OSCT
/ EGI CSIRT. The server is located at CERN.

Notice that in following the steps below you will be trusting the global
banning server and its policies, so that e.g. users banned by the OSCT /
EGI CSIRT will also be rejected at your site as a result.

The ``pap-admin`` command is normally installed in
``/opt/argus/pap/bin``

Add the Global Banning PAP server from CERN as a remote PAP:

``pap-admin add-pap centralbanning argus.cern.ch``
``"/DC=ch/DC=cern/OU=computers/CN=argus.cern.ch"``

The CERN PAP is now listed but is by default still disabled.

``pap-admin enable-pap centralbanning``

The CERN PAP is now enabled.

``pap-admin set-paps-order centralbanning default``

The CERN PAP policies are now parsed before the local policies, so that
e.g. a user banned by OSCT is immediately rejected. This step is
important as only with this order can black listing work.

``pap-admin refresh-cache centralbanning``

The local pap cache, comprising also the CERN policies, is refreshed and
the new policies are made available. The policies are then fetched
automatically by the server every ``polling interval`` seconds or
manually when the a ``refresh-cache`` command is sent to the server.

%META:TOPICMOVED{by="ad968f62f612332eff6b" date="1271340916"
from="EGEE.AuthzQSYaimInstall" to="EGEE.AuthzQSYumYaimInstall"}%
