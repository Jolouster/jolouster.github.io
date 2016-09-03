---
layout: post
title: Cambiar el directorio web en el servidor apache.
meta: El servidor web Apache permite configurar nuestro directorio de trabajo para el desarrollo web en local. Aquí se explica cómo hacerlo.
author: Jonathan López
category: Tutoriales
---

Cambiar el directorio raiz donde se almacenan nuestras aplicaciones web en el servidor apache. Para colocar nuestros desarrollos de aplicaciones web donde queramos (normalmente dentro del directorio del usuario).

Simplemente hay que editar el archivo `/etc/apache2/apache2.conf` y cambiar las siguientes líneas para dejarlas como las que se muestra a continuación:

	# <Directory /var/www/>
	<Directory /home/jonathan/www/>
		Options Indexes FollowSymLinks
		AllowOverride None
		Allow from all
		Require all granted
	</Directory>

La primera línea: `<Directory /var/www/>` es la que estaba originalmente. Simplemente se ha comentado para recordar cual era la original. Las otras líneas se han editado para permitir el acceso y la edición de los archivos que guardemos en estos directorios por parte del usurario normal (no root).