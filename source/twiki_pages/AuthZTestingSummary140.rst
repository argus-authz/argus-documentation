.. _argus-testing-summary-140:

Authorization Services Testing Summary
======================================

Hardware Description
--------------------

For the performance and aging tests, we installed:

-  EMI-1 Argus 1.4.0 (PAP, PDP and PEP Server):

   -  1 Xeon CPU 2.33GHz (Dual Core)
   -  5 GB RAM

-  PEP client ``pepcli``:

   -  3 hosts running the Grinder framework

Performance Tests
-----------------

The performance testing of the Authorization Services was done using the
hardware described above. The setup of the test was basically the same
as described
:ref:`here <argus-load-lifetime-testing>`, using the
following options. Each agent (host running grinder-framework) opens two
worker, and each worker opens 2 threads. Therefore a maximum of 12
(3\*2\*2) simultaneous requests are achieved.

Test Description (SSL enabled)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

This is the most common case, since the default configuration with YAIM
results in a PEPd with SSL enabled.

On the Argus Server
^^^^^^^^^^^^^^^^^^^

The default configuration was used The policies needed for this test
were added to the PAP

.. code-block:: bash

   pap-admin ap permit pfqan="/dteam" --action "submit" --resource "ce1\_.\*"

   pap-admin ap deny pfqan="/dteam" --action "submit" --resource "ce2\_.\*"

while the star denotes a number between 1 and 500, so in total the pap
was loaded with 1'000 policies each one containing a rule and a
obligation

The Test-Scripts
~~~~~~~~~~~~~~~~

To assure that not only cached requests are used by the PEPd, but that
the PEPd also sends requests to the PDP, more than 200 different sets of
Action/Resource/Obligation have been defined in the properties file.
This can easily be done by changing the resource (e.g. ce1_100,
ce1_101, ...). More than that 100 certificates issued by the `ARC
instant online CA <https://arc-emi.grid.upjs.sk/instantCA/>`__ were
randomly combined with those sets, leading to roughly 1'000 different
possible requests.

Results and Metrics
^^^^^^^^^^^^^^^^^^^


-  Test ran for about 26.5h without interruption
-  4'078'768 authorization requests processed
-  43 requests per second sustained
-  274 ms average round trip time (pepcli -> PEPd (-> PDP -> PEPd) ->
   pepcli)

Memory consumption of the 1.4.0 PDP during a load-test:

.. image:: /images/pdp_memory_consumption_1.4.1.png
   :width: 596px
   :height: 235px

Memory consumption of the 1.4.0 PEPd during a load-test:

.. image:: /images/pepd_memory_consumption_1.4.1.png
   :width: 598px
   :height: 237px

Average requests per second during the load-test:

.. image:: /images/requests_per_second_1.4.1.png
   :width: 583px
   :height: 210px

Aging Tests
-----------

This test was done analogue to the performance test, but with only one
Agent starting one worker starting one thread. This resulted in about 9
requests per second.

Test Description
~~~~~~~~~~~~~~~~

The test was done using the same options as the performance test.

Results and Metrics
~~~~~~~~~~~~~~~~~~~

-  Test ran for about 140h without interruption
-  4'951'677 authorization requests processed
-  9 requests per second sustained
-  97.5 ms average round trip time (pepcli -> PEPd (-> PDP -> PEPd) ->
   pepcli)

Memory consumption of the 1.4.0 PDP during a aging-test:

.. image:: /images/aging_test_pdp_mem_consumption_1.4.1.png
   :width: 603px
   :height: 241px

Memory consumption of the 1.4.0 PEPd during a aging-test:

.. image:: /images/aging_test_pepd_mem_consumption_1.4.1.png
   :width: 604px
   :height: 228px

Average requests per second during the aging-test:

.. image:: /images/aging_test_requests_per_second_1.4.1.png
   :width: 587px
   :height: 216px
