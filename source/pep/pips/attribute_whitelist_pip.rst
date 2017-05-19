.. argus_pep_pip_attribute_whitelist_pip:

Attribute White List PIP
^^^^^^^^^^^^^^^^^^^^^^^^

This PIP can be used to filter out attributes that should not be
accepted within a request.

Configuration
+++++++++++++

#. Create a new INI section for you PIP (you may
   choose any valid INI section name. e.g. ``WHITELIST_PIP``)
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

.. note::
   If a property is not given then all attributes within the
   section (i.e. action, environment, resource, or subject) are accepted.

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
