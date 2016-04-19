.. _argus_pepc_cli:

Argus PEP Clients
=================

.. _argus_pepc_pepcli:

pepcli
------

``pepcli`` is the main ARGUS PEP client, based on the C API of the PEP client.

The ``pepcli`` command allows you to submit a XACML request to the PEP
daemon and display the XACML response.

The command is very useful to check if a XACML policy applies (decision
is ``Permit``, ``Deny``, ``Not Applicable`` or ``Indeterminate``) for
the given XACML request. Or to check if the XACML response correspond to
the values you are expecting (``uidgid``, ``secondary-gids``, ...) for
the user mapping.

Installation
^^^^^^^^^^^^

The ``pepcli`` command is by default installed with the ``emi-argus``
metapackage, see :ref:`Argus Deployment <argus-deployment>`.
But you can also install it separately on your host for testing purpose.
To install it with YUM:

.. code-block:: bash

    yum install argus-pepcli

Usage
^^^^^

::

    Usage:
        pepcli --pepd <URL> --keyinfo <FILE> [options...]
        pepcli --pepd <URL> --subjectid <DN> [options...]

    Submit a XACML Request to the PEPd and show the XACML Response.

    Options:
     -p|--pepd <URL>         Argus PEP server endpoint URL.
     -k|--keyinfo <FILE>   XACML Subject key-info: proxy or X509 file.
     -s|--subjectid <DN>     XACML Subject identifier: user DN (format RFC2253).
     -f|--fqan <FQAN>        XACML Subject primary FQAN and FQANs
                             Add multiple --fqan options for secondary FQANs.
     -r|--resourceid <URI>   XACML Resource identifier.
     -a|--actionid <URI>     XACML Action identifier.
     -t|--timeout <SEC>      Connection timeout in second (default 30s).
     -x|--requestcontext     Show effective XACML Request context.
     -v|--verbose            Verbose.
     -q|--quiet              Turn off output.
     -d|--debug              Show debug information.
     -h|--help               This help.
     -V|--version            Display version and exit.
    TLS options:
     --capath <DIR>          Directory containing the server PEM encoded CA certificates.
     --cacert <FILE>         Server PEM encoded CA certificate filename.
     --cert <FILE>           Client PEM encoded certificate filename.
     --key <FILE>            Client PEM encoded private key filename.
     --keypasswd <PASSWD>    Password of the client private key
                             If the --keypasswd is omitted and the private key is encrypted,
                             then you will be prompted for the password.

The MAN page is also available: ``man pepcli``

Return Code
^^^^^^^^^^^

The ``pepcli`` command return ``0`` when a valid XACML Response have
been received, but neither content of the XACML response, nor the
decision value are checked.

On error the ``pepcli`` command returns the following value:

-  ``1`` on memory allocation error
-  ``2`` invalid option
-  ``3`` certchain file or content (missing certificate block) error
-  ``4`` XACML request error
-  ``5`` PEP-C client library error (see output for more detail)

Examples
^^^^^^^^

Submit a XACML Request to the PEP daemon endpoint URL
``https://argus.example.org:8154/authz`` (using SSL/TLS client
authentication) for the resource identified by ``my_resource_id`` for
the action ``my_action_id``, using the Grid proxy as credentials
``/tmp/x509up_u637``:

::

    $ pepcli --pepd https://argus.example.org:8154/authz --capath /etc/grid-security/certificate --cert ~/.globus/usercert.pem --key ~/.globus/userkey.pem  --keyinfo /tmp/x509up_u637--resourceid my_resource_id --actionid my_action_id
    Decision: Permit


.. _argus_pepc_javacli:

PEP Java Client Command Line
----------------------------

The PEP-J library contains a basic command line utility that is useful
for sending test requests to the PEP daemon.

The command line client, ``bin/testreq.sh``, takes a single parameter,
the path (absolute or relative) path to a client configuration file.
This client will create an empty request and then run any configured
PIPs in order to populate it. The :ref:`Static Attribute PIP <argus_pep_pip_static_attributes>`
is very useful for creating custom requests with which to test.
