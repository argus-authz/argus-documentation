.. argus_pep_pip_environment_time_pip:

Environment Time PIP
^^^^^^^^^^^^^^^^^^^^

This PIP populates a few time-related attributes within the
**environment** portion of the request.

.. tip::
   Using this PIP effective disables the response caching in a
   PEP Server as will make every request different.

Configuration
+++++++++++++

#. Create a new INI section for you PIP (you may
   choose any valid INI section name. e.g. ``TIME_PIP``)
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