---
layout: post
title: Crear un host virtual para desarrollo web
author: Jonathan López
---

## ¿Qué es un _host virtual_?

El *host* de un sitio web es el lugar donde este se aloja. Se accede a este mediante una IP, aunque se suele usar más frecuentemente un "dominio" que apunta a esa dirección IP y es mucho más fácil de leer y recordar por los seres humanos.

Pero... ¿una IP para un solo sitio web? No es práctico. Menos aun si desarrollamos varias aplicaciones web en nuestro propio ordenador.

La solución es crear *host virtuales*, que nos permiten almacenar cada sitio o aplicación web en un directorio diferente y acceder a ellos con un dominio diferente.

## ¿Cómo se hace?

**Primer paso.** Hay que decirle al servidor Apache que se existe un nuevo *host*. Para ello:

	sudo cp /etc/apache2/sites-available/default /etc/apache2/sites-available/misitio.dev

**NOTA:** La extensión "`.dev`" es un ejemplo. Te la puedes inventar, pero mejor no usar ".com", ".es", ".org" u otras que ya existan. Puedes tener una versión en producción en un servidor y otra de desarrollo en tu ordenador. Si usas la misma extensión en tu dominio, el navegador no sabrá a cual de las dos ir. Podría ser una fuente de problemas y a veces cuesta darse cuenta de este tipo de errores tontos. 

Puedes sustituir `misitio` por el nombre que tu quieras.

**Segundo paso.** Configuramos el nuevo host virtual.

	sudo gedit /etc/apache2/sites-available/misitio.dev

Recueda cambiar `misitio.dev` por el nombre que hayas puesto tu.

Tendremos un contenido parecido a esto, configurado a nuestras necesidades:

	<VirtualHost *:80>
		# The ServerName directive sets the request scheme, hostname and port that
		# the server uses to identify itself. This is used when creating
		# redirection URLs. In the context of virtual hosts, the ServerName
		# specifies what hostname must appear in the request's Host: header to
		# match this virtual host. For the default virtual host (this file) this
		# value is not decisive as it is used as a last resort host regardless.
		# However, you must set it for any further virtual host explicitly.
		#ServerName www.example.com

		ServerAdmin webmaster@misitio.dev

		## [1]-- DOMINIO QUE NOS INVENTAMOS Y QUEREMOS USAR EN NUESTRO HOST DE DESARROLLO --
		ServerName www.misitio.dev
		ServerAlias misitio.dev

		## [2]-- DIRECTORIO DONDE GUARDAMOS LOS ARCHIVOS DE NUESTRO SITIO/APLICACIÓN WEB --
		DocumentRoot /home/minombredeusuario/www/misitio.dev/

		## [3]-- CONFIGURACIÓN DEL HOST VIRTUAL DONDE ELEGIMOS QUIÉN PUEDE ACCEDER Y QUÉ TIPO DE ARCHIVOS SE USARÁN EN EL PROYECTO --
		<Directory /home/minombredeusuario/www/misitio.dev/>
			Options FollowSymLinks IncludesNOEXEC
			AllowOverride None
			Order allow,deny
			Allow from all
			AddType application/x-http-php .php
			AddType application/xml .xml
		</Directory>

		# Available loglevels: trace8, ..., trace1, debug, info, notice, warn,
		# error, crit, alert, emerg.
		# It is also possible to configure the loglevel for particular
		# modules, e.g.
		#LogLevel info ssl:warn

		ErrorLog ${APACHE_LOG_DIR}/error.log
		CustomLog ${APACHE_LOG_DIR}/access.log combined

		# For most configuration files from conf-available/, which are
		# enabled or disabled at a global level, it is possible to
		# include a line for only one particular virtual host. For example the
		# following line enables the CGI configuration for this host only
		# after it has been globally disabled with "a2disconf".
		#Include conf-available/serve-cgi-bin.conf
	</VirtualHost>

Solo se modifica las líneas que siguen a los comentarios `[1]`, `[2]` y `[3]`. El resto se deja igual.

Se podría investigar más el significado y posibles valores del resto de parámetros y adaptar el host lo máximo posible a nuestras necesidades. Lo ideal es que sea lo más parecido posible al host de producción en el que se ejecutará la aplicación web cuando esté terminada.

**Tercer paso.** Activar el host en apache. Simplemente hay que ejecutar:

	sudo a2ensite misitio.dev

**Cuarto paso.** Crear el dominio. Para que el navegador sepa que existe y pueda encontrarlo.

Hay que editar el archivo `/etc/hosts`. Se añade una *nueva* línea:

	127.0.0.1 	misitio.dev

**Ultimo paso.** Reiniciar el servidor web (Apache).

	sudo service apache2 reload

Ahora ya solo queda crear tu sitio web y guardarlo en la misma ruta que hayas indicado tras el comentario `[2]` del archivo `/etc/apache2/sites-available/misitio.dev`.

Esto es todo. Puedes crear tantos *Virtual Host* como quieras. Uno por cada proyecto que hagas.
