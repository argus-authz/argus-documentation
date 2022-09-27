.. _argus_entropy:

Entropy related problems
========================

In headless servers, sometimes Java processes hangs waiting on a thread that's
continuously in wait status, trying to generate random numbers, because entropy
is too low to generate enough random numbers.

Check the entropy level with the following command::

  $ cat /proc/sys/kernel/random/entropy_avail

If the obtained value is less than 1000, entropy is too low. This problem can be
solved in two different way:

- Running ``haveged`` daemon;
- Configure Argus daemons to use ``/dev/urandom`` as random device.


Run Haveged
-----------

Install EPEL repository and ``haveged`` package::

  $ sudo yum install -y epel-release
  $ sudo yum install -y haveged

Enable and run the daemon. On EL7 platforms::

  $ sudo systemctl enable haveged
  $ sudo systemctl start haveged

On EL6 platforms::

  $ sudo chkconfig haveged on
  $ sudo service haveged start


Change random device
--------------------

Edit the Argus services environment files, adding the option
``-Djava.security.egd=file:/dev/urandom`` to the Java options.

For the PAP service, edit the ``PAP_JAVA_OPTS`` variable in
``/etc/sysconfig/argus-pap`` file. Example::

  PAP_JAVA_OPTS="-Djava.security.egd=file:/dev/urandom"

For the PDP service, edit the ``PDP_JOPTS`` variable in
``/etc/sysconfig/argus-pdp`` file. Example::
  
  PDP_JOPTS="-Xmx256M -Djdk.tls.trustNameService=true -Djava.security.egd=file:/dev/urandom"

For the PEP daemon, edit the ``PEPD_JOPTS`` variable in
``/etc/sysconfig/argus-pepd`` file. Example:: 

  PEPD_JOPTS="-Xmx256M -Djdk.tls.trustNameService=true -Djava.security.egd=file:/dev/urandom"

Then restart the services. On EL7 platforms::

  $ sudo systemctl restart argus-pap argus-pdp argus-pepd

On EL6 platforms::

  $ for svc in pap pdp pepd; do sudo service argus-$svc restart; done

