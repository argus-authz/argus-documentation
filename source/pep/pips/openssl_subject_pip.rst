.. _argus_pep_pip_subject_converter:

OpenSSL Subject Converter PIP
-----------------------------

.. warning::
   **This PIP is new in Argus 1.3 (EMI).**

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

