%META:TOPICINFO{author="chad\_2elajoie\_40switch\_2ech"
date="1263402651" format="1.1" version="1.3"}%
%META:TOPICPARENT{name="AuthorizationFramework"}%

Argus: Policy Enforcement Point Daemon: Troubleshooting
=======================================================

PEP Daemon Returns "Stale" Results
----------------------------------

The PEPd keeps a short (10 minutes by default) response cache. So
identical requests made within a short time period will always provide
the same answer. If you're testing this can be a pain. You can clear the
cache using the ``pepdctl clearResponseCache`` command. You can also
turn of the cache through the ``maximumCachedResponses`` documented in
the `PEPd configuration <AuthZPEPDConfig>`__. Just be sure to enable it
again before you put the system under heavy load.

Note that the PDP also caches the policies it reads, so during testing
you may also want to `configure the PDP <AuthZPDPConfig>`__ to more
quickly pick up policies from the PAP via the ``retentionInterval``
option.

Testing a policy without submitting a job
-----------------------------------------

When authoring new policies or troubleshooting an existing policies it
can be helpful to mock up requests, instead of getting users to perform
the request over and over as you diagnose the problem. The PEPd offers a
C and Java command line tool. The `C tool <AuthZPEPCCLI>`__ is useful
for specifically testing cases where policies are based on the resource
ID, action ID, subject ID, and FQAN attributes. The `Java
tool <AuthZPEPJCLI>`__ allows you to mock up any request.

Here is an example of using the C command line tool to test a job
submission. It specifies the PEPd service, resource ID, action ID,
user's DN, and primary FQAN.

::

    /opt/glite/bin/pepcli  -v -x \
       -p http://vesta.switch.ch:8154/authz \
       -r http://authz-interop.org/xacml/resource/resource-type/wn \
       -a http://authz-interop.org/xacml/action/action-type/execute-now \
       -s "CN=Alessandro Usai,O=SWITCH,C=CH,DC=users,DC=switch,DC=grid,DC=quovadisglobal,DC=com" \
       -f /dech \
