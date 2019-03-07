Datasource para Micosoft SQL Server
=======================================


Debe tener instalado la versión que se requiera de **jboss-eap** para este ejemplo sera con **jboss-eap-7.1.0**


Para Micosoft SQL Server
+++++++++++++++

Para poder configurar un Datasource de Microsoft Sql Server en JBoss, es necesario agregar un módulo de sistema nuevo, que contengan el driver de Sql Server, junto con todas sus dependencias necesarias.


Pasos
+++++++++++

* Descargar el módulo comprimido en formato .zip que se encuentra en la sección Recursos.
* Descomprimir el archivo .zip en el directorio <JBoss>/modules/system/layers/base/com/microsoft.
* Agregar la sección del driver SqlServer en el archivo de configuración standalone.xml, ubicado en <JBoss>/standalone/configuration.
* Esta sección debe estar ubicada dentro de la sección <subsystem xmlns="urn:jboss:domain:datasources:4.0">/<datasources>/<drivers>.
::


    <driver name="MsSqlServer" module="com.microsoft.sqlserver.jdbc">
        <xa-datasource-class>com.microsoft.sqlserver.jdbc.SQLServerDriver</xa-datasource-class>
    </driver>

* Iniciar el servidor JBoss.
* Ingresar en la Consola de Administración de JBoss.
* Ejecutar la opción Add en la sección Configuration -> Subsystems -> Datasources -> Non-XA.

.. figure:: ../images/datasource/17.png

* Seleccionar la opción Microsoft SQLServer Datasource y continuar.


.. figure:: ../images/datasource/18.png

* Ingresar el nombre del datasource, y el Nombre JNDI (se debe dejar el prefijo java:). Luego continuar.


.. figure:: ../images/datasource/19.png

* Seleccionar el Driver JDBC configurado en el archivo standalone.xml. Se debe ingresar en la sección Detected Driver, escoger el nombre del driver configurado, y luego continuar.


.. figure:: ../images/datasource/20.png

* Definir el URL de la conexión, y las credenciales del usuario de BD. Para el caso del url, el prefijo debe quedar jdbc:sqlserver (eliminar la palabra microsoft si está presente). Luego continuar.

.. figure:: ../images/datasource/21.png

* Confirmar la configuración del Datasource, y finalizar.


.. figure:: ../images/datasource/22.png

* Verificar la conexión del Datasource usando la opción Test Connection. ubicada en la caja de opciones del Datasource recien creado.

.. figure:: ../images/datasource/23.png


* Este seria el contenido del standalone.xml::

            <datasources>
                <datasource jndi-name="java:jboss/datasources/ExampleDS" pool-name="ExampleDS" enabled="true" use-java-context="true">
                    <connection-url>jdbc:h2:mem:test;DB_CLOSE_DELAY=-1;DB_CLOSE_ON_EXIT=FALSE</connection-url>
                    <driver>h2</driver>
                    <security>
                        <user-name>sa</user-name>
                        <password>sa</password>
                    </security>
                </datasource>
                <datasource jta="true" jndi-name="java:/jdbc/Datasource-GS" pool-name="jdbc/Datasource-GS" enabled="true" use-ccm="false">
                    <connection-url>jdbc:sqlserver://192.168.0.94:1433;DatabaseName=SRVMSSQL</connection-url>
                    <driver-class>com.microsoft.sqlserver.jdbc.SQLServerDriver</driver-class>
                    <driver>MsSqlServer</driver>
                    <security>
                        <user-name>BXPLUSGU_V138</user-name>
                        <password>BXPLUSGU_V138</password>
                    </security>
                    <validation>
                        <valid-connection-checker class-name="org.jboss.jca.adapters.jdbc.extensions.mssql.MSSQLValidConnectionChecker"/>
                        <background-validation>true</background-validation>
                    </validation>
                </datasource>
                <datasource jta="true" jndi-name="java:/jdbc/Datasource-MX" pool-name="jdbc/Datasource-MX" enabled="true" use-ccm="false">
                    <connection-url>jdbc:sqlserver://192.168.0.94:1433;DatabaseName=SRVMSSQL</connection-url>
                    <driver-class>com.microsoft.sqlserver.jdbc.SQLServerDriver</driver-class>
                    <driver>MsSqlServer</driver>
                    <security>
                        <user-name>BXPLUS_V138</user-name>
                        <password>BXPLUS_V138</password>
                    </security>
                    <validation>
                        <valid-connection-checker class-name="org.jboss.jca.adapters.jdbc.extensions.mssql.MSSQLValidConnectionChecker"/>
                        <background-validation>true</background-validation>
                    </validation>
                </datasource>
                <drivers>
                    <driver name="h2" module="com.h2database.h2">
                        <xa-datasource-class>org.h2.jdbcx.JdbcDataSource</xa-datasource-class>
                    </driver>
                    <driver name="MsSqlServer" module="com.microsoft.sqlserver.jdbc">
                        <xa-datasource-class>com.microsoft.sqlserver.jdbc.SQLServerDriver</xa-datasource-class>
                    </driver>
                </drivers>
            </datasources>

.. figure:: ../images/datasource/24.png
