Configurar JBoss EAP con systemctl
=====================================

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
	Environment="JBOSS_HOME=/opt/jboss-eap-7.4"
	Environment="JBOSS_USER=jboss"
	PIDFile=/var/run/jboss-eap/jboss-eap.pid
	ExecStart=/opt/jboss-eap-7.4/bin/init.d/jboss-eap-rhel.sh start
	ExecStop=/opt/jboss-eap-7.4/bin/init.d/jboss-eap-rhel.sh stop


