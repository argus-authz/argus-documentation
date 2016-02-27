.. _argus_pepc_installation:

Argus: PEP C Client Library: Installation
=========================================

Local Installation
------------------

Download the latest
`package <http://etics-repository.cern.ch/repository/download/registered/org.glite/org.glite.authz.pep-api-c>`__
for your platform from the ETICS repository:

-  Either expand the downloaded ``tar.gz`` archive into the
   ``/opt/glite`` directory
-  Or install the downloaded RPM or Debian package.

The package includes the shared library in ``/opt/glite/lib`` (or
``/opt/glite/lib64`` on 64bit platform), the header files in
``/opt/glite/include``, and the documentation in
``/opt/glite/share/doc/glite-authz-pep-api-c``.

ETICS Dependency
----------------

If you build your project with ETICS, you have to add a
``Build & Runtime`` dependency on ``org.glite.authz.pep-api-c`` in your
project configuration.
