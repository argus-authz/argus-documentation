.. _argus_pap_operation:


Argus: Policy Administration Point (PAP): Operation
===================================================

Service Operation Commands
--------------------------

Start the PAP service
   ========   ===========
   Platform   Command
   ========   ===========
   EL6        ``service argus-pap start``
   EL7        ``systemctl start argus-pap``
   ========   ===========

Stop the PAP service
   ========   ===========
   Platform   Command
   ========   ===========
   EL6        ``service argus-pap start``
   EL7        ``systemctl start argus-pap``
   ========   ===========

Service Ports
-------------

-  Default service port: 8150
-  Default shutdown service and status port: 8151

Service Endpoints
-----------------

``/pap/services/ProvisioningService`` :
   this endpoint provides the policy provisioning interface

``/shutdown`` :
   this endpoint instructs the PAP standalone service to
   shutdown and is reachable only from localhost on the configured
   shutdown and status port

``/status`` :
   this endpoint provides current status information on
   the PAP is reachable only from localhost on the configured shutdown
   and status port

All the web services implemented by the PAP can be reached at the
``/pap/services/`` context.

Service Logs
------------

The PAP standalone log file ( ``pap-standalone.log``) can be found in
the ``$PAP_HOME/logs`` directory. If the PAP is deployed on top of
tomcat, the log file ( ``pap.log``) can be found in the
``$CATALINA_HOME/logs`` directory.
