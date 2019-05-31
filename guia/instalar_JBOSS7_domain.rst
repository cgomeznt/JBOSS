Instalar y Configurar un dominio simple de EAP 7.0.
==================================================

Esto fue certificado con Jboss-EAP 7.1.0

Vamos a configurar un dominio JBoss EAP 7.0 con los siguientes requisitos:

	* 1 Domain Controller en una máquina llamada host0
	* 1 Host Controller en una máquina host1 con dos instancias EAP Server11 y Server12
	* 1 Host Controller en una máquina host2 con tres instancias de EAP Servers21, Server22 y Server23
	* Host0 debe ejecutarse como el controlador maestro,
	* Host1 y Host2 son esclavos que se conectan a Host0
	* Server11 y Server21 son miembros del grupo de servidores primario (nombre = primary-server-group)
	* Server12 y Server22 pertenecen al grupo de servidores secundarios (nombre = secundary-server-group)
	* Server23 es el único miembro del grupo de servidores singleton (nombre = singleton-server-group)
	* En la vida real las maquinas Host0, Host1, Host2 se encuentra en su mayoría en una ubicación física diferente o en maquinas virtuales diferentes, pero para el propósito de este tutorial los simularemos en el mismo localhost utilizando una instalación EAP 7.1.0 y diferentes carpetas de configuración para cada Máquina.
	* Para mantenerlo simple, no cubriremos la configuración de JVM en detalle en esta parte.

