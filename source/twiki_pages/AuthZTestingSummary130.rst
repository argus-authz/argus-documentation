.. _argus-testing-summary-130:

Authorization Services Testing Summary
======================================

Hardware Description
--------------------

For the performance and aging tests, we installed:

-  EMI-1 Argus 1.3 (PAP, PDP and PEP Server):

   -  1 Xeon CPU 2.33GHz (Dual Core)
   -  5 GB RAM

-  PEP client ``pepcli``:

   -  4 hosts running the Grinder framework

Update: Results for the 1.3.1 update using the same Hardware


There is a significant drop in memory consumption coming with the 1.3.1
update for the pepd and the pdp server. Find some load test using those
Versions below.

Performance Tests
-----------------

The performance testing of the Authorization Services was done using the
hardware described above. The setup of the test was basically the same
as described
:ref:`here <argus-load-lifetime-testing>`, using the
following options. Each agent (host running grinder-framework) opens two
worker, and each worker opens 5 threads. Therefore a maximum of 40
(4\*2\*5) simultaneous requests are achieved.

Test Description (SSL enabled)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

This is the most common case, since the default configuration with YAIM
results in a PEPd with SSL enabled

On the Argus Server


The default configuration was used, only the cache-size was lowered to
200 requests

-  pepd.ini:
   ``maximumCachedResponses = 200``

The obligations needed for this test were added to the PAP

.. code-block:: bash

   pap-admin ap permit subject="CN=Joel Casutt D88E5396,O=SWITCH,DC=slcs,DC=switch,DC=ch" --action "submit" --resource "ce1\_.\*"

   pap-admin ap deny subject="CN=Joel Casutt D88E5396,O=SWITCH,DC=slcs,DC=switch,DC=ch" --action "submit" --resource "ce2\_.\*"


The Test-Scripts
~~~~~~~~~~~~~~~~

To assure that not only cached requests are used by the PEPd, but that
the PEPd also sends requests to the PDP, more than 200 different
requests need to be defined in the properties file. This can easily be
done by changing the resource (e.g. ce1_100, ce1_101, ...)

Results and Metrics
^^^^^^^^^^^^^^^^^^^

-  Test ran for about 16h without interruption
-  1'698'886 authorization requests processed
-  30 requests per second sustained
-  1340 ms average round trip time (pepcli -> PEPd (-> PDP -> PEPd) ->
   pepcli)

Memory usage of the PEPd during the load-test:

.. image:: /images/PEP-memory-usage.png
   :width: 598px
   :height: 233px


Memory usage of the PDP during the load-test:

.. image:: /images/PDP-memory-usage.png
   :width: 596px
   :height: 234px


Results and Metrics for the 1.3.1-update
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

-  Test ran for about 63h without interruption
-  ~6.9 M authorization requests processed
-  30 requests per second sustained
-  900 ms average round trip time (pepcli -> PEPd (-> PDP -> PEPd) ->
   pepcli)

Requests per second during the load test for the 1.3.1 update:

.. image:: /images/requests_per_second_1.3.1.png
   :width: 580px
   :height: 205px


Memory usage of the PDP during the load test for the 1.3.1 update:

.. image:: /images/pdp_memory_consumption_1.3.1.png
   :width: 596px
   :height: 232px

Memory usage of the PEPd during the load test for the 1.3.1 update:

.. image:: /images/pepd_memory_consumption_1.3.1.png
   :width: 596px
   :height: 234px


Aging Tests
-----------

This test was done analogue to the performance test, but with only one
Agent starting one worker starting one thread. This resulted in about 10
requests per second.

Test Description
~~~~~~~~~~~~~~~~

The test was done using the same options as the performance test.

Results and Metrics
~~~~~~~~~~~~~~~~~~~

-  Test ran for about 43h without interruption
-  1'486'711 authorization requests processed
-  10 requests per second sustained
-  101 ms average round trip time (pepcli -> PEPd (-> PDP -> PEPd) ->
   pepcli)

Memory usage of the PDP during the long-test:

.. image:: /images/PDP-memory-usage-long-test.png
   :width: 596px
   :height: 235px


Memory usage of the PEPd during the long-test:

.. image:: /images/PEP-memory-usage-long-test.png
   :width: 599px
   :height: 233px
