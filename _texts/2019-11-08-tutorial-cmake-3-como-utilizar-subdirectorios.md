---
layout: post
title: Tutorial CMake 3 - Subdirectorios en el proyecto
meta: Cómo configurar CMake para poder utilizar subdirectorios en nuestro proyecto.
category: Programación
author: Jonathan López
tag: C++
---

En el tutorial de CMake que estamos utilizando el proyecto de ejemplo que estábamos usando es muy muy simple. Todo el código está en una misma carpeta o directorio. Esto no es realista en un proyecto real. 

Lo habitual es tener el código divido en módulos o carpetas de código. 

Vamos a desarrollar el proyecto que estámos usando de ejemplo para incluir subcarpetas.

<pre><font color="#8F9D6A"><b>jonathan@jarvis</b></font>:<font color="#7587A6"><b>~/Proyectos/proyectoTest</b></font>$ tree -L 3
<font color="#7587A6"><b>.</b></font>
├── <font color="#7587A6"><b>build</b></font>
├── CMakeLists.txt
├── config.h
├── config.h.in
├── <font color="#7587A6"><b>lib</b></font>
├── main.cpp
└── <font color="#7587A6"><b>modules</b></font>
    ├── <font color="#7587A6"><b>goodbye</b></font>
    │   ├── CMakeLists.txt
    │   ├── goodbyeclass.cpp
    │   └── goodbyeclass.h
    └── <font color="#7587A6"><b>hello</b></font>
        ├── CMakeLists.txt
        ├── helloclass.cpp
        └── helloclass.h

</pre>

Hemos creado el directorio `modules` y dentro de éste 2 carpetas más: `hello` y `goodbye`. Obviamente no son nombres realistas que usaríamos en un proyecto real... pero para el ejemplo nos vale.

Dentro de la carpeta `hello` hemos creado 3 archivos:
- CMakeLists.txt
- helloclass.cpp
- helloclass.h

Dentro de la carpeta `goodbye` hemos creado otros 3 archivos: 
- CMakeLists.txt
- goodbyeclass.cpp
- goodbyeclass.h

La idea es poder utilizar estas 2 clases (`hello` y `goodbye`) en nuestro archivo `main.cpp`. El programa tendrá esta jerarquía:

~~~
                        Tutorial 
            (construido a partir de main.cpp)
                    ^           ^
                    |           |
    ----------------|           |---------------
    |                                           |
 Hello                                      Goodbye
(librería construida                (librería construida a
a partir de                             partir de goodbyeclass.h
helloclass.h y                          y goodbyeclass.cpp)
helloclass.cpp)
~~~


Para poder hacer esto, debemos añadir las siguientes líneas al archivo CMakeLists.txt de nuestra carpeta raíz del proyecto (proyectoTest/CMakeLists.txt).

~~~ cmake
add_subdirectory (${PROJECT_SOURCE_DIR}/modules/hello)
add_subdirectory (${PROJECT_SOURCE_DIR}/modules/goodbye)
~~~

De esta manera el CMake ya sabe qué tiene que buscar dentro de estos directorios otros archivos de código y un 'CMakeLists.txt'. 

La instrucción `add_subdirectory` tiene más opciones, pero para el uso que queremos darle de momento es suficiente. 

Ahora toca enlazar los módulos que se generen con nuestro programa `Tutorial`

~~~ cmake
target_link_libraries (Tutorial HelloLib GoogbyLib)
~~~

El archivo CMakeLists.txt del directorio raíz debe quedar como el siguiente:

~~~ cmake
cmake_minimum_required(VERSION 3.10.2)
project(Tutorial)

# Setting variables
set (testRasp_VERSION_MAJOR 1)
set (testRasp_VERSION_MINOR 0)

# -- AÑADIMOS LOS SUBDIRECTORIOS
add_subdirectory(${PROJECT_SOURCE_DIR}/modules/hello )
add_subdirectory(${PROJECT_SOURCE_DIR}/modules/goodbye)

# -- GENERAMOS EL ARCHIVO *.h EN EL QUE TENDREMOS LOS VALORES DE LAS VARIABLES
configure_file(${PROJECT_SOURCE_DIR}/config.h.in ${PROJECT_SOURCE_DIR}/config.h)

# -- ESPECIFICAMOS EL EJECUTABLE QUE SE GENERARÁ
add_executable(Tutorial main.cpp)

# -- EL EJECUTABLE 'Tutorial' USARÁ LAS LIBRERÍAS GENERADAS...
target_link_libraries(Tutorial HelloLib GoodbyeLib)
~~~ 

Siguiente paso: configurar los módulos.

Tanto el módulo `hello` como `goodbye` se configuran exactamente igual. Como los vamos a utilizar como librerías debemos configurarlos de la siguiente manera:

~/proyectoTest/modules/hello/CMakeLists.txt contiene únicamente:

~~~ cmake
# Creamos la librería Hello
add_library (HelloLib helloclass.cpp)
~~~

~/proyectoTest/modules/hello/CMakeLists.txt contiene únicamente:

~~~ cmake
# Creamos la librería Goodbye
add_library (GoodbyeLib goodbyclass.cpp)
~~~

Ya está. Ya tenemos todo configurado. Ahora solo queda compilar y comprobar que todo funciona.

En los archivos de código fuente del módulo `Hello` y `Goodbye` podemos implementar lo que queramos. Después, en `main.cpp` simplemente tenemos que incluir los módulos y usarlos.

Ejemplo:

~/proyectoTest/modules/hello/helloclass.h

~~~ cpp
#include <iostream>
#include <string>

#ifndef HelloLib
#define HelloLib

namespace jlu
{
    class Hello
    {
        public:
            Hello();
            ~Hello();
            void printHello(const std::string name);
    };
} // namespace jlu
#endif
~~~

~/proyectoTest/modules/hello/helloclass.cpp

~~~ cpp
#include "helloclass.h"

namespace jlu
{
    Hello::Hello()
    {
        std::cout << "Se ejecuta el constructor de la clase." << std::endl;
    }

    Hello::~Hello()
    {
        std::cout << "Destructor de la clase... " << std::endl;
    }

    void Hello::printHello(const std::string name)
    {
        std::cout << "Hola " << name << " ¿qué tal te va?" << std::endl;
    }
} // namespace jlu
~~~

~/proyectoTest/modules/goodbye/goodbyeclass.h

~~~ cpp
#include <iostream>
#include <string>

#ifndef GoodbyeLib
#define GoodbyeLib

namespace jlu
{
    class Goodbye
    {
        public:
            Goodbye();
            ~Goodbye();
            void printBye(const std::string name);
    };
} // namespace jlu
#endi
~~~

~/proyectoTest/modules/goodbye/goodbyeclass.cpp

~~~ cpp
#include "goodbye.h"

namespace jlu
{
    Goodbye::Goodbye()
    {
        std::cout << "Se ejecuta el constructor de la clase Goodbye." << std::endl;
    }

    Goodbye::~Goodbye()
    {
        std::cout << "Destructor de la clase Goodbye... " << std::endl;
    }

    void Goodbye::printBye(const std::string name)
    {
        std::cout << "¡Adiós! " << name << " que te vaya bonito..." << std::endl;
    }
} // namespace jl
~~~

~/proyectoTest/main.cpp

~~~ cpp
#include <iostream>
#include "config.h"
#include "modules/hello/helloclass.h"
#include "modules/goodbye/goodbye.h"

int main()
{
    std::cout << "Versión del programa: " << testRasp_VERSION_MAJOR << "." << testRasp_VERSION_MINOR << std::endl;
    jlu::Hello h;
    jlu::Goodbye a;
    h.printHello("Jonathan");
    a.printBye("Jonathan");
    return 0;
}
~~~

Para compilar:

~~~ bash
cmake --build build
~~~

Ejecutamos:

~~~ bash
jonathan@jarvis:~/Proyectos/proyectoTest$ ./build/Tutorial 
Versión del programa: 1.0
Se ejecuta el constructor de la clase.
Se ejecuta el constructor de la clase Goodbye.
Hola Jonathan ¿qué tal te va?
¡Adiós! Jonathan que te vaya bonito...
Destructor de la clase Goodbye... 
Destructor de la clase... 
~~~ 

