Configurando parametros del JVM
=================================

Link oficial:

https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.0/html/configuration_guide/configuring_jvm_settings

Configuring JVM Settings for a Standalone Server
++++++++++++++++++++++++++++++

JVM settings for standalone JBoss EAP server instances can be declared at runtime by setting the JAVA_OPTS environment variable before starting the server.

Find into file $EAP_HOME/bin/standalone.conf

An example of setting the JAVA_OPTS environment variable on Linux is shown below.::

	$ export JAVA_OPTS="-Xmx1024M"

The same setting can be used in a Microsoft Windows environment::

	set JAVA_OPTS="Xmx1024M"


