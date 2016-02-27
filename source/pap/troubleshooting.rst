.. _argus_pap_troubleshooting:

Argus: Policy Administration Point (PAP): Troubleshooting
=========================================================

Policies that use the ``pfqan`` attribute do not work properly (PAP v. 1.1)
===========================================================================

PAP version 1.1 ships with a buggy configuration file that encodes
policies that uses the ``pfqan`` attribute in a way that it's not
understood by the PDP. This bug is tracked
`here <https://savannah.cern.ch/bugs/?72078>`__. There is a quick
workaround for this issue:

-  save your policies to a file
-  remove all policies from the pap (pap-admin rap command).
-  shut down the pap service.
-  edit the /opt/argus/pap/conf/attribute-mappings.ini and substitue the
   line: <pre>pfqan.xacml-id =
   http://authz-interop.org/xacml/subject/voms-primary-fqan\ </pre> with
   this line: <pre>pfqan.xacml-id =
   http://glite.org/xacml/attribute/fqan/primary\ </pre>
-  start the pap service.
-  import the previously saved policies with the pap-admin apf command.
