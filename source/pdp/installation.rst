.. _argus_pdp_installation:

Argus Policy Decision Point (PDP) Installation
==============================================

EMI Installation
----------------

To deploy the Argus PDP for EMI, Please follow the documentation `Argus
Deployment for EMI <ArgusEMIDeployment>`__

gLite 3.2 Installation
----------------------

Prerequisites
~~~~~~~~~~~~~

-  A working Java 5, or better, runtime environment.

   -  The service will be started with the ``java`` command available on
      the command path of the user executing the start script.

-  LCG trusted Certificate Authorities certificates installed. In SL4,
   the ``lcg-ca`` package provides those certificates.

-  a valid PEM-encoded X509 private key and certificate issued by a
   trusted CA

gLite YUM and YAIM Installation
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The PAP, PDP, and PEPd are bundled as a single gLite node type
``glite-ARGUS``.

The PDP component is installed in ``/opt/argus/pdp``.

1 Execute the command: ``yum install glite-ARGUS`` 1 In your YAIM
site-info.def configuration set:

-  the ``ARGUS_HOST`` property to the FQDN hostname of the machine on
   which you are installing
-  the ``PAP_ADMIN_DN`` property to the subject DN of the user
   certificate of the individual who will maintain the PAP and use the
   ``pap-admin`` command
-  the ``USERS_CONF``, ``GROUP_CONF``, and ``VOS`` property to their
   appropriate values for your site 1 Run YAIM using the command
   ``yaim -c -s site-info.def -n ARGUS_server``

Manual Installation
~~~~~~~~~~~~~~~~~~~

#. `Download <http://etics-repository.cern.ch:8080/repository/download/registered/org.glite/org.glite.authz.pdp>`__
   the latest version version of the software.
#. Expand the downloaded archive in to the ``/opt/argus`` directory.

   -  This resulting installation directory ``/opt/argus/pdp`` will be
      referred to, in documentation, as PDP\_HOME

#. Edit the configuration file located in the ``$PDP_HOME/conf``
   directory within the expanded archive
