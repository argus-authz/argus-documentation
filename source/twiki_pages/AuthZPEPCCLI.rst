.. _argus_pepc_cli:

Argus PEP Client: ``pepcli``
============================

Installation
------------

The ``pepcli`` command is by default installed with the ``emi-argus``
metapackage, see `Argus EMI
Deployment <https://twiki.cern.ch/twiki/bin/view/EGEE/ArgusEMIDeployment>`__.
But you can install it separately on your host for testing purpose.

EMI Installation
~~~~~~~~~~~~~~~~

Install it with YUM: ``yum install argus-pepcli``

PEP Command Line Interface
--------------------------

This ``pepcli`` command allows you to submit a XACML request to the PEP
daemon and display the XACML response.

The command is very useful to check if a XACML policy applies (decision
is ``Permit``, ``Deny``, ``Not Applicable`` or ``Indeterminate``) for
the given XACML request. Or to check if the XACML response correspond to
the values you are expecting (``uidgid``, ``secondary-gids``, ...) for
the user mapping.

Usage
-----

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
-----------

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
--------

Submit a XACML Request to the PEP daemon endpoint URL
``https://argus.example.org:8154/authz`` (using SSL/TLS client
authentication) for the resource identified by ``my_resource_id`` for
the action ``my_action_id``, using the Grid proxy as credentials
``/tmp/x509up_u637``:

::

    $ pepcli --pepd https://argus.example.org:8154/authz --capath /etc/grid-security/certificate --cert ~/.globus/usercert.pem --key ~/.globus/userkey.pem  --keyinfo /tmp/x509up_u637--resourceid my_resource_id --actionid my_action_id
    Decision: Permit
