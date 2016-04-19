.. _argus_concepts:

Argus Concepts
==============

Argus Introduction
------------------

The Argus authorization service is designed to answer questions in the
form of *Can user X perform action Y on resource Z at this time?* Not
surprisingly, two pieces of information are required to answer this
question; the request that describes X, Y, and Z and the policy against
which the request is evaluated. The purpose of this introduction is to
provide an understanding of the logical contents of the request and the
policy. Such an understanding will help in creating appropriate access
control policies for a service. This introduction does not cover the
command line tools, simple policy language, or underlying XACML policies
used by the authorization service. That information can be found in the
Policy Administration Point documentation.

Attribute Based Descriptions
----------------------------

Before discussing the request and response it is important to understand
how users, actions, and resources are identified. Most grid deployers
will be familiar with X.509 subject distinguished names (DNs) based
access control. Access control systems which render authorization
decisions based only on an identifier, like a subject DN, are known as
identity-based access control systems. However, an application usually
knows more about a user than simply their identifier. It may know for
which organization the user works, or groups and professional
organizations to which the user belongs. An access control system which
can render a decision based on a set of attributes describing the
users/resources/actions is known as an attribute-based access control
system. Note, an attribute-based access control system where policies
are only based on an identifier attribute is functionally equivalent to
an identity-based access control system.

Argus is an attribute-based system. It uses attributes to identify the
user attempting to perform an action, the resource on which the action
is to be performed, the action itself, and other environmental
information. Within the system an attribute is made of 4 pieces of
information: a unique identifier/name, one or more values for the
attribute, the data type of the attribute values (e.g. a string, an
integer, an email address), and an optional identifier for the issuer of
the attribute. The following are examples of attribute based
descriptions.

In an identity based system a user (more properly known as a subject)
might have the identity 'jsmith'. In an attribute based system the
subject might be identified by the following attributes:

-  ``id``: subject-id, ``datatype``: string, ``value``: jsmith
-  ``id``: org, ``datatype``: string, ``value``: CERN
-  ``id``: affiliation, ``datatype``: string, ``value``: employee
-  ``id``: vo, ``datatype``: string, ``value``: lhcb, cms, atlas

In another example an action in an identity based access control system
might be identified by the string 'submit-job'. In an attribute based
system the action might be identified with the following attributes:

-  ``id``: action-id, ``datatype``: string, ``value``: submit-job
-  ``id``: pilot-job, ``datatype``: boolean, ``value``: false
-  ``id``: executable, ``datatype``: string, ``value``: /usr/bin/myexec
-  ``id``: expected-execution-duration, ``datatype``: integer,
   ``value``: 10

The exact attributes available within a request are determined by the
application seeking an authorization decision. Obviously each unique
application will have different actions it wishes to protect, a
different means of describing itself, and possibly different information
about the subject. Also, as can be seen by these examples, the amount of
information upon which a decision could be based is larger within an
attribute based access control system than an identity based system. As
demonstrated, the single piece of identity data used in an identity
based system can also be used within an attribute based one.

The Request
+++++++++++

An authorization decision request is simply a set of four different
attributes collections. The four collections are:

-  *subject* - contains attribute describing user who is trying to
   perform an action
-  *action* - contains attributes describing the action the subject is
   attempting to perform
-  *resource* - contains attributes describing the program within which
   the subject is attempting to perform the action
-  *environment* - contains attributes relevant to the decision but not
   part of the previous three collection (e.g. time/date of the request,
   machine used by the subject, etc.).

A request must contain at least one attribute in each of the *subject*,
*action*, and *resource* sets but it is common for there to be no
environment attributes.

The Policy
++++++++++

A policy is a collection of rules that are evaluated to determine the
result of an authorization request. The result of a policy evaluation
may be:

-  *permit* - indicates the subject is permitted to perform the action
   on the resource
-  *deny* - indicates the subject is not permitted to perform the action
   on the resource
-  *not applicable* - indicates no policy applied to the request and so
   no decision could be reached
-  *indeterminate* - indicates there was an error evaluating the policy

In order to determine whether a rule is met each rule contains a
*target*. The target defines one or more combination of attribute which
trigger the rule. When more than one combination is listed any one may
trigger the rule. For example, the target of a rule may stipulate the
following combination:

-  subject attribute ``vo`` has a value of *atlas* and the ``id`` of the
   action is *job-submit* and the ``id`` of the resource is *cern-ce*
-  subject attribute ``id`` has a value of *john* and the ``id`` of the
   action is *job-submit* and the ``id`` of the resource is *cern-ce*
-  subject attribute ``id`` has a value of *jane* and the ``id`` of the
   action is *job-submit* and the ``id`` of the resource is *cern-ce*

As you can see, each combination of attributes within the target needs
to be complete (that is they need to describe the whole situation that
would trigger a rule), there is no mechanism for expressing logical
operations like AND, OR, NOT. As you can imagine this could lead to a
lot of repeated information. To help alleviate this, policies, the
container of rules, can also contain targets. Therefore the previous
rule target could be written as follows:

-  A policy with target: the ``id`` of the action is *job-submit* and
   the ``id`` of the resource is *cern-ce*
-  A ``permit`` rule with the target combination:

   -  subject attribute ``vo`` has a value of *atlas*
   -  subject attribute ``id`` has a value of *john*
   -  subject attribute ``id`` has a value of *jane*

You can also have multiple rules within a single policy, for example:

-  A policy with target: the ``id`` of the action is *job-submit* and
   the ``id`` of the resource is *cern-ce*
-  A ``permit`` rule with the target combinations:

   -  subject attribute ``vo`` has a value of *atlas*
   -  subject attribute ``id`` has a value of *john*
   -  subject attribute ``id`` has a value of *jane*

-  A ``deny`` rule with the target combinations:

   -  subject attribute ``id`` has a value of *christoph*

In fact, policies can even contain other policies which helps in the
case where you may want a policy about a resource which in turn contains
a policy about actions (at that resource) and finally rules for that
action. For example:

-  A policy with target: ``id`` of the resource is *cern-ce* contains:

   -  A policy with target: the ``id`` of the action is *job-submit*
   -  A ``permit`` rule with the target combination:

      -  subject attribute ``vo`` has a value of *atlas*

   -  A ``deny`` rule with the target combination:

      -  subject attribute ``id`` has a value of *christoph*

   -  A policy with target: the ``id`` of the action is *job-manage*
   -  A ``permit`` rule with the target combination:

      -  subject attribute ``id`` has a value of *john*
      -  subject attribute ``id`` has a value of *jane*

Not Applicable and Indeterminate Results
++++++++++++++++++++++++++++++++++++++++

In some cases, either because no policy applied to the request or
because there was an internal error evaluating a policy, a decision
other than ``permit`` or ``deny`` is returned. In the event that a
result of ``not applicable`` or ``indeterminate`` is returned Argus will
treat this as a ``deny``. Thus Argus is said to be a *deny biased*
system.

Example Requests and Policies
+++++++++++++++++++++++++++++

Look at the :ref:`dedicated page <argus_request_and_policy_examples>` for request and policy examples.

Identifiers within Argus
------------------------

Argus makes use of various identifiers for attribute identifiers and
values. The identifiers must by
`URIs <http://tools.ietf.org/html/rfc3986>`__
(`URNs <http://tools.ietf.org/html/rfc2141>`__ or URLs). The
responsibility for defining such identifiers falls to different
individuals, depending on the expected scope of use of the identifiers.

Entity ID
+++++++++

For those familiar with other grid services one of the first differences
that you will encounter when setting up Argus is the requirement to
provide *entity IDs* for the service components. These IDs provide a way
of uniquely identify a logical service component. We use the term
"logical service component" because each service component may be
clustered. So the logical instance is the set containing all the
physical instances participating in the cluster.

It is the deployer of service component that determines this identifier.
The identifier may be any URI for which the deployer is authoritative.
That is, it must come from a domain (in the case of a URL) or namespace
(in the case of a URN) that the deployer controls. The following formula
is a reasonable means for generating these identifiers:
``http://{authz_domain}/{service_component_identifier}``. The domain
should be a domain name that is not linked to any particular servers
hostname but is instead related to the service (e.g. authz.example.org).
The component identifier should be ``pap``, ``pdp``, or ``pepd``
depending on which component is being installed.

If an organization runs more than one component an additional qualifier
may be added to appended to the path of the URL identifier. For example
an organization that runs a different PEPd for each computer cluster
they operate might choose the entity IDs
``http://authz.example.org/pepd/cluster1`` and
``http://authz.example.org/pepd/cluster2``

Resource ID
-----------

The resource identifier used to identify the resource being protected by
the authorization service (e.g. a compute cluster, a portal). This
identifier is specific to a given instance of the resource. If an
organization runs two different portals each portal receives a different
ID. These identifiers may then be used within a policy in order to
indicate policies that apply to the specific resource (i.e. policy A
applies to portal 1 and policy B applies to portal 2).

The resource identifier is selected by the deployer of the resource. Two
reasonable ways for generating this identifier are:

-  if the resource has a natural URI identifier associated with it (e.g.
   the main page of the portal) that may be used
-  if the resource does not have a natural URI identifier a synthetic
   one may be created with the following formula:
   ``http://{authz_domain}/resource/{resource_identifier}`` The
   ``authz_domain`` should be a domain name that is not linked to any
   particular server's hostname but is instead related to the
   authorization service (e.g. authz.example.org). The resource
   identifier is simply a unique string for the protected resource. A
   human intelligible string is best (e.g. ``sequencingPortal``).

Action ID
+++++++++

The action identifier is used to identify the action for which the
resource is requesting authorization. This action identifier is specific
to a given piece of software but all instances of that application use
the same identifier for a given action, it is not deployment specific.

The action identifier is selected by the developer of the application.
The identifier may be generated in one of two ways:

-  If the application is widely used a request could be made to a
   standards body, such as `IANA <http://www.iana.org/protocols>`__, for
   an unique identifier for each action of the application. This way all
   implementations of the specification will likely use the same
   identifier and thus make it a bit easier for policy writers.
-  The application developer may generate an identifier. A reasonable
   formula for such an identifier is:
   ``http://{application_domain_name}/{application_name}/action/{action_id} where the domain name is the domain component of the application's website and the action ID is a human readable string for the action (e.g. =readFile``,
   ``addUser``)

Attribute ID
++++++++++++

The attributes produced by an application identify bits of information
that the application was able to gather and make available for an
authorization request. The identifiers are specific to a given piece of
software but all instances of that application use the same identifier
for a given attribute, it is not deployment specific.

The attribute identifier is selected by the developer of the
application. The identifier may be generated in a couple of ways:

-  If the attribute is already defined by an existing standard (the LDAP
   schema standards are a great place to start looking) the identifier
   from that standard may be used. In the case of the LDAP schema the
   URN ``urn:oid:{ldap_attribute_oid}`` can be used.
-  If the attribute is likely to be widely used a request could be made
   to a standards body, such as
   `IANA <http://www.iana.org/protocols>`__, for an unique identifier
   for this attribute. This way all implementations of the specification
   will likely use the same identifier and thus make it a bit easier for
   policy writers.
-  The application developer may generate an identifier. A reasonable
   formula for such an identifier is:
   ``http://{application_domain_name}/{application_name}/attribute/{attribute_id}``
   where the domain name is the domain component of the application's
   website and the attribute ID is a human readable string for the
   attribute (e.g. ``username``, ``entitlements``)

In general, application developers should prefer already defined
attributes over creating their own.

Conclusion
----------

At this point you should understand what an attribute is, that a request
is a made of subject, action, resource, and environment attributes, what
a rule is and that policies are a collection of rules. You should also
understand that a policy or rule is triggered if any one of the
combination of attribute/values listed within its target is present
within the request.
