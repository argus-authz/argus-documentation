%META:TOPICINFO{author="ad968f62f612332eff6b" date="1267530271"
format="1.1" version="1.2"}%
%META:TOPICPARENT{name="AuthorizationFramework"}%

PEP Java Client Library Design
==============================

Requirements
------------

#. Absolute **minimal** set of dependencies:

   -  hessian
   -  object model
   -  httpclient (and deps)
   -  voms-java-api (and deps)

#. A very simple PEP client API (based on the KISS principle).
#. The PEP-J should be easily configurable, using only API calls (no
   need for configuration file).
#. It should hide the transport logic from the calling application, by
   using httpclient inside the client.
#. It should provide easy HTTPS/TLS client authN configuration (using
   VOMS API?).
#. It should be easy to configure the HTTP keep-alive. CREAM developers
   want to create one client and reuse it to spare the HTTPS/client
   authN overhead.
#. It should be clear how multithreading is handled.
#. It should be easily extendable with the notion of profiles.
#. Interfaces should be used as much as possible, where applicable.
#. It should provide simple PIP and OH handling functionalities.
#. It should provides at least a simple example.
#. It should be built/released for glite 3.2 first, but also for glite
   3.1 (as an internal patch)
#. It should be easy to build request object. Some sort of
   RequestBuilder is required.

   -  to populate the subject starting from a certificate (which cert
      format?)
   -  provide convenience methods to set resource and action ids

#. Nonblocking calls is not a priority.
#. We don't need a CLI at all. This functionality is already available
   in pepcli.

Dependencies
------------

List of dependencies available in ETICS what can be used:

-  bouncycastle.DEFAULT: bouncycastle v. 1.42
-  jakarta-commons-cli.DEFAULT: jakarta-commons-cli v. 1.0
-  jakarta-commons-codec.DEFAULT: jakarta-commons-codec v. 1.3
-  jakarta-commons-collections.DEFAULT: jakarta-commons-collections v.
   3.2
-  jakarta-commons-configuration.DEFAULT: jakarta-commons-configuration
   v.1.4
-  jakarta-commons-httpclient.DEFAULT: jakarta-commons-httpclient v.
   3.0.1
-  jakarta-commons-lang.DEFAULT: jakarta-commons-lang v. 2.3
-  jakarta-commons-logging.DEFAULT: jakarta-commons-logging v. 1.1
-  junit.DEFAULT: junit v. 3.8.1
-  log4j.DEFAULT: log4j v. 1.2.13-1
-  glite-security-trustmanager\_R\_2\_5\_5\_1\_GL32
-  glite-security-util-java\_R\_2\_5\_5\_1\_GL32
-  glite-security-voms-api-java\_R\_1\_8\_12\_1\_GL32

VOMS Java API version is still 1.8.12, I really don't know why the
version 1.9.14 is not yet available??

The Hessian library is still missing, but I will request its integration
in ETICS. But which version 3.2.1 or 4.0.3 to request?

API Prototype
-------------

::

    // create PEP-J configuration 
    PEPClientConfiguration config= new PEPClientConfiguration();
    config.addPEPDaemonEndpoint("https://pepd.example.org:8154/authz");
    config.setTrustStoreDirectory("/etc/grid-security/certificates");
    // other config params
    ...

    // create PEP-J client
    PEPClient client= new PEPClient(config);

    // create XACML request

    // authorize 
    Response resp= client.authorize(request);

    // parse XACML response

-- Main.ValeryTschopp - 02-Mar-2010
