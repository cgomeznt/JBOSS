Tunning un server para JBOSS EAP
================================


Creamos el usuario jboss que sere el propietario de la carpeta que se genere al descomprimir el .zip::

	#groupadd -g 550 jboss; useradd -c “Usuario Jboss” -g 550 -u 550 -s /bin/bash jboss; echo ‘XyzXyzXyzXyz’ | passwd –stdin jboss

A nivel sistema operativo agregamos los siguientes parametros::

	en /etc/security/limits.conf
	* soft nofile 65535
	* hard nofile 65535
	* hard rss unlimited
	en /etc/security/limits.d/90-nproc.conf
	* soft nproc 1024
	jboss soft nproc unlimited
	root soft nproc unlimited
	admwsph soft nproc unlimited
	en /etc/sysctl.conf
	net.ipv4.tcp_keepalive_time = 60
	net.ipv4.tcp_keepalive_intvl = 30
	net.ipv4.tcp_keepalive_probes = 3net.ipv4.tcp_fin_timeout=15
	net.ipv4.tcp_tw_reuse=1
	vm.drop_caches = 3

Deshabilitamos los procesos innecesarios en el sistemaoperativo como son: rpcbind, cups, postfix, rpcidmapd, rpcsvcgssd, autofs, nfslock; talvez algunos otros ya depende de cada uno de nosotros::

	#for i in rpcbind cups postfix rpcidmapd rpcsvcgssd autofs nfslock ;do chkconfig $i off;done
	#for i in rpcbind cups postfix rpcidmapd rpcsvcgssd autofs nfslock xinetd;do service $i stop;done

Utilizaremos el FS /opt para descomprimir el archivo .zip que contiene nuestro jboss, por lo que copiaremos el archivo .zip a dicha partición::

	# cd /opt/
	# cp -r /home/iu210545/jboss-eap-6.1.0.zip .

Descomprimimos el archivo zip, el cual generara una carpeta con el nombre jboss-eap-6.1.::

	# unzip jboss-eap-6.1.0.zip

Otorgamos los permisos correspondientes a la carpeta jboss-eap-6.1::

	$ cd /opt/jboss-eap-6.1/
	# chown -R jboss:jboss jboss-eap-6.1

Una vez realizado los pasos anteriores procedemos a configurar jboss-EAP-6.1; en este post trataremos una configuración de jboss-EAP-6.1 en modo standalone; por lo que generaremos el usuario para la consola administrativa de jboss y copiaremos la carpeta standalone de la ruta: /opt/jboss-eap-6.1 para generar nuestra instancia llamada myinstancia.

Generamos el usuario de la consola administrativa::

	$ ./add-user.sh

Este usuario y su password cifrado se almacena en la ruta /opt/jboss-eap-6.1/standalone/configuration/mgmt-users.properties.
Generamos una copia de la carpeta standalone y la llamaremos myinstance.::

	$ cd /opt/jboss-eap-6.1/
	$ cp -r standalone myinstancia

Apartir de esta instancia generada configuraremos los parametros de ip,puerto, para lo cual editaremos en archivo standalone.xml de la ruta siguiente: /opt/jboss-eap-6.1/myinstance/configuration/standalone.xml::

	$ vi /opt/jboss-eap-6.1/myinstance/configuration/standalone.xml

Nota: En este archivo .xml podemos definir la ip del servidor, asi como system properties, datasources,puertos,etc.
Asignaremos la ip en los siguientes tags::

	<inet-address,
	jboss.bind.address:192.168.10.15
	jboss.bind.address.management:192.168.10.15
	jboss.bind.address:192.168.10.15
	jboss.bind.address.unsecure:192.168.10.15

El siguiente parametro que nos interesa es el offset, este nos indica por que puerto levantara nuestra instancia; por default esta el valor 0 que significa que la instancia levantara por el puerto 8080, si queremos que levante por un puerto diferente simplemente hay que restarle 8080 al puerto que querramos que levante la instancia, por ejemplo: Queremos que nuestra instancia levante por el 28080 entonces le restaremos 8080, 28080 – 8080 = 20000, por lo que asignaremos en el offset el valor de 20000.

standard-sockets” default-interface=”public” port-offset=”${jboss.socket.binding.port-offset:20000
Con esto ya tenemos instalado nuestro Jboss middleware con su respectiva instancia y consola administrativa para deployar y realizar otras configuraciones en nuestra aplicación.

Para validar dicho funcionamiento, debemos accesar vía web a la instancia con la url 192.168.10.15:28080

Validacion-jbossDamos click en Administration Console, para que nos envie a la consola administrativa, si todo va bien nos pedira usuario y password; que debemos ingresar, este usuario y password es el que asignamos al ejecutar ./add-user.sh
User-pass-jboss


