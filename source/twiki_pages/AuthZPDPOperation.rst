%META:TOPICINFO{author="ad968f62f612332eff6b" date="1304080619"
format="1.1" version="1.5"}%
%META:TOPICPARENT{name="AuthorizationFramework"}%

Argus Policy Decision Point (PDP): Operation
============================================

Service Operation Commands
--------------------------

EMI - Argus 1.3
~~~~~~~~~~~~~~~

``/etc/init.d/argus-pdp start``
    Start the PDP service
``/etc/init.d/argus-pdp stop``
    Stop the PDP service
``/etc/init.d/argus-pdp restart``
    Restart the PDP service
``/etc/init.d/argus-pdp status``
    Provides PDP service status information
``/etc/init.d/argus-pdp reloadpolicy``
    Causes the currently cached copy of the policies received from the
    PAP can be flushed from memory, and retrieved anew from the PAP

gLite 3.2 - Argus 1.2
~~~~~~~~~~~~~~~~~~~~~

``$PDP_HOME/sbin/pdpctl.sh start``
    starts the service
``$PDP_HOME/sbin/pdpctl.sh stop``
    stops the services
``$PDP_HOME/sbin/pdpctl.sh status``
    provides service status data
``$PDP_HOME/sbin/pdpctl.sh reloadPolicy``
    causes the currently cached copy of the policy received from the PAP
    can be flushed from memory, and retrieved anew from the PAP

Service Information
-------------------

Service Ports
~~~~~~~~~~~~~

-  Default Service Port: 8152
-  Default Admin Port: 8153

The PDP service only requires the standard service port to be open to
those PEPd services which will communicate with the PDP. The PDP must
also be able to make outbound connections to those PAPs from which
remote policies will be retrieved.

Service Endpoint URLs
~~~~~~~~~~~~~~~~~~~~~

This service contains the following endpoint URLs:

-  ``https://HOSTNAME:8152/authz`` - This endpoint is the recipient of
   authorization requests.
-  ``http://127.0.0.1:8153/status`` - This endpoint provides current
   status information on the PDP. This endpoint is password protected.
-  ``http://127.0.0.1:8153/reloadPolicy`` - This endpoint instructs the
   PDP to flush, and retrieve anew, its policy from the PAP. This
   endpoint is password protected.
-  ``http://127.0.0.1:8153/shutdown`` - This endpoint instructs the PDP
   to shutdown. This endpoint is password protected.

**Note:** Admin services may be password protected and thus not
invokable without this password.

Logging and Logs
----------------

This service uses the `logback <http://logback.qos.ch/>`__ logging
library. Java developers are probably familiar with Apache Log4J,
logback is written by the developer who initially wrote Log4J and
contains a cleaner API and is much more performant. The configuration
file for the logging system can be found in
``$PDP_HOME/conf/logging.xml`` and changes to this file are picked up
every 5 minutes.

Enable Debug Logging
~~~~~~~~~~~~~~~~~~~~

To enable debug logging follow: 1 Locate the line that contains
``logger name="org.glite.authz"`` (line 10 in the default logging
config) 1 On the following line, change ``INFO`` to ``DEBUG``

In some cases it may be helpful to see the policy being evaluated for
each request. To do this: 1 Locate the line that contains
``org.glite.authz.message.policy`` (line 22 in the default logging
config) 1 On the following line, change ``INFO`` to ``DEBUG``

In some cases it may be helpful to see the incoming and outgoing
messages. To do this: 1 Locate the line that contains
``org.glite.authz.message.protocol`` (line 27 in the default logging
config) 1 On the following line, change ``INFO`` to ``DEBUG``

**NOTE** always change your logging levels back to their original values
once you are done debugging a problem. Keeping the system on the debug
logging level could fill up your disk partition in a short time.

Service Logs
~~~~~~~~~~~~

The service writes three different logs, located in
``/var/log/argus/pdp`` (EMI) or ``$PDP_HOME/logs`` (gLite):

-  **process.log** - This log file contains the normal, human-oriented
   logging messages that the system generates while in operation. This
   can be thought of as the debug log (though that's really only true if
   configured to log debug messages).
-  **access.log** - This file is an Apache-style access log showing
   information about incoming requests. This log is meant to be machine
   parsed. Each line contains the following '\|' (pipe) delimited
   fields:

   -  request time - time of the request, in the UTC timezone, from the
      Unix epoch (Jan 1, 1970, 00:00:00 UTC)
   -  remote host - the hostname or IP address of the host that
      contacted the PDP
   -  server host - the hostname or IP address on which the server
      received the request
   -  server port - the port on which the server received the request
   -  request path - the URL path that was requested

-  **audit.log** - This file contains information useful for auditing
   the system (e.g. results of authorization requests). This log is
   meant to be machine parsed. Each line contains the following '\|'
   (pipe) delimited fields:

   -  request time - time of the request, in the UTC timezone, from the
      Unix epoch (Jan 1, 1970, 00:00:00 UTC)
   -  requester ID - the entity ID of the PEP that made the request
   -  request ID - the ID of the incoming authorization request
   -  policy ID - the ID of the policy set that was evaluated in order
      to reach the authorization decision
   -  policy version - the version of the policy that was evaluated in
      order to reach the authorization decision
   -  policy decision - the authorization decision that was reached
   -  response ID - the ID of the authorization response sent back to
      the PEP
