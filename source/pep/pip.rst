.. _argus_pep_pip:

Argus PEP Server Policy Information Points (PIP)
================================================

Policy Information Points (PIPs) are plugins to the authorization
service that help populate and/or complete an authorization request.
PIPs may rely on information already within the request or they may
simply be able to self generate the data that they will add.


The following PIPs are currently available:

.. toctree::
   :maxdepth: 1
   
   pips/request_validator_pip
   pips/openssl_subject_pip
   pips/glite_xacml_profile_pip
   pips/common_xacml_profile_pip
   pips/authn_profile_pip


Other Policy Information Points
-------------------------------

Here are other PIPs that you can configure for testing or debugging
purpose:

.. toctree::
   :maxdepth: 1
   
   pips/attribute_whitelist_pip
   pips/environment_time_pip
   pips/static_attributes_pip


