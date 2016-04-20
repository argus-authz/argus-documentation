
Argus Quick Start: Site Policy Setup
====================================

Policy Basics
-------------

A site's policies are maintained using the command
``/opt/argus/pap/bin/pap-admin``. Initially the PAP contains an empty
policy for the site so no one will be permitted to do anything. The
command ``pap-admin list-policies`` lists policies, in the
:ref:`simplified policy language notation <argus_simplified_policy_language>`, currently active
in the PAP.

The policies you add will be evaluated from most to least recent and the
first policy that matches is the result returned by Argus. So, if you
added a policy that would deny a user and then added one that would
permit the user the result of an authorization request will be permit
since the permit policy is most recent. While not covered here, the
exhaustive PAP command line interface :ref:`reference <argus_pap_cli>`
provides information for how to re-order policies.

To begin, you will need to create permit policies, for individuals
users, VOs, and FQANs, which reflect your site's access policy. Each of
the commands listed require a resource ID and action ID which are
deployment and application specific, respectively. These ID were
described in the :ref:`introduction <argus_introduction>` to the Argus system. For
the purposes of this quick-start we'll use ``http://example.org`` as the
resource ID and ``http://example.org/action`` as the action ID. At this
point you do not need to worry about what the correct values would be
for a production deployment.

.. note::
   As you change policies you will need to restart the PDP in
   order to force it to reread the policy from the PAP. It would do this by
   default but only after a couple of hours. Restarting forces it do this
   immediately.

Permit by User
~~~~~~~~~~~~~~

Command:

.. code-block:: bash

   pap-admin add-policy permit --resource "RESOURCE_ID" --action "ACTION_ID" dn="USER_DN"

The provided USER_DN must be in the RFC2253 standard form, not the
proprietary format used by OpenSSL by default. To get the standard form
of the DN you can use the command

.. code-block:: bash

   openssl x509 -noout -nameopt RFC2253 -subject -in CERT_PATH

Here's an example that permits a user identified by their DN:

.. code-block:: bash

   pap-admin add-policy permit --resource "http://example.org" --action "http://example.org/action" dn="CN=John Smith,OU=Standard Commercial Certificate,O=SWITCH,L=Zuerich,ST=Zuerich,C=CH"

Permit by VO
~~~~~~~~~~~~

Command:

.. code-block:: bash

   pap-admin add-policy permit --resource "RESOURCE_ID" --action "ACTION_ID" vo="VO"

The name of a VO is the string that appears between, but no including,
the first two forward slashes ('/') of an FQAN.

Here's an example that permits a user identified by their VO:

.. code-block:: bash

   pap-admin add-policy permit --resource "http://example.org" --action "http://example.org/action" vo="atlas"

Permit by Primary FQAN
~~~~~~~~~~~~~~~~~~~~~~

Command:

.. code-block:: bash

   pap-admin add-policy permit --resource "RESOURCE_ID" --action "ACTION_ID" pfqan="FQAN"

The provided FQAN must be in full canonical (long) form (e.g.
``/atlas/Role=Production/Capability=NULL`` or
``/atlas/Role=NULL/Capability=NULL``). You may use `Java regular
expression <http://java.sun.com/j2se/1.5.0/docs/api/java/util/regex/Pattern.html>`__
in this string.

Here's an example that permits a user identified by their primary FQAN:

.. code-block:: bash

   pap-admin add-policy permit --resource "http://example.org" --action "http://example.org/action" pfqan="/atlas/Role=Production/.*"

Permit by Secondary FQAN
~~~~~~~~~~~~~~~~~~~~~~~~

Command:

.. code-block:: bash

   pap-admin add-policy permit --resource "RESOURCE_ID" --action "ACTION_ID" fqan="FQAN"

The provided FQAN must be in full canonical (long) form (e.g.
``/atlas/Role=Production/Capability=NULL`` or
``/atlas/Role=NULL/Capability=NULL``). You may use `Java regular
expression <http://java.sun.com/j2se/1.5.0/docs/api/java/util/regex/Pattern.html>`__
in this string.

Here's an example that permits a user identified by their secondary
FQAN:

.. code-block:: bash

   pap-admin add-policy permit --resource "http://example.org" --action "http://example.org/action" pfqan="/atlas/higgs/Role=NULL/Capability=NULL"


Explicit Banning
~~~~~~~~~~~~~~~~

At times you may wish to deny a specific user, FQAN, or VO. As you may
have guessed by looking at the commands above, you can create deny
policies by changing the ``permit`` argument to ``deny``.

Here are some examples:

.. code-block:: bash

   pap-admin add-policy deny --resource "http://example.org" --action "http://example.org/action" pfqan="/atlas/higgs/Role=NULL/Capability=NULL"

   pap-admin add-policy deny --resource "http://example.org" --action "http://example.org/action" vo="cms"


Removing Policies
~~~~~~~~~~~~~~~~~

The command ``pap-admin remove-policy POLICY_ID`` will remove an
existing policy from the PAP. The POLICY_ID is the ID for the policy
shown by the ``pap-admin list-policies --show-all-ids`` command.

Loading and Testing Policies
----------------------------

Now that you have some basic policy commands you should experiment with
creating and testing policies.

To test the policies you can use the command ``pepcli``. This debugging
tool can be used in two different ways. First you can supply it the cert
of a user or you can mock up a request by specifying a subject DN and
primary and second FQANs. See the ``pepcli``
:ref:`documentation <argus_pepc_cli>` for complete list of options or use the
examples below.

This command provides the result that the user, represented by the given
certificate, would receive:

.. code-block:: bash

   pepcli --pepd !http://127.0.0.1:8154/authz --resourceid "http://example.org" --actionid "http://example.org/action" --certchain CERT_PATH

The certificate may be an end-entity certificate, a plain proxy
certificate, or a proxy certificate with VOMS extensions.

This command allows you to create a request containing a specific user
DN, primary FQAN (the first one listed), and secondary FQANs (subsequent
ones listed):

.. code-block:: bash

   pepcli --pepd !http://127.0.0.1:8154/authz --resourceid "http://example.org" --actionid "http://example.org/action"  --subjectid USER_DN --fqan PRIMARY_FQAN --fqan SECONDARY_FQAN --fqan SECONDARY_FQAN


So, for example add a policy to allows people with the primary FQAN of
``/atlas/higgs/Role=NULL/Capability=NULL`` to be permitted using the
following command

.. code-block:: bash

   pap-admin add-policy permit --resource "http://example.org" --action "http://example.org/action" pfqan="/atlas/higgs/Role=NULL/Capability=NULL"

Then test the policy using the ``pepcli`` command

.. code-block:: bash

   pepcli --pepd !http://127.0.0.1:8154/authz --resourceid "http://example.org" --actionid "http://example.org/action"  --subjectid  "CN=John Smith,OU=Standard Commercial Certificate,O=SWITCH,L=Zuerich,ST=Zuerich,C=CH" --fqan "/atlas/higgs/Role=NULL/Capability=NULL"

The result should be a permit.

Cleaning Up
-----------

Before you go further be sure to clean out of your test policies from
the PAP. You do this by removing select policies as shown above or you
may use the command

.. code-block:: bash

   pap-admin remove-all-policies

to remove all policies from the PAP. Be sure to restart your PDP as well so that it
picks up your new policy set.

.. note::
   You are now done with this quick-start guide. At this point you
   should have a functioning Argus service and be able to add/remove basic
   policies from your site. It is recommended that you
