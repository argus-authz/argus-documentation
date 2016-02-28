
Argus EMIR Publisher Configuration (EMI-3)
==========================================

You can use
`EMIR-SERP <https://twiki.cern.ch/twiki/bin/view/EMI/SERP>`__ to publish
the Argus resource information to the
`EMIR <https://twiki.cern.ch/twiki/bin/view/EMI/EMIRegistry>`__\ egisty.
EMIR-SERP uses the information already available in the resource BDII
and publish it to an EMIR
`DSR <https://twiki.cern.ch/twiki/bin/view/EMI/DSR>`__ endpoint.

Local Resource BDII
-------------------

Check that the local resource BDII is running and return service
information:

``ldapsearch -x -h localhost -p 2170 -b 'GLUE2GroupID=resource,o=glue' objectCLass=GLUE2Service``

This query should return 1 entry for the Argus service. If it is not the
case, something is fishy in the installtion...

Install and Configure EMIR SERP
-------------------------------

Install the EMIR SERP package:

``yum install emir-serp``

Edit the configuration file ``/etc/emi/emir-serp/emir-serp.ini`` and set
the following:

::

    ...
    url = http://emitbdsr1.cern.ch:9126
    ...
    [servicesFromResourceBDII]
    resource_bdii_url = ldap://localhost:2170/GLUE2GroupID=resource,o=glue
    ...

And start the EMIR-SERP service:

``/sbin/service emir-serp start``

You can verifiy that the information is correctly published by browsing:
http://emitbdsr1.cern.ch:9126/services
