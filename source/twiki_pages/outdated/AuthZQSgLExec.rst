%META:TOPICINFO{author="mischa\_2esalle\_40cern\_2ech" date="1346071204"
format="1.1" version="1.6"}%
%META:TOPICPARENT{name="AuthorizationFramework"}%

THIS PAGE IS OUTDATED.
======================

**Please go to the new `GLExec Argus Quick Installation
Guide <https://wiki.nikhef.nl/grid/GLExec_Argus_Quick_Installation_Guide>`__**

-- Main.OscarKoeroo - 26-Oct-2009

Argus: Quick Start:: Using gLExec with the Argus framework
==========================================================

Purpose
-------

The purpose of this section is to give a short description how to
perform a quick install for gLExec and configure it to use the Argus
framework as authorization and mapping back-end.

For extended information about
``gLExec and the LCMAPS C PEP Plug-in using Argus``:

-  `gLExec description <AuthZPEPgLExecInfo>`__
-  `gLExec with Argus Installation procedure <AuthZPEPgLExecInstall>`__
-  `Configuration options <AuthZPEPgLExecConfig>`__
-  `How to troubleshoot <AuthZPEPgLExecTroubleshooting>`__

Quick install
-------------

-  Install the gLExec package and its dependencies on your system.

   -  See page `gLExec with LCMAPS C PEP Plug-in installation
      guide <AuthZPEPgLExecInstall>`__ for package install details.
   -  All packages are required, except those explicitly mentioned as
      optional.
   -  Note: The links to the packages are linked towards the packages
      build for RHEL 5 (or derivative) x86\_64 architectures. For other
      operating system version, please traverse the directories of the
      associated links.

-  Use the configuration file example for the
   `glexec.conf <https://twiki.cern.ch/twiki/bin/view/EGEE/AuthZPEPgLExecConfig#Workable_example_gLExec_Configur>`__,
   `lcas.db <https://twiki.cern.ch/twiki/bin/view/EGEE/AuthZPEPgLExecConfig#Workable_example_LCAS_Configurat>`__
   and
   `lcmaps.db <https://twiki.cern.ch/twiki/bin/view/EGEE/AuthZPEPgLExecConfig#Workable_example_LCMAPS_Configur>`__
   files and create them in:

   -  **``/opt/glite/etc/glexec.conf``**
   -  **``/opt/glite/etc/lcas/lcas-glexec.db``**
   -  **``/opt/glite/etc/lcmaps/lcmaps-glexec.db``** configuration
      files.

-  Touch the file **``/opt/glite/etc/lcas/ban_users.db``**.
-  Set the file permissions of the **``/opt/glite/etc/glexec.conf``** to
   **0640** **``(-rw-r-----)``** owned by **``root:root``**.
-  Set the file permission of the **``/opt/glite/sbin/glexec``**
   executable to **4555** **``(-r-sr-sr-x)``** owner by
   **``root:root``**.
-  Check if the path **``/var/log/glexec/``** exists for file logging
   (when syslog is not selected)

Quick configure
---------------

The installation of the gLExec tool is now finished. It still needs to
be configured to work with your authorization service. We assume that
this is done, if not, please go to the `Argus Quick Start: glite-ARGUS
Installation <AuthzQSYumYaimInstall>`__ page to help you setup the Argus
service node.

Change the following options in the
`lcmaps.db <https://twiki.cern.ch/twiki/bin/view/EGEE/AuthZPEPgLExecConfig#Workable_example_LCMAPS_Configur>`__
file to resemble your installation:

-  ``"--pep-daemon-endpoint-url <url to your PEP Daemon>"``

   -  This option must contain a valid URL (with appropriate port
      number) to the PEP Daemon on the Argus Service node e.g.
      ``"--pep-daemon-endpoint-url https://ares.switch.ch:8154/authz "``

-  ``"--resourceid http://authz-interop.org/xacml/resource/resource-type/wn"``

   -  This option represents the type of resource from which the LCMAPS
      C PEP Plug-in is executed. See for detailed options
      `here <https://twiki.cern.ch/twiki/bin/view/EGEE/AuthZPEPgLExecConfig#LCMAPS_C_PEP_Plug_in_Configurati>`__.

-  ``"--actionid http://authz-interop.org/xacml/action/action-type/execute-now"``

   -  This option represents the type of action that is requested from
      which the LCMAPS C PEP Plug-in is executed. See for detailed
      options
      `here <https://twiki.cern.ch/twiki/bin/view/EGEE/AuthZPEPgLExecConfig#LCMAPS_C_PEP_Plug_in_Configurati>`__.

-  ``"--capath /etc/grid-security/certificates"``

   -  This option set the directory containing the trusted CA anchors
      (EUGridPMA bundle).

-  ``"--pep-certificate-mode implicit"``

   -  This option will automatically use the user proxy certificate for
      the TLS client authenitcation on the PEP daemon.

Quick test
----------

Run gLExec with the following command in order to perform a quick test:

::

    export GLEXEC_CLIENT_CERT=/tmp/x509up_u7381
    /opt/glite/sbin/glexec /usr/bin/id; echo $?

The expected output is:

::

    uid=40205(testuseraccount) gid=2013(testusergroup)
    0

Maintenance and troubleshooting
-------------------------------

Please proceed to the detailed page about where to find information in
the system logfiles and how to
`troubleshoot <AuthZPEPgLExecTroubleshooting>`__ the current
installation.

%META:TOPICMOVED{by="ad968f62f612332eff6b" date="1272892995"
from="EGEE.GettingStarted3" to="EGEE.AuthZQSgLExec"}%
