http://www.mastertheboss.com/eBooks/eap-linux.pdf


Configuraciones de las variables JBOSS_HOME JAVA_HOME

-Djboss.bind.address=192.168.1.30 -Djboss.bind.address.management=192.168.1.30

JAVA_OPTS="$JAVA_OPTS -Dcom.sun.management.jmxremote, -Dcom.sun.management.jmxremote.port=9999 -Dcom.sun.management.jmxremote.ssl=false -Dcom.sun.management.jmxremote.authenticate=false

./standalone.sh -c standalone-full.xml -bmanagement 192.168.1.20

./standalone.sh -Djboss.bind.address.management=192.168.1.20

./standalone.sh --server-config=standalone-full.xml -b 192.168.1.20 -Djboss.bind.address.management=192.168.1.20

./standalone.sh --server-config=standalone-full.xml -Dcom.sun.management.jmxremote, -Dcom.sun.management.jmxremote.port=9999 -Dcom.sun.management.jmxremote.ssl=false -Dcom.sun.management.jmxremote.authenticate=false


service:jmx:remoting-jmx://192.168.1.20:9999

JAVA_OPTS="$JAVA_OPTS -Djava.util.logging.manager=org.jboss.logmanager.LogManager"

JBOSS_MODULES_SYSTEM_PKGS="org.jboss.logmanager"

JAVA_OPTS="$JAVA_OPTS -Djava.util.logging.manager=org.jboss.logmanager.LogManager"
JAVA_OPTS="$JAVA_OPTS -Xbootclasspath/p:$JBOSS_HOME/modules/system/layers/base/org/jboss/logmanager/main/jboss-logmanager-1.5.4.Final-redhat-1.jar"

-Djava.util.logging.manager=org.jboss.logmanager.LogManager

if [ "x$JBOSS_MODULES_SYSTEM_PKGS" = "x" ]; then
    JBOSS_MODULES_SYSTEM_PKGS="org.jboss.byteman,org.jboss.logmanager"
fi


INSTALACIÓN JBOSS EAP 6
Aquí les presentamos un tutorial de como instalar JBoss EAP 6.

Vamos primero con una pequeña historia de donde nace JBoss, JBoss es un producto comprado por Red Hat empresa la cual es sponsor de algunos proyectos open source, uno de ellos es JBoss que es un Servidor de Aplicaciones para aplicativos Java, JBoss esta dentro de las 3 mejores marcas de Servidores de Aplicaciones junto a Oracle con Weblogic e IBM con Websphere, a diferencia de los otros productos JBoss es un producto open source que puede ser descargado por cualquier persona a tra ves de la siguiente dirección http://jboss.org, este software puede ser instalado y usado por cualquiera, el incoveniennte esta en que si algo falla, se depende bastante de las respuesta de los foros de la comunidad, es aqui donde Red Hat entra y nos brinda una versión empresarial la cual tiene el respaldo de Red Hat dando a las personas que adquieran este soporte una seguridad de que los problemas que puedan surgir son solucionados a traves de el Soporte de Red Hat.

Bueno dejando de lado el comercial, comencemos con la instalación.


Descargamos JBoss de la pagina http://www.jboss.org/jbossas/downloads
En cuanto tengamos el paquete descargado, que en este caso en un archivo .zip, lo que necesitamos es descomprimir este paquete en alguna ruta de nuestro sistema de archivos, a traves del siguiente comando.
               [user@host~]$ unzip jboss-eap-6.1.zip -d ruta/para/descomprimir

Crear una variable de entorno para JBoss, esto lo hacemos para el caso de linux editando el archivo .bashrc el cual se encuentra dentro del HOME del usuario con el cual se ejecutara  JBoss añadiendo las siguientes lineas. (En este caso se instalo sobre el home del usuario jboss), esta variable de entorno es ocupada por los scripts de ejecucion de JBoss.
               JBOSS_HOME=/home/jboss/jboss-eap-6.1
               export JBOSS_HOME 

Añadir un usuario de administración para JBoss, lo haremos ejecutando las siguiente instrucciones
               [user@host~]$ cd $JBOSS_HOME/bin
               [user@host~]$ ./add-user.sh

Elegir la opción de crear un usuario de Administración.
                                       What type of user do you wish to add?

                                     a) Management User (mgmt-users.properties)

                                     b) Application User (application-users.properties)

                                    (a): a

Llenar los datos del usuario a crear como se indica a continuación.
                                  Enter the details of the new user to add.

                                 Realm (ManagementRealm) :

                                 Username : admin

                                Password : jboss

                               Re-enter Password : jboss

                              About to add user ‘admin’ for realm ‘ManagementRealm’

                             Is this correct yes/no? yes

Responder a las preguntas de confirmación de manera afirmativa con “yes”.
                            Added user ‘admin’ to file ‘/home/jboss/jboss-eap-6.1/standalone/configuration/mgmt- users.properties’

                            Added user ‘admin’ to file ‘/home/jboss/jboss-eap-6.1/domain/configuration/mgmt- users.properties’

                            Is this new user going to be used for one AS process to connect to another AS process?

                            e.g. for a slave host controller connecting to the master or for a Remoting connection for server to server EJB calls. yes/no? yes

La ejecución de este script nos arrojara una cadena secreta que sera utilizada para realizar la conexión de las maquinas host controller hacia el domain controller .
                             To represent the user add the following to the server-identities definition <secret value=”czNyZjFuYzByIw==” />

Por ultimo hay que probar nuestra instalacion ejcutando JBoss, lo haremos de la siguiente forma en una terminal ejecutamos los siguiente comandos.
               [user@host~]$ cd $JBOSS_HOME/bin
               [user@host~]$ ./standalone.sh

Si la ejecución fue correcta tan solo necesitamos verificar en un navegador web entrar a la siguiente url http://localhost:9990, y nos pedira el usuario y contraseña del usuario que creamos anteriormente.
Aqui terminamos la instalación de JBoss, esperando como siempre les sirva de ayuda, continuaremos con mas articulos de JBoss comentando y detallando cada una de las ventajas que este nos brinda.



Como preparar servidor de aplicaciones Jboss EAP 6.1 con RHEL 6.4
Por dream1980
En este post trataremos la forma de instalar un servidor de aplicaciones Jboss 6.1 de acuerdo a criterios personales, el HD se manejara de 80GB con LMV; como primer punto lo importante son los FS con los que se deben de contar en el SO a continuación se muestran los que utilizaremos:

File System	Tamaño
/	5,0G
/boot	250M
/home	5,0G
/jboss_app_logs	25G
/opt	10,0G
/tmp	5,0G
/usr	5,0G
/var	5,0G
Estos son los FS que utilizaremos para nuestro servidor con Jboss Middleware.

Creamos el usuario jboss que sere el propietario de la carpeta que se genere al descomprimir el .zip:

[root@toliuapcomcair5 jboss]#groupadd -g 550 jboss; useradd -c “Usuario Jboss” -g 550 -u 550 -s /bin/bash jboss; echo ‘XyzXyzXyzXyz’ | passwd –stdin jboss
A nivel sistema operativo agregamos los siguientes parametros:

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
Deshabilitamos los procesos innecesarios en el sistemaoperativo como son: rpcbind, cups, postfix, rpcidmapd, rpcsvcgssd, autofs, nfslock; talvez algunos otros ya depende de cada uno de nosotros:

[root@toliuapcomcair5 jboss]#for i in rpcbind cups postfix rpcidmapd rpcsvcgssd autofs nfslock ;do chkconfig $i off;done
[root@toliuapcomcair5 jboss]#for i in rpcbind cups postfix rpcidmapd rpcsvcgssd autofs nfslock xinetd;do service $i stop;done
Utilizaremos el FS /opt para descomprimir el archivo .zip que contiene nuestro jboss, por lo que copiaremos el archivo .zip a dicha partición:

[root@toliuapcomcair5 opt]# cd /opt/
[root@toliuapcomcair5 opt]# cp -r /home/iu210545/jboss-eap-6.1.0.zip .
Descomprimimos el archivo zip, el cual generara una carpeta con el nombre jboss-eap-6.1

[root@toliuapcomcair5 opt]# unzip jboss-eap-6.1.0.zip
Otorgamos los permisos correspondientes a la carpeta jboss-eap-6.1

[jboss@toliuapcomcair5 ~]$ cd /opt/jboss-eap-6.1/
[root@toliuapcomcair5 jboss]# chown -R jboss:jboss jboss-eap-6.1
Una vez realizado los pasos anteriores procedemos a configurar jboss-EAP-6.1; en este post trataremos una configuración de jboss-EAP-6.1 en modo standalone; por lo que generaremos el usuario para la consola administrativa de jboss y copiaremos la carpeta standalone de la ruta: /opt/jboss-eap-6.1 para generar nuestra instancia llamada myinstancia.

Generamos el usuario de la consola administrativa:

[jboss@toliuapcomcair5 bin]$ ./add-user.sh
Este usuario y su password cifrado se almacena en la ruta /opt/jboss-eap-6.1/standalone/configuration/mgmt-users.properties.
Generamos una copia de la carpeta standalone y la llamaremos myinstance.

[jboss@toliuapcomcair5 ~]$ cd /opt/jboss-eap-6.1/
[jboss@toliuapcomcair5 jboss-eap-6.1]$ cp -r standalone myinstancia
Apartir de esta instancia generada configuraremos los parametros de ip,puerto, para lo cual editaremos en archivo standalone.xml de la ruta siguiente: /opt/jboss-eap-6.1/myinstance/configuration/standalone.xml
[jboss@toliuapcomcair5 bin]$ vi /opt/jboss-eap-6.1/myinstance/configuration/standalone.xml
Nota: En este archivo .xml podemos definir la ip del servidor, asi como system properties, datasources,puertos,etc.
Asignaremos la ip en los siguientes tags:, <inet-address,

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

Validacion-jboss-2

Voila!!!, todo funcionando correctamente, espero les sirva este post.


