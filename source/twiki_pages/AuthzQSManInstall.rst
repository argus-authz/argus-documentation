%META:TOPICINFO{author="ad968f62f612332eff6b" date="1257252646"
format="1.1" reprev="1.2" version="1.2"}%
%META:TOPICPARENT{name="AuthorizationFramework"}%

Argus: Quick Start: Manual Installation
=======================================

Prerequisites
-------------

In order to run the Argus service you will need the following:

-  a working Java 5 or better installation. Use ``java -version`` to
   determine your Java version.
-  a recent version of openssl (>0.9.7) installed on your system
-  LCG trusted Certificate Autorities certificates installed. In SL4 and
   SL5, the ``lcg-CA`` package provides those certificates.
-  a valid, PEM encoded X509 server certificate issued by a trusted CA.

Installation
------------

The Argus service is composed of three components, the PAP, PDP, and
PEPd. The following steps provide instructions for installing each
component.

PAP Installation
~~~~~~~~~~~~~~~~

1
`Download <http://etics-repository.cern.ch:8080/repository/download/registered/org.glite/org.glite.authz.pap-service/>`__
the latest version version of the software. 1 Expand the downloaded
archive to the ``/opt/argus`` directory.

-  This resulting installation directory ``/opt/argus/pap`` will be
   referred to, in the documentation, as PAP\_HOME 1 Export the
   environment variable ``PAP_HOME=/opt/argus/pap`` 1 Add the
   ``/opt/argus/pap/bin`` to your ``PATH`` in order to use the PAP
   ``pap-admin`` command

PDP Installation
~~~~~~~~~~~~~~~~

#. `Download <http://etics-repository.cern.ch:8080/repository/download/registered/org.glite/org.glite.authz.pdp>`__
   the latest version version of the software. 1 Expand the downloaded
   archive in to the ``/opt/argus`` directory.

   -  This resulting installation directory ``/opt/argus/pdp`` will be
      referred to, in documentation, as PDP\_HOME

PEPd Installation
~~~~~~~~~~~~~~~~~

1
`Download <http://etics-repository.cern.ch:8080/repository/download/registered/org.glite/org.glite.authz.pep-daemon/>`__
the latest version version of the software. 1 Expand the downloaded
archive in to the ``/opt/argus`` directory.

-  The resulting installation directory ``/opt/argus/pepd`` will be
   referred to, in documentation, as PEPD\_HOME

Starting/Stop the Services
--------------------------

To start/stop the PAP you can use the command
``/opt/argus/pap/bin/pap-standalone start|stop``.

To start/stop the PDP you can use the command
``/opt/argus/pdp/bin/pdpctl.sh start|stop``.

To start/stop the PEPd you can use the command
``/opt/argus/pepd/bin/pepdctl.sh start|stop``.

You should always start the PAP before the PDP or else the PDP will not
be to retrieve its policy from the PAP.

--------------

Installation is now complete, proceed to `configuring the Argus
service <AuthzQSServiceConfig>`__.
