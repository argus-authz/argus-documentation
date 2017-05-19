.. _argus_pep_pip_request_validator:

Request Validator PIP
---------------------

.. warning::
   **This PIP is new in Argus 1.3 (EMI).**

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

    
    