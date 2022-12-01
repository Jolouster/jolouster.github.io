---
layout: post
title: CPP optimización de estructuras
author: Jonathan López
category: Programación
meta: El orden de los elementos en una estructura importa
tag: C++
---

Ejemplo:

```cpp
struct ejemplo1 {
	char a; 			// 1 byte
	short b; 			// 2 bytes
	int c; 				// 4 bytes
};
```

En esta estructura se esperaría que el tamaño de la estructura fuera 7 bytes pero no es así. Ocupa 8 bytes.

```cpp
struct ejemplo2 {
	char a; 		// 1 byte
	char b; 		// 1 byte
	int c; 			// 4 byte
	short d; 		// 2 byte
};
```

Esta ultima debería ocupar 8 bytes pero ocupa 9.

Por optimización el compilador trata de pillar bloques contiguos de memoria donde almacenar las estructuras. Usa el tamaño del tipo de variable más grande que se emplea en la estructura. Para ello mete "paddings" para rellenar huecos si los elementos no están bien ordenados.

Para ver los tamaños que realmente ocupan:

```cpp
sizeof(ejemplo1);  // = 8 bytes
sizeof(ejemplo2);  // = 12 bytes
```

En estos ejemplos la primera estructura no se puede optimizar. La segunda sí, poniendo el `short` antes del `int`.


Conclusiones:

- En programación el orden importa.
- Hay que conocer bien todos los detalles para saber qué se puede mejorar.


Hoy día la memoria es muy barata y no es "necesario" optimizar mucho, pero para sistemas embebidos sí lo es. Para mejorar el rendimiento tanto de velocidad como del uso de memoria de un programa también.

Si no se vigila esto desde un principio, luego hay que sopesar si vale la pena el tiempo y el esfuerzo en hacer estas optimizaciones.




keywords: #programación/Cpp #eficiencia
