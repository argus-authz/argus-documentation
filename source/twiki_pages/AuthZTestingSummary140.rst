.. _argus-testing-summary-140:

Authorization Services Testing Summary
======================================

Hardware Description
--------------------

For the perfomance and aging tests, we installed:

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
`here <https://twiki.cern.ch/twiki/bin/view/EGEE/AuthZLLT>`__, using the
following options. Each agent (host running grinder-framework) opens two
worker, and each worker opens 2 threads. Therefore a maximum of 12
(3\*2\*2) simultaneous requests are achieved.

Test Description (SSL enabled)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

This is the most common case, since the default configuration with YAIM
results in a PEPd with SSL enabled

On the Argus Server


The default configuration was used The policies needed for this test
were added to the PAP

-  pap-admin ap permit pfqan="/dteam" --action "submit" --resource
   "ce1\_.\*"
-  pap-admin ap deny pfqan="/dteam" --action "submit" --resource
   "ce2\_.\*"

while the star denotes a number between 1 and 500, so in total the pap
was loaded with 1'000 policies each one containing a rule and a
obligation

The Test-Scripts


To assure that not only cached requests are used by the PEPd, but that
the PEPd also sends requests to the PDP, more than 200 different sets of
Action/Resource/Obligation have been defined in the properties file.
This can easily be done by changing the resource (e.g. ce1\_100,
ce1\_101, ...). More than that 100 certificates issued by the `ARC
instant online CA <https://arc-emi.grid.upjs.sk/instantCA/>`__ were
randomly combined with those sets, leading to roughly 1'000 different
possible requests.

Results and Metrics


-  Test ran for about 26.5h without interruption
-  4'078'768 authorization requests processed
-  43 requests per second sustained
-  274 ms average round trip time (pepcli -> PEPd (-> PDP -> PEPd) ->
   pepcli)

-  Memory consumption of the 1.4.0 pdp during a load-test: <br /> <img
   src="%ATTACHURLPATH%/pdp\_memory\_consumption\_1.4.1.png"
   alt="pdp\_memory\_consumption\_1.4.1.png" width='596' height='235' />

-  Memory consumption of the 1.4.0 pepd during a load-test: <br /> <img
   src="%ATTACHURLPATH%/pepd\_memory\_consumption\_1.4.1.png"
   alt="pepd\_memory\_consumption\_1.4.1.png" width='598' height='237'
   />

-  Average requests per second during the load-test: <br /> <img
   src="%ATTACHURLPATH%/requests\_per\_second\_1.4.1.png"
   alt="requests\_per\_second\_1.4.1.png" width='583' height='210' />

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

-  Memory consumption of the 1.4.0 pdp during a aging-test: <br /> <img
   src="%ATTACHURLPATH%/aging\_test\_pdp\_mem\_consumption\_1.4.1.png"
   alt="aging\_test\_pdp\_mem\_consumption\_1.4.1.png" width='603'
   height='241' />

-  Memory consumption of the 1.4.0 pepd during a aging-test: <br /> <img
   src="%ATTACHURLPATH%/aging\_test\_pepd\_mem\_consumption\_1.4.1.png"
   alt="aging\_test\_pepd\_mem\_consumption\_1.4.1.png" width='604'
   height='228' />

-  Average requests per second during the aging-test: <br /> <img
   src="%ATTACHURLPATH%/aging\_test\_requests\_per\_second\_1.4.1.png"
   alt="aging\_test\_requests\_per\_second\_1.4.1.png" width='587'
   height='216' />
