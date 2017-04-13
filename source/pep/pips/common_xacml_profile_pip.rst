.. _argus_pep_pip_common_xacml_authz_profile:

Common XACML Authorization Profile PIP
--------------------------------------

.. warning::
   **This profile is supported since Argus 1.6 (EMI-3).**

This PIP allows the PEP client to send only the end-user certificate or
proxy as lone Subject *Key-Info* attribute. It will then parse the
certificate, extract all the information from the certificate required
by the Common XACML Authorization Profile, and populate the request with
attributes found in the certificate/proxy.

This PIP implements the
`Common XACML Authorization Profile (1.1.1) <https://twiki.cern.ch/twiki/bin/view/EMI/CommonXACMLProfileV1_1>`__
specifications.

Configuration
^^^^^^^^^^^^^

#. Create a new INI section for you PIP (you may
   choose any valid INI section name. e.g. ``COMMONXACMLPROFILE_PIP``)
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

.. note::
   If the ``acceptedProfileIDs`` is not defined, then all profile
   IDs present in the request environment *profile-id* attribute are
   accepted.

Required Request Attributes
^^^^^^^^^^^^^^^^^^^^^^^^^^^

This PIP requires that the request environment contains a *profile-id*
attribute with the profile identifier, and that the request subject
contains the certificate, and its chain, that were used to authenticate
to the service, in the *key-info* attribute:

-  `The Profile Identifier <https://twiki.cern.ch/twiki/bin/view/EMI/CommonXACMLProfileV1_1#Profile_Identifier_Attribute>`__
   Attribute

   -  **type:** Environment
   -  **id:** http://dci-sec.org/xacml/attribute/profile-id
   -  **data type:** http://www.w3.org/2001/XMLSchema#anyURI
   -  **multiple values allowed:** no
   -  **description:** The profile ID implemented by the incoming
      request, typically
      ``http://dci-sec.org/xacml/profile/common-authz/1.1``

-  `The Subject Key-Info (certificate or proxy, with chain) <https://twiki.cern.ch/twiki/bin/view/EMI/CommonXACMLProfileV1_1#Subject_Key_Info_Attribute>`__
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

-  The CA Issuer Subject Attribute

   -  **type:** Subject
   -  **id:** http://glite.org/xacml/attribute/x509-subject-issuer
   -  **data type:** urn:oasis:names:tc:xacml:1.0:data-type:x500Name
   -  **multiple values allowed:** no
   -  **description:** X.509 distinguished name of the authority which
      issued the end-entity certificate. The values are in RFC2253 format.

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
  
