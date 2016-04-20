.. _argus_pep_pip:

Argus PEP Server Policy Information Points (PIP)
================================================

Policy Information Points (PIPs) are plugins to the authorization
service that help populate and/or complete an authorization request.
PIPs may rely on information already within the request or they may
simply be able to self generate the data that they will add.

.. _argus_pep_pip_request_validator:

Request Validator PIP
---------------------

**NOTE: This PIP is new in Argus 1.3 (EMI).**

The Request Validator PIP validates the incoming authorization request.
It checks that the incoming authorization request contains at least one
subject, one resource and one action, and that the attributes within
them have at least one value, which is not null or an empty string.

Configuration
^^^^^^^^^^^^^

#. Create a new INI section for you PIP (you may
   choose any valid INI section name. e.g. ``REQVALIDATOR_PIP``)
#. Into the PIP INI section add the ``parserClass`` property with the
   value
   ``org.glite.authz.pep.pip.provider.RequestValidatorPIPIniConfigurationParser``
#. Configure how the authorization request must be validated

PIP Configuration Properties
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

+----------------------------+---------------------------------------------------------------+-----------+---------------+
| Property                   | Description                                                   | Required? | Default Value |
+============================+===============================================================+===========+===============+
| validateRequestSubjects    | Require at least one subject with non-empty attribute values  | N         | ``true``      |
+----------------------------+---------------------------------------------------------------+-----------+---------------+
| validateRequestResources   | Require at least one resource with non-empty attribute values | N         | ``true``      |
+----------------------------+---------------------------------------------------------------+-----------+---------------+
| validateRequestAction      | Require one action with non-empty attribute values            | N         | ``true``      |
+----------------------------+---------------------------------------------------------------+-----------+---------------+
| validateRequestEnvironment | Require one environment with non-empty attribute values       | N         | ``false``     |
+----------------------------+---------------------------------------------------------------+-----------+---------------+

Example Configuration
^^^^^^^^^^^^^^^^^^^^^

The following example shows a PEP server configuration with the request
validator PIP enabled. The PIP validates the incoming authorization
request and checks that it contains at least one subject, at least one
resource and one action, all with non-empty or null attributes values.

::

    [SERVICE]
    entityId = https://example.org/pep
    hostname = example.org
    pips = REQVALIDATOR_PIP

    [PDP]
    pdps = http://localhost:8152/authz

    [SECURITY]
    trustInfoDir = /etc/grid-security/certificates

    [REQVALIDATOR_PIP]
    parserClass = org.glite.authz.pep.pip.provider.RequestValidatorPIPIniConfigurationParser
    validateRequestSubjects = true
    validateRequestResources = true
    validateRequestAction = true
    validateRequestEnvironment = false



.. _argus_pep_pip_subject_converter:

OpenSSL Subject Converter PIP
-----------------------------

**NOTE: This PIP is new in Argus 1.3 (EMI).**

The OpenSSL Subject Converter PIP transforms on-the-fly an incoming
request subject attribute *subject-id* and/or attribute *subject-issuer*
value from the old, unsupported and wrong OpenSSL oneline format (e.g.
"/C=CH/O=example.org/CN=John Doe") into a correct RFC2253 format value
(e.g. "CN=John Doe,O=example.org,C=CH") with the correct datatype.

Configuration
^^^^^^^^^^^^^

#. Create a new INI section for you PIP (you may
   choose any valid INI section name. e.g. ``OPENSSLSUBJECT_PIP``)
#. Into the PIP INI section add the ``parserClass`` property with the
   value
   ``org.glite.authz.pep.pip.provider.OpenSSLSubjectPIPIniConfigurationParser``
#. Configure which subject attribute ID and datatype values must be
   transformed from the OpenSSL format into the RFC2253 format.

PIP Configuration Properties
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

+----------------------------------+------------------------------------------------------------------------------------------------+-----------+---------------------------------------------------------------------------------------------------------+
| Property                         | Description                                                                                    | Required? | Default Value                                                                                           |
+==================================+================================================================================================+===========+=========================================================================================================+
| opensslSubjectAttributeIDs       | The space separated list of subject attribute IDs containing an OpenSSL value to convert       | No        | ``urn:oasis:names:tc:xacml:1.0:subject:subject-id`` ``http://glite.org/xacml/attribute/subject-issuer`` |
+----------------------------------+------------------------------------------------------------------------------------------------+-----------+---------------------------------------------------------------------------------------------------------+
| opensslSubjectAttributeDatatypes | The space separated list of subject attribute datatypes containing an OpenSSL value to convert | No        | ``http://www.w3.org/2001/XMLSchema#string``                                                             |
+----------------------------------+------------------------------------------------------------------------------------------------+-----------+---------------------------------------------------------------------------------------------------------+

Example Configuration
^^^^^^^^^^^^^^^^^^^^^

The following example shows a PEP server configuration with the OpenSSL
Subject Converter PIP enabled, and transforming both the subject
attribute IDs ``urn:oasis:names:tc:xacml:1.0:subject:subject-id`` and
``http://glite.org/xacml/attribute/subject-issuer``, with the datatype
``http://www.w3.org/2001/XMLSchema#string`` values from the OpenSSL
oneline format into the RFC2253 format.

::

    [SERVICE]
    entityId = https://example.org/pep
    hostname = example.org
    pips = OPENSSLSUBJECT_PIP

    [PDP]
    pdps = http://localhost:8152/authz

    [SECURITY]
    trustInfoDir = /etc/grid-security/certificates

    [OPENSSLSUBJECT_PIP]
    parserClass = org.glite.authz.pep.pip.provider.OpenSSLSubjectPIPIniConfigurationParser
    opensslSubjectAttributeIDs = http://glite.org/xacml/attribute/subject-issuer urn:oasis:names:tc:xacml:1.0:subject:subject-id
    opensslSubjectAttributeDatatypes = http://www.w3.org/2001/XMLSchema#string


.. _argus_pep_pip_grid_authz_profile:

Grid Authorization Profile PIP
------------------------------

**NOTE: This is the default profile supported starting from Argus 1.2.**

This PIP allows the PEP client to send only the end-user certificate or
proxy as lone Subject *Key-Info* attribute. It will then parse the
certificate, extract all the information from the certificate required
by the gLite Grid XACML Authorization Profiles, and populate the request
with attributes found in the certificate/proxy.

This PIP implements the `XACML Grid Worker Node Authorization Profile
(v.1.0) <https://edms.cern.ch/document/1058175>`__ and the `XACML Grid
Computing Element Authorization Profile
(v.1.0) <https://edms.cern.ch/document/1078881>`__ specifications.

Configuration
^^^^^^^^^^^^^

#. Create a new INI section for you PIP (you may
   choose any valid INI section name. e.g. GLITEXACMLPROFILE\_PIP)
#. Into the PIP INI section add the ``parserClass`` property with the
   value
   ``org.glite.authz.pep.pip.provider.GLiteAuthorizationProfilePIPIniConfigurationParser``
#. To enable VOMS attribute certificate support add the ``vomsInfoDir``
   property with a value corresponding to the absolute path of the VOMS
   ``vomsdir``, traditionally ``/etc/grid-security/vomsdir``.
#. If, in the ``SECURITY`` section, the ``trustInfoDir`` property is not
   already set, add it with a value of the absolute filesystem path of
   your IGTF trust bundle.
#. Configure which profile IDs are to be accepted.

PIP Configuration Properties
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

+--------------------+--------------------------------------------------------------------------------------+-----------+---------------+
| Property           | Description                                                                          | Required? | Default Value |
+====================+======================================================================================+===========+===============+
| acceptedProfileIDs | The space separated list of accepted authorization profile IDs                       | No        | None.         |
+--------------------+--------------------------------------------------------------------------------------+-----------+---------------+
| vomsInfoDir        | The absolute path to the VOMS ``vomsdir`` directory.                                 | Y         | None.         |
+--------------------+--------------------------------------------------------------------------------------+-----------+---------------+
| vomsInfoRefresh    | The refresh interval time in minutes of the ``vomsInfoDir`` directory.               | No        | 60            |
+--------------------+--------------------------------------------------------------------------------------+-----------+---------------+
| requireCertificate | The request Subject attribute key-info MUST be present in the incoming request.      | No        | ``true``      |
+--------------------+--------------------------------------------------------------------------------------+-----------+---------------+
| requireProxy       | The request Subject attribute key-info MUST to be a proxy (PEM encoded proxy chain). | No        | ``false``     |
+--------------------+--------------------------------------------------------------------------------------+-----------+---------------+

**NOTE: If the ``acceptedProfileIDs`` is not defined, then all profile
IDs present in the request environment *profile-id* attribute are
accepted.**

Required Request Attributes
^^^^^^^^^^^^^^^^^^^^^^^^^^^

This PIP requires that the request environment contains a *profile-id*
attribute with the profile identifier, and that the request subject
contains the certificate, and its chain, that were used to authenticate
to the service, in the *key-info* attribute:

-  The Profile Identifier

   -  **type:** Environment
   -  **id:** http://glite.org/xacml/attribute/profile-id
   -  **data type:** http://www.w3.org/2001/XMLSchema#anyURI
   -  **multiple values allowed:** no
   -  **description:** The profile ID implemented by the incoming
      request.

-  The Certificate or Proxy Certificate (with chain)

   -  **type:** Subject
   -  **id:** urn:oasis:names:tc:xacml:1.0:subject:key-info
   -  **data type:** http://www.w3.org/2001/XMLSchema#string
   -  **multiple values allowed:** no
   -  **description:** The PEM encoded certificate chain. No certificate
      order is assumed however all certificates must be version 3
      certificates. Zero or one VOMS attribute certificate may also be
      included.

Populated Effective Request Attributes
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The PIP will process the request subject *key-info* attribute and
populate the following attributes:

-  The Subject Identifier

   -  **type:** Subject
   -  **id:** urn:oasis:names:tc:xacml:1.0:subject:subject-id
   -  **data type:** urn:oasis:names:tc:xacml:1.0:data-type:x500Name
   -  **multiple values allowed:** no
   -  **description:** This is the Subject DN as given in the end-entity
      certificate. It is in RFC2253 format.

-  The End-entity Certificate Issuer

   -  **type:** Subject
   -  **id:** http://glite.org/xacml/attribute/subject-issuer
   -  **data type:** urn:oasis:names:tc:xacml:1.0:data-type:x500Name
   -  **multiple values allowed:** yes
   -  **description:** This is the Subject DN of the root CA and all
      subordinate CAs that signed within the end-entity certificate
      chain. It is in RFC2253 format.

If VOMS support is enabled and a VOMS certificate is included within a
user's proxy certificate, the following attributes will be populated
within the request:

-  The VO Name

   -  **type:** Subject
   -  **id:** http://glite.org/xacml/attribute/virtual-organization
   -  **data type:** http://www.w3.org/2001/XMLSchema#string
   -  **multiple values allowed:** yes
   -  **description:** The names of the VOs to which the user is a
      member. Currently there is only ever one value.

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

Example Configuration
^^^^^^^^^^^^^^^^^^^^^

The following example shows a PEP Server configuration with the Grid
authorization profile PIP enabled, and accepting both the
``http://glite.org/xacml/profile/grid-ce/1.0`` and the
``http://glite.org/xacml/profile/grid-wn/1.0`` XACML Grid authorization
profiles.

::

    [SERVICE]
    entityId = https://example.org/pep
    hostname = example.org
    pips = GLITEXACMLPROFILE_PIP

    [PDP]
    pdps = http://localhost:8152/authz

    [SECURITY]
    trustInfoDir = /etc/grid-security/certificates

    [GLITEXACMLPROFILE_PIP]
    parserClass = org.glite.authz.pep.pip.provider.GLiteAuthorizationProfilePIPIniConfigurationParser
    vomsInfoDir = /etc/grid-security/vomsdir
    acceptedProfileIDs = http://glite.org/xacml/profile/grid-ce/1.0 http://glite.org/xacml/profile/grid-wn/1.0


.. _argus_pep_pip_common_xacml_authz_profile:

Common XACML Authorization Profile PIP
--------------------------------------

**NOTE: This profile is supported since Argus 1.6 (EMI-3).**

This PIP allows the PEP client to send only the end-user certificate or
proxy as lone Subject *Key-Info* attribute. It will then parse the
certificate, extract all the information from the certificate required
by the Common XACML Authorization Profile, and populate the request with
attributes found in the certificate/proxy.

This PIP implements the `Common XACML Authorization Profile
(1.1.1) <https://twiki.cern.ch/twiki/bin/view/EMI/CommonXACMLProfileV1_1>`__
specifications.

Configuration
^^^^^^^^^^^^^

#. Create a new INI section for you PIP (you may
   choose any valid INI section name. e.g. COMMONXACMLPROFILE\_PIP)
#. Into the PIP INI section add the ``parserClass`` property with the
   value
   ``org.glite.authz.pep.pip.provider.CommonXACMLAuthorizationProfilePIPIniConfigurationParser``
#. To enable VOMS attribute certificate support add the ``vomsInfoDir``
   property with a value corresponding to the absolute path of the VOMS
   ``vomsdir``, traditionally ``/etc/grid-security/vomsdir``.
#. If, in the ``SECURITY`` section, the ``trustInfoDir`` property is not
   already set, add it with a value of the absolute filesystem path of
   your IGTF trust bundle.
#. Configure which profile IDs are to be accepted, normally
   ``http://dci-sec.org/xacml/profile/common-authz/1.1``

PIP Configuration Properties
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

+--------------------+--------------------------------------------------------------------------------------+-----------+---------------+
| Property           | Description                                                                          | Required? | Default Value |
+====================+======================================================================================+===========+===============+
| acceptedProfileIDs | The space separated list of accepted authorization profile IDs                       | No        | None.         |
+--------------------+--------------------------------------------------------------------------------------+-----------+---------------+
| vomsInfoDir        | The absolute path to the VOMS ``vomsdir`` directory.                                 | YES       | None.         |
+--------------------+--------------------------------------------------------------------------------------+-----------+---------------+
| vomsInfoRefresh    | The refresh interval time in minutes of the ``vomsInfoDir`` directory.               | No        | 60            |
+--------------------+--------------------------------------------------------------------------------------+-----------+---------------+
| requireCertificate | The request Subject attribute key-info MUST be present in the incoming request.      | No        | ``false``     |
+--------------------+--------------------------------------------------------------------------------------+-----------+---------------+
| requireProxy       | The request Subject attribute key-info MUST to be a proxy (PEM encoded proxy chain). | No        | ``false``     |
+--------------------+--------------------------------------------------------------------------------------+-----------+---------------+

**NOTE: If the ``acceptedProfileIDs`` is not defined, then all profile
IDs present in the request environment *profile-id* attribute are
accepted.**

Required Request Attributes
^^^^^^^^^^^^^^^^^^^^^^^^^^^

This PIP requires that the request environment contains a *profile-id*
attribute with the profile identifier, and that the request subject
contains the certificate, and its chain, that were used to authenticate
to the service, in the *key-info* attribute:

-  `The Profile
   Identifier <https://twiki.cern.ch/twiki/bin/view/EMI/CommonXACMLProfileV1_1#Profile_Identifier_Attribute>`__
   Attribute

   -  **type:** Environment
   -  **id:** http://dci-sec.org/xacml/attribute/profile-id
   -  **data type:** http://www.w3.org/2001/XMLSchema#anyURI
   -  **multiple values allowed:** no
   -  **description:** The profile ID implemented by the incoming
      request, typically
      ``http://dci-sec.org/xacml/profile/common-authz/1.1``

-  `The Subject Key-Info (certificate or proxy, with
   chain) <https://twiki.cern.ch/twiki/bin/view/EMI/CommonXACMLProfileV1_1#Subject_Key_Info_Attribute>`__
   Attribute

   -  **type:** Subject
   -  **id:** urn:oasis:names:tc:xacml:1.0:subject:key-info
   -  **data type:** http://www.w3.org/2001/XMLSchema#base64Binary
   -  **multiple values allowed:** yes
   -  **description:** The multiple values are the base64 encoded DER
      blocks of the certicate/proxy chain.

Populated Effective Request Attributes
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The PIP will process the request subject *key-info* attribute and
populate the following attributes:

-  The Subject Identifier Attribute

   -  **type:** Subject
   -  **id:** urn:oasis:names:tc:xacml:1.0:subject:subject-id
   -  **data type:** urn:oasis:names:tc:xacml:1.0:data-type:x500Name
   -  **multiple values allowed:** no
   -  **description:** X.509 distinguished name of the end-entity
      certificate. The value is in RFC2253 format, e.g. "CN=John
      Doe,DC=example,DC=org"

-  The Subject Issuer Attribute

   -  **type:** Subject
   -  **id:** http://dci-sec.org/xacml/attribute/subject-issuer
   -  **data type:** urn:oasis:names:tc:xacml:1.0:data-type:x500Name
   -  **multiple values allowed:** yes
   -  **description:** X.509 distinguished name of the authority(ies)
      which issued the end-entity certificate. The values are in RFC2253
      format.

If VOMS support is enabled and a VOMS certificate is included within a
user's proxy certificate, the following attributes will be populated
within the request:

-  The Virtual Organization (VO) Attribute

   -  **type:** Subject
   -  **id:** http://dci-sec.org/xacml/attribute/virtual-organization
   -  **data type:** http://www.w3.org/2001/XMLSchema#string
   -  **multiple values allowed:** yes
   -  **description:** The names of the VOs to which the user is a
      member. Currently there is only ever one value.

-  The Primary Group and Group Attributes

   -  **type:** Subject
   -  **id:** http://dci-sec.org/xacml/attribute/group/primary and
      http://dci-sec.org/xacml/attribute/group
   -  **data type:** http://www.w3.org/2001/XMLSchema#string
   -  **multiple values allowed:** no (primary group), yes (groups)
   -  **description:** The primary group name, and the list of all group
      names

-  The Primary Role and Role Attributes

   -  **type:** Subject
   -  **id:** http://dci-sec.org/xacml/attribute/role/primary and
      http://dci-sec.org/xacml/attribute/role
   -  **data type:** http://www.w3.org/2001/XMLSchema#string
   -  **issuer:** The group name to which this role belong.
   -  **multiple values allowed:** no (primary role), yes (roles)
   -  **description:** The primary role, and roles list assigned to the
      subject.

Example Configuration
^^^^^^^^^^^^^^^^^^^^^

The following example shows a PEP Server configuration with the Common
XACML authorization profile PIP enabled, and accepting the
``http://dci-sec.org/xacml/profile/common-authz/1.1`` EMI Common XACML
Authorization profile.

::

    [SERVICE]
    entityId = https://argus.example.org/pep
    hostname = argus.example.org

    pips = COMMONXACMLPROFILE_PIP

    [PDP]
    pdps = https://argus.example.org:8152/authz

    [SECURITY]
    trustInfoDir = /etc/grid-security/certificates

    [COMMONXACMLPROFILE_PIP]
    parserClass = org.glite.authz.pep.pip.provider.CommonXACMLAuthorizationProfilePIPIniConfigurationParser
    vomsInfoDir = /etc/grid-security/vomsdir
    acceptedProfileIDs = http://dci-sec.org/xacml/profile/common-authz/1.1

Other Policy Information Points (PIP)
-------------------------------------

Here are other PIPs that you can configure for testing or debugging
purpose

Attribute White List PIP
^^^^^^^^^^^^^^^^^^^^^^^^

This PIP can be used to filter out attributes that should not be
accepted within a request.

Configuration
+++++++++++++

#. Create a new INI section for you PIP (you may
   choose any valid INI section name. e.g. WHITELIST\_PIP)
#. Into the PIP INI section add the ``parserClass`` property with the
   value
   ``org.glite.authz.pep.pip.provider.AttributeWhitelistPIPIniConfigurationParser``
#. Configure which request attributes are to be accepted

PIP Configuration Properties
++++++++++++++++++++++++++++

+-------------------------------+--------------------------------------------------------------------------------+-----------+---------------+
| Property                      | Description                                                                    | Required? | Default Value |
+===============================+================================================================================+===========+===============+
| acceptedActionAttributes      | spaced delimited list attribute IDs that may appear in the request action      | N         | None.         |
+-------------------------------+--------------------------------------------------------------------------------+-----------+---------------+
| acceptedEnvrionmentAttributes | spaced delimited list attribute IDs that may appear in the request environment | N         | None.         |
+-------------------------------+--------------------------------------------------------------------------------+-----------+---------------+
| acceptedResourceAttributes    | spaced delimited list attribute IDs that may appear in the request resource    | N         | None.         |
+-------------------------------+--------------------------------------------------------------------------------+-----------+---------------+
| acceptedSubjectAttributes     | spaced delimited list attribute IDs that may appear in the request subject     | N         | None.         |
+-------------------------------+--------------------------------------------------------------------------------+-----------+---------------+

**NOTE: if a property is not given then all attributes within the
section (i.e. action, environment, resource, or subject) are accepted.**

Example Configuration
+++++++++++++++++++++

The following example shows a PEP Server configuration with the
whitelist PIP enabled, accepting only the *key-info* attribute from the
request subject, and filtering out all other subject attributes. All the
other request attributes (action, resource and environment) are accepted
as is.

::

    [SERVICE]
    entityId = https://example.org/pep
    hostname = example.org
    pips = WHITELIST_PIP

    [PDP]
    pdps = http://localhost:8152/authz

    [SECURITY]
    trustInfoDir = /etc/grid-security/certificates

    [WHITELIST_PIP]
    parserClass = org.glite.authz.pep.pip.provider.AttributeWhitelistPIPIniConfigurationParser
    acceptedSubjectAttributes = urn:oasis:names:tc:xacml:1.0:subject:key-info

Environment Time PIP
^^^^^^^^^^^^^^^^^^^^

This PIP populates a few time-related attributes within the
**environment** portion of the request.

**Note**, using this PIP effective disables the response caching in a
PEP Server as will make every request different.

Configuration
+++++++++++++

#. Create a new INI section for you PIP (you may
   choose any valid INI section name. e.g. TIME\_PIP)
#. To PIP INI section add the ``parserClass`` property with the value
   ``org.glite.authz.pep.pip.provider.EnvironmentTimePIPIniConfigurationParser``
#. Add the name of the created PIP INI section to the list of PIPs in
   the ``SERVICE`` section

Prerequisite Request Attributes
+++++++++++++++++++++++++++++++

None.

Populate Effective Request Attributes
+++++++++++++++++++++++++++++++++++++

This PIP will populate the following attributes within the environment
portion of the request.

-  The Current Time

   -  **type:** environment
   -  **id:** urn:oasis:names:tc:xacml:1.0:environment:current-time
   -  **data type:** http://www.w3.org/2001/XMLSchema#time
   -  **issuer:** any
   -  **multiple values allowed:** no
   -  **description:** The time, in the UTC timezone, the request was
      issued

-  The Current Date

   -  **type:** environment
   -  **id:** urn:oasis:names:tc:xacml:1.0:environment:current-date
   -  **data type:** http://www.w3.org/2001/XMLSchema#date
   -  **issuer:** any
   -  **multiple values allowed:** no
   -  **description:** The date, in the UTC timezone, the request was
      issued

-  The Current Date and Time

   -  **type:** environment
   -  **id:** urn:oasis:names:tc:xacml:1.0:environment:current-dateTime
   -  **data type:** http://www.w3.org/2001/XMLSchema#dateTime
   -  **issuer:** any
   -  **multiple values allowed:** no
   -  **description:** The date and time, in the UTC timezone, the
      request was issued

Example Configuration
+++++++++++++++++++++

The following example shows a PEP Server configuration with the
Environment Time PIP enabled:

::

    [SERVICE]
    entityId = https://example.org/pep
    hostname = example.org
    pips = TIME_PIP

    [PDP]
    pdps = http://localhost:8152/authz

    [TIME_PIP]
    parserClass = org.glite.authz.pep.pip.provider.EnvironmentTimePIPIniConfigurationParser

.. _argus_pep_pip_static_attributes:

Static Attributes PIP
^^^^^^^^^^^^^^^^^^^^^

This PIP can populate the action, environment, resource, and subject of
the request with a static set of attributes.

This PIP is very useful for testing as it allows for the creation of any
arbitrary request.

Configuration
+++++++++++++

#. Create a new INI section for you PIP (you may
   choose any valid INI section name)
#. To PIP INI section add the ``parserClass`` property with the value
   ``org.glite.authz.pep.pip.provider.StaticPIPIniConfigurationParser``
#. Define the property ``staticAttributesFile`` with a fully qualified
   path to a file that will hold the definitions for the static
   attributes
#. If populating action attributes, define the property
   ``actionAttributes`` with a space delimited list of the INI sections,
   defined in the ``staticAttributesFile`` file, that represent the
   attributes that should be treated as action attributes.
#. If populating environment attributes, define the property
   ``environmentAttributes`` with a space delimited list of the INI
   sections, defined in the ``staticAttributesFile`` file, that
   represent the attributes that should be treated as environment
   attributes.
#. If populating resource attributes, define the property
   ``resourceAttributes`` with a space delimited list of the INI
   sections, defined in the ``staticAttributesFile`` file, that
   represent the attributes that should be treated as resource
   attributes.
#. If populating subject attributes, define the property
   ``subjectAttributes`` with a space delimited list of the INI
   sections, defined in the ``staticAttributesFile`` file, that
   represent the attributes that should be treated as subject
   attributes.
#. If the defined subject attributes should be added to each subject in
   the request, define the property
   ``includeSubjectAttribtuesInAllSubjects`` with a value of "true"
#. Optionally define the property ``defaultAttributeIssuer`` to a value
   that will be used as the attribute issuer if the attribute definition
   does not define an issuer.
#. Add the name of the created PIP INI section to the list of PIPs in
   the ``SERVICE`` section

To define your static attribute files, repeat the following steps for
each static attribute you wish to define:

#. Create a new INI section for you PIP (you may
   choose any valid INI section name)
#. Define the property ``id`` with the value of the ID of the attribute
#. Optionally define the property ``datatype`` with the datatype of the
   attribute. If no datatype is define the default data type will be
   ``http://www.w3.org/2001/XMLSchema#string``
#. Optionally define the property ``issuer`` with the ID of the issuer
   for the attribute.
#. Define the property ``values`` with a delimited string representing
   the values of the attribute. See next step for the delimiter.
#. Optionally define the property ``valueDelimiter`` with a delimiter
   string used to separate values in the previous property. If no
   delimiter is defined the default delimiter is ',' (comma).

Prerequisite
++++++++++++

None.

Populate Attributes
+++++++++++++++++++

This PIP will populate those attributes defined in the
``staticAttributesFile`` file and referenced by either the
``actionAttributes`` , ``environmentAttributes`` ,
``resourceAttributes`` , or ``subjectAttributes`` properties.

Example Configuration
+++++++++++++++++++++

The following example shows a PEP Server configuration with the Static
Attributes PIP enabled:

::

    [SERVICE]
    entityId = https://example.org/pep
    hostname = example.org
    pips = STATIC

    [PDP]
    pdps = http://localhost:8152/authz

    [STATIC]
    parserClass = org.glite.authz.pep.pip.provider.StaticPIPIniConfigurationParser
    staticAttributesFile = /path/to/some/file.ini
    actionAttributes = actionId
    resourceAttributes = resourceId
    subjectAttributes = subjectId

And here is a static attribute definition file, note that this file can
include attributes which are not currently used as action, environment,
resource, or subject attributes:

::

    [actionId]
    id = urn:oasis:names:tc:xacml:1.0:action:action-id
    values = submit

    [resourceId]
    id = urn:oasis:names:tc:xacml:1.0:resource:resource-id
    values = http://example.org/wn

    [subjectId]
    id = urn:oasis:names:tc:xacml:1.0:subject:subject-id
    datatype = urn:oasis:names:tc:xacml:1.0:data-type:x500Name
    values = CN=foo

