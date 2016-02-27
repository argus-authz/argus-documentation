%META:TOPICINFO{author="ad968f62f612332eff6b" date="1361525528"
format="1.1" reprev="1.27" version="1.27"}%
%META:TOPICPARENT{name="AuthorizationFramework"}%

Argus PEP Server Obligation Handlers
====================================

Obligation handlers are plugins used by the authorization service in
order to adjust the environment, under which an action will run, to a
state that meets a particular set of obligations. Obligation are things
like "write output to directory X" or "perform all work as user 1".

Grid Map POSIX Account Mapping Obligation Handler
-------------------------------------------------

**NOTE: The Grid Map Account Mapping Obligation Handler only works with
the `gLite Grid Authorization Profile
PIP <AuthZPEPPIP#gLite_Grid_Authorization_Profile>`__, or with clients
implementing the `XACML Grid Worker Node Authorization Profile
(v.1.0) <https://edms.cern.ch/document/1058175>`__ or the `XACML Grid
Computing Element Authorization Profile
(v.1.0) <https://edms.cern.ch/document/1078881>`__ specifications.**

This obligation handler maps a subject ID, given as a DN, and set of
FQANs, for example those provided by the `gLite Grid Authorization
Profile PIP <AuthZPEPPIP#gLite_Grid_Authorization_Profile>`__, in to a
POSIX account (login name, primary group name and secondary group
names).

This mapping is controlled by two `grid map files <AuthZMapFile>`__, one
that provides the mapping of the subject to an account indicator (login
name or pool account indicatior) and one that maps the subject to a set
of group names (one primary and any number of secondary).

Configuration
~~~~~~~~~~~~~

#. Create the account and group `mapping files <AuthZMapFile>`__
   appropriate for your environment
#. Create a new `INI section <AuthZINIFile>`__ for your handler (you may
   choose any valid INI section name)
#. To handler INI section add the ``parserClass`` property with the
   value
   ``org.glite.authz.pep.obligation.dfpmap.DFPMObligationHandlerConfigurationParser``
#. Define at least the required ( ``accountMapFile``, ``groupMapFile``,
   ``gridMapDir``) and any optional configuration properties for this
   handler

OH Configuration Properties
^^^^^^^^^^^^^^^^^^^^^^^^^^^

+----------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------+-------------+---------------------------------------------------------------+---------------+
| Property                               | Description                                                                                                                                | Required?   | Default Value                                                 | Since         |
+========================================+============================================================================================================================================+=============+===============================================================+===============+
| ``accountMapFile``                     | The absolute path to the `map file <AuthZMapFile>`__ used to map a subject to a POSIX login name.                                          | Y           | None.                                                         | Argus 1.0     |
+----------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------+-------------+---------------------------------------------------------------+---------------+
| ``groupMapFile``                       | The absolute path to the `map file <AuthZMapFile>`__ used to map a subject to a set of POSIX groups.                                       | Y           | None.                                                         | Argus 1.0     |
+----------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------+-------------+---------------------------------------------------------------+---------------+
| ``gridMapDir``                         | The absolute path to the grid map directory.                                                                                               | Y           | None.                                                         | Argus 1.0     |
+----------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------+-------------+---------------------------------------------------------------+---------------+
| ``handledObligationId``                | The identifier of the obligation handled by the handler. The obligation handler is triggered only if the obligation ID match this value.   | N           | ``http://glite.org/xacml/obligation/local-environment-map``   | Argus 1.5     |
+----------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------+-------------+---------------------------------------------------------------+---------------+
| ``preferDNForLoginName``               | Indicates whether to prefer a DN based mapping for the login name mapping over a primary FQAN login name mapping.                          | N           | ``true``                                                      | Argus 1.1     |
+----------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------+-------------+---------------------------------------------------------------+---------------+
| ``preferDNForPrimaryGroupName``        | Indicates whether to prefer a DN based mapping for the primary group name mapping over a primary FQAN group name mapping                   | N           | ``true``                                                      | Argus 1.2     |
+----------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------+-------------+---------------------------------------------------------------+---------------+
| ``noPrimaryGroupNameIsError``          | Indicates that the failure to find a primary group mapping in the group map file cause the obligation handler to fail..                    | N           | ``false``                                                     | Argus 1.2     |
+----------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------+-------------+---------------------------------------------------------------+---------------+
| ``refreshPeriod``                      | The period, in minutes, between when the map files are checked, and if they have been changed, reread.                                     | N           | 15                                                            | Argus 1.0     |
+----------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------+-------------+---------------------------------------------------------------+---------------+
| ``requireSubjectKeyInfo``              | The obligation handler will only be applied if the request subject contains a *key-info* attribute (PEM encoded certificate)               | N           | ``true``                                                      | Argus 1.3.0   |
+----------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------+-------------+---------------------------------------------------------------+---------------+
| ``useSecondaryGroupNamesForMapping``   | The obligation handler will create lease file names containing the secondary groups of the user                                            | N           | ``true``                                                      | Argus 1.3.1   |
+----------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------+-------------+---------------------------------------------------------------+---------------+

**NOTE: the default ``preferDNForLoginName`` property value was *false*
for Argus 1.0 and Argus 1.1. From Argus 1.2 the default is *true*.**

Required Response Obligation Trigger
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

This obligation handler is triggered if the PDP response contains the
obligation ``http://glite.org/xacml/obligation/local-environment-map``,
or the value defined by the *handledObligationId* parameter.

Required Request Attributes
~~~~~~~~~~~~~~~~~~~~~~~~~~~

This obligation handler **requires** the following request attributes in
order to correctly map the user. The attributes can be provided by the
`gLite Grid Authorization Profile
PIP <AuthZPEPPIP#gLite_Grid_Authorization_Profile>`__, or directly by
the clients implementing the `XACML Grid Worker Node Authorization
Profile (v.1.0) <https://edms.cern.ch/document/1058175>`__ or the `XACML
Grid Computing Element Authorization Profile
(v.1.0) <https://edms.cern.ch/document/1078881>`__ specifications.

-  The Subject Identifier

   -  **type:** Subject
   -  **id:** urn:oasis:names:tc:xacml:1.0:subject:subject-id
   -  **data type:** urn:oasis:names:tc:xacml:1.0:data-type:x500Name
   -  **multiple values allowed:** no
   -  **description:** This is the Subject DN as given in the end-entity
      certificate. It is in RFC2253 format.

-  The VOMS Primary FQAN

   -  **type:** Subject
   -  **id:** http://glite.org/xacml/attribute/fqan/primary
   -  **data type:** http://glite.org/xacml/datatype/fqan
   -  **issuer:** DN of the attribute certificate issuer
   -  **multiple values allowed:** no
   -  **description:** The primary Fully Qualified Attribute Name (FQAN)
      for the subject

-  The VOMS FQANs

   -  **type:** Subject
   -  **id:** http://glite.org/xacml/attribute/fqan
   -  **data type:** http://glite.org/xacml/datatype/fqan
   -  **multiple values allowed:** yes
   -  **description:** All the Fully Qualified Attribute Name (FQAN)s
      for the subject

Response Obligation Results
~~~~~~~~~~~~~~~~~~~~~~~~~~~

This result of this obligation handler is the **replacement** of the
generic ``http://glite.org/xacml/obligation/local-environment-map`` with
the more specific obligation
``http://glite.org/xacml/obligation/local-environment-map/posix``.

This later obligation carries the account and group names in the
following attribute assignments:

-  one ``http://glite.org/attribute/xacml/user-id`` account login name
-  zero or one ``http://glite.org/attribute/xacml/group-id/primary``
   primary group name
-  zero or more ``http://glite.org/xacml/attribute/group-id`` secondary
   group names

Example Configuration
^^^^^^^^^^^^^^^^^^^^^

This is an example PEPd configuration file with one gridmap POSIX
account mapping obligation handler defined:

::

    [SERVICE]
    entityId = http://argus.example.org/pep
    hostname = argus.example.org
    obligationHandlers = ACCOUNT_MAPPING_OH

    [PDP]
    pdps = https://argus.example.org:8152/authz

    [ACCOUNT_MAPPING_OH]
    parserClass = org.glite.authz.pep.obligation.dfpmap.DFPMObligationHandlerConfigurationParser
    accountMapFile = /etc/grid-security/grid-mapfile
    groupMapFile = /etc/grid-security/group-mapfile
    gridMapDir = /etc/grid-security/gridmapdir

Account and Group Mapping
~~~~~~~~~~~~~~~~~~~~~~~~~

This Obligation Handler uses the following logic to determine the
mapping of the subject to a POSIX account.

Preconditions
'''''''''''''

-  The input to this process is the subject DN of the end-entity
   certificate of the user and optionally a primary FQAN and a list of
   secondary FQANs.
-  When dealing with the account map file (the gridmap file) and the
   group map file, entries are evaluated in the order listed in the
   file. Once a match is found processing stops.
-  The grid map directory is populated with information for all
   configured pool accounts on the system. A pool account is considered
   "configured" if there is a zero-byte file, whose name is the pool
   account name, in the grid map directory. The grid map directory must
   also be read/writable by the user running the authorization service.

Mapping Steps
'''''''''''''

#. If a primary FQAN is given it is checked against the mappings listed
   in the account map file. If the primary FQAN matches a key in the map
   file then the associated value provides the account indicator.
#. If no account indicator was determined by means of the primary FQAN
   the subject DN is checked against the mappings listed in the account
   map file. If the DN matches a key in the map file then the associated
   value provides the account indicator. If no match is found processing
   stops and no map is available.
#. If the account indicator starts with a period ('.'), its value,
   without the period, is considered to be a pool account name prefix.
   If the account indicator does not start with a period it is a POSIX
   account name. If no account indicator was determined the mapping
   process fails.
#. If a primary FQAN is given then it is evaluated against entries in a
   group map file. The first entry that matches determines the primary
   group name. If no match is found, the mapping process fails.
#. If one or more secondary FQANs are given then each one is matched
   against the group map file and each match determines a secondary
   group name. If no matches occur then there are no secondary group
   names associated with the account.
#. If the account indicator is a POSIX account name, and zero or one
   primary group and zero or more secondary group names were determined
   then the mapping is completed. The user is mapped to that account.
#. If the account indicator is a pool account name prefix a lookup in
   the grid map directory occurs. The file looked for is generated
   according to the template
   ``encoded_dn{:primary_group_name{:secondary_group_name}*}?`` with the
   secondary group names listed in ascending alphabetical order.
#. If the file exists and has a link count of 2 then the hard link is
   followed to a file whose name is used as the POSIX account name. The
   last modified time of the two files is updated to the current time.
   If the link count is not 2 or the POSIX account name does not start
   with the account name prefix, the mapping process fails.
#. If the file does not exist, a list of files, within the grid map
   directory, matching the pool account name prefix followed exclusively
   by one or more numeric digits, is retrieved. An example regular
   expression representation of this would be ``prod[0-9]+``
#. This list of files is searched for a file whose link count is 1, this
   is a candidate pool account file. A hard link to the pool account
   file, whose name corresponds to the filled in template described
   above is then created. If no such pool account file is found the
   mapping process fails.
#. The pool account file link count is rechecked, if it is more than 2
   (indicating another request mapped to that same file at the same
   time) the created link is removed and the mapping process reverts to
   the previous step.
#. If the link count is 2 the mapping is complete. The user is mapped to
   the pool account corresponding to the given file.

**Note:** In the case where no FQANs are available, this obligation
handler only returns a login name. No group information is returned.

DN Encoding Rules
'''''''''''''''''

#. Leading zeros are removed from attribute types that are encoded as
   dotted decimal OIDs
#. DirectoryString attribute values of type PrintableString and
   UTF8String are not output in hexadecimal format
#. DirectoryString attribute values of types other than PrintableString
   and UTF8String are output in hexadecimal format
#. Leading and trailing white space characters are removed from
   non-hexadecimal attribute values (unless the value consists entirely
   of white space characters)
#. Internal substrings of one or more white space characters are
   converted to a single space in non-hexadecimal attribute values
#. Relative Distinguished Names containing more than one Attribute Value
   Assertion (AVA) are output in the following order: an alphabetical
   ordering of AVAs containing standard keywords, followed by a numeric
   ordering of AVAs containing OID keywords.
#. The only characters in attribute values that are escaped are those
   which section 2.4 of RFC 2253 states must be escaped (they are
   escaped using a preceding backslash character)
#. The entire name is converted to upper case using US localization
#. The entire name is converted to lower case using US localization
#. The name is finally normalized using normalization form KD, as
   described in the Unicode Standard and UAX #15
#. The DN is converted to the non-standard, openssl one line format
#. The string is then URL-encoded

Be Aware
''''''''

-  A reorder of entries in the group map file that results in a change
   in the primary group will cause an incoming user to be mapped to a
   new account as this information is part of the link created in the
   grid map directory.
-  A reordering of entries in the group map file that does **not**
   result in a change to the primary group but provides the same
   secondary groups in a different order does **not** result in a new
   mapping for a returning user since the link created in the grid map
   directory orders the secondary group names in ascending alphabetical
   order.
