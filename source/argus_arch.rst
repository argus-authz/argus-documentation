<img src="%ATTACHURLPATH%/peacock.png" alt="peacock.png" width='100'
height='84' />

Argus Authorization Service
===========================

Summary
-------

The Argus Authorization Service renders consistent authorization
decisions for distributed services (e.g., user interfaces, portals,
computing elements, storage elements). The service is based on the XACML
standard, and uses authorization policies to determine if a user is
allowed or denied to perform a certain action on a particular service.

The Argus Authorization Service is composed of three main components:

-  The Policy Administration Point (PAP) provides the tools to author
   authorization policies, organize them in the local repository and
   configure policy distribution among remote PAPs.
-  The Policy Decision Point (PDP) implements the authorization engine,
   and is responsible for the evaluation of the authorization requests
   against the XACML policies retrieved from the PAP.
-  The Policy Enforcement Point Server (PEP Server) ensures the
   integrity and consistency of the authorization requests received from
   the PEP clients. Lightweight PEP client libraries are also provided
   to ease the integration and interoperability with other EMI services
   or components.

The following graphic shows the interaction between the components of
the service:

<img src="%ATTACHURLPATH%/glite-ARGUS\_components-v2.png" width="900"
alt="Argus Components" />

**Note:** In Argus, the PEP is separated in a client/server
architecture. The PEP Server handles the lightweight PEP client
requests, and runs on the Argus node.

Argus Service Installation
--------------------------

The following section provides instructions for setting up an Argus
environment quickly. It does not provide an exhaustive description of
every possible deployment model or configuration option, that can be
found in the following `Service
Components <AuthorizationFramework#Service_Components>`__ and `Enabled
Applications <AuthorizationFramework#Enabled_Applications>`__ sections.

Before you continue it is recommend that you read this `introduction to
the Argus system <AuthZIntro>`__. This will provide you with a better
understanding of how the components work together, what information
passes between the components and how policies are formed.

Argus EMI Deployment
~~~~~~~~~~~~~~~~~~~~

For EMI, the Argus Service is installed with YUM, and configured with
YAIM. Please follow the :ref:`Argus Deployment for EMI documentation <ArgusEMIDeployment>`.

gLExec Worker Node with Argus Deployment
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To install and configure an Argus compatible gLExec worker node, follow
these `GLExec Argus Quick Installation
Guide <https://wiki.nikhef.nl/grid/GLExec_Argus_Quick_Installation_Guide>`__

Service Components
------------------

If you are beginning to install the authorization service from scratch,
you should install the components in the order listed here; PAP, then
PDP, then PEPd. You don't have to, but it makes the most sense for most
use cases.

PAP: Policy Administration Point
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The Policy Administration Point (PAP) provides three major functions:

-  Provide the tools for authoring policies
-  Store and manage authored policies
-  Provide managed policies to other authorization service components

<table border="0"> <tr> <td style="padding-right:
15px;">\ `Installation <AuthZPAPInstall>`__\ </td> <td
style="padding-right: 15px;">\ `Configuration <AuthZPAPConfig>`__\ </td>
<td style="padding-right:
15px;">\ `Operation <AuthZPAPOperation>`__\ </td> <td
style="padding-right: 15px;">\ `pap-admin CLI <AuthZPAPCLI>`__\ </td>
<td style="padding-right: 15px;">\ `Simplified Policy
Language <SimplifiedPolicyLanguage>`__\ </td> <td style="padding-right:
15px;">\ `Troubleshooting <AuthZPAPTroubleshoot>`__\ </td> </tr>
</table>

PDP: Policy Decision Point
~~~~~~~~~~~~~~~~~~~~~~~~~~

The Policy Decision Point (PDP) is a policy evaluation engine. The PDP
receives authorization requests from Policy Enforcement Points and
evaluates these requests against authorization policies retrieved from
the PAP.

<table border="0"> <tr> <td style="padding-right:
15px;">\ `Installation <AuthZPDPInstall>`__\ </td> <td
style="padding-right: 15px;">\ `Configuration <AuthZPDPConfig>`__\ </td>
<td style="padding-right:
15px;">\ `Operation <AuthZPDPOperation>`__\ </td>
<td>`Troubleshooting <AuthZPDPTroubleshoot>`__\ </td> </tr> </table>

PEP: Policy Enforcement Point
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The Policy Enforcement Point (PEP) is the client to the authorization
service. It gathers information relevant to an authorization request
(e.g. who the user, what action they are attempting to perform, which
service they are attempting to perform the action on, etc.) and sends
the request to the PDP for evaluation. The PEP then acts upon returned
result by allowing the request to proceed (in the case a positive
authorization decision) or by denying the action (in the event of a
negative decision).

In Argus, the PEP is separated in a client/server architecture. The PEP
Server handles the lightweight PEP client requests, and runs on the
Argus node. Lightweight PEP client libraries are available to authorize
requests from the application side, and to enforce decision locally.

<table border="0"> <tr> <th style="padding-right: 15px;">PEP Server</th>
<th style="padding-right: 15px;">PEP Client C API</th> <th
style="padding-right: 15px;">PEP Client Java API</th> </tr> <tr> <td
style="padding-left: 10px; padding-right:
15px;">\ `Installation <AuthZPEPDInstall>`__\ </td> <td
style="padding-left: 10px; padding-right:
15px;">\ `Installation <AuthZPEPCInstall>`__\ </td> <td
style="padding-left: 10px; padding-right:
15px;">\ `Installation <AuthZPEPJInstall>`__\ </td> </tr> <tr> <td
style="padding-left: 10px; padding-right:
15px;">\ `Configuration <AuthZPEPDConfig>`__\ </td> <td
style="padding-left: 10px; padding-right: 15px;">\ `Programming
Interface (API) <AuthZPEPCAPI>`__\ </td> <td style="padding-left: 10px;
padding-right: 15px;">\ `Programming Interface
(API) <AuthZPEPJavaAPI>`__\ </td> </tr> <tr> <td style="padding-left:
10px; padding-right: 15px;">\ `Operation <AuthZPEPDOperation>`__\ </td>
<td style="padding-left: 10px; padding-right: 15px;">\ `Command Line
(pepcli) <AuthZPEPCCLI>`__\ </td> <td style="padding-left: 10px;
padding-right: 15px;"></td> </tr> <tr> <td style="padding-left: 10px;
padding-right:
15px;">\ `Troubleshooting <AuthZPEPDTroubleshoot>`__\ </td> <td
style="padding-left: 10px; padding-right: 15px;"></td> <td
style="padding-left: 10px; padding-right: 15px;"></td> </tr> </table>

Enabled Applications
--------------------

The following application contain an Argus PEP client and can make
authorization requests to the Argus service.

<table border="0"> <tr> <th style="padding-right: 15px;">GSI PEP
Callout</th> <th style="padding-right: 15px;">gLExec with PEP
Plug-in</th> </tr> <tr> <td style="padding-left: 10px; padding-right:
15px;">\ `Module Description <AuthZPEPGSIInfo>`__\ </td> <td
style="padding-left: 10px; padding-right:
15px;">\ `Introduction <https://wiki.nikhef.nl/grid/GLExec_Argus_Quick_Installation_Guide#Introduction>`__\ </td>
</tr> <tr> <td style="padding-left: 10px; padding-right:
15px;">\ `Installation <AuthZPEPGSIInstall>`__\ </td> <td
style="padding-left: 10px; padding-right:
15px;">\ `Installation <https://wiki.nikhef.nl/grid/GLExec_Argus_Quick_Installation_Guide#Package_installation>`__\ </td>
</tr> <tr> <td style="padding-left: 10px; padding-right:
15px;">\ `Configuration <AuthZPEPGSIConfig>`__\ </td> <td
style="padding-left: 10px; padding-right:
15px;">\ `Configuration <https://wiki.nikhef.nl/grid/GLExec_Argus_Quick_Installation_Guide#Manual_configuration>`__\ </td>
</tr> <tr> <td style="padding-left: 10px; padding-right:
15px;">\ `Troubleshooting <AuthZPEPGSITroubleshooting>`__\ </td> <td
style="padding-left: 10px; padding-right:
15px;">\ `Troubleshooting <https://wiki.nikhef.nl/grid/GLExec_Argus_Quick_Installation_Guide#Debugging_hints>`__\ </td>
</tr> </table>

Support and Monitoring
----------------------

GGUS Support
~~~~~~~~~~~~

General support (installation, site administrator) for Argus is
available through `GGUS <https://ggus.eu>`__

Argus Support Mailing List
~~~~~~~~~~~~~~~~~~~~~~~~~~

Argus specific (developer, site administrator) questions can be sent
directly to the argus-support@googlegroups.com `mailing
list <https://groups.google.com/d/forum/argus-support>`__. You don't
need a Google email address or a Google account to send or receive
emails from this mailing list.

-  **Subscription:** To subscribe to the support mailing list, simply
   send an email to: argus-support+subscribe@googlegroups.com
-  **Unsubscribe:** You can unsubscribe from the list at anytime by
   sending an email to: argus-support+unsubscribe@googlegroups.com

**NOTE:** The mailing list was previously argus-support@cern.ch, but it
have been migrated to argus-support@googlegroups.com at the end of the
EMI project (April 2013).

Nagios Monitoring
~~~~~~~~~~~~~~~~~

Nagios plugins are available to monitor an Argus server.

-  For **EMI**: `Argus Nagios Probes Documentation
   (EMI) <ArgusEMINagiosProbes>`__
-  For **gLite 3.2**: `Installation and Configuration of the Nagios
   plugins for Argus (gLite 3.2) <AuthZNagios>`__

Development Information
-----------------------

Argus Product Team
~~~~~~~~~~~~~~~~~~

Since the beginning of EMI the Argus development is led by the `Argus
PT <https://twiki.cern.ch/twiki/bin/view/EMI/Argus>`__.

Security Assessment
~~~~~~~~~~~~~~~~~~~

In June 2011, the Universitat Autònoma de Barcelona (Manuel Brugnoli and
Elisa Heymann, CAOS - UAB) have finished the vulnerabitlity assessment
of the Argus services. The document is available here:

-  `20110601-Argus\_Vulnerability\_Assessment.pdf <%ATTACHURL%/20110601-Argus_Vulnerability_Assessment.pdf>`__:
   Argus Vulnerability Assessment (Universtat Autònoma de Barcelona,
   June 2011)

Specifications
~~~~~~~~~~~~~~

-  XACML 2.0 Specifications `eXtensible Access Control Markup Language
   (XACML) Version
   2.0 <http://docs.oasis-open.org/xacml/2.0/access_control-xacml-2.0-core-spec-os.pdf>`__
-  `SAML 2.0 Profile of XACML, Version 2 (Working Draft
   5) <http://www.oasis-open.org/committees/download.php/24681/xacml-profile-saml2.0-v2-spec-wd-5-en.pdf>`__
-  `SOAP Profile for
   XACML-SAML <http://www.switch.ch/grid/support/documents/xacmlsaml.pdf>`__
-  XACML Profile for the gLite WN `XACML Grid Worker Node Authorization
   Profile (v. 1.0) <https://edms.cern.ch/document/1058175>`__
-  XACML Profile for the gLite CE `XACML Grid Computing Element
   Authorization Profile (v.
   1.0) <https://edms.cern.ch/document/1078881/>`__
-  XACML Authorization Profile for EMI `Common XACML Authorization
   Profile (v.
   1.1) <https://twiki.cern.ch/twiki/bin/view/EMI/CommonXACMLProfileV1_1>`__

Requirements
~~~~~~~~~~~~

-  `Requirements Document <https://edms.cern.ch/document/944192>`__
   *(EDMS document 944192)*
-  `Testing Plan <https://edms.cern.ch/document/986067>`__ *(EDMS
   document 986067)*

Presentations
~~~~~~~~~~~~~

-  `20110531-EMI\_AllHands\_2011-Argus\_Integration.ppt <%ATTACHURL%/20110531-EMI_AllHands_2011-Argus_Integration.ppt>`__:
   Argus - EMI Authorization Integration (EMI AH 2011, 31 May 2011,
   Lund)
-  `20110412-EGI\_UF\_2011-Argus.ppt <%ATTACHURL%/20110412-EGI_UF_2011-Argus.ppt>`__:
   Argus - EMI Authorization Service (EGI UF 2011, 12 April 2011,
   Vilnus)
-  `20100917\_EGI-TF\_ArgusSecurity.ppt <%ATTACHURL%/20100917_EGI-TF_ArgusSecurity.ppt>`__:
   Argus Security (EGI TF 2010 Security Session, 17 Sept. 2010,
   Amsterdam)
-  `introduction\_authz\_service.ppt <%ATTACHURL%/introduction_authz_service.ppt>`__:
   General introduction to the authorization service
-  `100602\_argus\_intro\_rod.ppt <%ATTACHURL%/100602_argus_intro_rod.ppt>`__:
   Introduction to Argus for ROD (EGI ROD Workshop, 2 June 2010,
   Amsterdam)

Souce Code Information
~~~~~~~~~~~~~~~~~~~~~~

We have migrated the Argus source code to
`GitHub <http://github.com>`__.

-  Argus Authorization Service: https://github.com/argus-authz

The source code was previousely stored in the CERN subversion server.
**Please do not use the SVN repository anymore**

-  WebSVN View: https://svnweb.cern.ch/world/wsvn/glxa

Development Tools
~~~~~~~~~~~~~~~~~

The Argus PT uses the following `development tools <ArgusPTDevTools>`__.

For performance and load testing we use the following `testing
suite <AuthZLLT>`__.

Argus Production Settings and Optimization
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Production sites can optimize the Argus Service settings to their
specific needs. Please have a look at the `Argus Fine
Tuning <ArgusEMIFineTuning>`__ documentation.

Perfomance and Load Testing
~~~~~~~~~~~~~~~~~~~~~~~~~~~

Results and metrics of the performance and load testing can be found
here:

-  For a physical machine:

   -  `Versions 1.3.0 and 1.3.1 <AuthZTestingSummary130>`__
   -  `Version 1.4.0 <AuthZTestingSummary140>`__

-  For a virtual machine:

   -  `Version 1.4.0 <AuthZTestingSummary140V>`__

Additional Support
------------------

-  `HERAS-AF <http://www.herasaf.org/index.php>`__ project has supported
   the project by providing a good XACML policy engine and excellent,
   ongoing, support of their code.
-  YourKit is kindly supporting this open source projects with its
   full-featured Java Profiler. YourKit, LLC is the creator of
   innovative and intelligent tools for profiling Java and .NET
   applications. Take a look at YourKit's leading software products:
   `YourKit Java
   Profiler <http://www.yourkit.com/java/profiler/index.jsp>`__ and
   `YourKit .NET
   Profiler <http://www.yourkit.com/.net/profiler/index.jsp>`__.
-  This product includes software developed by the `Caucho
   Technology <http://www.caucho.com/>`__.

About the name Argus
--------------------

In Greek mythology Argus was a 100-eyed giant that was meant to watch
and protect various things and people including the Goddess Io. He was
slain by Hermes but the gods chose to preserve his hundred eyes and
affix them to the tail-feathers of a brilliantly colored bird, the
peacock, in homage. The peacock logo is provided by the royalty free
clip art site `clker.com <http://www.clker.com>`__.
