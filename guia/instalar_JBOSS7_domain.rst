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

.. figure:: ../images/domain/02.png



Configuración del Workspace
Para completar el paso de la instalación, primero vamos a preparar el Workspace, crear la estructura de directorios, descargar e instalar EAP 7.1, luego configurar los hosts master y slaves.

Usaré /opt/jboss/lab/ como carpeta raíz 

En esta carpeta raíz, se crear una subcarpeta de laboratorios y 3 subcarpetas en ella, para los diferentes archivos de configuración de hosts::

	$ mkdir -p /opt/jboss/lab/{host0,host1,host2}


Instalar Jboss EAP
++++++++++++++++++++++


Visite Red Hat Developers para descargar el archivo zip EAP 7.1.0 y extraerlo en la carpeta /opt/jboss/lab/::

	$ jboss-eap-7.1.0.zip -d /opt/jboss/lab/

Ahora tendrá las siguientes subcarpetas en los laboratorios::

	host0 
	host1 
	host2 
	jboss-eap-7.1.0


En la vida real, tendría un binario de instalación separado para cada host, pero para este tutorial usaremos los mismos binarios de instalación compartidos por los 3 hosts.

Los archivos de configuración que se utilizarán para cada host se especificarán en el comando de inicio. Esta es una práctica recomendada que le permite ejecutar varias instancias de EAP en modo Dominio en la misma máquina usando los mismos archivos de instalación. También le permite actualizar a una versión más nueva de EAP sin afectar o sobrescribir sus archivos de configuración.

Crear Management User
++++++++++++++++++++++


Cree un usuario de administración adminusando la secuencia de comandos add-user::


	./add-user.sh 

	¿Qué tipo de usuario desea agregar? 
	 a) Usuario de administración (mgmt-users.properties) 
	 b) Usuario de la aplicación (application-users.properties)
	(a): a

	Introduzca los detalles del nuevo usuario a agregar.
	Usando el dominio 'ManagementRealm' tal como se descubre en los archivos de propiedades existentes.
	Nombre del usuario : admin
	El usuario 'admin' ya existe y está habilitado,  ¿quiere ... 
	 a). Actualice la contraseña del usuario y los roles 
	 b) Habilite el usuario existente 
	 c) Escriba un nuevo nombre de usuario.
	(a): a
	Las recomendaciones de la contraseña se listan a continuación. Para modificar estas restricciones modifique el archivo de configuración add-user.properties.
	 - La contraseña debe ser diferente al nombre de usuario
	 - La contraseña no debe ser uno de los siguientes valores restringidos {root, admin, administrator}
	 - La contraseña debe tener por lo menos 8 caracteres, 1 caracteres alfabéticos, 1 dígito(s), 1 símbolos que no sean alfanuméricos
	Contraseña : 
	Reintroduzca la contraseña : 
	¿ A qué grupos quiere que este usuario pertenezca? (introduzca una lista o deje en blanco para ninguno)[  ]: 
	Usuario actualizado 'admin' al archivo '/opt/jboss/lab/jboss-eap-7.1/standalone/configuration/mgmt-users.properties'
	Usuario actualizado 'admin' al archivo '/opt/jboss/lab/jboss-eap-7.1/domain/configuration/mgmt-users.properties'
	Se actualizó el usuario 'admin' con los grupos ' al archivo '/opt/jboss/lab/jboss-eap-7.1/standalone/configuration/mgmt-groups.properties'
	Se actualizó el usuario 'admin' con los grupos ' al archivo '/opt/jboss/lab/jboss-eap-7.1/domain/configuration/mgmt-groups.properties'
	¿Este nuevo usuario se va a utilizar para que un proceso AS se conecte a otro proceso AS?  
	 por ejemplo: para que un controlador host de esclavos se conecte al maestro o para una conexión remota para llamadas EJB de servidor a servidor.
	¿si/no? y
	Para representar el usuario agregue lo siguiente a la definición del servidor-identidades <secret value="VmVuZXp1ZWxhLjIx" />

Creando configuration files
+++++++++++++++++++++++++++++

Ahora vamos a replicar la configuración actual en los 3 hosts individuales antes de continuar. Inicialice los archivos de configuración para cada host copiando la carpeta jboss-eap-7.1.0 /domain en cada uno de los tres hosts.::

	cp -r /opt/jboss/lab/jboss-eap-7.1/domain/ host0/domain

	cp -r /opt/jboss/lab/jboss-eap-7.1/domain/ host1/domain

	cp -r /opt/jboss/lab/jboss-eap-7.1/domain/ host2/domain



Esto debería incluir tres subcarpetas en hostX /domain::

	configuration/
	data/
	tmp/


Ahora tenemos la configuración básica en la que podemos confiar para configurar un Domain Controller Master en host0 y slaves. Host Controllers en host1 y host2.

Configurar the Domain Controller/Master en Host0
+++++++++++++++++++++++++++++++++++++++++++++++++

La configuración del controlador de dominio se establece en dos archivos: host.xml y domain.xml

Host.xml
+++++++++++


Edite el archivo host-master.xml ubicado en host0/domain/configuration Reemplace el maestro de nombre de host con host0-master::

	<host xmlns="urn:jboss:domain:5.0" name="host0-master">

Compruebe la configuración del controlador de dominio::

	<domain-controller>
	    <local/>
	</domain-controller>


Esto significa que host0 es nuestro Domain Controller; Veremos la diferencia con un Host Constroller simple en la siguiente sección.

Ahora compruebe los parámetros de la interfaz de gestión::

    <interfaces>
        <interface name="management">
            <inet-address value="${jboss.bind.address.management:127.0.0.1}"/>
        </interface>
    </interfaces>












