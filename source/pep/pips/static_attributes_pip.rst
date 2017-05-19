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

