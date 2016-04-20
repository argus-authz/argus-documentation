.. _argus-development-tools:

Argus Development Tools
=======================

The tools used by the Argus PT to develop the Argus Authorization
Service

Development
-----------

-  Eclipse IDE for all Java and C development (http://www.eclipse.org/)
-  GitHub for the version control of all Argus components (https://github.com/argus-authz)
-  Wiki for the development and product documentation (http://argus-documentation.readthedocs.org/en/latest/)

Build
-----

-  Maven for the compilation of the main Java components (http://maven.apache.org/)
-  ANT for the compilation of the PEP Java client library (http://ant.apache.org/)
-  Autotools, configure and make for the compilation of the C components


Test
----

-  JUnit for some of the Java components
-  Grinder for the internal load and stress testing of the server
   components (http://grinder.sourceforge.net/)

QA
--

-  GGUS for users support (http://www.ggus.org)
-  LCG Savannah for bugs, tasks and patches tracking
   (https://savannah.cern.ch)
-  A code review was done by the PSNC Security Team, but we don't have
   enough resource within the Argus PT to do it ourself
-  CERN vNode test bed using the automatic YAIM-gen for product
   certification

Infrastructure and Repository
-----------------------------

-  Some virtual machines for internal testing
-  CERN YUM repository for deployment
-  YAIM for the glite-ARGUS node configuration
