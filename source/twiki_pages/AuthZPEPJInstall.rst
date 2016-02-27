%META:TOPICINFO{author="ad968f62f612332eff6b" date="1297859992"
format="1.1" reprev="1.7" version="1.7"}%
%META:TOPICPARENT{name="AuthorizationFramework"}%

Argus: PEP Java Client Library: Installation
============================================

Local Installation
------------------

Download the latest `gLite
package <http://etics-repository.cern.ch/repository/download/registered/org.glite/org.glite.authz.pep-java>`__
or `EMI
package <http://etics-repository.cern.ch/repository/download/registered/emi/emi.argus.pep-api-java>`__
for your platform from the ETICS repository:

-  Either expand the downloaded tar.gz archive into the /opt/glite
   directory
-  Or install the downloaded RPM or Debian package.

ETICS Dependencies
------------------

If you build your project with ETICS, you have to add a some
dependencies in your project configuration.

EMI Project: argus-pep-api-java v. 2.0
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

For the EMI release, the PEP Java client API package have been renamed
to ``argus-pep-api-java``.

The hessian dependency have been removed, and hessian is now included in
the emi-argus-pep-common v. 2.1.

``Build & Runtime`` dependency:

-  ``emi.argus.pep-api-java`` package: argus-pep-api-java >= 2.0
-  ``emi.argus.pep-common`` package: argus-pep-common >= 2.1 (includes
   java-hessian internally)

``Runtime`` dependencies:

-  ``org.glite.security.voms-api-java`` package:
   glite-security-voms-api-java >= 1.9.18
-  ``bouncycastle`` package external >= 1.45
-  ``jakarta-commons-httpclient`` package external >= 3.0.1
-  ``jakarta-commons-codec`` package external >= 1.3
-  ``jakarta-commons-logging`` package external >= 1.1

org.glite Project: glite-authz-pep-java v. 2.0
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

For the gLite 3.2 release, the PEP Java client API package is named
``glite-authz-pep-java``.

It has the following ``Runtime`` dependencies:

-  glite-authz-pep-common >= 2.0
-  glite-security-voms-api-java >= 1.9.17
-  bouncycastle >= 1.41
-  hessian-java >= 3.2.1
-  jakarta-commons-httpclient >= 3.0.1
-  jakarta-commons-codec >= 1.3
-  jakarta-commons-logging >= 1.1
