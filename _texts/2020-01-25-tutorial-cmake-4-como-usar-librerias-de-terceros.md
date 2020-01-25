---
layout: post
title: Tutorial CMake 4 - Usar librerías de terceros
meta: Cómo configurar CMake para poder utilizar librerías de terceros en nuestros proyectos
category: Programación
author: Jonathan López
tag: C++
---

Para no reinventar la rueda es recomendable utilizar las librerías de terceros que implementen funcionalidades que nuestro proyecto necesita. Esto tiene varias ventajas:

* No tenemos que reinventar la rueda. No tenemos que hacer el mismo trabajo que otros ya han hecho y comprobado.
* Ahorro de tiempo en la depuración (si la libería está probada y es una versión estable no *"debería"* tener bugs)
* Nos permite centrarnos en la lógica de nuestro proyecto y no en cómo se implementan ciertas funcionalidades de bajo nivel.

Ahora la pregunta sería, ¿cómo incluyo yo estas librerías de terceros en mi proyecto?

Veremos como afrontar esta situación con CMake.

Vamos a emplear la librería ["pqxx"](http://pqxx.org/development/libpqxx) que nos permite conectarnos a una base de datos PostgreSQL como caso de uso real que se podría dar.

Primero creamos nuestro proyecto de ejemplo: "accessToDB"

~~~ bash
$ mkdir -p accessToDB/{build,modules,thirdparty}
$ touch main.cpp CMakeList.txt
$ cd accessToDB/thirdparty
~~~

Descargamos la librería en un zip desde su repositorio en GitHub: [https://github.com/jtv/libpqxx/](https://github.com/jtv/libpqxx/)

El archivo ZIP que nos terminamos de descargar lo descomprimimos en la carpeta "thirdpaty" del proyecto. En esta carpeta suelo colocar todo el código de terceros que uso en mis proyectos (frameworks, librerías, etc).

~~~ bash
$ unzip libpqxx-master.zip
~~~

### Instalamos la librería en nuestro sistema

Primero debemos instalar la librería en nuestro sistema para después poder utilizarlo.

En los paquetes de código fuente que se comparten en GitHub, y muchos otros sitios, es típico que se incluya el archivo "README.md" o en español "LEERME.md" donde el creador da instrucciones específicas de cómo compilar y usar su código. Para compilar la librería simplemente hay que abrir este archivo y leer las instrucciones que nos dan para ello.

Al final tendremos nuestra librería compilada y lista para usarse.

De hecho, una vez compilada e instalada, la carpeta de código fuente que nos hemos descargado practicamente la podríamos eliminar sin problemas.

Peeero si vamos a querer compilar nuestro programa para diferentes plataformas (para Windows, para Mac y para Linux por ejemplo) **mejor no borrar el directorio**. Esto ya lo explicaremos en otro artículo del blog, de momento vayamos paso a paso desde el principio.

Por defecto en Linux Ubuntu 18.04, al instalar la librería esta se copia en el directorio `/usr/local/include/` y es ahí donde habrá que buscarla.

### Configuramos nuestro proyecto

Ahora solo queda configurar nuestro archivo `CMakeLists.txt` para incluir la librería y crear un código muy básico en main.cpp para comprobar que todo funciona correctamente.

Como ya hemos estudiado los anteriores tutoriales, se supone que somos capaces de generar un `CMakeLists.txt` básico como el siguiente y sabemos qué significan cada una de sus líneas.

~~~ bash
cmake_minimum_required(VERSION 3.10.2)

# set language to use: C++
enable_language(CXX)

project (accessToDB)

add_executable(accessToDB main.cpp)
~~~~

Con esto tendríamos un proyecto básico. Para usar la librería `pqxx` tenemos que añadir las siguientes líneas **ANTES** de `add_executable(accessToDB main.cpp)`:

~~~ cmake
# --- INCLUDE libpqxx LIBRARY TO PROJECT
set (CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -lpqxx -lpq")
set (PQXX /usr/local/include/pqxx)
find_library (PQXX_LIB pqxx)
find_library (PQ_LIB pq)
~~~

Luego, **DESUÉS DE** la línea `add_executable(accessToDB main.cpp)` enlazamos la librería con nuestro ejecutable.

~~~ cmake
target_link_libraries (accessToDB ${PQXX_LIB} ${PQ_LIB})
~~~

Con esto ya estaría.

### Explicación paso a paso

Como no deberíamos usar nada en nuestros scripts, archivos de configuración y programas que no entendamos completamente antes, vamos a explicar estas líneas una a una. Así podremos emplearlas en nuestros proyectos como mejor nos convengan, adaptarlas a nuestras necesidades y aprender que es lo realmente importante aquí.

La primera línea: 

~~~ cmake
set (CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -lpqxx -lpq")
~~~

Establece la variable `CMAKE_CXX_FLAGS` con los parámetros que ya tenía y les **añade** los que ahora nos interesa que son `-lpqxx` y `-lpq`. 

Se hace de esta manera por si se ha empleado antes `CMAKE_CXX_FLAGS` para establecer alguna otra configuración. Esta variable son los "parámetros" que se le pasan al compilador cuando va a construir nuestro progama.

La segunda línea:

~~~ cmake
set (PQXX /usr/local/include/pqxx)
~~~

Guarda en la variable `PQXX` la ruta donde debe buscar la librería que hemos instalado previamente. Así, cuando se enlace nuestro programa con la librería el programa encargado de hacerlo sabrá donde está. Justo de esta búsqueda se encargan las dos siguientes líneas:

~~~ cmake
find_library (PQXX_LIB pqxx)
find_library (PQ_LIB pq)
~~~

Estas líneas le dicen a CMake que debe buscar, en las rutas por defecto donde se guardan las librerías, alguna que se llame `pqxx` y otra que se llame `pq`. Si las encuentra, el resultado se almacenará en las variables `PQXX_LIB` y en `PQ_LIB` y no se volverá a realizar dicha búsqueda hasta que se reseteen dichas variables.

En mi caso, tras ejecutar estas 2 instrucciones, las variables pasan a tener los siguientes valores:

~~~ bash
PQXX_LIB: /usr/local/lib/libpqxx.a
PQ_LIB: /usr/lib/x86_64-linux-gnu/libpq.so
~~~

Esto son las rutas completas a las librerías que usará nuestro programa.

***Opcional:***
Si tu también quieres ver los valores de estas variables puedes hacerlo añadiendo las 2 siguientes líneas después de las instrucciones `find_library`:

~~~ cmake
message("PQXX_LIB: " ${PQXX_LIB})
message("PQ_LIB: " ${PQ_LIB})
~~~

Fin del código opconal. Continuamos.

La instrucción `find_library` tiene más opciones. Se le podría especificar rutas concretas donde buscar *además de las rutas por defecto*. Podeís investicar más en la documentación [aquí](https://cmake.org/cmake/help/v3.16/command/find_library.html?highlight=find_library).

Finalmente enlazamos los archivos de las librerías que acabamos de buscar con nuestro programa. Lo cual hacemos con la ultima línea que añadimos al final de nuestro CMakeList.txt:

~~~ cmake 
target_link_libraries (accessToDB ${PQXX_LIB} ${PQ_LIB})
~~~

En esta ultima línea se emplean las mismas variables que se han empleado en las instrucciones que buscan las librerías `PQXX_LIB` y `PQ_LIB` y que ahora contienen la ruta exacta donde se encuentran los archivos de nuestra librería.

### Pruebas

En este punto deberíamos tener ya nuestro CMakeLists.txt tal que así:

~~~ cmake
cmake_minimum_required(VERSION 3.10.2)

# set language to use: C++
enable_language(CXX)

project (accessToDB)

# --- INCLUDE libpqxx LIBRARY TO PROJECT
set (CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -lpqxx -lpq")
set (PQXX /usr/local/include/pqxx)
find_library (PQXX_LIB pqxx)
find_library (PQ_LIB pq)

## --- OPTIONAL
message("PQXX_LIB: " ${PQXX_LIB})
message("PQ_LIB: " ${PQ_LIB})
## --- END OPTIONAL CODE

# --- make our executable file
add_executable(accessToDB main.cpp)

# --- link with pqxx library
target_link_libraries (accessToDB ${PQXX_LIB} ${PQ_LIB})
~~~

Ahora toca añadir a nuestro `main.cpp` el código necesario para comprobar que nos podemos conectar a nuestra base de datos y hacer consultas. Si conseguimos hacer un "SELECT" podremos hacer cualquier otra cosa, así que solo probaremos esto.

En mi ordenador tengo instalado PostgresSQL y he creado una base de datos de pruebas con una tabla y unos registros inventados pero muy parecidos a los que usaré en un proyecto real. Cambia los datos de conexión a la base datos y la consulta para ajustarlos a los que tengas tu.

Mi archivo `main.cpp` es el siguiente:

~~~ cpp

#include <iostream>
#include <pqxx/pqxx>

/*
 * Archivo de prueba de una conexión con C++ estándar con PostgreSQL
 */
int main(int argc, char *argv[])
{
    try
    {
        pqxx::connection connect("dbname=pegasus hostaddr=127.0.0.1 port=5432 user=postgres password=580140*");
		/**
		 * Recuerda cambiar el nombre de la base de datos por la que tengas tu (dbname)
		 * Cambia también el usuario y la contraseña por las que hayas especificado tu al crear la base de datos
		 */

        std::cout << "Connected to " << connect.dbname() << std::endl;
        pqxx::work W(connect);

        pqxx::result R = W.exec("SELECT var_name FROM conf_resources order by id asc;");

		// Recuerda cambiar la consulta para que se ajuste a lo que tengas tu en tu propia base de datos

        std::cout << "Found " << R.size() << " resources: " << std::endl;
        for (auto row : R)
            std::cout << row[0].c_str() << std::endl;

        std::cout << "Making changes definite: ";
        W.commit();
        std::cout << "OK." << std::endl;
    }
    catch (const std::exception &e)
    {
        std::cerr << e.what() << std::endl;
        return 1;
    }
    return 0;
}
~~~

Ale, ya está todo. Ahora toca compilar y ejecutar nuestro programa.

Vamos a la terminal y nos colocamos en la carpeta inicial de nuestro proyecto:

<pre><span style="background-color:#0087AF"><font color="#FFFFFF"><b> jonathan </b></font></span><span style="background-color:#585858"><font color="#0087AF"> </font></span><span style="background-color:#585858"><font color="#D0D0D0"><b>~ </b></font></span><font color="#585858"> </font>cd Proyectos/accesToDB/
<span style="background-color:#0087AF"><font color="#FFFFFF"><b> jonathan </b></font></span><span style="background-color:#585858"><font color="#0087AF"> </font></span><span style="background-color:#585858"><font color="#BCBCBC">~ </font></span><span style="background-color:#585858"><font color="#8A8A8A"> </font></span><span style="background-color:#585858"><font color="#BCBCBC">Proyectos </font></span><span style="background-color:#585858"><font color="#8A8A8A"> </font></span><span style="background-color:#585858"><font color="#D0D0D0"><b>accesToDB </b></font></span><font color="#585858"> </font>tree -L 2
<font color="#4196FF"><b>.</b></font>
├── <font color="#4196FF"><b>build</b></font>
├── CMakeLists.txt
├── main.cpp
├── <font color="#4196FF"><b>modules</b></font>
└── <font color="#4196FF"><b>thirdparty</b></font>
    └── <font color="#4196FF"><b>libpqxx</b></font>

4 directories, 2 files
</pre>

Le decimos a CMake donde está el código inical y que debe compilar dentro de la carpeta `build`.

<pre><span style="background-color:#0087AF"><font color="#FFFFFF"><b> jonathan </b></font></span><span style="background-color:#585858"><font color="#0087AF"> </font></span><span style="background-color:#585858"><font color="#BCBCBC">~ </font></span><span style="background-color:#585858"><font color="#8A8A8A"> </font></span><span style="background-color:#585858"><font color="#BCBCBC">Proyectos </font></span><span style="background-color:#585858"><font color="#8A8A8A"> </font></span><span style="background-color:#585858"><font color="#D0D0D0"><b>accesToDB </b></font></span><font color="#585858"> </font>cmake -H. -Bbuild
-- The CXX compiler identification is GNU 7.4.0
-- Check for working CXX compiler: /usr/bin/c++
-- Check for working CXX compiler: /usr/bin/c++ -- works
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Detecting CXX compile features
-- Detecting CXX compile features - done
Se compila para Linux
-- The C compiler identification is GNU 7.4.0
-- Check for working C compiler: /usr/bin/cc
-- Check for working C compiler: /usr/bin/cc -- works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Detecting C compile features
-- Detecting C compile features - done
system: Linux
build type: Debug
machine: 
compiler: /usr/bin/c++
PQXX_LIB: /usr/local/lib/libpqxx.a
PQ_LIB: /usr/lib/x86_64-linux-gnu/libpq.so
-- Configuring done
-- Generating done
-- Build files have been written to: /home/jonathan/Proyectos/accesToDB/build
</pre>

En la carpeta `build` se han generado unos archivos automáticamente. No vamos a explicar que hacen ni nada parecido pero son necesarios para construir nuestro proyecto. Quizás en otro artículo expliquemos estos detalles más avanzados.

Ahora solo queda compilar y comprobar que funciona nuestro programa.

<pre><span style="background-color:#0087AF"><font color="#FFFFFF"><b> jonathan </b></font></span><span style="background-color:#585858"><font color="#0087AF"> </font></span><span style="background-color:#585858"><font color="#BCBCBC">~ </font></span><span style="background-color:#585858"><font color="#8A8A8A"> </font></span><span style="background-color:#585858"><font color="#BCBCBC">Proyectos </font></span><span style="background-color:#585858"><font color="#8A8A8A"> </font></span><span style="background-color:#585858"><font color="#D0D0D0"><b>accesToDB </b></font></span><font color="#585858"> </font>cmake --build build/
<font color="#FC5275"><b>Scanning dependencies of target testPSQL</b></font>
[ 50%] <font color="#789B6A">Building CXX object CMakeFiles/testPSQL.dir/main.cpp.o</font>
[100%] <font color="#80C470"><b>Linking CXX executable testPSQL</b></font>
[100%] Built target testPSQL
<span style="background-color:#0087AF"><font color="#FFFFFF"><b> jonathan </b></font></span><span style="background-color:#585858"><font color="#0087AF"> </font></span><span style="background-color:#585858"><font color="#BCBCBC">~ </font></span><span style="background-color:#585858"><font color="#8A8A8A"> </font></span><span style="background-color:#585858"><font color="#BCBCBC">Proyectos </font></span><span style="background-color:#585858"><font color="#8A8A8A"> </font></span><span style="background-color:#585858"><font color="#D0D0D0"><b>accesToDB </b></font></span><font color="#585858"> </font>./build/testPSQL 
Connected to pegasus
Found 10 resources: 
SYS
AL99999
AL99998
AL99997
AL99996
AL99995
AL99994
AL99993
AL99992
AL99991
Making changes definite: OK.
<span style="background-color:#0087AF"><font color="#FFFFFF"><b> jonathan </b></font></span><span style="background-color:#585858"><font color="#0087AF"> </font></span><span style="background-color:#585858"><font color="#BCBCBC">~ </font></span><span style="background-color:#585858"><font color="#8A8A8A"> </font></span><span style="background-color:#585858"><font color="#BCBCBC">Proyectos </font></span><span style="background-color:#585858"><font color="#8A8A8A"> </font></span><span style="background-color:#585858"><font color="#D0D0D0"><b>accesToDB </b></font></span><font color="#585858"> </font>
</pre>

¡Perfecto! Funciona como se esperaba.

Espero que te haya ayudado.
