.. _argus_pep_pip_authn_profile_pip:

Authentication Profile PIP
--------------------------

.. warning::
   **This PIP is new since Argus 1.7.1**

This PIP uses the informations extracted from the certificate by
:ref:`argus_pep_pip_common_xacml_authz_profile` and
:ref:`argus_pep_pip_grid_authz_profile`,
so it must be configured as the last PIP into the PEP server.

This PIP looks in the request subject attributes for the attribute containing
subject of the CA that issued the certificate contained in the request, and
the attribute containing the VO name linked to the request.

If no attribute is found holding the CA subject, the PIP returns without modifying the request.
Otherwise, this PIP checks whether the included certificate subject and VOMS attributes are
allowed by local authentication profile policies.

If the policies are NOT met, subject and VOMS attributes are removed from the request.
If the policies are met, the ``x509-authn-profile`` attribute is set, containing the
authentication profile resolved for the request.


Configuration
^^^^^^^^^^^^^

#. Create a new INI section for the PIP, with any valid INI section name
   (e.g. ``AUTHN_PROFILE_PIP``).
#. In this section add the ``parserClass`` property with the value
   ``org.glite.authz.pep.pip.provider.authnprofilespip.AuthenticationProfilePIPConfigurationParser``.
#. Add the ``authenticationProfilePolicyFile`` property with a value corresponding to
   the absolute path of the authentication profile policies file,
   traditionally ``/etc/grid-security/vo-ca-ap-file``.
#. Add the ``trustAnchors.directory`` property with a value of the absolute path of the
   trust anchors directory, where the PIP search the policy info files,
   typically ``/etc/grid-security/certificates``.
#. Add the  ``trustAnchors.policyFilePattern`` property with a value corresponding to a
   bash globbing expression that matches the policy info files, e.g. ``policy-*.info``.
#. Optionally, add the ``trustAnchors.refreshIntervalInSecs`` property with a
   value in seconds, that is the refresh time interval used by the PIP to reload the policy
   files, e.g. 14400.

PIP Configuration Properties
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

+----------------------------------------+--------------------------------------------------------------+-----------+--------------------------------------+
| Property                               | Description                                                  | Required? | Default Value                        |
+========================================+==============================================================+===========+======================================+
| ``authenticationProfilePolicyFile``    | Absolute path of authentication profile policy file          | Yes       | ``/etc/grid-security/vo-ca-ap-file`` |
+----------------------------------------+--------------------------------------------------------------+-----------+--------------------------------------+
| ``trustAnchors.directory``             | Absolute path where policy info files are located            | No        | ``/etc/grid-security/certificates``  |
+----------------------------------------+--------------------------------------------------------------+-----------+--------------------------------------+
| ``trustAnchors.policyFilePattern``     | Regular expression that matches the policy files to read     | No        | ``policy-*.info``                    |
+----------------------------------------+--------------------------------------------------------------+-----------+--------------------------------------+
| ``trustAnchors.refreshIntervalInSecs`` | Time, in seconds, with which the PIP reload the policy files | No        | 14400                                |
+----------------------------------------+--------------------------------------------------------------+-----------+--------------------------------------+


Populated Effective Request Attributes
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This PIP will process the request subject ``x509-subject-issuer`` attribute and
populate the following attribute:

-  The X.509 Authentication Profile Attribute

   -  **type:** Subject
   -  **id:** http://glite.org/xacml/attribute/x509-authn-profile
   -  **data type:** http://www.w3.org/2001/XMLSchema#string
   -  **multiple values allowed:** yes
   -  **description:** The alias of the policy file that contains the DN of the CA issuer.


Required Request Attributes
^^^^^^^^^^^^^^^^^^^^^^^^^^^

This PIP requires that the request environment contains the attributes
extracted from the X.509 certificate/proxy by the other PIPs.


-  The X.509 Subject Issuer Attribute

   -  **type:** Subject
   -  **id:** http://glite.org/xacml/attribute/subject-issuer
   -  **data type:** urn:oasis:names:tc:xacml:1.0:data-type:x500Name
   -  **multiple values allowed:** no
   -  **description:** Distinguished Name of the CA issuer.


Example Configuration
^^^^^^^^^^^^^^^^^^^^^

The following example shows a PEP Server configuration with the Authentication Profile
PIP enabled.

::

    [SERVICE]
    entityId = https://argus.example.org/pep
    hostname = argus.example.org

    pips = COMMONXACMLPROFILE_PIP AUTHN_PROFILE_PIP

    [PDP]
    pdps = https://argus.example.org:8152/authz

    [AUTHN_PROFILE_PIP]
    parserClass = org.glite.authz.pep.pip.provider.authnprofilespip.AuthenticationProfilePIPConfigurationParser
    authenticationProfilePolicyFile = /etc/grid-security/vo-ca-ap-file
    trustAnchors.directory = /etc/grid-security/certificates
    trustAnchors.policyFilePattern = policy-*.info
    trustAnchors.refreshIntervalInSecs = 14400


