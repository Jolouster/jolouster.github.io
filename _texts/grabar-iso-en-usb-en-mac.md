---
layout: post
title: Grabar una ISO en USB desde la terminal en Mac OSx
author: Jonathan López
source: https://youtu.be/fu2g_E2ZK8A
---


# CÓMO GRABAR UNA IMAGEN ISO EN UN USB DESDE LA TERMINAL EN MAC

Hay que terner ya descargada la imagen ISO que queremos grabar.

Recuerda cambiar 'nombreimagen.iso' por el nombre de tu archivo ISO que tengas en tu ordenador. También has de cambiar el nombre o ruta a la memoria USB a la que tengas tu. 

Vamos, que no copies y pegues tal cual está aquí. Adapta los nombres primero.

* Inserta la memoria USB al ordenador.
* Abre la terminal y ves al directorio donde tengas tu la imagen ISO.
* Vemos la lista de todos las unidades y paticiones que tengamos en nuestra máquina.

		diskutil list

* Buscamos el nombre de nuestra unidad USB y nos quedamos con ese dato para usarlo después. Se encuentra bajo la columna "IDENTIFIER".
* Convertimos la imagen ISO.

		hdiutil convert -format UDRW -o destino.img nombreimagen.iso

* En el directorio actual se habrá creado 'destino.img.dmg'.
* Desmontamos la memoria USB.

		diskutil unmountDisk /dev/disk5

	Recuerda el nombre de tu unidad USB para poner la correcta. 'disk5' es la mía en este caso. En el tuyo puede variar.

* Copiar la imagen del disco que hemos obtenido tal cual a la memoria USB. Se usa el comando 'dd'. Para más información sobre este comando usar `man dd` en la terminal.

		sudo dd if=destino.img.dmg of=/dev/rdisk5 bs=1m

	Recuerda cambiar el numero '5' en 'rdisk' por el que te corresponda a ti.

* Cuando termine, puede tardar, desconecta físicamente la memoria USB y vuelve a conectarla. Podrás ver que se ha grabado la imagen ISO correctamente.

Ya está.