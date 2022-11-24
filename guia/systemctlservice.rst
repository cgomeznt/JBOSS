Configurar JBoss EAP con systemctl
=====================================

Antes de crear el archivo /etc/systemd/system/jboss-eap.service, se debe contemplar lo siguiente::

Tener creado el usuario de jboss.

Que la carpeta /var/run/jboss-eap/ el propietario sea jboss.

Que la carpeta /var/log/jboss-eap/ el propietario sea jboss.

Tener configurado todas las variables de entorno y en especial JAVA_HOME

::

	vi /etc/systemd/system/jboss-eap.service

	[Unit]
	Documentation=man:systemd-sysv-generator(8)
	Description=SYSV: JBoss EAP startup script
	Before=shutdown.target
	Conflicts=shutdown.target

	[Service]
	Type=forking
	RestartSec=10
	Restart=always
	TimeoutSec=5min
	IgnoreSIGPIPE=no
	KillMode=process
	GuessMainPID=no
	RemainAfterExit=no

	Environment="JAVA_HOME=/usr/java/jdk-12/bin/java"
	Environment="JBOSS_HOME=/opt/jboss-eap-7.4"
	Environment="JBOSS_USER=jboss"

	PIDFile=/var/run/jboss-eap/jboss-eap.pid
	ExecStart=/opt/jboss-eap-7.4/bin/init.d/jboss-eap-rhel.sh start
	ExecStop=/opt/jboss-eap-7.4/bin/init.d/jboss-eap-rhel.sh stop

	[Install]
	WantedBy=multi-user.target

NOTA: Tambien pueden utilizar esta ruta /usr/lib/systemd/system/jboss-eap.service


