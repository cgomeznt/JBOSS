Command Line Interface
==========================

The Command Line Interface (CLI) es la herramienta administrativoa para administrar domain o standalone servers y ejecutar operaciones administrativas 

localhost:9990 es por defecto la IP y puerto para WildFly CLI client.::

	./bin/jboss-cli.sh
	You are disconnected at the moment. Type 'connect' to connect to the server
	or 'help' for the list of supported commands.
	[disconnected /]
	 
	[disconnected /] connect
	[domain@localhost:9990 /]
	 
	[domain@localhost:9990 /] quit
	Closed connection to localhost:9990

Sino podemos suministrar a donde nos queremos conectar::

	./bin/jboss-cli.sh
	You are disconnected at the moment. Type 'connect' to connect to the server
	[disconnected /] connect 192.168.0.10:9990
	Connected to standalone controller at 192.168.0.1:9990
