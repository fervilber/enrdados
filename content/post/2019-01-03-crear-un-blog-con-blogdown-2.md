---
title: Crear un blog con blogdown (2)
author: F.VilBer
date: '2019-01-03'
categories: [editar documentos]
tags:
  - blogdown
  - R
slug: crear-un-blog-con-blogdown-2
toc: true
---

## Problemas
He sufrido bastante hasta que la web del blog ha funcionado de manera fluída. Primero por la instalacion de HUGO en [Netlify](https://www.netlify.com/), y después con otros aspectos pequeños que provocaban errores.

Voy a air poniendo los errores que hacían petar el sistema:

## Errores con Git 
Para subir las cosas desde mi PC a gitHub en RSTUDIO, he usado siempre el entorno git que tiene por defecto, pero con blogdown he tenido muchos problemas y acabo haciendolo de manera manual desde `Terminal`.

Junto a la ventana de la consola de RSTUDIO se abre una pestaña nueva que dice *Terminal*. Es una terminal de línea de comandos de toda la vida, y podemos usarlo particularmente para todo el tema de git o versionado de los documentos del proyecto.

Si no has actualizado el origen git desde los menus de RSTUDIO, lo puedes hacer también desde este Terminal con el siguiente código:

    git init
    git remote add origin https://github.com/<NOMRE_USUARIO/<PROYECTO>.git

He comprobado que va mucho más rápido que hacerlo con Terminal que con la interface de RSTUDIO pues en mi caso se colapsa y da errores muchas veces al hacer un commit.

Cuando tengo un post nuevo o quiero actualizar la web de gitHub me pongo en Terminal y escribo lo siguiente:


```
git init
git add .
git commit -m "cambios de 4 de enero"
git push 
```

De esta forma me pide las contraseñas directamente en terminal en la conexión y va muy rápido.

## Problemas en los cambios de plantilla
Como he probado muchas plantillas, y algunas no funcionaban he tenido multiples problemas y descubrí que muchos se debían a que algunasplantillas añaden carpetas innecesarias que despues hay que borrar.

Al ejecutar el comando `blogdown::serve_site()` se crea siempre una nueva imagen completa del blog en la carpeta `public/`.
PAra evitar problemas cuando cambies de plantilla debes borrar completamente la carpeta public y revisar el resto de carpetas como *content* o *static* pues seguramente se habrán creado ficheros que eran específicos de la plantilla (theme) anterior y que provocan el fallo de renderizado.

## netlify variables
Ya expliqué en [Crear un blog con blogdown (1)](../crear-un-blog-con-blogdown-1) el problema con las versiones de HUGO que usa netlify, pues por defecto carga la versión 0.2 bastante antigüa. 

Aunque dí pronto con la solución, resulta que no me funcionaba porque al escribirla en netlify la ponía entr comillas, "0.43" en lugar de 0.43. Ese cambio hacía que no se ejecutase y direa un error en el deploy que tardé mucho en identificar.

