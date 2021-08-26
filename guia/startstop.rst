Start & Stop JBOSS
===============


Starting JBoss EAP
+++++++++++++++++++

Start JBoss EAP as a Standalone Server::

	$ EAP_HOME/bin/standalone.sh

Start JBoss EAP in a Managed Domain::

	$ EAP_HOME/bin/domain.sh

Stopping JBoss EAP
+++++++++++++++++

Hay dos formas la primero con el comando kill y la otra técnica utilizando el CLI

Comando kill::

	kill -15 PID

CLI::

	$EAP_HOME/bin/jboss-cli.sh --connect

	shutdown


Otra forma
++++++++++++++


https://access.redhat.com/solutions/1320133


JBoss EAP 7::

	$JBOSS_HOME/bin/init.d/jboss-eap-rhel.sh start|stop|restart


The next step is create a file named jbosseap6|7.service or a name of your choice inside the directory /etc/systemd/system with the following content::

	[Unit]
	Description=JBoss EAP Systemctl script
	After=network-online.target

	[Service]
	Type=forking
	ExecStart=/opt/server/jboss-eap-6/bin/init.d/jboss-as-standalone.sh start
	ExecStop=/opt/server/jboss-eap-6/bin/init.d/jboss-as-standalone.sh stop
	ExecReload=/opt/server/jboss-eap-6/bin/init.d/jboss-as-standalone.sh restart
	PIDFile=/var/run/jboss-as/jboss-as-standalone.pid

	[Install]
	WantedBy=multi-user.target

**Note:**

in these kind of script the variable substitution does not work, so it is necessary change the EAP installation path according your environment.

After=network-online.target is required to start JBoss EAP after the network is "up" during the system start-up. See also this documentation, knowledge and wiki page for details.

If you want to tune ulimit for this JBoss EAP service process like max open files (ulimit -n) or max user processes (ulimit -u), you need to specify LimitNOFILE and LimitNPROC to the above service file. See also this knowledge for details.


To extend start up / shutdown timeout, add TimeoutStartSec and TimeoutStopSec under [Service] entry::

    [Service]
    TimeoutStartSec=600
    TimeoutStopSec=600

Then you will be able to start, stop and restart by issuing the follow commands::
	
	systemctl start jbosseap6
	systemctl stop  jbosseap6
	systemctl restart jbosseap6

To do the EAP be loaded in the RHEL boot, execute the command below::

	systemctl enable jbosseap6

**NOTE :**

While starting multiple instances at the boot time. It might be required to adjust the STARTUP_WAIT timeout in jboss-*.conf.

Disclosure: The above script are provided as it is. It was tested in Red Hat Enterprise Linux 7 and should work without problems. Red Hat Global Support Services does not cover script development and changes to it. Please refer the Production Support Scope of Coverage.

Root Cause
************
JBoss EAP 6 and 7 do not have a systemctl compatible script.

