---
layout: post
title: Crear un repositorio GIT en Dropbox y compartirlo con otros.
meta: Un repositorio GIT en Dropbox te permite trabajar con otros en tus proyectos. Ideal para programadores.
author: Jonathan López
category: Tutoriales
---

Se supone que ya tenemos instalado Git y una cuenta creada en Dropbox.

En la carpeta *local* donde tengamos nuestro proyecto, o dónde lo vayamos a colocar, creamos el repositorio local.

	git init

Se añaden los archivos que toquen ('*' para indicar "todo lo que haya en este directorio").

	git add *

Se realiza un commit.

	git commit -m "primer commit"

Ahora creamos el repositorio remoto en Dropbox. 

Vamos a nuestro Dropbox y creamos una carpeta dónde vayamos a guardar el repositorio

	mkdir ~/Dropbox/miproyecto
	git init --bare nombredelproyecto.git

Solo que sincronizar ambos repositorios.

Vamos a nuestro repositorio *local* y añadimos el repositorio *remoto* que acabamos de crear.

	git remote add origin ~/Dropbox/misproyectos/nombredelproyecto.git

Subimos nuestro repositorio local al remoto.

	git push -u origin master

Para que otra persona pueda trabajar con ese proyecto se debe compartir la carpeta del proyecto que tenemos en Dropbox (`~/Dropbox/miproyecto`). 

La otra persona deberá descargarse una copia del repositorio:

	git clone ~/Dropbox/misproyectos/nombredelproyecto.git /path_de_destino/dir

Cuando haga cualquier cambio, tras un comit, puede volver a subirlo al Dropbox para que se sincronice.

	git push origin master

"master" si quiere integrar los cambios en la rama principal. 

Para sincronizar el repositorio local con los cambios que hayan podido realizar las otras personas con las que compartimos el repositorio:

	git pull origin 

También es posible crear ramas secundarias del proyecto y después integrarlas en la principal. Se puede trabajar con este repositorio como en cualquier otro.
