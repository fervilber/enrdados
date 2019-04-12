---
title: Guía para imprimir en pdf desde R
author: F.VilBer
date: '2019-04-05'
slug: guia-para-imprimir-en-pdf-desde-r
categories:
  - diseño
  - editar documentos
  - markdown
  - R
tags:
  - R
  - tipografía
  - sintaxis
  - formato
description: 'Guía para imprimir documentos pdf de calidad con R y LaTeX'
thumbnail: '/img/pag_1.jpg'
math: true
toc: yes
---

He hecho una recopilación de indicaciones, trucos y preguntas para aprender a imprimir informes de calidad desde R con *RSTUDIO*, *knitr* y **LaTeX** . Creo que es un tema imprescindible para los científicos o ingenieros pues al final, nuestro trabajo debe ser plasmado en documentos y las posibilidades que ofrece R con *rmarkdown* y *LaTeX* son realmente sorprendentes.

[**LaTeX**](https://www.latex-project.org/) es el procesador de textos por excelencia, pero hemos de reconocer que no es fácil iniciarse, es un lenguaje de marcado poderoso, pero muy complejo, por eso es tan importante la aprición de otros lenguajes intermedios como *rmarkdown*, que simplifican las reglas de marcado y hacen de traductores. 

Con markdown solo es necesario aprender unas pocas reglas de marcado o etiquetas sencillas y eso nos permite dar formato complejo a cualquier documento. *Rmarkdown* fue, para mi, un gran descubrimiento, pues ponía al alcance del público generalista una manera fácil de dar formatos y calidad a los documentos aprovechando toda la fortaleza de editores poderosos como *LaTeX*.

Acostumbrado a los problemas de los procesadores de texto tipo windows como word, que eran una verdadera pesadilla para documentos grandes y complejos como un Proyecto de Construcción, el uso de markdown es una luz en el camino, lo que me sorprende es que no tenga mayor difusión y uso.

Tengo que confesar que hacer proyectos, pliegos, contratos o libros en el entorno de RSTUDIO con herramientas como `blogdown` o `bookdown`, o simplemente con *Rmarkdown* clásico es mucho más sencillo y tiene salidas gráficas e impresas de mucha mayor calidad que las obtenidas con microsoft word, y son ¡**herramientas gratuitas**!.

Comenzar es difícil, por eso voy a mostrar cómo hago yo los documentos, los trucos que uso para obtener *pdf* impresos de calidad excepcional.


## Herramientas para empezar
Es necesario que tengas instalado R, RSTUDIO y además alguna versión de *LaTeX* en tu PC. La recomendada por [Yihui Xie](https://bookdown.org/yihui/rmarkdown/installation.html) es *TinyTeX* pues se puede realizar desde el propio entorno de RSTUDIO de la siguiente manera:

```
  install.packages("tinytex")
  tinytex::install_tinytex()  # install TinyTeX
```
En mi caso, como ya usaba *LaTeX* hace años con el editor *LyX*, tengo instalada [MiKTeX](https://miktex.org/) con la que me apaño bien de momento.

Puede que tengas un lío de nombres en la cabeza (R, *rmarkdown*, *Knitr*, *pandoc*, *latex*..), pero es que hay un montón de lenguajes que se relacionan mutuamente y hace posible el flujo de documentación y su integración, la siguiente imagen lo describe muy bien:

![Proceso de creación de un documento](/img/knitr_pandoc.png)

Como ves una de las ventajas de todo esto, es que puedes mezclar código y programación con texto en un documento, y que además la salida final de ese documento puede ser en múltiples formatos, con un mismo fichero base. Es decir, que podrías sacar un fichero *pdf* hecho con LaTeX o un fichero de word con el mismo fichero inicial de *Rmarkdown*.

## Cómo se crea un pdf con R

Mirando el diagrama anterior nos vamos a centrar en la salida de documento *pdf*, y vemos que desde el fichero de *rmarkdown* al *pdf* hay 3 pasos que transforman internamente el fichero origen: *knitr* --> *pandoc* -->  *LaTeX*.

El flujo es el siguiente:

  - *knitr* transforma el código R en formato *markdown*, es decir inserta las imágenes generadas o las salidas de R en texto formateado etiquetado como *markdown*.
  - *pandoc* es el gran desconocido, pero es el corazón de la bestia, pues transforma el texto *markdown* en otro texto formateado según la salida final elegida (*pdf* LaTeX, word, html...). Para especificar el formato de salida metemos unos encabezados en el fichero rmarkdown que se llaman *YAML*.
  - *LaTeX* toma el código salida de *pandoc* y lo convierte en por ejemplo un fichero *pdf* completamente acabado. <a href="https://es.wikipedia.org/wiki/LaTeX" target="_blank">LaTeX</a> se ocupa de cosas como insertar un índice de capítulos o de figuras, meter el encabezado o pie de página, ordenar los párrafos con el borde, usar tipografía adecuada, ordenar las imagenes para que quepan en el documento, insertar un pie de imagen....
  
Vamos a centrarnos en la parte práctica de crear un pdf desde R y todo se reduce al encabezado **YAML** que cada fichero de *rmarkdown* lleva. 

Se trata de unas palabras-etiquetas que definen caracteristicas del documento y el tipo de salida, formatos etc.. 

El código YAML  va delimitado por 3 marcas de signo menos seguidas: `---`. Un ejemplo de encabezado YAML es el siguiente:

```
--- 
title: "Informe estado Obras"
author: 
- name: F.VilBer
- affiliation: Universidad de la vida
- name: M. López
  affiliation: Escuela terrenal
date: "`r format(Sys.time(), '%B %d, %Y')`"
abstract: "Este documento explica los principios de rmarkdown..."
keywords: "Informe de estado de las obras de .."
lang: es
documentclass: article
papersize: a4
output:
  pdf_document:
    latex_engine: xelatex
    lof: yes
    lot: yes
    number_sections: yes
    toc: yes
    toc_depth: 2
  html_document:
    fig_caption: yes
    theme: journal
    toc: yes
    toc_depth: 2    
---
```
## Parámetros YAML

En este encabezado que ponemos al principio de cada documento *rmarkdown* en RSTUDIO definimos las características de formato, mediante *etiquetas*. Un listado completo de las etiquetas disponibles lo vemos en la web oficial de <a href="https://pandoc.org/MANUAL.html#templates" target="_blank">pandoc</a>.

La mayoría de los parámetros se definen con su nombre: título, autor, fecha. Como vemos en el ejemplo anterior la fecha la escribimos directamente con código R, aunque se podría poner directamente con texto entrecomillado.

Si usamos nuestro amado idioma castellano, debemos especificar `lang: es` para que los capítulos e índices automáticos generados no sean en inglés. 

 - *papersize: a4* especifica el tamaño de la página final en este caso A4, el por defecto de *LaTeX* suele ser el americano `letter`. Cambiando a a5 por ejemplo y ejecutando *preview* nos transformaría el documento completo a una página más pequeña.
 - *documentclass:* especifica el tipo de documento. LaTeX tiene múltiples plantillas de documento, y normalmente se instalan desde el gestor de paquetes,(el *Package Manager* de *MiKTeX* por ejemplo). Los tipos de documento habituales son artículo, libro, carta. Son cambios importantes, por ejemplo el tipo libro `book` hace que cada capítulo vaya en una pagina nueva, mientras que con `article` va todo el texto seguido. También hay formatos de más versátiles predefinidos como el [koma-script](https://ctan.org/pkg/koma-script) muy completo y utilizado por la comunidad, pero que requiere su instalación en *LaTeX* previa.
 - *output:* este parámetro indica el tipo de documento de salida, si va a ser una web, un fichero word o un pdf. Para cada caso hay varias opciones de configuración, por ejemplo los parámetros `lof: yes` y `lot: yes` y `toc=yes` *que están indentados*, nos dicen que se generarán indices automáticos de figuras, tablas y de títulos en el documento *pdf* final. `number_sections: yes` indica que se numerarán los títulos automáticamente, y después decimos hasta qué profundidad de título saldrá en el índice `toc_depth: 2`.
 - `latex_engine: xelatex` esto si es importante, aquí indicamos el motor de *LaTeX* que vamos a usar para transformar el markdown simple en código *LaTeX*. El que viene por defecto con RSTUDIO es *pdflatex* y *xelatex*. Recomendamos encarecidamente usar este ultimo, pues pdflatex se ha quedado un poco desfasado y puede implicar algunos cambios respecto a lo que vamos a indicar en los siguientes aparatados.
 
Vamos a exponer más parámetros interesantes con los que definir al detalle nuestro documento en base al siguiente ejemplo:

 ```
--- 
title: "Loren Ipsum"
author: "FVB"
date: "`r format(Sys.time(), '%B %d, %Y')`"
classoption: oneside
keywords: "pandoc, r markdown, knitr"
geometry:
- top=1in
- bottom=1in
- right=0.5in
- left=1in
graphics: yes
lang: es
link-citations: yes
linkcolor: red
documentclass: scrartcl
papersize: a5
citecolor: blue
urlcolor: blue
fontsize: 11pt
fontfamily: bookman
header-includes:
- \usepackage{graphicx}
- \usepackage{fancyhdr}
- \pagestyle{fancy}
- \fancyhead[L]{\slshape \rightmark}
- \fancyhead[R]{\includegraphics[height=1cm]{logo1.jpg}}
- \fancyfoot[R]{\textit{\scriptsize www.enrdados.net }}
- \fancyfoot[C]{\thepage}
- \renewcommand{\headrulewidth}{0.2pt}
- \usepackage{floatpag}
- \floatpagestyle{empty}
output:
  pdf_document:
    latex_engine: xelatex
    lof: yes
    lot: yes
    number_sections: yes
    toc: yes
    toc_depth: 2
---
```

## Una o dos caras
 
Con el parámetro `classoption:` indicamos si el documento final será a una cara `oneside`  o a dos `twoside`.
 
Lo importante de poner a dos caras es que los márgenes, así como los encabezados pueden cambiar en este caso si la página es par (Even) o impar (Odd). 

## Márgenes
Se especifican con el parámetro `geometry`, y los valores se pueden dar en cm o en pulgadas (in). Si el documento es a dos caras, y los márgenes no son simétricos, el pdf se verá claramente como cambian estos de una pagina par a impar. 

Si todos los márgenes son iguales podemos escribir todo así en una sola línea: `geometry: "margin=2cm"`

```
geometry:
- top=1in
- bottom=1in
- right=0.5in
- left=1in

```
## Tipo de letra
Esto del tipo de letra que usa LaTeX es un poco quebradero de cabeza, pues no valen los mismos tipos de letra del sistema en windows. Solo se pueden especificar aquellos que estén en LaTeX instalados. Normalmente son archivos *.sty que se instalan con el desarrollo de LaTeX.

Otra opción es meterlo directamente en el encabezado `header-includes:` con código latex directo, pero siempre es más fácil así. Debes revisar tus opciones de letras en LaTeX, normalmente los nombres siguientes son válidos: *palatino, helvet, utopia, times, pifont, courier, bookman, newcent, newcent, avant*.

```
  fontsize: 11pt
  fontfamily: bookman
```

También se puede especificar el tipo de letra según el uso:

 - mainfont: letra del texto y párrafos normales
 - sansfont: es la letra usada para textos del tipo *sans serif* sin terminaciones do de palo seco.
 - monofont: es el tipo de letra usado en los chunk de código

```
  mainfont: LiberationSerif
  sansfont: LiberationSans
  monofont: LiberationMono
```

## Personalizar encabezados y pies de página
Mucha gente que usa RSTUDIO y ha probado las salidas en formato pdf me decía que quedaban documentos de gran calidad, pero que no podían insertar pies de página o encabezados personalizados. 

Es cierto que markdown es un lenguaje de marcado simple y no llega a hacer cosas complejas, pero para eso está LaTeX, para las cosas complejas. Por lo que si queremos meter encabezados y pies de página debemos recurrir a insertar código latex directamente, en el apartado YAML tras el parámetro `header-includes:`.

Lo que pongamos a continuación será código latex puro y por ejemplo con las siguientes líneas definiremos un encabezado y pie de pagina bastante complejo.

```
header-includes:
- \usepackage{graphicx}
- \usepackage{fancyhdr}
- \pagestyle{fancy}
- \fancyhead[L]{\slshape \rightmark}
- \fancyhead[R]{\includegraphics[height=1cm]{logo1.jpg}}
- \fancyfoot[R]{\textit{\scriptsize www.enRdados.net }}
- \fancyfoot[C]{\thepage}
- \renewcommand{\headrulewidth}{0.2pt}
- \usepackage{floatpag}
- \floatpagestyle{empty}
```
Lo primero que hace es cargar las librerías de latex *graphicx*, para insertar una imagen y *fancyhdr* para la gestión de encabezados y pies.

Después define el estilo de pagina *fancy*, que es el que permite personalizar todo. A parir de aquí es cuando definimos el contenido, primero del encabezado con `\fancyhead[L]{\slshape \rightmark}` que nos dice que en el encabezado izquierdo [L] va la numeración y el nombre del capítulo en el que esté la página.

Con esto `\fancyhead[R]{\includegraphics[height=1cm]{logo1.jpg}}` indicamos que en el lado derecha [R] de right del encabezado vamos a insertar una imagen correspondiente al fichero `logo1.jpg` que está en el mismo directorio que el fichero rmarkdown . Además la altura de la imagen en la pagina será de 1 cm.

Con `\fancyfoot[R]` definimos el pie de página derecho, en el que se pone un link a nuestra web y en otra linea decimos que en el centro del pie de pagina ponga la numeración de la página `\thepage`.

La separación entre el texto de página y el encabezado se define como una linea fina 0,4 de grueso mediante el comando: `\renewcommand{\headrulewidth}{0.2pt}`. En este caso no hay linea en el pie de página, pero si quisiéramos poner una de separación lo haríamos con: `\renewcommand{\footrulewidth}{0.4pt}`.

En documentos a doble cara, podemos especificar incluso diferentes encabezados para las páginas par o impares simplemente indicándolo en la definición. Recordemos que en inglés par es even e impar odd, por lo que izquierda de pagina par es `[LE]`, centro de pagina impar `[CO]`. Ejemplo:

```
\fancyhead[LE,RO]{\slshape \rightmark}
\fancyhead[LO,RE]{\slshape \leftmark}
\fancyfoot[C]{\thepage}
```
## Ejemplo

Os dejo en imagen algunas paginas del documento *pdf* generado de ejemplo con un encabezado YAML como el descrito en apartados anteriores e impreso en A5, el indice se crea automáticamente y como vemos los encabezados y pies van acordes con los capítulos:


![indice](/img/pag_1.jpg)

![Primera página](/img/pag_2.jpg)

![página 5 del artículo](/img/pag_5.jpg)


## LaTeX puro

Los documentos *rmarkdown* admiten código *LaTeX* directo. El código LaTeX va precedido de la barra inclinada `\` y a continuación la etiqueta correspondiente. 

Este código se puede introducir en cualquier parte del documento, y complementa al que metemos en el encabezado YAML  que va dentro de `header-includes`como vimos antes.

Por ejemplo si después del encabezado *YAML*  y antes de comenzar con el texto escribimos lo siguiente, crearemos una página inicial solo con el título y meteremos **manualmente** un índice de figuras y de títulos:

```
<!-- pag sin encabezado para portada  -->
\begin{titlepage}
    \begin{center}
        \begin{LARGE}
            \textsc{INFORME DE LUCES NOCTURNAS \\
            JUNIO 2017 \\ }
            \textsc{--- \\
            DATOS DEL INSTUTO DE LUZ MURCIANO}
        \end{LARGE}
        \textsc{ \\}
        \textsc{ \\}
        \includegraphics[width=0.8\linewidth]{imag/luces.jpg}
    \end{center}
    \begin{large}
        \textit{-Informe análisis de luces nocturnas-}
    \end{large}
    \begin{flushright}
        \textit{F.Villalba \\
        desde Cieza, Murcia, España \\
        }
    \end{flushright}
\end{titlepage}

<!-- pag sin encabezado para TOC -->
\begin{titlepage}
    \tableofcontents
\end{titlepage}

<!-- pag sin encabezado para TOC de figuras -->
\begin{titlepage}
    \begin{scriptsize} 
        \listoffigures
    \end{scriptsize} 
\end{titlepage}

\pagebreak
```

Veamos algunas expresiones de LaTeX:

  - `\pagebreak` incluye un salto de página.
  - `\newpage` y `\clearpage` meten una nueva pagina en blanco
  - `\\*` inserta un salto de línea y rohibe el salto de pagina posterior.
  - `\hfill\break` inserta un salto de línea.
  - `\includegraphics[width=3cm, height=4cm]{imag/universe.jpg}` inserta una imagen, para ello antes debe cargarse el paquete `\usepackage{graphicx}`
  -  insertar referencias cruzadas.  `\label{fig:imagen_1}` y después si queremos hacer referencia a esta imagen hacemos:  `\ref{fig:imagen_1}`.
  
Las medidas pueden darse en unidades o usar referencias como:

  * `\columnsep`	distancia entre las columnas
  * `\columnwidth`	anchura de una columna
  * `\linewidth`	amplitud de línea en el ambiente actual
  * `\paperwidth`	anchura del papel
  * `\paperheight`	altura del papel
  * `\textwidth`	anchura del texto
  * `\textheight`	altura del texto
  * `\unitleght`	unidades de longitud en el ambiente picture

Quizás nos interese también conocer el paquete *fancy* que es el que permite en LaTeX hacer los encabezados y pies de página de los documentos, la información completa está aquí ( 
<a href="https://osl.ugr.es/CTAN/macros/latex/contrib/fancyhdr/fancyhdr.pdf" target="_blank">fancyhdr</a> ) en resumen:

  * `\thepage` el número de la página
  * `\chaptermark` el título del capítulo
  * `\chaptername` el nombre "Capítulo", (chapter en ingles) el literal no el titulo
  * `\thechapter` el número del capítulo
  * `\leftmark`y `\rightmark` contienen las referencias últimas al capítulo, que van cambiando a medida que avanza el documento. 
