Crear un Java KeyStore File
===============================

En este laboratorio se estara creando un archivo Java KeyStore el cuan tendrá la llave del certificado del servidor, un certificado del servidor y el certificado publico de la CA que firmo el certificado del servidor.

Luego que se tenga el archivo Java KeyStore se va configurar el JBoss standalone para que utilice dicho Java KeyStore

Crear un Java KeyStore File y firmarlo con una CA
++++++++++++++++++++++++++++

Información extraída de https://www.digicert.com/kb/csr-ssl-installation/tomcat-keytool.htm

Crear un nuevo keystore::

	keytool -genkey -alias srvutils -keyalg RSA -keysize 2048 -storepass  changeit -keystore keystore.jks

	¿Cuáles son su nombre y su apellido?
	  [Unknown]:  srvutils.local
	¿Cuál es el nombre de su unidad de organización?
	  [Unknown]:  Sop App
	¿Cuál es el nombre de su organización?
	  [Unknown]:  LDT Private
	¿Cuál es el nombre de su ciudad o localidad?
	  [Unknown]:  DC
	¿Cuál es el nombre de su estado o provincia?
	  [Unknown]:  CCS
	¿Cuál es el código de país de dos letras de la unidad?
	  [Unknown]:  VE
	¿Es correcto CN=srvutils.local, OU=Sop App, O=LDT Private, L=DC, ST=CCS, C=VE?
	  [no]:  si

	Introduzca la contraseña de clave para <srvutils>
		(INTRO si es la misma contraseña que la del almacén de claves):  changeit
	Volver a escribir la contraseña nueva: changeit



Consultamos el archivo Java KeyStore::

	keytool -list -v -keystore ./keystore.jks -storepass  changeit

	Tipo de Almacén de Claves: JKS
	Proveedor de Almacén de Claves: SUN

	Su almacén de claves contiene 1 entrada

	Nombre de Alias: srvutils
	Fecha de Creación: 24/08/2021
	Tipo de Entrada: PrivateKeyEntry
	Longitud de la Cadena de Certificado: 1
	Certificado[1]:
	Propietario: CN=srvutils.local, OU=Sop App, O=LDT Private, L=DC, ST=CCS, C=VE
	Emisor: CN=srvutils.local, OU=Sop App, O=LDT Private, L=DC, ST=CCS, C=VE
	Número de serie: 2dfbb407
	Válido desde: Tue Aug 24 13:12:54 EDT 2021 hasta: Mon Nov 22 12:12:54 EST 2021
	Huellas digitales del Certificado:
		 MD5: 92:75:FB:0F:B4:C3:39:E7:95:35:90:97:7B:B3:82:21
		 SHA1: 06:39:AA:FF:E8:C9:4B:88:F5:59:39:2A:16:B1:6B:B8:6A:92:E4:45
		 SHA256: B7:E4:A7:28:B2:B5:63:5B:A7:E7:4E:DD:69:B6:7B:5C:35:1A:67:03:5C:DA:69:AA:3C:EC:6B:C9:0A:EE:9F:D9
		 Nombre del Algoritmo de Firma: SHA256withRSA
		 Versión: 3

	Extensiones: 

	#1: ObjectId: 2.5.29.14 Criticality=false
	SubjectKeyIdentifier [
	KeyIdentifier [
	0000: 27 A3 43 D2 CE 64 A6 DD   45 C7 C6 02 D3 AA C3 26  '.C..d..E......&
	0010: E1 F9 F1 70                                        ...p
	]
	]



	*******************************************
	*******************************************



Crear el request del certificado desde el nuevo keystore::

	keytool -certreq -alias srvutils -file srvutils.csr -storepass changeit -keystore keystore.jks


El archivo request creado CSR **srvutils.csr** deberá ser enviado a la CA que lo firmara y deberá espera que le suministren el certificado firmado y también el certificado publico de la CA. Ver el siguiente link de como crear su propia CA https://github.com/cgomeznt/Certificados/blob/master/guia/cacentos7.rst

::

	openssl x509 -req -days 185 -extfile conf/srvutils.conf -extensions v3_req -CA certs/CA_empresa.crt -CAkey private/CA_empresa.key -CAserial ca.srl -CAcreateserial -in request/srvutils.csr -out newcerts/srvutils.crt

	Signature ok
	subject=/C=VE/ST=CCS/L=DC/O=LTD Private/OU=Sop Middleware/CN=Carlos Gomez
	Getting CA Private Key
	Enter pass phrase for private/CA_empresa.key:Venezuela21

Lo anterior se ejecuta en un servidor que tengo configurado la Entidad Certificadora y va generar el archivo (SSL CRT) del certificado y ese lo debemos esperar nos entreguen al igual que el (CRT de la CA), para que puedan continuar con el proceso.

La siguiente Información es extraída de https://knowledge.digicert.com/quovadis/ssl-certificates/ssl-installation/how-do-i-install-an-ssl-certificate-into-jboss.html

Una vez que haya obtenido todos los archivos que necesitará durante este proceso (SSL CRT y CRT de la CA), estará listo para instalarlos en el archivo Java KeyStore que creó inicialmente junto con el CSR. Es importante recordar que en el archivo Java KeyStore ya esta el llave KEY del certificado.

Importar el certificado de la CA dentro del archivo Java KeyStore::

	keytool -import -trustcacerts -alias root -file CA_empresa.crt -keystore keystore.jks

	Introduzca la contraseña del almacén de claves:  changeit
	Propietario: EMAILADDRESS=root@personal.local, CN=PERSONAL, OU=Sop App, O=Default Company Ltd, L=CCS, ST=DC, C=VE
	Emisor: EMAILADDRESS=root@personal.local, CN=PERSONAL, OU=Sop App, O=Default Company Ltd, L=CCS, ST=DC, C=VE
	Número de serie: ddff243bcbceacc1
	Válido desde: Mon Aug 23 15:06:20 EDT 2021 hasta: Thu Aug 21 15:06:20 EDT 2031
	Huellas digitales del Certificado:
		 MD5: 04:97:A4:4A:90:BB:F1:14:DE:FD:BE:36:15:59:4B:12
		 SHA1: 79:99:36:30:82:93:04:A0:DA:C4:E6:C3:F3:A5:63:84:57:A4:AF:CF
		 SHA256: 3E:D6:5B:0A:8D:FA:F5:70:CB:D1:DB:65:24:1D:E5:4A:A1:E1:F4:71:C8:18:BA:22:2C:CF:C7:AA:64:ED:50:67
		 Nombre del Algoritmo de Firma: SHA256withRSA
		 Versión: 3

	Extensiones: 

	#1: ObjectId: 2.5.29.35 Criticality=false
	AuthorityKeyIdentifier [
	KeyIdentifier [
	0000: 8D 43 A0 20 E3 1C EB F6   C5 F7 E6 1D DB D2 8E 61  .C. ...........a
	0010: F7 B6 AA 84                                        ....
	]
	]

	#2: ObjectId: 2.5.29.19 Criticality=false
	BasicConstraints:[
	  CA:true
	  PathLen:2147483647
	]

	#3: ObjectId: 2.5.29.14 Criticality=false
	SubjectKeyIdentifier [
	KeyIdentifier [
	0000: 8D 43 A0 20 E3 1C EB F6   C5 F7 E6 1D DB D2 8E 61  .C. ...........a
	0010: F7 B6 AA 84                                        ....
	]
	]

	¿Confiar en este certificado? [no]:  si
	Se ha agregado el certificado al almacén de claves


Consultamos el archivo Java KeyStore y debemos observar dos (2) entradas, la llave y ahora el CRT de la CA::

	keytool -list -v -keystore ./keystore.jks -storepass  changeit

Importar el certificado SSL dentro del archivo Java KeyStore::

	keytool -import -trustcacerts -alias servercrt -file srvutils.crt -keystore keystore.jks
	Introduzca la contraseña del almacén de claves:  
	Se ha agregado el certificado al almacén de claves

Consultamos el archivo Java KeyStore y debemos observar tres (3) entradas, la llave, el CRT de la CA y el CRT del certificado::

	keytool -list -v -keystore ./keystore.jks -storepass  changeit

	Tipo de Almacén de Claves: JKS
	Proveedor de Almacén de Claves: SUN

	Su almacén de claves contiene 3 entradas

	Nombre de Alias: srvutils
	Fecha de Creación: 24/08/2021
	Tipo de Entrada: PrivateKeyEntry
	Longitud de la Cadena de Certificado: 1
	Certificado[1]:
	Propietario: CN=srvutils.local, OU=Sop App, O=LDT Private, L=DC, ST=CCS, C=VE
	Emisor: CN=srvutils.local, OU=Sop App, O=LDT Private, L=DC, ST=CCS, C=VE
	Número de serie: 2dfbb407
	Válido desde: Tue Aug 24 13:12:54 EDT 2021 hasta: Mon Nov 22 12:12:54 EST 2021
	Huellas digitales del Certificado:
		 MD5: 92:75:FB:0F:B4:C3:39:E7:95:35:90:97:7B:B3:82:21
		 SHA1: 06:39:AA:FF:E8:C9:4B:88:F5:59:39:2A:16:B1:6B:B8:6A:92:E4:45
		 SHA256: B7:E4:A7:28:B2:B5:63:5B:A7:E7:4E:DD:69:B6:7B:5C:35:1A:67:03:5C:DA:69:AA:3C:EC:6B:C9:0A:EE:9F:D9
		 Nombre del Algoritmo de Firma: SHA256withRSA
		 Versión: 3

	Extensiones: 

	#1: ObjectId: 2.5.29.14 Criticality=false
	SubjectKeyIdentifier [
	KeyIdentifier [
	0000: 27 A3 43 D2 CE 64 A6 DD   45 C7 C6 02 D3 AA C3 26  '.C..d..E......&
	0010: E1 F9 F1 70                                        ...p
	]
	]



	*******************************************
	*******************************************


	Nombre de Alias: root
	Fecha de Creación: 24/08/2021
	Tipo de Entrada: trustedCertEntry

	Propietario: EMAILADDRESS=root@personal.local, CN=PERSONAL, OU=Sop App, O=Default Company Ltd, L=CCS, ST=DC, C=VE
	Emisor: EMAILADDRESS=root@personal.local, CN=PERSONAL, OU=Sop App, O=Default Company Ltd, L=CCS, ST=DC, C=VE
	Número de serie: ddff243bcbceacc1
	Válido desde: Mon Aug 23 15:06:20 EDT 2021 hasta: Thu Aug 21 15:06:20 EDT 2031
	Huellas digitales del Certificado:
		 MD5: 04:97:A4:4A:90:BB:F1:14:DE:FD:BE:36:15:59:4B:12
		 SHA1: 79:99:36:30:82:93:04:A0:DA:C4:E6:C3:F3:A5:63:84:57:A4:AF:CF
		 SHA256: 3E:D6:5B:0A:8D:FA:F5:70:CB:D1:DB:65:24:1D:E5:4A:A1:E1:F4:71:C8:18:BA:22:2C:CF:C7:AA:64:ED:50:67
		 Nombre del Algoritmo de Firma: SHA256withRSA
		 Versión: 3

	Extensiones: 

	#1: ObjectId: 2.5.29.35 Criticality=false
	AuthorityKeyIdentifier [
	KeyIdentifier [
	0000: 8D 43 A0 20 E3 1C EB F6   C5 F7 E6 1D DB D2 8E 61  .C. ...........a
	0010: F7 B6 AA 84                                        ....
	]
	]

	#2: ObjectId: 2.5.29.19 Criticality=false
	BasicConstraints:[
	  CA:true
	  PathLen:2147483647
	]

	#3: ObjectId: 2.5.29.14 Criticality=false
	SubjectKeyIdentifier [
	KeyIdentifier [
	0000: 8D 43 A0 20 E3 1C EB F6   C5 F7 E6 1D DB D2 8E 61  .C. ...........a
	0010: F7 B6 AA 84                                        ....
	]
	]



	*******************************************
	*******************************************


	Nombre de Alias: servercrt
	Fecha de Creación: 24/08/2021
	Tipo de Entrada: trustedCertEntry

	Propietario: CN=srvutils.local, OU=Sop App, O=LDT Private, L=DC, ST=CCS, C=VE
	Emisor: EMAILADDRESS=root@personal.local, CN=PERSONAL, OU=Sop App, O=Default Company Ltd, L=CCS, ST=DC, C=VE
	Número de serie: b539e7db965032df
	Válido desde: Tue Aug 24 13:17:50 EDT 2021 hasta: Fri Feb 25 12:17:50 EST 2022
	Huellas digitales del Certificado:
		 MD5: 6C:0F:79:22:B2:44:4B:FD:EE:D1:47:2D:70:30:96:BD
		 SHA1: 54:8D:B8:D2:52:88:77:BA:0E:C0:5D:FA:40:2A:1E:ED:FA:15:B9:4B
		 SHA256: 2A:6C:59:14:EB:BD:40:1F:E6:26:23:EC:A5:E8:AC:E3:3E:0F:A4:0B:F3:33:77:EC:BD:22:B6:28:85:BD:37:4F
		 Nombre del Algoritmo de Firma: SHA256withRSA
		 Versión: 3

	Extensiones: 

	#1: ObjectId: 2.5.29.19 Criticality=false
	BasicConstraints:[
	  CA:false
	  PathLen: undefined
	]

	#2: ObjectId: 2.5.29.15 Criticality=false
	KeyUsage [
	  DigitalSignature
	  Non_repudiation
	  Key_Encipherment
	]

	#3: ObjectId: 2.5.29.17 Criticality=false
	SubjectAlternativeName [
	  DNSName: srvscmutils.EMPRESA.local
	  DNSName: monitoreo.EMPRESA.local
	  IPAddress: 192.168.0.20
	]



	*******************************************
	*******************************************


Ya con esto tenemos nuestro archivo Java KeyStore listo....!!!


Asignar el nuevo Java KeyStore en JBOSS
++++++++++++++++++++++++++++++++++++++++

Esto aplica par JBoss 7.x standalone. ir a la ruta del root standalone::

	cd /opt/jboss-eap-7.4/standalone/configuration/

Crear un respaldo del archivo standalone.xml::

	cp  standalone.xml standalone.xml.original

Editar el archivo standalone.xml y colocar la siguiente información dentro del TAG  <security-realms>::


	<security-realms>
	    <security-realm name="CertificateRealm">
		<server-identities>
		    <ssl>
		        <keystore path="keystore.jks" relative-to="jboss.server.config.dir" keystore-password="changeit"/>
		    </ssl>
		</server-identities>
		<authentication>
		    <truststore path="truststore.jks" relative-to="jboss.server.config.dir" keystore-password="changeit"/>
		</authentication>
	    </security-realm>
	</security-realms>

En el mismo archivo standalone.xml buscar el TAG de <subsystem xmlns="urn:jboss:domain:undertow> buscar esta linea::

	<https-listener name="https" socket-binding="https" security-realm="ApplicationRealm" enable-http2="true"/>

y remplazarla por esta otra::

	<https-listener name="https" secure="true" enabled-protocols="TLSv1.1,TLSv1.2" security-realm="CertificateRealm" socket-binding="https"/>

Entonces quedaría algo así::

	<subsystem xmlns="urn:jboss:domain:undertow:3.1">
	    <buffer-cache name="default"/>
	    <server name="default-server">
		<http-listener name="default" socket-binding="http" redirect-socket="https"/>
		<https-listener name="https" secure="true" enabled-protocols="TLSv1.1,TLSv1.2" security-realm="CertificateRealm" socket-binding="https"/>


listo ya con esto tenemos configurado JBoss EAP 7.x para que utilice certificados, solo resta realizar los Troubleshooting

En un navegador abrir y colocar la siguiente URL https://192.168.1.20:8443 y podremos ver que levanta con el certificado, también podemos hacer la consulta con openssl de la siguiente forma::

	openssl s_client -connect 192.168.1.20:8443



