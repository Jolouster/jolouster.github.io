---
layout: post
title: El framework Qt
meta: Descripción general del framework Qt para el desarrollo multiplataforma.
author: Jonathan López
source: https://es.wikipedia.org/wiki/Qt_(biblioteca) 
category: Programación
tag: framework Qt, C++
---

En el trabajo estoy usando de forma habitual el framework[^1] Qt para crear aplicaciones en C++. Pero este mismo framework se puede usar con otros lenguajes como por ejemplo Python, Ruby, C#, PHP, etc.

No soy ningún experto en este framework, aún (me gustaría llegar a serlo). De echo voy a empezar esta serie de artículos sobre el *framework Qt* para obligarme a estudiarlo más a fondo e ir explicando mi experiencia con él. 

Con el tiempo he visto que *si no sabes explicar algo, es que no has llegado a comprenderlo completamente*. Así que me he puesto el reto de explicar lo que se sobre este framework Qt. Poco a poco iré publicando más artículos sobre este entorno de trabajo. 

## Objetivos del framework Qt.

El framework Qt **usa C++ como lenguaje nativo**. Su objetivo es facilitar la creación de aplicaciones multiplataforma. Es decir que usando este framework puedes crear aplicaciones que pueden funcionar, sin cambiar nada del código, en sistemas operativos de Microsoft, en Mac OSx, iOS, Linux y Android entre otros. 

Este framework integra herramientas de traducción para hacer aplicaciones multi-idioma, un IDE muy completo y ampliable con plugins y una biblioteca muy extensa que facilitan mucho la vida al programador.

## Dónde se usa.

Qt se ha usado en aplicaciones muy conocidas y en ámbitos muy diferentes. Por mencionar unas muy conocidas: el escritorio KDE, Skype, Doxygen, Adobe Photoshop Album. En la [página oficial](http://qt.io) *fardan* de las grandes marcas que lo usan.

Últimamente la empresa encargada de desarrollar este framework está haciendo un gran esfuerzo para impulsar el desarrollo de aplicaciones embebidas en dispositivos móviles y en automoción. 

Se usa principalmente para desarrollar aplicaciones con interfaz gráfica, pero se puede crear aplicaciones de línea de comandos o servicios que se ejecutan en segundo plano en servidores. Con este framework puedes desarrollar cualquier aplicación software que te imagines.

Como extra, este framework tiene un lenguaje llamado **QML**[^2]. Está basado en javascript y yo aun no lo he tocado... Ya llegará. QML permite crear unas interfaces gráficas impresionantes. Se pueden adaptar perfectamente a distintos tamaños de pantalla e incluso crear animaciones y juegos bastante interesantes. 

## Documentación.

En [la página oficial de documentación de Qt](http://doc.qt.io/) puedes encontrar toda la necesaria, muy completa.

Obviamente está en inglés. El lenguaje de los programadores por excelencia. Si no sabes inglés, antes de empezar a estudiar ningún lenguaje de programación aprende este idioma.

En castellano no hay documentación oficial o apenas. Hay una [wiki de Qt](http://wiki.qt.io/Main), traducida a varios idiomas. Pero no está totalmente traducida al castellano ni es una documentación completa.

## Actualizaciones.

Cada cierto tiempo van sacando actualizaciones del framework. A fecha de 2016 el proyecto está muy vivo y a primeros del año sacaron la versión 5.7 y la versión 4 de su IDE "Qt Creator" para producción. 

En ocasiones entre una y otra versión hay diferencias que las hacen un poco incompatibles. Puede que hayan sustituido ciertas clases por otras o se haya incorporado alguna nueva y dejado de usar otras. Por lo que es recomendable que se revise bien las novedades de la nueva versión antes de actualizar. 

En el IDE que trae Qt se pueden tener varias versiones perfectamente. No sería un problema mantener una versión antigua para dar soporte a ciertas aplicaciones y la última para las nuevas aplicaciones.

## Mi opinión sobre el framework de Qt.

Es muy bueno en general. Te permite trabajar con C++ con una abstracción bastante alta (evitar preocuparte por detalles a bajo nivel). Es rápido desarrollar aplicaciones gráficas y cuando se llega a entender bien, es muy cómodo y te ahorra mucho código. Tu programa puede estar orientado perfectamente a eventos de forma muy sencilla.

Está orientado a realizar aplicaciones genéricas de todo tipo. Si se quiere algo más concreto como procesado de imágenes, mejor integrarlo con otros frameworks más especializados como OpenCV. La integración Qt + OpenCV, por ejemplo, es posible y funcionan muy bien juntos.

Los programas finales son bastante eficientes y pequeños. Obviamente no son tan eficientes como una aplicación nativa, hecha expresamente para ese SO o plataforma, pero no hay mucha diferencia y tiene la ventaja de la portabilidad. Se puede trabajar con librerías externas por lo que el tamaño del ejecutable de nuestra aplicación puede ser realmente mínimo. 

Por otro lado, para crear aplicaciones embebidas o para otros sistemas operativos, en ***teoría*** es sencillo. En la práctica se te puede complicar la cosa. Hay que instalar diferentes compiladores, y *para aplicaciones embebidas*, hay que compilar todo el framework Qt para determinadas plataformas *antes* de usarlo. En mi caso, lo he intentado varias veces sin éxito. Supongo que es debido a alguna configuración errónea. 

En la documentación oficial muestran muchas opciones y es muy genérica. Supongo que porque han querido hacer una única documentación válida para todas las plataformas. Y como hay tantas opciones y hay que configurarlo expresamente para nuestra plataforma embebida hay muchas opciones de que algo salga mal... y así ha sido en mi caso. Una vez configurado todo, he comprobado (en el ordenador de conocidos) que la portabilidad entre plataformas es inmediata y funciona perfectamente. 

Estoy investigando y estudiando más sobre el tema y cuando lo consiga publicaré un tutorial.

Para sacarle el máximo provecho cuanto más se conozca mejor. Es muy extenso y tiene muchas opciones y posibilidades, por lo que puede llevar cierto tiempo aprenderlo. Pero creo que vale la pena y se pueden crear aplicaciones muy profesionales con este framework. 


--- 
[^1]: "Framework" es la palabra inglesa que se usa para referirse al entorno de trabajo. Un conjunto o estructura de bibliotecas, compiladores, IDE, clases y métodos predefinidos, etc. que facilitan la creación de aplicaciones.