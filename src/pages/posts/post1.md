---
layout: ../../layouts/MarkDownPostLayout.astro
title: "Proceso de Compilación en C++"
pubDate: 2023-08-17
description: "Explicación sencilla sobre cómo funciona el proceso de compilación en un programa en C++"
author: "Thgear27"
tags: ["c++"]
---

Cuando empezamos con C++, una de las cosas que nos puede llegar a generar bastante confusión es el proceso de compilación. Cuando tenemos más de un archivo de código fuente, entender este mismo es fundamental para poder encontrar errores y estructurar mejor nuestros programas. En este post voy a explicar cómo funciona el proceso de compilación (lo suficiente para trabajar con más de un archivo de código fuente).

# El Proceso de Compilación

La compilación es básicamente traducir el código fuente escrito en C++ en lenguaje máquina. Dicho lenguaje máquina es el que la computadora va a "entender" y ejecutará en última instancia. Muchas veces se confunde **El proceso de compilación** con la **compilación**. Parece tonto pero en realidad es importante hacer esta distinción. Cuando hablamos de **Proceso de compilación** nos referimos a la generación del ejecutable, el producto final vaya. Cuando hablamos de **compilación** nos referimos simplemente a la traducción del código fuente en C++ a código ensamblador o código máquina.
Para evitar confusiones con los nombres, pasaré a nombrar al proceso de compilación como _generación del ejecutable_ en ciertas ocasiones.

Vamos a ver un ejemplo de un programa en C++ y a partir de ahí iremos analizando cada paso se se sigue para llegar a tener nuestro ejecutable.
Tenemos el siguiente programa en nuestro archivo `main.cpp`:

```cpp main.cpp
#include <iostream>

int main() {
   std::cout << "Hola mundo" << std::endl;
   return 0;
}
```

Ahora en nuestra terminal deberemos ejecutar el siguiente comando:

```bash
 g++ -save-temps main.cpp -o ejecutable
```

Básicamente lo que hace dejarnos nuestro ejecutable y los archivos intermedios utilizados durante el _proceso de compilación_

Inténtalo, te debería dejar los siguientes archivos:

```bash
$ ls -la
drwxr-xr-x 8 thgear thgear   4096 ago 16 21:12 .
drwxr-xr-x 8 thgear thgear   4096 ago 16 14:45 ..
-rwxr-xr-x 1 thgear thgear  15960 ago 16 21:12 ejecutable
-rw-r--r-- 1 thgear thgear 906842 ago 16 21:12 ejecutable-main.ii
-rw-r--r-- 1 thgear thgear   2128 ago 16 21:12 ejecutable-main.o
-rw-r--r-- 1 thgear thgear   1341 ago 16 21:12 ejecutable-main.s
-rw-r--r-- 1 thgear thgear     84 ago 16 21:08 main.cpp
```

## Preprocesador

El **preprocesador** es la fase inicial del proceso de compilación, nuestro archivo `main.cpp` no está preparado aún para poder ser traducido a código máquina porque no sabe que cosa son las líneas que empiezan por **`#`**.
El preprocesador se encarga de resolver todas las _directivas de preprocesador_ en nuestro programa.
Esas directivas son todas aquellas que empiezan con un **`#`**.
Cada directiva tiene una función en específico, entre ellas tenemos a la **inclusión de archivos**, **definición de macros**, **directivas condicionales**, etc.
No es mi objetivo hablar de las directivas de preprocesador en este post, básicamente lo que hacen es determinar lo que se va a compilar y lo que no se va a compilar, así de simple.

Por ejemplo, la directiva que utilizamos de `#include <iostream>` simplemente lo que hace es irse al archivo llamado `iostream`, _copiar_ los contenidos de dicho archivo y pegarlos dentro de nuestro `main.cpp` justo donde está la directiva, ¿Nada más simple que eso no?.

Abre el archivo `ejecutable-main.ii` con el editor de código de tu preferencia. Te darás cuenta de que tiene una cantidad de línea de código demasiado grande para lo que es nuestro programa, un simple hola mundo.
Son alrededor de 36000 líneas de código. ¿Cómo es eso posible?
Bueno, ya te dije que `#include` copia y pega los contenidos del archivo que está siendo incluido, entonces, si dicho archivo tiene más directivas de preprocesador (y vaya que _iostream_ las tiene), esas directivas también tiene que ser resueltas, terminando en un archivo muy grande.

## La Compilación

Bueno ya tenemos nuestro archivo con todas las directivas de preprocesador resueltas, lo siguiente que sucede es _la compilación_ en sí.
Se pasa de nuestro archivo `ejecutable-main.ii` a el archivo intermedio `ejecutable-main.s`.
Este archivo contiene instrucciones en ensamblador, dicho ensamblador dependerá de la arquitectura de la máquina en la cual está compilando. Ojo que cada archivo `.cpp` se compila por separado e independientemente de los demás.
Intenta ver los contenidos `ejecutable-main.s`. No es tan grande así que mostraré los contenidos en mi caso:

```asm
	.file	"main.cpp"
	.text
#APP
	.globl _ZSt21ios_base_library_initv
	.section	.rodata
.LC0:
	.string	"Hola"
#NO_APP
	.text
	.globl	main
	.type	main, @function
main:
.LFB1982:
	.cfi_startproc
	pushq	%rbp
	.cfi_def_cfa_offset 16
	.cfi_offset 6, -16
	movq	%rsp, %rbp
	.cfi_def_cfa_register 6
	leaq	.LC0(%rip), %rax
	movq	%rax, %rsi
	leaq	_ZSt4cout(%rip), %rax
	movq	%rax, %rdi
	call	_ZStlsISt11char_traitsIcEERSt13basic_ostreamIcT_ES5_PKc@PLT
	movq	_ZSt4endlIcSt11char_traitsIcEERSt13basic_ostreamIT_T0_ES6_@GOTPCREL(%rip), %rdx
	movq	%rdx, %rsi
	movq	%rax, %rdi
	call	_ZNSolsEPFRSoS_E@PLT
	movl	$0, %eax
	popq	%rbp
	.cfi_def_cfa 7, 8
	ret
	.cfi_endproc
.LFE1982:
	.size	main, .-main
	.section	.rodata
	.type	_ZNSt8__detail30__integer_to_chars_is_unsignedIjEE, @object
	.size	_ZNSt8__detail30__integer_to_chars_is_unsignedIjEE, 1
_ZNSt8__detail30__integer_to_chars_is_unsignedIjEE:
	.byte	1
	.type	_ZNSt8__detail30__integer_to_chars_is_unsignedImEE, @object
	.size	_ZNSt8__detail30__integer_to_chars_is_unsignedImEE, 1
_ZNSt8__detail30__integer_to_chars_is_unsignedImEE:
	.byte	1
	.type	_ZNSt8__detail30__integer_to_chars_is_unsignedIyEE, @object
	.size	_ZNSt8__detail30__integer_to_chars_is_unsignedIyEE, 1
_ZNSt8__detail30__integer_to_chars_is_unsignedIyEE:
	.byte	1
	.ident	"GCC: (GNU) 13.2.1 20230801"
	.section	.note.GNU-stack,"",@progbits

```

## El Ensamblador

Bien, ahora viene la parte del ensamblador, nuestro archivo con instrucciones en ensamblador se convierte en `ejecutable-main.o`.
Este archivo es un archivo de código objeto.
Contiene código directamente en código máquina aunque aún no está listo para ser ejecutado.
Si intentas ver los contenidos del archivo `ejecutable-main.o` lo más probable es que no entiendas nada, no tienes por qué, es código máquina.
Cada archivo con extensión _.o_ (archivos objetos) tiene información de cada función y fragmento de nuestro programa y es más probable que trabajemos con estos archivos _.o_ si queremos dividir nuestro programa en varios archivos, eso lo veremos más adelante en este post.

## El Linker

La parte final del **proceso de compilación** es el linkado o enlazado.
El linker se encarga de combinar todos los archivos objeto y cualquier dependencia externa (si tuviéramos) para generar un solo ejecutable. El linker también evita que existan más de una implementación de una función en el programa y resuelve las referencias entra diferentes símbolos y funciones, seguro que alguna vez te has topado con algún error de "referencia no encontrada" o "definiciones múltiples", bueno, pues déjame decirte que ese error un error del linker y si llegas a ese punto, la compilación independiente de cada archivo _.cpp_ ya pasó.

Si ejecutamos el programa, tendremos la siguiente salida, todo ha salido bien, el compilador `g++` ha hecho su trabajo.

```bash
$ ./ejectuable
Hola Mundo
```

# Trabajar con más de un archivo

Para trabajar con más de un archivo vamos a usar todos los conceptos que hemos visto hasta ahora. Vamos a compilar un proyecto mio desde cero con la terminal. Creo que será mejor tener un ejemplo práctico que explicarte como sería hipotéticamente con archivos de prueba. De todas formas, el proyecto que te muestro a continuación es muy sencillo, no utiliza ninguna dependencia y solo tiene archivos _.cpp_ de entrada, justo lo que hemos visto. Vamos por el ejemplo.

- Clona el respositorio de mi proyecto (Se demorará un poco, la carpeta _Renders_ pesa mucho, no sabía de la existencia de .gitignore en ese entonces)

```bash
git clone https://github.com/Thgear27/Simple-3D-renderer.git
```

Dentro de la carpeta `src` encontrarás el código fuente del proyecto. Hay 7 archivos con extensión _.cpp_ y otros 7 archivos con extensión _.hpp_.
Los archivos con extensión _.hpp_ funcionan iguales que como funciona el archivo `iostream` por poner un ejemplo. Son archivos que serán incluidos con las directivas de preprocesador.
Dichos archivos no contienen las implementaciones de funciones si no solo declaraciones por adelantado.
Si en los archivos _.hpp_ o _.h_ hubieran definiciones de funciones, sería un problema porque en el momento en el que 2 archivos _.cpp_ incluyan a dicho archivos _.hpp_, al momento de _generar el ejecutable_, el linker se quejaría diciendo que hay _múltiples definiciones_.

Bien, ahora haremos lo siguiente:

- No dirigimos al directorio `src`

```bash
cd src
```

- Para cada archivo _.cpp_ ejecutaremos `g++ -c <nombre-del-archivo>.cpp -o <nombre-del-archivo>.o`, te dejo los comandos para que los copies y los pegues simplemente:

Lo que hace la opción **-c** es decirle a g++ que solo compile el archivo y que no intente generar ningún ejecutable a partir de él. La opción **-o** le especifica el nombre del archivo de salida.

```bash
g++ -c main.cpp -o main.o
g++ -c math.cpp -o math.o
g++ -c matrix.cpp -o matrix.o
g++ -c model.cpp -o model.o
g++ -c my_gl.cpp -o my_gl.o
g++ -c Renderer.cpp -o Renderer.o
g++ -c tgaimage.cpp -o tgaimage.o
```

- Una vez tengas todos los archivos _.o_. Solo faltaría enlazarlos, para ello ejecuta el siguiente comando en la terminal:

La opción **-o** especifica el nombre del archivos de salida que en este caso tiene que estar ubicado en la carpeta raíz del proyecto, tiene como entrada todos los archivos objeto, por eso el _"\*.o"_.

```bash
g++ -o ../ejecutablefinal *.o
```

- Al final podemos ver que nuestro ejecutable en nuestra carpeta raíz:

```terminal
$ ls -l
total 2012
-rwxr-xr-x  1 thgear thgear  132104 ago 17 10:42 ejecutablefinal <-- Nuestro ejecutable
-rw-r--r--  1 thgear thgear    6809 ago 16 22:36 Makefile
drwxr-xr-x  8 thgear thgear    4096 ago 16 22:36 obj
-rw-r--r--  1 thgear thgear    1027 ago 16 22:36 README.md
drwxr-xr-x 10 thgear thgear    4096 ago 16 22:36 renders
drwxr-xr-x  2 thgear thgear    4096 ago 17 10:41 src
-rw-r--r--  1 thgear thgear  867934 ago 16 22:36 test3.tga
-rw-r--r--  1 thgear thgear 1025897 ago 17 10:47 test6.tga
```
- Si ejecutas el programa te dejará un archivo llamado `test3.tga`. Es una imagen de un formato antiguo, si quieres verla puedes usar esta página: [http://schmittl.github.io/tgajs/](http://schmittl.github.io/tgajs/)

## Resumen

Para compilar un programa en C++ necesitamos saber que existen diferentes fases, la primera de ellas es la del **preprocesador**, en ella se resuelven todas las directivas que empiecen con un **#**.
Luego tenemos la **compilación**, en el cual podemos agrupar también al ensamblado, cada archivo _.cpp_ es compilado independientemente y nos deja un archivo objeto con extensión _.o_.
Luego es el **linker** el que se encarga de unir todos los archivos objeto para poder así generar nuestro ejecutable.
