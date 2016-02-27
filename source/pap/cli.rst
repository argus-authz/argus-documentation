.. _argus_pap_cli:

Argus Policy Administration Point (PAP): Administration
=======================================================

The CLI (Command Line Interface) allows to perform all of the policy
management operations as well as to set most of the configuration
information of the PAP including authorization settings. All these
operations are accessible by means of (sub)commands of the PAP CLI and
they are divided in three sections: *policy management*, *pap
management* and *authorization management*.

The command to invoke the CLI is ``pap-admin``.

Running the pap-admin client
----------------------------

Type:

::

    pap-admin --help

to get a list of the commands supported by the current version of the
PAP command line client.

The general usage is the following:

::

    usage: pap-admin [global-options] <command> [options] [args]

-  ``global-options`` : are options shared by all commands (all these
   options are reported by ``--help``).
-  ``command`` : the command to perform.
-  ``options`` : command specific options.

Type:

::

    pap-admin <command> --help

to see the available command specific options.

Global options:

-  ``-h,--help``: help message;
-  ``-v,--verbose``: set verbose commands output;
-  ``--host <arg>``: specifies the target PAP hostname (default is
   localhost). This option defines the PAP endpoint to be contacted as
   follows:
   `https://arg:port/pap/services/ <https://arg:port/pap/services/>`__;
-  ``--port <arg>``: specifies the port on which the target PAP is
   listening (default is 8150);
-  ``--url <arg>``: specifies the full target PAP endpoint (default:
   https://localhost:8150/pap/services/);
-  ``--cert <arg>``: specifies non-standard user certificate;
-  ``--key <arg>``: specifies non-standard user private key;
-  ``--password <arg>``: specifies the password used to decrypt the
   user's private key;
-  ``--proxy <arg>``: specifies a user proxy to be used for
   authentication;

By default, the ``pap-admin`` is configured to contact a PAP daemon on
localhost port 8150 (i.e., the standalone PAP daemon). If you want to
contact a PAP on another host use the ``--host`` option, a PAP listening
on a different port use the ``--port`` option. Alternatively the full
endpoint can be specified with the ``--url`` option.

**NOTE:** The Argus PDP caches the policy pulled from the PAP. If you
make policy changes to the PAP you will need to restart the PDP in order
to force it to refresh its policy. Also, the PEPd caches results from
the PDP for a short time (up to 10 minutes, by default) so you may need
to restart the PEPd as well.

Exit status
~~~~~~~~~~~

Each command returns 0 for success or non-zero in case of error.

X509 authentication A valid X509 certificate or proxy certificate is needed in order to run the ``pap-admin`` client. The certificate to be used by the command is found as follows:
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

1. if the user is *root* then the client looks for host certificate at
the usual location, i.e. ``/etc/grid-security/hostcert.pem`` and
``/etc/grid-security/hostkey.pem``. These locations can be overridden by
setting the ``X509_USER_CERT`` and ``X509_USER_KEY`` environment
variables. 2. if the user is not *root*, then the client looks for a
proxy certificate at the usual location, i.e.,
``/tmp/x509up_u%GREEN%<uid>%ENDCOLOR%``. The default proxy location can
be overridden using the ``X509_USER_PROXY`` environment variable. If no
proxy is found, then the client looks for a certificate (and the
relative private key) at the usual location, i.e.,
``$HOME/.globus/usercert.pem`` and ``$HOME/.globus/userkey.pem``. These
locations can be overriden by setting the ``X509_USER_CERT`` and
``X509_USER_KEY`` environment variables.

This behaviour can be overridden by specifying the ``--cert``, ``--key``
and ``--proxy`` command line options.

Client configuration (since version 1.3.0)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The ``pap-admin.properties`` file, found in ``/etc/argus/pap`` (EMI) or
``/opt/argus/pap/conf`` (gLite), allows to set the following properties:

+------------+--------------------------------------------------------------------------------------------------------------------------+-----------------+
| Property   | Description                                                                                                              | Default Value   |
+============+==========================================================================================================================+=================+
| host       | The PAP host that will be contacted by the pap-admin CLI if no host is explicitly specified with the ``--host`` option   | localhost       |
+------------+--------------------------------------------------------------------------------------------------------------------------+-----------------+
| port       | The remote PAP service port number                                                                                       | 8150            |
+------------+--------------------------------------------------------------------------------------------------------------------------+-----------------+

This property file is expecially useful when administering a PAP
installed on a remote machine.

Policy Management Commands
--------------------------

This set of commands allows to perform policy management operations. All
the commands in this section modifies the *default* pap. In order to
target another local pap use the option ``--pap <alias>``.

Command: list-policies
~~~~~~~~~~~~~~~~~~~~~~

List policies. By default the policies of the *default* pap are listed
unless option ``--pap`` is specified.

::

    usage: pap-admin [global-options] list-policies [options]

Command specific options:

-  ``--pap <alias>``: list policies of pap "alias" ( *default* pap is
   assumed if this option is missing);
-  ``--all``: list policies of all the defined paps;
-  ``-srai,--show-ra-ids``: show resource and action ids;
-  ``-sai,--show-all-ids``: show all ids (resource, action and rule
   ids);
-  ``--show-xacml``: print policies using XACML.

Command: ban
~~~~~~~~~~~~

Allows to ban an attribute (i.e. SUBJECT, FQAN, CA, etc.).

A *deny* rule is added for the given attribute into the specified
resource/action value. If the resource or the action values are not
specified then ".\*" is assumed.

::

    usage:  pap-admin [global-options] ban [options] <id> <value>

-  ``id``: id of the attribute. The list of supported id depends on the
   Argus version:

   -  Argus v. 1.0: dn, ca, vo, fqan, pfqan.
   -  Argus v. 1.1: subject, subject-issuer, vo, fqan, pfqan.

-  ``value``: value of the attribute - **note:** If you are using Argus
   v. 1.0, the DN must be in RFC2253 format, which can be obtained from
   ``openssl`` using the command
   ``openssl x509 -in <cert.pem> -noout -subject -nameopt rfc2253``

Command specific options:

-  ``-a,--action <value>``: specify an action value (default is ".\*")
-  ``-r,--resource <value>``: specify a resource value (default is
   ".\*")
-  ``--pap <alias>``: add the policy to the pap "alias" ( *default* pap
   is assumed if this option is missing)
-  ``--private``: set the policy as private
-  ``--public``: set the policy as public

**Semantic of the command**: the *resource* and the *action* where the
deny rule is inserted are chosen as follows:

-  if the first resource found in the repository matches the given one,
   then that resource is used, otherwise a new one is created.
-  if a matching resource was found, then if its first action matches
   the given one then this action is used, otherwise a new action is
   created (i.e. inside the new resource or inside the matched
   resource).
-  otherwise a new resource/action are created and inserted on the top.

Example:

::

    pap-admin ban subject "CN=host.test.foo.it, L=FOO, OU=Host, O=ORGANIZATION, C=IT"

Command: un-ban
~~~~~~~~~~~~~~~

Allows to un-ban an attribute (i.e. SUBJECT, FQAN, CA, etc.), that means
removing a ``deny`` rule (if it exists in the given resource/action) for
the given attribute.

::

    usage: pap-admin [global-options] un-ban [options] <id> <value>

-  ``id``: id of the attribute. The list of supported id depends on the
   Argus version:

   -  Argus v. 1.0: dn, ca, vo, fqan, pfqan.
   -  Argus v. 1.1: subject, subject-issuer, vo, fqan, pfqan.

-  ``value``: value of the attribute - **note:** If you are using Argus
   v. 1.0, the DN must be in RFC2253 format, which can be obtained from
   ``openssl`` using the command
   ``openssl x509 -in <cert.pem> -noout -subject -nameopt rfc2253``

Command specific options:

-  ``-a,--action <value>``: specify an action value (default is ".\*")
-  ``-r,--resource <value>``: specify a resource value (default is
   ".\*")
-  ``--pap <alias>``: remove the ban policy from the pap *alias* (
   *default* pap is assumed if this option is missing)

**Semantic of the command**: the target *resource* and *action* to
search the deny rule for are chosen as follows:

-  the target *resource* is the first matching *resource* in the
   repository;
-  inside the target *resource* the target *action* is the first
   matching *action* ;
-  if no target *resource* or *action* were found than the result is an
   error message saying "ban policy not found".

Example:

::

    pap-admin un-ban subject "CN=host.test.foo.it, L=FOO, OU=Host, O=ORGANIZATION, C=IT"

Command: add-policy
~~~~~~~~~~~~~~~~~~~

Add a permit/deny policy.

::

    usage: pap-admin [global-options] add-policy [options] <permit|deny> <id=value>...

-  ``permit|deny``: effect of the policy.
-  ``id=value``: a string in the form "<id>=<value>", where *id* is any
   of the attribute ids that can be specified in the simplified policy
   language and *value* the value to be assigned (e.g. fqan=/vo/group).

Required command options: 1 ``--action-id <action-id>`` optionally with
``--rule-id <rule-id>``: allows to specify an action-id to insert the
policy into. 1 ``--resource <value>`` and ``--action <value>``: allows
to specify a resource/action value to insert the policy into.

The two groups (1 and 2) of required options are mutually exclusive.

This command allows to add a (permit/deny) rule into an action by
specifying an action-id (in this case the action must already exist) or
a resource/action value. In the latter case a new resource and/or action
are created if they don't already exist. The command returns an error if
there are more than one existing resource and/or action with the same
value. By default the rule is inserted at the top of an action unless
the ``--bottom`` option is given. If the ``--rule-id`` is set the rule
is inserted before the given rule-id or after if the ``--after`` option
is present.

Command specific options:

-  ``--pap <alias>``: add the policy to the pap "alias" ( *default* pap
   is assumed if this option is missing);
-  ``--action-id <action-id>``: specify an action id;
-  ``--rule-id <rule-id>``: specify a rule id (requires option
   ``--action-id``);
-  ``--resource <value>``: specify a resource value;
-  ``--action <value>``: specify an action value;
-  ``--after``: insert the rule after the given rule id;
-  ``--bottom``: insert the rule at the bottom of the list of rules of
   the action.
-  ``--obligation`` ``<obligationId>``: specify an obligation. (**since
   version 1.2.0**)
-  ``--obligation-scope`` ``<scope>``: Defines in which scope the
   obligation will be defined. Possible values: ``action``,
   ``resource``. If not specified, ``resource`` is used as default.
   (**since version 1.2.0**)

Command: add-policies-from-file
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Add policies (resources or actions) defined in the given file.

::

    usage: pap-admin [global-options] add-policies-from-file [options] <file> [resourceId]

-  ``file``: text file containing the policies to add (policies defined
   with the simplified policy language)
-  ``resourceId``: the *resource* to insert the policies into.

If *resourceId* is not specified then *file* must contain *resource*
elements that will be added, by default, at the bottom (unless option
``--pivot`` is specified). Otherwise if *resourceId* is not specified
then *file* must contain *action* elements that will be added, by
default, at the bottom inside *resourceId* (unless option ``--pivot`` is
specified).

Command specific options:

-  ``--pap <alias>``: add the policies to the pap "alias" ( *default*
   pap is assumed if this option is missing);
-  ``--pivot <id>``: insert before <id>;
-  ``--after``: modifies the behavior of the ``--pivot`` option in
   insert after <id>.

Command: update-policy-from-file
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Update a resource/action with a new resource/action defined in a given
file.

::

    usage: pap-admin [global-options] update-policy-from-file [options] <id> <file>

-  ``id``: id, as listed by the command ``pap-admin lp --show-all-ids``
   command, of the resource or action to be updated;
-  ``file``: text file containing the new policy definition (using the
   simplified policy language syntax).

In order to modify an action the ``file`` must contain only the new
action, for example:

::

    action ".*" {
        rule deny { subject="/DC=ch/DC=cern/OU=Organic  Units/OU=Users/CN=user/CN=111111/CN=user name" }
    } 

Command specific options:

-  ``--pap <alias>``: update the policies for pap "alias" ( *default*
   pap is assumed if this option is missing);

Command: remove-policy
~~~~~~~~~~~~~~~~~~~~~~

Remove policy by id.

::

    usage: pap-admin [global-options] remove-policy [options] id...

-  ``id``: id, as listed by the command ``pap-admin lp --show-all-ids``
   command, of the policy (resource, action or rule) to remove;

Command specific options:

-  ``--pap <alias>``: remove policies of pap "alias" ( *default* pap is
   assumed if this option is missing);

Command: remove-all-policies
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Remove all policies of a pap. Use option ``--pap`` to specify a pap
different than the default one.

::

    usage: pap-admin [global-options] remove-all-policies [options]

Command specific options:

-  ``--pap <alias>``: remove the policies of pap "alias" ( *default* pap
   is assumed if this option is missing);

Command: move
~~~~~~~~~~~~~

Move a resource, action or rule before or after another, respectively,
resource, action or rule.

::

    usage: pap-admin [global-options] move [options] <id> <pivotId>

-  ``id``: id, as listed by the command ``pap-admin lp --show-all-ids``
   command, of the policy (resource, action or rule) to move;
-  ``pivotId``: id of the pivot policy ( *id* is moved before *pivotId*)

If *id* refers to a resource, action or rule then *pivotId* must be,
respectively, a resource, action or rule id.

Command specific options:

-  ``--pap <alias>``: move the policy of pap "alias" ( *default* pap is
   assumed if this option is missing);
-  ``--after``: move *id* after *pivotId*.

Command: add-obligation (**since version 1.2.0**)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Adds on obligation to an existing resource or action policy.

::

    usage: pap-admin [global-options] add-obligation <policyId> <obligationId>

-  ``policyId``: the id of the policy where the obligation is to be
   added. In order the get the policyId of existing policies, run the
   ``list-policies`` command with the ``--show-all-ids`` option.
-  ``obligationId``: the id of the obligation that will be added.

Command specific options:

-  ``--pap <alias>``: add on policies defined in the pap "alias" (
   *default* pap is assumed if this option is missing);

Command: remove-obligation (**since version 1.2.0**)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Removes an obligation from an existing resource or action policy.

::

    usage: pap-admin [global-options] remove-obligation <policyId> <obligationId>

-  ``policyId``: the id of the policy where the obligation is to be
   removed. In order the get the policyId of existing policies, run the
   ``list-policies`` command with the ``--show-all-ids`` option.
-  ``obligationId``: the id of the obligation that will be removed.

Command specific options:

-  ``--pap <alias>``: add on policies defined in the pap "alias" (
   *default* pap is assumed if this option is missing);

PAP Management Commands
-----------------------

This set of commands allows to perform management operations of the
PAPs.

Command: ping
~~~~~~~~~~~~~

Ping a PAP and return version information.

::

    usage: pap-admin [global-options] ping

Command: add-pap
~~~~~~~~~~~~~~~~

Add a remote or local pap.

::

    usage: pap-admin [global-options] add-pap [options] <alias> [<endpoint> <dn>]

-  ``alias``: a friendly (unique) name used to identify the pap
-  ``endpoint``: endpoint of the remote pap in the form:
   <verbatim>[<protocol>://]<host>:[<port>/[path]]</verbatim>
-  ``dn``: DN of the remote pap

A just added pap is disabled by default (its policies are not sent to
the PDP), use the command :ref:`enable-pap <argus_pap_cli_enable_pap>` to enable it.

By default a pap is considered to be private (use the ``--public``
option to set the pap as public). Policies defined in a public pap can
be fetched from other remote PAPs, while this is not allowed when the
PAP is set to private.

If ``endpoint`` and ``dn`` are present the pap is considered to be
remote (unless option ``--local`` is specified), otherwise it is local.
For the endpoint the only required parameter is the hostname, these are
the default values:

-  *protocol*: https
-  *port*: 8150
-  *service path*: pap/services

When a new pap is added, the PAP service tries immediately to fetch its
policies. If the remote pap is not reachable, the pap-admin command
prints an error message clarifying that the pap was successfully added,
but the fetching of the policies failed.

If the option ``--no-policies`` is given, the policies are not fetched
at pap creation time but automatically by the server every
``polling interval`` seconds or manually when the a ``refresh-cache``
command is sent to the server.

Examples of endpoint are:

-  ``test.site.com`` (hostname);
-  ``test.site.com:9999`` (hostname and port);
-  ``test.site.com:9999/service_path`` (hostname, port, and service
   path);
-  ``https://test.site.com:9999/service_path`` (full URL).

Command specific options:

-  ``-l,--local``: set the pap as local;
-  ``--remote``: set the pap as remote;
-  ``--private``: set the pap as private;
-  ``--public``: set the pap as public;
-  ``--no-policies``: do not fetch the policies now.

Example:

::

    pap-admin add-pap cnaf_pap test.cnaf.infn.it "/C=IT/O=INFN/OU=Host/L=CNAF/CN=test.cnaf.infn.it"

Command: update-pap
~~~~~~~~~~~~~~~~~~~

Update pap information.

::

    usage: pap-admin [global-options] update-pap [options] <alias> [<endpoint> <dn>]

The input is the same as for the "add-pap" command, the effect is to
update old information with the new one. The *alias* of a pap cannot be
modified. In the case of a remote pap the policies are fetched
immediately unless option ``--no-policies`` is given.

Command: remove-pap
~~~~~~~~~~~~~~~~~~~

Remove a pap and delete its policies.

::

    usage: pap-admin [global-options] remove-pap <alias>

-  ``alias``: alias of the pap to remove

Command: list-paps
~~~~~~~~~~~~~~~~~~

List all defined paps.

::

    usage: pap-admin [global-options] list-paps [options]

Command specific options:

-  ``-l``: use a long list format (displays all the information of a
   pap).

.. _argus_pap_cli_enable_pap:

Command: enable-pap
~~~~~~~~~~~~~~~~~~~

Set a pap as enabled (i.e. PDPs will get its policies).

::

    usage: pap-admin [global-options] enable-pap <alias>

Command: disable-pap
~~~~~~~~~~~~~~~~~~~~

Set a pap as disabled (i.e. PDPs won't get its policies).

::

    usage: pap-admin [global-options] disable-pap <alias>

Command: get-paps-order
~~~~~~~~~~~~~~~~~~~~~~~

Get paps ordering.

::

    usage: pap-admin [global-options] get-paps-order

If no ordering is defined the output message is:
``No ordering has been defined``. If the *default* pap is not listed in
the ordering (like in the no ordering defined case) by default it is
placed for first.

Command: set-paps-order
~~~~~~~~~~~~~~~~~~~~~~~

Define paps ordering.

::

    usage: pap-admin [global-options] set-paps-order [alias]...

-  ``alias``: a valid pap alias.

All the aliases must be valid (existing). If no arguments are given then
the current ordering (if there's any defined) is deleted.

| Example:
|  The remote pap *osct* contains banning policies and we want that
  policies to be evaluated for first. This is command to issue:

::

    pap-admin set-paps-order osct default

If the PAP service contains other paps beyond the *osct*, then their
policies are evaluated after the *osct* and *default* pap policies.
Since the ordering contains only the *osct* and the *default* paps it is
not guaranteed a special order for the evaluation of the policies of all
the other paps (except that they are evaluated after these two paps).

Command: refresh-cache
~~~~~~~~~~~~~~~~~~~~~~

Invalidates the local policy cache and retrieves policies from remote
paps.

::

    usage: pap-admin [global-options] refresh-cache [alias]...

-  ``alias``: a valid pap alias.

The arguments identify the paps that will be contacted. If no arguments
are given, all the defined remote paps are contacted.

Command: get-polling-interval
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Get the polling interval in seconds.

::

    usage: pap-admin [global-options] get-polling-interval

Command: set-polling-interval
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Invalidates the local policy cache and retrieves policies from remote
paps.

::

    usage: pap-admin [global-options] set-polling-interval <seconds> 

-  ``seconds``: polling interval in seconds.

Authorization Management Commands
---------------------------------

This set of commands implement Access Control List (ACL) management for
PAP administrators.

Command: list-acl
~~~~~~~~~~~~~~~~~

The ``list-acl`` command provides an easy way of knowing the
authorization configuration of a running PAP.

Typing:

::

    pap-admin list-acl

prints out the Access Control Entries (ACEs) comprising the ACL
currently defined for the running PAP.

Example:

::

    ~# pap-admin list-acl

    /voms-ws/Role=PAP-Admin :
       POLICY_READ_LOCAL|POLICY_READ_REMOTE|POLICY_WRITE|CONFIGURATION_READ|CONFIGURATION_WRITE

    "/C=IT/O=INFN/OU=Personal Certificate/L=CNAF/CN=Andrea Ceccanti" :
       POLICY_READ_LOCAL|POLICY_READ_REMOTE|POLICY_WRITE|CONFIGURATION_READ|CONFIGURATION_WRITE

    ANYONE :
       CONFIGURATION_READ|CONFIGURATION_WRITE

Required permissions : ``CONFIGURATION_READ``.

Command: add-ace
~~~~~~~~~~~~~~~~

The ``add-ace`` command allows to add (or change) an ACE to the PAP ACL.
Note that if an ACE entry already exists on the server for the principal
specified in the command, the permissions in such ACE are **replaced**
by the ones specified in the command.

Usage:

::

    pap-admin add-ace <principal> <permissions>

where:

-  ``principal`` can be either an X509 DN or a VOMS FQAN. ``ANYONE`` can
   be used to assign permissions to any authenticated user.
-  ``permissions`` is a ``|`` separated list of :ref:`PAP permissions <argus_pap_permissions>` that will
   be assigned to ``principal``. The ``ALL`` shortcut can be used to
   assign all permission.

Example:

::

    pap add-ace '/atlas/Role=VO-Admin' 'ALL'

Required permissions: ``CONFIGURATION_READ|CONFIGURATION_WRITE``

Command: remove-ace
~~~~~~~~~~~~~~~~~~~

The ``remove-ace`` command removes an ACE from the PAP ACL.

Usage:

::

    pap-admin remove-ace <principal>

where:

-  ``principal`` can be either an X509 DN or a VOMS FQAN. ``ANYONE`` can
   be used to remove permissions assigned to any authenticated user.

Example:

::

    pap remove-ace '/atlas/Role=VO-Admin'

Required permissions: ``CONFIGURATION_READ|CONFIGURATION_WRITE``
