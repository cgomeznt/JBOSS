Crear un Java KeyStore File
===============================

Información extraída de https://www.digicert.com/kb/csr-ssl-installation/tomcat-keytool.htm

Crear un nuevo keystore::

	# keytool -genkey -alias srvutils -keyalg RSA -keysize 2048 -storepass  changeitore.jksore keyst 
	¿Cuáles son su nombre y su apellido?
	  [Unknown]:  Carlos Gomez
	¿Cuál es el nombre de su unidad de organización?
	  [Unknown]:  Sop Middleware
	¿Cuál es el nombre de su organización?
	  [Unknown]:  LTD Private
	¿Cuál es el nombre de su ciudad o localidad?
	  [Unknown]:  DC
	¿Cuál es el nombre de su estado o provincia?
	  [Unknown]:  CCS
	¿Cuál es el código de país de dos letras de la unidad?
	  [Unknown]:  VE
	¿Es correcto CN=Carlos Gomez, OU=Sop Middleware, O=LTD Private, L=DC, ST=CCS, C=VE?
	  [no]:  si


	Introduzca la contraseña de clave para <srvutils>
		(INTRO si es la misma contraseña que la del almacén de claves):  changeit
	Volver a escribir la contraseña nueva: changeit


Consultamos el archivo Java KeyStore

	# keytool -list -v -keystore ./keystore.jks -storepass  changeit

	Tipo de Almacén de Claves: JKS
	Proveedor de Almacén de Claves: SUN

	Su almacén de claves contiene 1 entrada

	Nombre de Alias: srvutils
	Fecha de Creación: 23/08/2021
	Tipo de Entrada: PrivateKeyEntry
	Longitud de la Cadena de Certificado: 1
	Certificado[1]:
	Propietario: CN=Carlos Gomez, OU=Sop Middleware, O=LTD Private, L=DC, ST=CCS, C=VE
	Emisor: CN=Carlos Gomez, OU=Sop Middleware, O=LTD Private, L=DC, ST=CCS, C=VE
	Número de serie: 47ea8d2d
	Válido desde: Mon Aug 23 17:41:56 EDT 2021 hasta: Sun Nov 21 16:41:56 EST 2021
	Huellas digitales del Certificado:
		 MD5: C5:12:CF:09:4C:EC:D9:AA:1B:FC:68:5B:56:60:25:9D
		 SHA1: 3B:89:ED:23:87:A4:31:64:D4:7C:05:BF:BB:F3:7C:D1:B1:15:2D:E9
		 SHA256: 07:17:78:3C:98:02:4D:0F:61:A0:EC:D7:20:5B:2B:4B:E0:5E:5D:4F:97:2C:30:EA:66:70:6E:FB:79:11:36:68
		 Nombre del Algoritmo de Firma: SHA256withRSA
		 Versión: 3

	Extensiones: 

	#1: ObjectId: 2.5.29.14 Criticality=false
	SubjectKeyIdentifier [
	KeyIdentifier [
	0000: EC 2F B9 B0 61 E7 EF 2C   1A 56 50 E6 8C 95 7D 7D  ./..a..,.VP.....
	0010: C4 2B D6 5F                                        .+._
	]
	]



	*******************************************
	*******************************************


Crear el request del certificado desde el nuevo keystore::

	keytool -certreq -alias srvutils -file srvutils.csr -storepass changeit -keystore keystore.jks


El archivo request creado CSR **srvutils.csr** deberá ser enviado a la CA que lo firmara y deberá espera que le suministren el certificado firmado y también el certificado publico de la CA. Ver el siguiente link de como crear su propia CA https://github.com/cgomeznt/Certificados/blob/master/guia/cacentos7.rst

::

	# openssl x509 -req -days 185 -extfile conf/srvutils.conf -extensions v3_req -CA certs/CA_empresa.crt -CAkey private/CA_empresa.key -CAserial ca.srl -CAcreateserial -in request/srvutils.csr -out newcerts/srvutils.crt
	Signature ok
	subject=/C=VE/ST=CCS/L=DC/O=LTD Private/OU=Sop Middleware/CN=Carlos Gomez
	Getting CA Private Key
	Enter pass phrase for private/CA_empresa.key:Venezuela21

Lo anterior va generar el archivo (SSL CRT) del certificado y ese lo debemos enviar al igual que el (CRT de la CA), para que puedan continuar con el proceso.

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
	Fecha de Creación: 23/08/2021
	Tipo de Entrada: PrivateKeyEntry
	Longitud de la Cadena de Certificado: 1
	Certificado[1]:
	Propietario: CN=Carlos Gomez, OU=Sop Middleware, O=LTD Private, L=DC, ST=CCS, C=VE
	Emisor: CN=Carlos Gomez, OU=Sop Middleware, O=LTD Private, L=DC, ST=CCS, C=VE
	Número de serie: 47ea8d2d
	Válido desde: Mon Aug 23 17:41:56 EDT 2021 hasta: Sun Nov 21 16:41:56 EST 2021
	Huellas digitales del Certificado:
		 MD5: C5:12:CF:09:4C:EC:D9:AA:1B:FC:68:5B:56:60:25:9D
		 SHA1: 3B:89:ED:23:87:A4:31:64:D4:7C:05:BF:BB:F3:7C:D1:B1:15:2D:E9
		 SHA256: 07:17:78:3C:98:02:4D:0F:61:A0:EC:D7:20:5B:2B:4B:E0:5E:5D:4F:97:2C:30:EA:66:70:6E:FB:79:11:36:68
		 Nombre del Algoritmo de Firma: SHA256withRSA
		 Versión: 3

	Extensiones: 

	#1: ObjectId: 2.5.29.14 Criticality=false
	SubjectKeyIdentifier [
	KeyIdentifier [
	0000: EC 2F B9 B0 61 E7 EF 2C   1A 56 50 E6 8C 95 7D 7D  ./..a..,.VP.....
	0010: C4 2B D6 5F                                        .+._
	]
	]



	*******************************************
	*******************************************


	Nombre de Alias: root
	Fecha de Creación: 23/08/2021
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
	Fecha de Creación: 23/08/2021
	Tipo de Entrada: trustedCertEntry

	Propietario: CN=Carlos Gomez, OU=Sop Middleware, O=LTD Private, L=DC, ST=CCS, C=VE
	Emisor: EMAILADDRESS=root@personal.local, CN=PERSONAL, OU=Sop App, O=Default Company Ltd, L=CCS, ST=DC, C=VE
	Número de serie: b539e7db965032de
	Válido desde: Mon Aug 23 17:45:32 EDT 2021 hasta: Thu Feb 24 16:45:32 EST 2022
	Huellas digitales del Certificado:
		 MD5: 9C:26:A9:B5:BC:E2:63:64:C1:28:26:50:E4:00:1E:2D
		 SHA1: D8:8A:A9:90:25:B8:1E:8C:00:96:64:8B:C0:BB:01:B3:7E:1A:55:08
		 SHA256: 04:36:92:C7:3B:85:A0:02:51:0A:63:A2:34:60:DD:EC:BB:25:D3:D7:DE:AF:13:28:81:1F:AF:6E:C4:B8:28:16
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



