%META:TOPICINFO{author="ad968f62f612332eff6b" date="1299513795"
format="1.1" version="1.8"}%
%META:TOPICPARENT{name="AuthorizationFramework"}%

Argus: GSI PEP Callout
======================

Module Description
==================

The Globus Toolkit version 3.2 and later have the ability to customize
the authorization and gridmap lookup (currently available in the GridFTP
and Gatekeeper servers). The `Globus Authorization
Callouts <http://www.globus.org/toolkit/security/callouts/>`__ framework
allows to plug in authorization and mapping modules.

The GSI PEP Callout module implements the functionality to authorize and
map the user by calling out to the `Argus PEP
Server <AuthorizationFramework#Policy_Enforcement_Point>`__.

Authorization and Mapping
=========================

Based on the Grid credientials, typically a proxy certificate, the GSI
PEP Callout module will send an authorization request to the `Argus PEP
Server <AuthorizationFramework#Policy_Enforcement_Point>`__, and then
parse the authorization response decision to authorize the user and the
obligations to map him to a local account.

XACML Profile
=============

The GSI PEP Callout module implements the `XACML Grid Worker Node
Authorization Profile 1.0 <https://edms.cern.ch/document/1058175>`__,
and by default uses the identifiers described in the profile.

XACML Request
-------------

The GSI PEP Callout module sends a request to the PEP Daemon with the
following elements:

-  XACML subject with the attribute element:

   -  AttributeId: ``urn:oasis:names:tc:xacml:1.0:subject:key-info``
   -  Value: The PEM encoded Grid credentials provided by the calling
      service

-  XACML resource with the attribute element:

   -  AttributeId: ``urn:oasis:names:tc:xacml:1.0:resource:resource-id``
   -  Value: The value of the `GSI PEP Callout configuration
      directive <AuthZPEPGSIConfig#Configuration_Directives>`__
      ``xacml_resourceid``.

-  XACML action with the attribute element:

   -  AttributeId: ``urn:oasis:names:tc:xacml:1.0:action:action-id``
   -  Value: The service being requested by the client (e.g. ``file``
      for GridFTP) or the name of the service passed to the gatekeeper.

-  XACML environment with the attribute element:

   -  AttributeId: ``http://glite.org/xacml/attribute/profile-id``
   -  Value: ``http://glite.org/xacml/profile/grid-wn/1.0`` (Default)

XACML Response
--------------

The PEP Daemon sends back a response to the GSI PEP Callout module. The
following response elements are parsed to authorize and map the user:

-  XACML decision element: Contains the **authorization decision**
   ``Permit``, ``Deny``, ``Indeterminate`` or ``NotApplicable``
-  XACML obligation, ObligationId:
   ``http://glite.org/xacml/obligation/local-environment-map/posix``,
   with the attribute assignment element:

   -  AttributeId: ``http://glite.org/xacml/attribute/user-id``
   -  Value: Contains the **local identity mapping** of the user.

The local identity mapping will only succeed if the authorization
decision is ``Permit``.
