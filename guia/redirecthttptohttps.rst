Redirecting HTTP to HTTPS in JBoss EAP 7
==================================

Información obtenida de https://access.redhat.com/solutions/2901211

Environment
+++++++++++++++

Red Hat JBoss Enterprise Application Platform (EAP)
7.x

Issue
+++++++

Need to always redirect users from HTTP to HTTPS for security.
How to redirect users from HTTP to HTTPS on ports 8080 and 8081?

Resolution
+++++++++++++++

A rewrite rule can be used to redirect users. In the undertow subsystem (standalone*.xml or domain.xml) one will need to create a new rewrite filter and then enable the filter in a new filter-ref. Here are the JBoss CLI commands for the same configuration::

	/subsystem=undertow/configuration=filter/rewrite=http-to-https:add(redirect="true",target="https://myhostname:443%U")
	/subsystem=undertow/server=default-server/host=default-host/filter-ref=http-to-https:add(predicate="equals(%p,8080)")

This is a thoroughly explanation for each command and how it reflects on the Undertow subsystem in the configuration files (standalone*.xml or domain.xml):

1.- The first command creates the new rewrite filter in the filters section. In the example below, users will be redirected to https://myhostname:443/my-app. %U is a place-holder for the original request URL path; the usage of %U is to make the redirect friendly and keep users' original request URL path.::

    <filters>
        ...
        <rewrite name="http-to-https" redirect="true" target="https://myhostname:443%U"/>
    </filters>

2.- The second command, enables the filter and configure a predicate in the host section. The predicate is where to configure what the rewrite filter applies to. In the example below, our rewrite filter will only apply to requests going to port 8080.::

    <server name="default-server">
        <host name="default-host" alias="localhost">
            ...
            <filter-ref name="http-to-https" predicate="equals(%p,8080)"/>

The predicate can be created using attributes and Undertow's built in operators, as well as, Boolean operators. Thus, to redirect from port 8080 and port 8081 the following command can be used::


	/subsystem=undertow/server=default-server/host=default-host/filter-ref=http-to-https:add(predicate="equals(%p,8080) or equals(%p,8081)")

Which is reflected on the configuration files (standalone*.xml or domain.xml) as::


Diagnostic Steps
++++++++++++++

Check if the redirect is working properly by sending request to the plain HTTP port. Check the Location header of the response to confirm the rewrite is working properly::


	# curl -I http://localhost:8080/sample
	HTTP/1.1 302 Found
	Connection: keep-alive
	X-Powered-By: Undertow/1
	Server: JBoss-EAP/7
	Location: https://localhost:8443/sample
	Content-Length: 0
	Date: Thu, 02 Feb 2017 22:56:34 GMT
		<filter-ref name="http-to-https" predicate="equals(%p,8080) or equals(%p,8081)"/> 
