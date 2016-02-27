%META:TOPICINFO{author="mischa\_2esalle\_40cern\_2ech" date="1346071025"
format="1.1" version="1.4"}%
%META:TOPICPARENT{name="AuthorizationFramework"}%

THIS PAGE IS OUTDATED.
======================

**Please go to the new `GLExec Argus Quick Installation
Guide <https://wiki.nikhef.nl/grid/GLExec_Argus_Quick_Installation_Guide>`__**

-- Main.OscarKoeroo - 23-Oct-2009

Argus: gLExec with LCMAPS C PEP Plug-in: Installation
=====================================================

Manual Installation
===================

This is describing a very brief manual installation of gLExec on the
worker node using the LCMAPS C PEP Plug-in to contact the Argus
framework. The example installation instructions will involve a
Scientific Linux (CERN) version 5 x86\_64 architecture operating system
specific packages. This example also holds for CentOS 5 x86\_64 and
other RedHat Enterprise version 5 derivative operating systems.

Newer version of all the libraries might be available. These are the
listed packages that are certified by SA3 t the time of Friday 23
October 2009.

Library dependencies
--------------------

Install the following (3rd party) RPMS for a basic gLExec installation
(or newer):

-  `glite-security-voms-api-c-1.8.12-1.sl5.x86\_64.rpm <http://etics-repository.cern.ch:8080/repository/download/registered/org.glite/org.glite.security.voms-api-c/1.8.12/sl5_x86_64_gcc412>`__
-  `glite-security-voms-api-cpp-1.8.12-1.sl5.x86\_64.rpm <http://etics-repository.cern.ch:8080/repository/download/registered/org.glite/org.glite.security.voms-api-cpp/1.8.12/sl5_x86_64_gcc412>`__
-  `vdt\_globus\_essentials-VDT1.10.1x86\_64\_rhap\_5-3.x86\_64.rpm <http://glitesoft.cern.ch/EGEE/gLite/R3.2//glite-GENERIC/sl5/x86_64/RPMS.externals/>`__
   (direct
   `RPM <http://glitesoft.cern.ch/EGEE/gLite/R3.2//glite-GENERIC/sl5/x86_64/RPMS.externals/vdt_globus_essentials-VDT1.10.1x86_64_rhap_5-3.x86_64.rpm>`__)

Basic gLExec packages
---------------------

Install the following RPMS for a basic gLExec installation:

-  `glite-security-glexec-0.7.0-2.sl5.x86\_64.rpm <http://etics-repository.cern.ch/repository/download/registered/org.glite/org.glite.security.glexec/0.7.0/sl5_x86_64_gcc412>`__
-  `glite-security-lcas-1.3.11-3.sl5.x86\_64.rpm <http://etics-repository.cern.ch:8080/repository/download/registered/org.glite/org.glite.security.lcas/1.3.11/sl5_x86_64_gcc412>`__
-  `glite-security-lcas-interface-1.3.11-1.sl5.x86\_64.rpm <http://etics-repository.cern.ch:8080/repository/download/registered/org.glite/org.glite.security.lcas-interface/1.3.11/sl5_x86_64_gcc412>`__
-  `glite-security-lcas-plugins-basic-1.3.2-3.sl5.x86\_64.rpm <http://etics-repository.cern.ch:8080/repository/download/registered/org.glite/org.glite.security.lcas-plugins-basic/1.3.2/sl5_x86_64_gcc412>`__
-  `glite-security-lcmaps-1.4.11-2.sl5.x86\_64.rpm <http://etics-repository.cern.ch/repository/download/registered/org.glite/org.glite.security.lcmaps/1.4.11/sl5_x86_64_gcc412>`__
-  `glite-security-lcmaps-plugins-verify-proxy-1.4.7-1.sl5.x86\_64.rpm <http://etics-repository.cern.ch/repository/download/registered/org.glite/org.glite.security.lcmaps-plugins-verify-proxy/1.4.7/sl5_x86_64_gcc412>`__
-  `glite-security-lcmaps-plugins-basic-1.4.0-1.sl5.x86\_64.rpm <http://etics-repository.cern.ch/repository/download/registered/org.glite/org.glite.security.lcmaps-plugins-basic/1.4.0/sl5_x86_64_gcc412>`__

----++ The packages for the LCMAPS C PEP plug-in

-  `glite-security-lcmaps-plugins-c-pep-1.0.4-2.sl5.x86\_64.rpm <http://etics-repository.cern.ch/repository/download/registered/org.glite/org.glite.security.lcmaps-plugins-c-pep/1.0.4/sl5_x86_64_gcc412>`__
-  `glite-authz-pep-c-1.3.0-2.sl5.x86\_64.rpm <http://etics-repository.cern.ch:8080/repository/download/registered/org.glite/org.glite.authz.pep-c/1.3.0/sl5_x86_64_gcc412>`__

----++ Optional packages

-  `glexec-wrapper-scripts-0.0.3-1.noarch.rpm <http://etics-repository.cern.ch:8080/repository/download/registered/org.glite/org.glite.security.glexec-wrapper-scripts/0.0.3/noarch>`__
-  `glite-security-lcas-plugins-check-executable-1.2.1-3.sl5.x86\_64.rpm <http://etics-repository.cern.ch:8080/repository/download/registered/org.glite/org.glite.security.lcas-plugins-check-executable/1.2.1/sl5_x86_64_gcc412>`__
-  `glite-yaim-glexec-wn-2.0.3-0.noarch.rpm <http://etics-repository.cern.ch/repository/download/registered/org.glite/org.glite.yaim.glexec-wn/2.0.3/noarch>`__

YUM Installation
================

Use the following repository:
http://glitesoft.cern.ch/EGEE/gLite/R3.2/glite-GENERIC/sl5/x86_64/RPMS.updates/

Configuration
=============

Please move forward to the `gLExec with LCMAPS C PEP Plug-in
configuration guide <AuthZPEPgLExecConfig>`__ for all options.

It is advised to install gLExec with Argus support from YAIM and use the
YAIM manual pages for help in the configuration of YAIM.
