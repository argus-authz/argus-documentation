.. _argus_mapping:

Mapping details
===============

.. attention::
   | New behavior from Argus 1.7.0

Argus 1.7.0 fixes an incorrect behavior in groups mapping, in particular
in the handling of secondary group names, as described in issues `pep-7`_ and `pep-11`_.

This changes have an impact in the creation of hard links in the ``/etc/grid-security/gridmapdir``
directory, links used to map Grid users to a local pool account.

Since version 1.7.0, these link are created with the following pattern::

   <dn>:<primary group>:<secondary group>

If needed, to keep compatibility with the LCMAPS ``-do_not_use_secondary_gids``
option used by the CREAM YAIM module, in Argus 1.7 you would need to add::

   useSecondaryGroupNamesForMapping = false

in the ``[ACCOUNTMAP_OH]`` section of the ``/etc/argus/pepd/pepd.ini``
and restart the service.


Example
^^^^^^^

With the default settings, Argus PEP server use secondary groups for mapping.
Performing a request with ``pepcli``, for example::

   $ pepcli --capath /etc/grid-security/certificates/ --cert .globus/usercert.pem --key .globus/userkey.pem -k /tmp/x509up_u0 -p https://argus.cnaf.test:8154/authz -a "ANY" -r "resource-2"
   Resource: resource-2
   Decision: Permit
   Obligation: http://glite.org/xacml/obligation/local-environment-map/posix (caller should resolve POSIX account mapping)
   Username: tst13
   Group: testvo
   Secondary Groups: testvo testvo

Into ``/etc/grid-security/gridmapdir`` we'll found the mapping::

   $ ll -hrt /etc/grid-security/gridmapdir/
   ...
   -rw-r--r--. 2 root root 0 Jan 25 14:17 tst13
   -rw-r--r--. 2 root root 0 Jan 25 14:17 %2fc%3dit%2fo%3digi%2fcn%3dtest0:testvo:testvo

To disable the usage of secondary groups, edit ``/etc/argus/pepd/pepd.ini`` setting::

   [ACCOUNTMAP_OH]
   parserClass = org.glite.authz.pep.obligation.dfpmap.DFPMObligationHandlerConfigurationParser
   accountMapFile = /etc/grid-security/grid-mapfile
   groupMapFile = /etc/grid-security/groupmapfile
   gridMapDir = /etc/grid-security/gridmapdir
   
   useSecondaryGroupNamesForMapping = false
   
Restart the PEP server. Now, repeating the same request with ``pepcli``
the mapping obtained will be::

   $ ll -hrt /etc/grid-security/gridmapdir/
   ...
   -rw-r--r--. 2 root root 0 Jan 25 14:08 tst06
   -rw-r--r--. 2 root root 0 Jan 25 14:08 %2fc%3dit%2fo%3digi%2fcn%3dtest0:testvo


.. _pep-7: https://github.com/argus-authz/argus-pep-server/issues/7
.. _pep-11: https://github.com/argus-authz/argus-pep-server/issues/11