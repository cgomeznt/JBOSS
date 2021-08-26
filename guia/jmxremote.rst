Activar el JMX remote
======================

Informacióm obtenida de:

https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.2/html/performance_tuning_guide/monitoring_performance

Asegure que tiene creado un usuario administrador. se puede crear de forma separada el usuario administrador de un usuario de Monitoreo.
Cuando inicie el JBoss EAP, enlace la interfaz administrativa con la IP que usara para el monitoreo remoto en el servidor::

	$ JBOSS_HOME/bin/standalone.sh -bmanagement=IP_ADDRESS

Use la siguiente URI con el usuario administrador en un JVM monitoring tool para conectar al JBoss EAP server. La URI abajo es usada como porto por default para la administración (9990).::

	service:jmx:remote+http://IP_ADDRESS:9990


Ahora para hacer las pruebas utilizamos el cliente Jconsole de Java.::

	$JAVA_HOME/bin/jconsole

Si las configuración anterior no funciona
+++++++++++++++++++++++++++++++++++++++++++

leer el archivo $JBOSS_HOME/bin/client/README-CLI-JCONSOLE.txt, pero cuidado que el ejemplo utiliza ";" y eso es para windows, dejo un ejemplo funcional::


	$JAVA_HOME/bin/jconsole -J-Djava.class.path=$JAVA_HOME/lib/jconsole.jar:$JAVA_HOME/lib/tools.jar:/home/cgomeznt/jboss-client.jar



NOTA:MONITORING THE JBOSS EAP / WILDFLY APPLICATION SERVER WITH THE COMMAND LINE INTERFACE (CLI) y NON-INTERACTIVE MODE

https://blog.akquinet.de/2014/09/15/monitoring-the-jboss-eap-wildfly-application-server-with-the-command-line-interface-cli/

https://github.com/wildfly/wildfly/blob/main/docs/src/main/asciidoc/_admin-guide/Command_Line_Interface.adoc
