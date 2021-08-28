Run JBoss non-root
====================

Si esta ejecutando JBoss como non-root, y cambiaron la configuracion para que JBoss escuche por los puertos 80 y/o 443, no podra iniciar por el puerto 80 y el 443, le arrojara este error::

	13:37:09,975 ERROR [org.jboss.msc.service.fail] (MSC service thread 1-1) MSC000001: Failed to start service org.wildfly.undertow.listener.default: org.jboss.msc.service.StartException in service org.wildfly.undertow.listener.default: WFLYUT0082: Could not start 'default' listener.
		at org.wildfly.extension.undertow.ListenerService.start(ListenerService.java:211)
		at org.jboss.msc.service.ServiceControllerImpl$StartTask.startService(ServiceControllerImpl.java:1739)
		at org.jboss.msc.service.ServiceControllerImpl$StartTask.execute(ServiceControllerImpl.java:1701)
		at org.jboss.msc.service.ServiceControllerImpl$ControllerTask.run(ServiceControllerImpl.java:1559)
		at org.jboss.threads.ContextClassLoaderSavingRunnable.run(ContextClassLoaderSavingRunnable.java:35)
		at org.jboss.threads.EnhancedQueueExecutor.safeRun(EnhancedQueueExecutor.java:1990)
		at org.jboss.threads.EnhancedQueueExecutor$ThreadBody.doRunTask(EnhancedQueueExecutor.java:1486)
		at org.jboss.threads.EnhancedQueueExecutor$ThreadBody.run(EnhancedQueueExecutor.java:1363)
		at java.lang.Thread.run(Thread.java:745)
	Caused by: java.net.SocketException: Permission denied


	
Instalamos el paquete authbind, para CentOS 7.x::

	wget https://s3.amazonaws.com/aaronsilber/public/authbind-2.1.1-0.1.x86_64.rpm

Una vez que se haya instalado authbind, ejecute lo siguiente según los puertos en los que desee que Tomcat escuche::

	sudo touch /etc/authbind/byport/80
	sudo chmod 500 /etc/authbind/byport/80
	sudo chown jboss /etc/authbind/byport/80
	sudo touch /etc/authbind/byport/443
	sudo chmod 500 /etc/authbind/byport/443
	sudo chown jboss /etc/authbind/byport/443
	

En el template de Systemctl para JBoss utilizamos::

	ExecStart=/usr/bin/authbind --deep opt/jboss-eap-7.4/bin/init.d/jboss-eap-rhel.sh start
	ExecStop=/usr/bin/authbind --deep opt/jboss-eap-7.4/bin/init.d/jboss-eap-rhel.sh stop


Link editado de:

	https://github.com/cgomeznt/Tomcat/blob/master/guia/non-root.rst

Link de original de Gitlab:

	https://gitlab.com/jcsm72/bd-knowledg/-/tree/master/Apache-Tomcat
	
Link original:

	https://blog.webhosting.net/how-to-get-tomcat-running-on-centos-7-2-using-privileged-ports-1024/
	
Link para la descarga:

	https://github.com/tootedom/authbind-centos-rpm/tree/master/authbind/RPMS/x86_64
