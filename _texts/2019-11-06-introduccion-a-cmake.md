---
layout: post
title: Introducción a Cmake (1)
meta: Empezar a usar Cmake desde cero. Una pequeña guía de uso para torpes
category: Programación
author: Jonathan López
tag: C++
---

La idea es crear una serie de artículos en el que se explica cómo usar [Cmake](https://cmake.org) desde cero.

Por una parte para ayudar a la gente que quiere empezar a utilizar esta gran herramienta en sus proyectos de programación con una documentación de calidad en español que le guíe paso a paso desde cero.

Por otra parte para hacer una recopilación de mis propios apuntes y tenerlos siempre disponibles.

## ¿Qué es Cmake?
Es una herramienta que nos permite compilar, testear y empaquetar nuestro proyecto de software.

### Explicación.
Imaginemos que tenemos un proyecto realizado con el lenguaje C++. Este proyecto está compuesto por varias clases con sus respectivos archivos '.h' y '.cpp'. Unas clases dependen de otras, incluso como son bastantes, las organizamos en diferentes carpetas para poder reutilizar ese código y tenerlo todo bien ordenado.

Compilar todo eso una y otra vez a mano es una autentica locura. 

Si sumamos a este proyecto los test unitarios, que solo queremos para depuración. O si trabajamos solo con una o dos clases, no siempre querremos compilar *todo* el proyecto una y otra vez. A veces solo bastaría con compilar una parte y listo.

Quizás queramos compilar unas veces para Windows, otras veces para Linux y otras para MacOS. Realizar las diferentes compilaciones a mano puede ser muy tedioso y complicado.

Para hacer todo esto es Cmake.

## Documentación oficial

En [cmake.org](https://cmake.org) podemos encontrar la ultima versión de Cmake así como la documentación oficial. En caso de duda, esta es la documentación que prevalece. Yo puedo haberlo entendido mal o equivocarme al escribirlo aquí. 

El problema que le veo a la documentación oficial de Cmake es que está solo en inglés y que no es muy amigable para alguien que empieza desde cero. 

## Uso básico de Cmake

Tenemos un único archivo para nuestro proyecto.

~~~ bash
~/proyectoTest
    |
    --> main.cpp
~~~

El archivo `main.cpp` bien podría ser el típico "Hola mundo!" para hacer las pruebas iniciales.

Para compilar y generar el ejecutable llamado 'test' simplemente tenemos que ejecutar:

~~~ bash
g++ main.cpp -o test
~~~

Si lo queremos ejecutar:

~~~ bash
./test
~~~

Todo muy básico y sencillo, ¿no? 

Pues empecemos a usar Cmake desde aquí.

En la carpeta del proyecto creamos un archivo llamado `CMakeLists.txt`. Siempre se debe llamar así para que Cmake lo encuentre.

~~~ bash
~/proyectoTest
    |
    |-> main.cpp
    --> CMakeLists.txt
~~~

Dentro de este archivo `CMakeLists.txt` escribimos lo siguiente:

~~~ bash
# Versión mínima de Cmake que se debe usar en este proyecto
cmake_minimum_required(VERSION 3.10)

# El nombre que queramos dar al proyecto. Aqui elegimos 'Tutorial'.
project(Tutorial)

# Generamos el ejecutable 'Tutorial' a partir del archivo que tenemos.
add_executable(Tutorial main.cpp)
~~~

Listo, ya tenemos nuestra primera configuración de Cmake hecha. Para compilar el proyecto, solo tenemos que ejecutar lo siguente en la terminal:

~~~ bash
~/proyectoTest$ cmake -H. -Bbuild
~~~

Cmake crea el directorio donde "construirá" el proyecto llamado `build` y dentro crea los archivos de configuración del proyecto.

Después toca compilar el proyecto:

~~~ bash
~/proyectoTest$ cmake --build build
~~~

Ya está. Podemos comprobar que dentro de `build` se ha creado el ejecutable 'Tutorial'. Podemos ejecutarlo:

~~~ bash
~/proyectoTest$ ./build/Tutorial
~~~

Esto es lo más básico con lo que podemos empezar a usar Cmake. Si tenéis alguna duda dejad un comentario y tan pronto como me sea posible os intentaré respoder.
