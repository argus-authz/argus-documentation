.. _argus-manual-configuration:

Manual configuration
====================

This guide show how to configure Argus with a basic setup.
Basically configuration consist in:

- set service host name in configuration files;
- set service endpoints
- set certificates path;

Requirements
~~~~~~~~~~~~

First, verify that the operating system has Java 1.8 as default JVM:

.. code-block:: bash

   $ java -version
   openjdk version "1.8.0_77"

If the command above shows an older JVM version, change the system configuration with

.. code-block:: bash

   alternatives --config java


PAP
~~~

``/etc/argus/pap/pap-admin.properties``
   #. Set ``host`` variable with the machine host name.

``/etc/argus/pap/pap_configuration.ini``
   #. Set ``entity_id``.
   #. Set ``hostname`` with server host name.
   #. Set ``certificate`` with the absolute path of the X509 certificate of the server.
   #. Set ``private_key`` with the absolute path of the X509 private key of the server.

``/etc/argus/pap/pap_authorization.ini``
   Keep default values for basic setup.

``/etc/sysconfig/argus-pap``
   Keep default values for basic setup.

More details about PAP configuration can be found in :ref:`PAP configuration <argus_pap_configuration>`.


PDP
~~~

``/etc/argus/pdp/pdp.ini``
   #. Set ``entity_id``.
   #. Set ``hostname`` with server host name.
   #. Set ``paps`` with PAP endpoint; one or more PAP endpoints can be specified in a space separated list.
   #. Set ``serviceCertificate`` with the absolute path of the X509 certificate of the server.
   #. Set ``servicePrivateKey`` with the absolute path of the X509 private key of the server.
   #. Set ``trustInfoDir`` with the absolute path of the trusted CAs.

``/etc/sysconfig/argus-pdp``
   Keep default values.

More details about PDP configuration can be found in :ref:`PDP configuration <argus_pdp_configuration>`.

PEP
~~~

``/etc/argus/pepd/pepd.ini``
   #. Set ``entity_id``.
   #. Set ``hostname`` with server host name.
   #. Set ``pdps`` with PDP endpoint; one or more PDP endpoints can be specified in a space separated list.
   #. Set ``serviceCertificate`` with the absolute path of the X509 certificate of the server.
   #. Set ``servicePrivateKey`` with the absolute path of the X509 private key of the server.
   #. Set ``trustInfoDir`` with the absolute path of the trusted CAs.
   #. Set ``vomsInfoDir`` with the absolute path of the VOMS directory.

``/etc/sysconfig/argus-pepd``
   Keep default values.

More details about PEP configuration can be found in :ref:`PEP configuration <argus_pepd_configuration>`.

Grid Mapping
~~~~~~~~~~~~
Ensure that following Grid mapping file are present:

``/etc/grid-security/grid-mapfile``:
   This file contains mapping between Grid users and local pool account

``/etc/grid-security/groupmapfile``:
   This file contains mapping between Grid users and local Unix groups.

Moreover, ensure there is present the directory:

``/etc/grid-security/gridmapdir``:
   This directory contains pool accounts and users linked to them.

More information about the syntax can be found in :ref:`Argus Grip Map File Syntax <authz_grid_map_file>`.


VOMS configuration
~~~~~~~~~~~~~~~~~~
Ensure that following files are present:

``/etc/grid-security/voms-grid-mapfile``
   This file contains mapping between Grid users and local pool account, similar to ``grid-mapfile`` described above.

Ensure that following directory are present:

``/etc/grid-security/vomsdir``
   This directory contains a sub-directory for each supported VOs.
   In each of this sub-directory there is a ``.lsc``, a file
   that contains a description of the certificate chain of the certificate
   used by a VOMS server to sign VOMS attributes.

For example for the some test VO, the directory structure is:

.. code-block:: bash

   vomsdir/
   ├── testers.eu-emi.eu
   │   └── emitestbed07.cnaf.infn.it.lsc
   ├── test.vo
   │   └── vgrid02.cnaf.infn.it.lsc
   └── test.vo.2
       └── vgrid02.cnaf.infn.it.lsc


The content for the ``test.vo`` ``lsc`` file is:

::

   /C=IT/O=INFN/OU=Host/L=CNAF/CN=vgrid02.cnaf.infn.it
   /C=IT/O=INFN/CN=INFN Certification Authority


More information about VOMS can be found in `VOMS documentation <http://italiangrid.github.io/voms/documentation.html>`__.

Restart services
~~~~~~~~~~~~~~~~

Restart Argus services to enable the new configuration.

In EL6-based systems, run this command:

.. code-block:: bash

   for srv in argus-pap argus-pdp argus-pepd; do service $srv restart; done

In EL7-based systems, run:

.. code-block:: bash

   systemctl restart argus-pap argus-pdp argus-pepd

More details about service management can be found in :ref:`PAP operations <argus_pap_operation>`,
:ref:`PDP operations <argus_pdp_operation>` and :ref:`PEP operation <argus_pepd_operation>`.
