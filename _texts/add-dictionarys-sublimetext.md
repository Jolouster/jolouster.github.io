---
layout: post
title: Añadir diccionarios de diferentes idiomas a Sublime Text
author: Jonathan López
source: http://apuntes-snicoper.readthedocs.io/es/latest/editors/sublime_text/sublime_text_3.html
---

# AÑADIR DICCIONARIOS DE IDIOMA A SUBLIME TEXT

Solo hay que descargar los diccionarios de los idiomas que queramos y copiarlos en el directorio adecuado. Lo podemos hacer con los siguientes comandos:

	cd ~/Downloads
	git clone git://github.com/SublimeText/Dictionaries.git Dictionaries

Nos descarga a Downloads/Dictionaries unos cuantos diccionarios en varios idiomas. Ahora toca copiar los diccionarios que queramos a la carpeta adecuada.

	mkdir -p ~/.config/sublime-text-3/Packages/Language

	cp Dictionaries/Spanish.* ~/.config/sublime-text-3/Packages/Language

Podemos añadir más idiomas si queremos repitiendo el ultimo comando.

En ST, en el menú `View -> Dictionary`, seleccionamos el idioma y luego para resaltar los errores de idioma le damos a `F6`.
