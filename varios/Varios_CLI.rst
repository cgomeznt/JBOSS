Conectar al Jboss CLI
++++++++++++++++++++++++++++
::

	jboss-cli.sh --connect controller=IP_ADDRESS

	$ /opt/jboss/jboss-eap-7.1.0/bin/jboss-cli.sh --connect controller=192.168.0.4

Informacion de los deployments
++++++++++++++++++++++++++++++++

	[standalone@192.168.0.4:9990 /] deployment-info
	NAME       RUNTIME-NAME PERSISTENT ENABLED STATUS 
	CONSIS.ear CONSIS.ear   true       true    OK     
	[standalone@192.168.0.4:9990 /] deployment-info --name=CONSIS.ear
	NAME       RUNTIME-NAME PERSISTENT ENABLED STATUS 
	CONSIS.ear CONSIS.ear   true       true    OK     
	[standalone@192.168.0.4:9990 /] 

o::

	[standalone@192.168.0.4:9990 /] /deployment=CONSIS.ear:read-attribute(name=status)
	{
	    "outcome" => "success",
	    "result" => "OK"
	}
	[standalone@192.168.0.4:9990 /] 

o::

	/opt/jboss/jboss-eap-7.1.0/bin/jboss-cli.sh --connect controller=192.168.0.4 --command="deployment-info --name=CONSIS.ear"


Deploy
+++++++++

	[standalone@192.168.0.4:9990 /] deploy /home/scm/svn/workspace/BXMAS/FE-AEBXMAS-458/scm/Make_EAR/JBoss7_BXMAS_8080/dist/CONSIS.ear
	[standalone@192.168.0.4:9990 /] 

o::

	/opt/jboss/jboss-eap-7.1.0/bin/jboss-cli.sh --connect controller=192.168.0.4 --command="deploy /u06/app/oracle/domain/EAR_Weblogic/8080/CONSIS.ear"


Undeploy
+++++++++

	[standalone@192.168.0.4:9990 /] undeploy CONSIS.ear
	[standalone@192.168.0.4:9990 /] 

o::

	/opt/jboss/jboss-eap-7.1.0/bin/jboss-cli.sh --connect controller=192.168.0.4 --command="undeploy CONSIS.ear"


Start
+++++++++

Stop
+++++++++

	[standalone@192.168.0.4:9990 /] shutdown
	[disconnected /] exit

o::

	/opt/jboss/jboss-eap-7.1.0/bin/jboss-cli.sh --connect controller=192.168.0.4 --command="shutdown"



