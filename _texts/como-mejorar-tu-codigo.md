---
layout: post
title: Cómo mejorar tu código
author: Jonathan López
---

Independientemente del lenguaje que se use para programar siempre se puede mejorar nuestros programas siguiendo estas "normas" o recomendaciones básicas:

1. Usa **nombres autoexplicativos**, con significado.
	Tu código debería poder leerse como si fuese un libro. Muy muy muy importante. Seguir siempre el mismo estilo y forma de elegir los nombres. 

2. Crear funciones y métodos **cortos**.
	Hace fácil seguir la ejecución del código. Muy relacionado con el siguiente punto.

3. Las funciones y métodos **deben hacer una única cosa**.
	¿Por qué evitar la funciones que hacen muchas cosas?
	* Son propensas a cambios.
	* Cuestan más testearlas.
	* Dificulta añadir nuevas funcionalidades.

4. **Limitar** al máximo **el número de argumentos**.
	Dificultan la lectura del código y complican el testing. Si son necesarios muchos argumentos, sustituirlos por un objeto que contenga toda la información.

5. **No repetir código**.
	¿Por qué es importante evitarlo? La repetición de código...
	* Obliga a testear lo mismo varias veces.
	* Hay que acordarse de modificarlo en todos los sitios donde se use.
	* Implica mayor cantidad de código a mantener.

6. **Evitar los comentarios**.
	Mejor hacer el código más fácil de leer y entender. Cambiar nombres y refactorizar. Documentar lo imprescindible. 
	Porque...
	* se suelen ignorar al leer el código,
	* cuando se realizan cambios en el código se suele olvidar actualizar los comentarios.

7. Usar **un único formato y estilo** en el código. 
	Para saber más puedes leer ["Aplicar la guía de estilo en C++"](http://jolouster.com/texts/aplicar-estilos-cpp.html). Quizás también te ayude en tu lenguaje.

8. Un objeto no debería conocer la estructura de otros objetos con los que iteractua.
	Código como este:

		getX().getY().getZ().doSomething()

	Hay que evitarlo. Huele mal. Rediseñar y pensar mejor la arquitectura de nuestro progarama.

9. **Abstraer los datos**.
	Tener claro si una "clase" es una simple estructura de datos o un objeto con comportamiento. *Evitar el **abuso*** de funciones "get" y "set".

10. Usar **excepciones en lugar de devolver códigos de error**.
	Crear excepciones propias que sean claras y autoexplicativas con un simple vistazo. Que devuelvan mensajes de error claros y descriptivos.

11. Cuando se use código que no sea nuestro, usarlo de manera **que sea fácil de sustituir**.

12. Usar los [**test unitarios**](http://devexperto.com/testing-1/).
	Permite *validar rápidamente* nuestro código y nos evita escribir código de más.

Con esto conseguiremos que nuestro código mejore muchísimo. Hay que tratar de aplicar estas recomendaciones de forma habitual para que sea algo consistente en nuestros programas.

Aplicar todo esto de forma regular y natural nos puede llevar su tiempo. Pero todo es parte de nuestra mejora continua y necesaria como ***programadores profesionales***. 

Los *test unitarios* ya son de por sí un mundo, yo de hecho no tengo mucho que aprender sobre el tema... 

El siguiente paso será aprender:

- a diseñar mejor, mejorar la arquitectura de nuestro software
- refactorizar, mejorar el código espagueti que ya tengamos funcionando.

Con esto ya tenemos para rato... Pero paso a paso todo se consigue. 

*¡Ánimo!*