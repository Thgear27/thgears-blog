---
layout: ../../layouts/MarkDownPostLayout.astro
title: "Instalar fuentes en linux"
pubDate: 2023-08-26
description: "Cómo instalar fuentes en un sistema linux"
author: "Thgear27"
tags: ["Linux"]
---

En Linux, personalizar la apariencia del sistema operativo es una tarea accesible y versátil.
Una forma de lograrlo es mediante la instalación de fuentes tipográficas personalizadas.
Aunque existen interfaces gráficas para realizar esta tarea, la línea de comandos ofrece un enfoque rápido y eficiente para instalar fuentes en todo el sistema.
A través de unos simples pasos en la terminal, es posible agregar nuevas fuentes y hacerlas disponibles para diversas aplicaciones.
A continuación, se presenta un tutorial paso a paso sobre cómo instalar fuentes en Linux utilizando comandos de la línea de comandos (CLI).

## Instalación
En este caso vamos a estar instalando **Mononoki Nerd Font** desde la página de [nerdfont.com](https://www.nerdfonts.com/font-downloads)

- Descargamos la fuente y extraemos todos los archivos del _.zip_:
```bash
$ unzip Mononoki.zip 
Archive:  Mononoki.zip
  inflating: MononokiNerdFont-Regular.ttf  
  inflating: MononokiNerdFontMono-Regular.ttf  
  inflating: MononokiNerdFontPropo-Regular.ttf  
  inflating: MononokiNerdFont-BoldItalic.ttf  
  inflating: MononokiNerdFontMono-BoldItalic.ttf  
  inflating: MononokiNerdFontPropo-BoldItalic.ttf  
  inflating: MononokiNerdFontMono-Bold.ttf  
  inflating: MononokiNerdFontPropo-Bold.ttf  
  inflating: MononokiNerdFont-Bold.ttf  
  inflating: MononokiNerdFontMono-Italic.ttf  
  inflating: MononokiNerdFontPropo-Italic.ttf  
  inflating: MononokiNerdFont-Italic.ttf  
  inflating: LICENSE.txt             
  inflating: readme.md  
```

- Creamos la carpeta fonts dentro de `~/.local/share/` si no existe:
```bash
$ mkdir ~/.local/share/fonts
```

- Ubicados en el directorio donde se encuentran todos nuestros archivos con extensión _.ttf_
los copiamos todos en la ruta `~/.local/share/fonts`:
```bash
$ cp ./*.ttf ~/.local/share/fonts/
```

- Una vez hecho eso ahora solo es cuestión de actualizar la caché de fuentes de sistema con el siguiente comando:
```bash
$ fc-cache -f -v
```
> Para desinstalar la fuente solo tendrás que eliminar los archivos _.ttf_ de las fuentes y volver a actualizar la caché de fuentes del sistema.