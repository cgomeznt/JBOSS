Guía Rápida Jboss para Bx+
=======================



Compilar y Actualizar ambiente a la última revisión
+++++++++++++++++++++++++++++++++++++++++

	$ workspace

	$ cd BXMAS/FE-AEBXMAS-458/

	$ git-all.sh

	$ cd scm/Make_EAR/JBoss7_BXMAS_8080/

	$ ./make.sh 8021


Si solicitan reiniciar el ambiente
++++++++++++++++++++++++++++++++++

	Start Jboss
	+++++++++++++++

		$ /etc/init.d/jboss-eap start BXMAS 8021


	Stop Jboss
	++++++++++++++

		$ /etc/init.d/jboss-eap stop BXMAS 8021




Actualizar el ambiente de forma manualmente 
+++++++++++++++++++++++++++++++++++++++++++

El EAR debe estar en la siguiente ruta: "/opt/jboss/EAR_Jboss/8021/CONSIS.ear"

	$ jboss-deploy.sh BXMAS 8021




