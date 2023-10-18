---
layout: ../../layouts/MarkDownPostLayout.astro
title: "Shaders en OpenGL"
pubDate: 2023-09-18
description: "Resumen de la sintaxis de GLSL"
author: "Thgear27"
tags: ["glsl"]
---

# Shaders en GLSL

Los shaders son pequeños programas que son ejecutados por la GPU.
Estos programas son ejecutados en diversas partes de la **Graphics Pipeline**.
En el caso de **GLSL**, cada shader solo puede comunicarse con el otro a través de las _inputs_ y _outputs_ de estos mismos.

Estos shaders son escritos en un lenguaje muy parecido a **C**,
de que tenemos características útiles tales como tipos de datos nuevos para manipular matrices y vectores fácilmente.

Veamos la estructura básica de un shader en _GLSL_:

```cpp
#version version_number

in type in_variable_name;

out type out_variable_name;

uniform type uniform_name;

void main() {
  // ...
}
```

Los shaders siempre comienzan con una declaración de versión.
Esta versión hace alusión a la version del lenguaje de _GLSL_ que se está utilizando

# Tipos de datos

EL GLSL se pueden utilizar los tipos de datos más básicos conocidos. Tenemos `int`, `float`, `double`, `uint` y `bool`.
Debido a que GLSL es usado para trabajar con gráficos, también tenemos otros tipos de
datos extras como vectores y matrices.

# Vectores

Un vector de GLSL es un contenedor de hasta 4 componentes. Lo típico de _x_, _y_, _z_ y _w_.
Observar la siguiente tabla.

| Tipo de dato | Número de componentes | Tipo de dato (Componente) |
| ------------ | --------------------- | ------------------------- |
| vecn         | n                     | float                     |
| bvecn        | n                     | boolean                   |
| ivecn        | n                     | insteger                  |
| uvecn        | n                     | unsigned integers         |
| dvecn        | n                     | double                    |

Podemos acceder a las componentes de un vector de la siguiente manera:

```cpp
vec4 someVec;
float x = someVec.x; // Accedemos a la componente x
float y = someVec.y; // Accedemos a la componente y
float z = someVec.z; // Accedemos a la componente z
```

Resulta que también tenemos otra forma de acceder a las componentes
con una selección especial llamada **swizzling**.

```cpp
vec2 vect;
vec4 result = vect.xxyy; // result = (vect.x, vect.x, vect.y, vect.y)
vec3 result2 = vect.zyx; // result2 = (vect.z, vect.y, vect.x)
```

# Ins y Outs

Los _ins_ y _outs_ son la forma en la que los shaders se comunican entre sí.
Si definimos una variable que es un _out_ en un shader, esta variable será
accesible en el shader que se encuentre después de este siempre y cuando
la variable sea definida como un _in_, con el mismo tipo de dato y el mismo nombre.

## Entrada de vertices en el Vertex Shader

Algo peculiar sucede con el **vertex shader**. Sabemos por la _Graphics Pipeline_ que los vertices entran a **vertex shader** sin haber pasado por otro shader anteriormente. Por lo tanto, la entrada en este tipo de shader es un poco diferente.
Utilizamos `layout (location = 0)` para especificar el índice de la entrada de los vertices.
El número que vaya en location debe ser el mismo que el que se encuentra en el **vertex array object**, cuando lo especificamos con `glVertexAttribPointer`.

```cpp
//--------------------V ese 0 hace referencia al location
glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 6 * sizeof(float), (void*)0);
//------------------------V ese 0 también
glEnableVertexAttribArray(0);
```

Luego el shader sería así:

```cpp
#version 330 core
layout (location = 0) in vec3 aPos;
...
```

## Salida del color en el Fragment Shader

De manera peculiar también, para el **fragment shader** tenemos que especificar en la salida de este shader una variable _out_ de tipo `vec4`. Esta variable es la que se encarga de almacenar el color de cada pixel que se va a dibujar en la pantalla.

```cpp
#version 330 core
out vec4 FragColor;
...
```

# Uniforms

Las _uniform_ son otra forma de pasar información de la CPU a los shaders (que están en la GPU).
Las uniforms son globales, lo qe quiere decir que única por _shader program_ y puede ser accedida desde cualquier paso(shader individual) en este mismo.
Por ejemplo:

```cpp
#version 330 core

out vec4 FragColor;

// Le mandaremos información a esta variable desde la CPU
uniform vec4 thcolor;

void main() {
  FragColor = thcolor;
}
```

Luego en el código de OpenGL:

```cpp
glUseProgram(shaderProgram);
int vertexColorLocation = glGetUniformLocation(shaderProgram, "thcolor");
glUniform4f(vertexColorLocation, 0.0f, 0.5f, 0.0f, 1.0f);
```
