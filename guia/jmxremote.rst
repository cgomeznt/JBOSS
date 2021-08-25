Activar el JMX remote
======================

Informacióm optenida de:

https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.2/html/performance_tuning_guide/monitoring_performance

Asegure que tiene creado un usuario administrador. se puede crear de forma separada el usuario administrador de un usuario de Monitoreo.
Cuando inicie el JBoss EAP, enlace la interfaz administrativa con la IP que usara para el monitoreo remoto en el servidor::

	$ EAP_HOME/bin/standalone.sh -bmanagement=IP_ADDRESS

Use la siguiente URI con el usuario administrador en un JVM monitoring tool para conectar al JBoss EAP server. La URI abajo es usada como porto por default para la administración (9990).

service:jmx:remote+http://IP_ADDRESS:9990


Ahora para hacer las pruebas utilizamos el cliente **cmdline-jmxclient-0.10.3.jar** de la siguiente forma::

	$ $JAVA_HOME/bin/java -jar /usr/local/bin/cmdline-jmxclient-0.10.3.jar - localhost:8050 java.lang:type=Memory HeapMemoryUsage

	11/12/2018 11:33:46 -0400 org.archive.jmx.Client HeapMemoryUsage: 
	committed: 514850816
	init: 536870912
	max: 514850816
	used: 50560328

También podemos utilizar Jconsole de Java.

