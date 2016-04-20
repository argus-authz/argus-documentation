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

Then restart PAP service: see :ref:`PAP operations <argus_pap_operation>` for detailed commands.

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

Then restart PDP service: see :ref:`PDP operations <argus_pdp_operation>` for detailed commands.

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

Then restart PEP service: see :ref:`PEP operation <argus_pepd_operation>` for detailed commands.

More details about PEP configuration can be found in :ref:`PEP configuration <argus_pepd_configuration>`.
