%META:TOPICINFO{author="ad968f62f612332eff6b" date="1310117151"
format="1.1" reprev="1.10" version="1.10"}%
%META:TOPICPARENT{name="AuthorizationFramework"}%

Nagios plugins for Argus Authorization Service
==============================================

It is possible to monitor the state of the Argus Authorization Services
(PAP, PDP and PEP Server) with some Nagios plugins. These plugins will
monitor the activity of the Argus components, and eventually try to
restart the service if a component is down.

Requirement
-----------

To install the Nagios plugins for Argus on the service host, you need to
install the following components:

-  Nagios NRPE
-  Nagios Plugins
-  python

You can install it with the YUM command:

``yum install nagios-nrpe nagios-plugins python``

Enable and start the Nagios NRPE service on the host:

::

    chkconfig nrpe on
    service nrpe start

Installation for Argus 1.3 (EMI)
--------------------------------

#. Download the Nagios Argus plugins
   ``[[%ATTACHURL%/argus-nagios-0.2.2-EMI.tar.gz][argus-nagios-0.2.2-EMI.tar.gz]]``
   file
#. Extract the downloaded file:
   ``tar xvzf argus-nagios-0.2.2-EMI.tar.gz``

Argus 1.3 (EMI) Service Host Configuration
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

On the host where the Argus Service is running, do the following:

#. Add following lines to ``sudoers`` file by using ``visudo`` (assuming
   nagios plugins are run as user **nagios**)

::

        Defaults:nagios !requiretty
        Cmnd_Alias ARGUS = /etc/init.d/argus-pap, /etc/init.d/argus-pdp, /etc/init.d/argus-pepd
        nagios ALL = NOPASSWD: ARGUS

#. Copy the 7 plugins (``*.py`` files) into your local Nagios plugins
   directory. e.g. ``/usr/lib64/nagios/plugins`` (make sure they have
   execute permissions)
#. Edit your local Nagios NRPE configuration file
   ``/etc/nagios/nrpe.cfg``, and add the following command definitions:

::

    #
    # Argus Service monitoring
    #
    command[check_argus_pap]=/usr/lib64/nagios/plugins/check_argus_pap.py
    command[check_argus_pdp]=/usr/lib64/nagios/plugins/check_argus_pdp.py
    command[check_argus_pepd]=/usr/lib64/nagios/plugins/check_argus_pepd.py
    command[perf_argus_pdp]=/usr/lib64/nagios/plugins/perf_argus_pepd_pdp.py -s PDP -w 300 -c 400
    command[perf_argus_pepd]=/usr/lib64/nagios/plugins/perf_argus_pepd_pdp.py -s PEPD -w 512 -c 640
    command[restart_argus_pap]=/usr/lib64/nagios/plugins/restart_argus_pap.py 
    command[restart_argus_pdp]=/usr/lib64/nagios/plugins/restart_argus_pdp.py
    command[restart_argus_pepd]=/usr/lib64/nagios/plugins/restart_argus_pepd.py 

#. Restart Nagios NRPE: ``/etc/init.d/nrpe restart``

The values for the warning and critical stage are recommendations, they
can be adjusted if necessary.

Nagios Server Configuration
~~~~~~~~~~~~~~~~~~~~~~~~~~~

On the host running your Nagios server, do the following:

#. Copy the ``run_commands`` script into the
   ``<NAGIOS_PATH>/libexec/eventhanlders/`` directory (the file must
   executable for the **nagios** user).
#. Define the following command in your Nagios ``commands.cfg``
   configuration file (beware, you might have to adapt the path of the
   ``run_command`` script)

::

    define command {
            command_name    do_event
            command_line    /usr/local/nagios/libexec/eventhandlers/run_command  $SERVICESTATE$ $SERVICESTATETYPE$ $SERVICEATTEMPT$ $HOSTADDRESS$ $ARG1$
    }

#. Add the following into your Nagios server objects-file (replace
   ``ARGUS_HOSTNAME`` by the hostname running your Argus service):

::

    define service {
       use generic-service
       host_name <ARGUS_HOSTNAME>
       service_description Argus PAP
       check_command check_nrpe!check_argus_pap
       max_check_attempts  4
       event_handler_enabled  1
       event_handler  do_event!restart_argus_pap
    }

    define service {
       use generic-service
       host_name <ARGUS_HOSTNAME>
       service_description Argus PDP
       check_command check_nrpe!check_argus_pdp
       max_check_attempts  4
       event_handler_enabled  1
       event_handler  do_event!restart_argus_pdp
    }

    define service {
       use generic-service
       host_name <ARGUS_HOSTNAME>
       service_description Argus PEP Server
       check_command check_nrpe!check_argus_pepd
       max_check_attempts  4
       event_handler_enabled  1
       event_handler do_event!restart_argus_pepd
    }

    define service {
       use generic-service
       host_name <ARGUS_HOSTNAME>
       service_description Argus PDP Stats
       check_command check_nrpe!perf_argus_pdp
    }

    define service {
       use generic-service
       host_name <ARGUS_HOSTNAME>
       service_description Argus PEP Server Stats
       check_command check_nrpe!perf_argus_pepd
    }

Installation for Argus 1.2 (gLite 3.2)
--------------------------------------

#. Download the Nagios Argus plugins
   `nagios\_argus.tar.gz <%ATTACHURL%/nagios_argus.tar.gz>`__ file
#. Extract the downloaded file ``nagios_argus.tar.gz``

Argus 1.2 Service Host Configuration
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

On the host where the Argus Service is running, do the following:

#. Add following lines to ``sudoers`` file by using ``visudo`` (assuming
   nagios plugins are run as user **nagios**)

::

        Defaults:nagios !requiretty
        Cmnd_Alias ARGUS = /etc/init.d/pepd, /etc/init.d/pdp, /etc/init.d/pap-standalone
        nagios ALL = NOPASSWD: ARGUS

#. Copy the 7 plugins (``*.py`` files) into your local Nagios plugins
   directory. e.g. ``/usr/lib64/nagios/plugins`` (make sure they have
   execute permissions)
#. Edit your local Nagios NRPE configuration file ``nrpe.cfg`` (e.g.
   ``/etc/nagios/nrpe.cfg``), and add the following command definitions:

::

    command[check_argus_pap]=/usr/lib64/nagios/plugins/check_argus_pap.py
    command[check_argus_pepd]=/usr/lib64/nagios/plugins/check_argus_pepd.py
    command[check_argus_pdp]=/usr/lib64/nagios/plugins/check_argus_pdp.py
    command[check_argus_pdp_perf]=/usr/lib64/nagios/plugins/check_argus_pepd_pdp.py -s PDP -w 300 -c 400
    command[check_argus_pepd_perf]=/usr/lib64/nagios/plugins/check_argus_pepd_pdp.py -s PEPD -w 600 -c 1000
    command[restart_argus_pap]=/usr/lib64/nagios/plugins/restart_argus_pap.py 
    command[restart_argus_pepd]=/usr/lib64/nagios/plugins/restart_argus_pepd.py 
    command[restart_argus_pdp]=/usr/lib64/nagios/plugins/restart_argus_pdp.py

#. Restart Nagios NRPE: ``/etc/init.d/nrpe restart``

Nagios Server Configuration
~~~~~~~~~~~~~~~~~~~~~~~~~~~

On the host running your Nagios server, do the following:

#. Copy the ``run_commands`` script into the
   ``<NAGIOS_PATH>/libexec/eventhanlders/`` directory (the file must
   executable for the **nagios** user).
#. Define the following command in your Nagios ``commands.cfg``
   configuration file (beware, you might have to adapt the path of the
   ``run_command`` script)

::

    define command {
            command_name    do_event
            command_line    /usr/local/nagios/libexec/eventhandlers/run_command  $SERVICESTATE$ $SERVICESTATETYPE$ $SERVICEATTEMPT$ $HOSTADDRESS$ $ARG1$
    }

#. Add the following into your Nagios server objects-file (replace
   ``ARGUS_HOME_NAME`` by the hostname running your Argus service):

::

    define service {
       use generic-service
       host_name <ARGUS_HOST_NAME>
       service_description ARGUS PAP
       check_command check_nrpe!check_argus_pap
       max_check_attempts  4
       event_handler_enabled  1
       event_handler      do_event!restart_argus_pap
    }

    define service {
       use generic-service
       host_name <ARGUS_HOST_NAME>
       service_description ARGUS PDP
       check_command check_nrpe!check_argus_pdp
       max_check_attempts  4
       event_handler_enabled  1
       event_handler      do_event!restart_argus_pdp
    }

    define service {
       use generic-service
       host_name <ARGUS_HOST_NAME>
       service_description ARGUS PEPD
       check_command check_nrpe!check_argus_pepd
       max_check_attempts  4
       event_handler_enabled  1
       event_handler      do_event!restart_argus_pepd
    }

    define service {
       use generic-service
       host_name <ARGUS_HOST_NAME>
       service_description ARGUS PDP PERF
       check_command check_nrpe!check_argus_pdp_perf
    }

    define service {
       use generic-service
       host_name <ARGUS_HOST_NAME>
       service_description ARGUS PEDP PERF
       check_command check_nrpe!check_argus_pepd_perf
    }

%META:FILEATTACHMENT{name="nagios\_argus.tar.gz"
attachment="nagios\_argus.tar.gz" attr="" comment="old Nagios plugins
for Argus 1.2 (gLite 3.2)" date="1271157743" path="nagios\_argus.tar.gz"
size="4656" stream="nagios\_argus.tar.gz"
tmpFilename="/usr/tmp/CGItemp51817" user="ad968f62f612332eff6b"
version="1"}% %META:FILEATTACHMENT{name="argus-nagios-0.2.2-EMI.tar.gz"
attachment="argus-nagios-0.2.2-EMI.tar.gz" attr="" comment="Argus 1.3
(EMI) Nagios plugins" date="1309965199"
path="argus-nagios-0.2.2-EMI.tar.gz" size="5615"
stream="argus-nagios-0.2.2-EMI.tar.gz"
tmpFilename="/usr/tmp/CGItemp15727" user="ad968f62f612332eff6b"
version="1"}%
