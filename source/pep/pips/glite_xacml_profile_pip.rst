
.. _argus_pep_pip_grid_authz_profile:

Grid Authorization Profile PIP
------------------------------

.. warning::
   **This is the default profile supported starting from Argus 1.2.**

This PIP allows the PEP client to send only the end-user certificate or
proxy as lone Subject *Key-Info* attribute. It will then parse the
certificate, extract all the information from the certificate required
by the gLite Grid XACML Authorization Profiles, and populate the request
with attributes found in the certificate/proxy.

This PIP implements the
`XACML Grid Worker Node Authorization Profile (v.1.0) <https://edms.cern.ch/document/1058175>`__
and the
`XACML Grid Computing Element Authorization Profile (v.1.0) <https://edms.cern.ch/document/1078881>`__
specifications.

Configuration
^^^^^^^^^^^^^

#. Create a new INI section for you PIP (you may
   choose any valid INI section name. e.g. ``GLITEXACMLPROFILE_PIP``)
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

