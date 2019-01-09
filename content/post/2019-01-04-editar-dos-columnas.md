---
title: Documento a dos columnas
author: F.VilBer
date: '2019-01-04'
slug: editar-a-dos-columnas
categories:
  - editar documentos
tags:
  - pdf
  - columna
  - bookdown
toc: true
---


Suelo trabajar con unos modelos de proyecto tipo para generar documentos. Cuando inicio un nuevo proyecto los copio y cambio cosas sobre estas plantillas.

Hoy quería hacer un documento en formato `pdf` con salida en **dos columnas**. Es algo que ya había necesitado antes, así que me puse a buscar.

<!--more-->
![ejemplo de salida a dos col en pdf](/post/2019-01-04-editar-pdf-a-dos-columnas_files/dos_col.png)

Recuerdo que me costó unas horas de investigación en su día, pero di con la tecla y por eso lo pongo en el blog, para que a otros les sea más fácil.

## Antecedentes
Casi todos los documentos que hago en RSTUDIO y que requieren salida en papel o pdf los hago con la plantilla de [bookdown](https://bookdown.org/) a la que añado algunas cosas en *LaTeX*  si necesito configurar encabezados o pies de página.

## Salida a 2 columnas
Para hacer un documento a **dos columnas** hay que añadir simplemente dos líneas al encabezado *YAML* del fichero `index.Rmd`. El fichero `index.Rmd` es el inicio del libro (`bookdown`) y el que contiene la configuración general.

 * una para incluir el paquete de LaTeX *lipsum* `- \usepackage{lipsum}` y
 * otra para especificar las dos columnas con la marca de YAML: `classoption: twocolumn`

Quedaría algo así (ver última línea):
```
--- 
title: "Informe tipo 2"
author: "FVB (fervilber@gmail.com)"
date: "`r Sys.Date()`"
header-includes:
- \usepackage[utf8]{inputenc}
- \usepackage{graphicx}
- \usepackage{lipsum}
output: bookdown::pdf_book
documentclass: article
bibliography: [book.bib, packages.bib]
biblio-style: apalike
link-citations: yes
description: "Informe tipo 2"
lang: es
geometry: 
- tmargin = 2 cm
- bmargin= 3 cm
- lmargin= 2.5 cm
- rmargin= 2 cm
linkcolor: blue
urlcolor: blue
citecolor: red
mainfont: "Bookman"
papersize: a4
fontsize: 10pt
classoption: twocolumn
---
```

Con esto he conseguido un pdf a dos columnas como la imagen de arriba.

