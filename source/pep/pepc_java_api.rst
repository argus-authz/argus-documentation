.. _argus_pepj_api:

Argus PEP Client: Java Programming Interface
============================================

The Argus PEP Java client library is used to communicate with the Argus
PEP daemon. It authorizes request and receives response back from Argus.

Javadoc
-------

Javadoc for the PEP Java client API:
http://argus-authz.github.com/argus-pep-api-java/javadoc/2.X/index.html

PEP XACML Object Model
----------------------

The PEP client XACML object model implemented in the package
``org.glite.authz.common.model`` follow this schema:


.. figure:: /images/Argus_PEP_ObjectModel.png
   :align: center
   :scale: 25%

Basic Example
-------------

This is a very simplified example, omitting all the error handling, on
how to create a PEP client, a request, and then authorize the request
and process the response.

1. Create a PEP client configuration and initialize it:

.. code-block:: java

    PEPClientConfiguration config = new PEPClientConfiguration();
    config.addPEPDaemonEndpoint("https://argus.example.org:8154/authz");

    // trust and key material for the HTTPS connection with client authentication
    config.setTrustMaterial("/etc/grid-security/certificates");
    config.setKeyMaterial("/etc/grid-security/hostcert.pem", "/etc/grid-security/hostkey.pem", "keystore_password");

2. Create the PEP client based on the config:

.. code-block:: java

    PEPClient pep = new PEPClient(config);

At this point you have a multi-threaded PEP client that can be **reuse**
to submit many authorization requests to the PEP server.

3. Create an authorization request for a user proxy certificate, based
on a profile:

.. code-block:: java

    // read the user proxy
    PEMFileReader reader = new PEMFileReader();
    X509Certificate[] userproxy = reader.readCertificates("/tmp/x509up_u959");

    // create the request for a given profile
    AuthorizationProfile profile = GridWNAuthorizationProfile.getInstance();
    Request request = profile.createRequest(userproxy,
                                           "http://example.org/wn/cluster1",
                                            GridWNAuthorizationProfile.ACTION_EXECUTE);

4. Authorize the request with the Argus PEP daemon:

.. code-block:: java

    Response response = pep.authorize(request);

5. Extract the user mapping information from the response:

.. code-block:: java

    // will throw an exception if the authorization response is not *Permit*, or if the obligation is not present
    Obligation posixMappingObligation = profile.getObligationPosixMapping(response);
    String userId = profile.getAttributeAssignmentUserId(posixMappingObligation);
    String groupId = profile.getAttributeAssignmentPrimaryGroupId(posixMappingObligation);
    List<String> groupIds = profile.getAttributeAssignmentGroupIds(posixMappingObligation);

Processing Authorization Decision
---------------------------------

The PEP client MUST abide by the authorization decision as described in
here:

-  If the decision is ``Permit``, then the PEP client SHALL permit
   access. If **obligations** accompany the decision, then the PEP
   client SHALL permit access **only if it understands and it can and
   will enforce** those obligations.
-  If the decision is ``Deny``, then the PEP client SHALL deny access.
-  If the decision is ``NotApplicable``, meaning that no policy apply,
   then the PEP client SHALL deny access.
-  If the decision is ``Inderterminate``, then the PEP client SHALL deny
   access. The decision status message and status code should be used to
   produce an error message.

GUI
---

There is also a Java-based GUI available for sending requests to a PEPd.
Just download the application to your desktop and start it.
Once you've downloaded it you can restart it
by double-clicking the Argus-PEP-Client.jnlp file.

:download:`Java-based GUI <../resources/Argus-Pep-Client.jnlp>`

.. figure:: /images/Pep-Client-Gui-Screenshot.png
   :align: center
   :scale: 60%

