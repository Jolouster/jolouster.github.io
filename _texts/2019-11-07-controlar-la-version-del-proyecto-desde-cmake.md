---
layout: post
title: Controlar la versión del proyecto con CMake
meta: Introducción a CMake 2. Cómo controlar la versión del proyecto desde la configuración de CMake
category: Programación
author: Jonathan López
tag: C++
---

En este artículo se muestra cómo controlar la versión de nuestro proyecto desde la configuración de CMake. 

Asi de paso se sientan las bases para el uso de variables y otras interesantes funciones de CMake que después nos darán mucho juego.

Partimos del proyecto que creamos en el artículo anterior ("Introducción a CMake (1)").


~~~ bash
~/proyectoTest
    |
    |-> build/
    |-> main.cpp
    |-> CMakeLists.txt
~~~
    
## Uso de variables en CMake

En el archivo de configuración de `CMakeLists.txt` podemos definir variables que después podremos utilizar en nuestro código fuente para realizar compilaciones condicionales o para cualquier otra cosa que se nos pueda ocurrir.

Como ejemplo vamos a definir la versión del proyecto con este sistema.

En CMakeLists.txt definimos las variables de esta manera:

~~~ bash
set (nombre_variable_descriptivo 23)
~~~

En nuestro ejemplo vamos a declarar 2 variables:

~~~ bash
set (Tutorial_VERSION_MAJOR 1)
set (Tutorial_VERSION_MINOR 0)
~~~

Perfecto, ya tenemos nuestras variables. Para utilizarlas en nuestro programa:

~~~ bash
configure_file (archivoEntrada archivoSalida)
~~~

Ahora para hacer uso de esta instrucción hay que saber que CMake por defecto tambien tiene sus propias variables. Una de ellas es `PROJECT_SOURCE_DIR`. Esta variable nos dice el directorio "raíz" de nuestro proyecto. Para utilizarla lo haremos de la siguiente manera:

~~~ bash
configure_file (${PROJECT_SOURCE_DIR}/config.h.in ${PROJECT_SOURCE_DIR}/config.h)
~~~

Esta línea lo que hace es acceder del contenido de "config.h.in", sustituir los las variables que usaremos en este archivo por el valor final que tendrán.

Ahora cuando las utilicemos se entenderá mejor. Creamos el archivo `config.h.in` y en él escribimos:

~~~ bash
#define VERSION_MAJOR @Tutorial_VERSION_MAJOR@
#define VERSION_MINOR @Tutorial_VERSION_MINOR@
~~~

Cuando construyamos el proyecto con CMake, al llegar aquí, sustituirá `@Tutorial_VERSION_MAJOR@` por un `1` y `@Tutorial?VERSION_MINOR@` por un `0` y creará con esta información el archivo `config.h` de forma automática.

Ahora en nuestro `main.cpp` podríamos tener:

~~~ cpp
#include <iostream>
#include "config.h"  // Archivo generado automáticamente

int main () {
    std::cout << "Hola mundanal ruido!" << std::endl;
    std::cout << "Versión: " << VERSION_MAJOR << ":" << VERSION_MINOR << std::endl;

    return 0;
}
~~~

Podemos construir ahora el proyecto. En la terminal:

~~~ bash
~/proyectoTest$ cmake --build build
~~~

Ejecutamos:

~~~ bash
~/proyectoTest$ ./build/Tutorial
~~~

Resultado:

~~~ bash
Hola mundanal ruido!
Versión: 1.0
~~~

Ahora ya sabemos cómo usar variables y cómo emplearlas en nuestro proyecto.

El ejemplo es muy básico y sencillo pero ilustra muy bien los conceptos. A partir de aquí se puede ir complicando y haciendo más elaborado todo nuestro entorno de desarrollo.