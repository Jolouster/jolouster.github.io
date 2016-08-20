---
layout: post
title: Aplicar la guía de estilo en C++
author: Jonathan López
---

Es una buena práctica de programación tener una guía de estilo, pero sobre todo, ***aplicarla***. 

### ¿Por qué es importante la guía de estilo?

* Para homogeneizar el código escrito por diferentes personas.
* Legibilidad del código fuente. Si cada archivo tiene un estilo diferente, cuesta más leerlo.
* Facilita la refactorización. Se distingue fácilmente dónde empieza y termina cada bloque de código.
* Los nombres de variables, funciones, métodos, clases y demás, siguen una misma lógica. Facilita las búsquedas y crear nuevos nombres.

### ¿Cómo se crea y usa?

Hay dos partes:

1. Un archivo donde se explica las "reglas" seguidas al programador. Para entenderlo nada mejor que ver un ejemplo. [Mi guía de estilo en Github](https://github.com/Jolouster/Guia-de-estilo.git).
2. Configuración del entorno de desarrollo (IDE). Todo IDE que se precie tiene una sección de configuración donde se puede especificar el sangrado, el espaciado que habrá, la alineación de diferentes partes del código, etc.

El problema aparece cuando cada programador tiene su propia guía de estilo y se siente cómodo y a gusto con ella. Heredamos código de otros, o lo descargamos de Github, y que necesitamos incluir en nuestro proyecto.

¿Vamos a obligar a todos a seguir una guía de estilo que no les gusta o no les "complace"? ¿Tenemos que ir archivo por archivo y línea por línea modificando el "formato" del código para que se ajuste a nuestra guía de estilo?

Nadie me lo había dicho antes, pero existen programas que se dedican exactamente a eso. No sabía ni qué buscar en Internet para encontrarlos.

### Herramienta extra

Hace apenas unas semanas descubrí un plugin para *Qt Creator* que tiene como objetivo ayudar a aplicar la guía de estilo. No sabía más. Estos días he estado investigando para aplicarlo y he visto que simplemente es una ayuda para integrar en el IDE otras herramientas que son las que hacen realmente el trabajo.

Es el plugin ***Beautifier***, en la [documentación de Qt sobre este](http://doc.qt.io/qtcreator/creator-beautifier.html) se explica cómo instalarlo y ponerlo en marcha. 

Da la opción de usar 3 programas diferentes que en teoría hacen lo mismo. Solo he probado uno, el primero. Estos son:

* [Artistic Style](http://astyle.sourceforge.net/)
* [ClangFormat](http://clang.llvm.org/docs/ClangFormat.html)
* [Uncrustify](http://uncrustify.sourceforge.net/)

Cada uno de ellos se puede usar con diferentes IDE, editores de texto o directamente desde la consola.

He leído sobre los tres y todos tienen un porrón de opciones. El que me ha parecido más sencillo de configurar y poner en marcha ha sido *Artistic Style*. 

Me interesaba ponerlo a funcionar cuanto antes y no perder demasiado tiempo en ello. Mi trabajo es crear código, corregirlo y mantenerlo, no darle el formato para que sea bonito de ver.

Simplemente he creado un archivo llamado `estilo.astylerc` con el siguiente contenido:

	# ARCHIVO CONFIGURACIÓN DE ESTILOS PARA C++
	# ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
	# Fecha: Junio 2016
	# Autor: Jonathan López
	#
	# Para más información ver el archivo: 'guia-estilo.dox' en el directorio de la documentación del proyecto.
	# Documentación de astyle: http://astyle.sourceforge.net/astyle.html#_Usage

	# OPCIONES PARA EL PROGRAMA 'ASTYLE'

	# Muestra los errores por la salida estandar
	#errors-to-stdout
	# Fija el tipo de 'fin de línea'
	lineend=linux

	# GUIA DE ESTILO

	# Llaves
	style=allman
	add-brackets

	# Sangrado

	# Tabulaciones, equivalentes a 4 espacios
	indent=force-tab=4
	# Dónde se aplica:
	indent-classes
	indent-switches
	indent-namespaces
	indent-labels
	indent-preprocessor

	#indent-preproc-block		# En la versión instalada esta opción aun no la tengo
	#indent-preproc-define		# En la versión instalada esta opción aun no la tengo
	#indent-preproc-cond		# En la versión instalada esta opción aun no la tengo

	indent-col1-comments
	min-conditional-indent=1

	# Espaciado en los bloques de código (ejem. 'if', 'for', 'while',...)
	break-blocks

	# Espaciado alrededor de los operadores
	pad-oper

	# Espaciado alrededor de los paréntesis
	#pad-first-paren-out
	#pad-paren-in
	pad-header

	# Espaciado alrededor del operador puntero y referencia ('*', '&' o '^')
	align-pointer=middle
	align-reference=name

	# Limite el largo de las líneas a 80 carácteres
	max-code-length=80
	break-after-logical

Ahora solo queda seguir la documentación de Qt y ver donde se guarda el archivo. Hay varias opciones y yo elegí guardarlo en el directorio del proyecto.

Después ejecuto el plugin en *Qt Creator*: `Tools > Beautifier > Artistic Style > Format Current File`

Sorpresa, en las primeras ejecuciones no funcionaba. Me mostraba los errores que tenía el archivo de configuración. Tras corregirlos, ya funcionaba.

La verdad, no noté muchos cambios al principio, ya estaba siguiendo la guía de estilos que había configurado en el plugin. Alguna línea en la que faltaba un salto de línea o espacio entre operadores, pero poco más. Así que ĺo he probado con código que no lo seguía y ha funcionad perfectamente.

Por ultimo, he añadido un atajo de teclado para ejecutar el plugin para ahorrar tiempo y a volar.

Un gustazo. Ahora puedo coger el código de mis compañeros de trabajo y homogeneizarlo con el mio en unos segundos.