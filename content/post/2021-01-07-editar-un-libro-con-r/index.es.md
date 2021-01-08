---
title: Editar un libro con R
author: F.VilBer
date: '2021-01-07'
slug: []
categories:
  - editar documentos
  - diseño
  - R
tags:
  - bookdown
  - formato
  - pdf
toc: true  
description: ''
thumbnail: '/post/2021-01-07-editar-un-libro-con-r/index.es_files/libro_abuelo.jpg'
---

El mes pasado (dic-2019) me dio por zanjar uno de mis objetivos del año, o más bien de los últimos años, que había estado retrasando por pura vagancia (procrastinación para los anglofilos), y es que llevo tiempo detrás de sacar un libro que ya estaba escrito, pero faltaba editar, componerlo y mandar a imprimir los ejemplares. 

Se trata de una historia familiar, y gracias a este trabajo navideño he tenido - otra vez - que ponerme las pilas en cuestión de edición de documentos largos en R (tipo tesis) y el resultado ha sido muy satisfactorio.

Como es un tema que a muchos os puede interesar, voy a resumir el proceso, los trucos y el código que he usado para hacer un libro solo con RSTUDIO. ¡Ojo! un libro de __literatura__, aunque al fin y al cabo, sea prosa o código lo que importa es el proceso. 

## Paso 1. Escribir el libro
Lo primero es escribir el libro y para esto nada mejor que empezar con un simple documento markdown o `*.Rmd` desde RSTUDIO. 

La sintaxis *Rmarkdown* es muy simple y potente, básicamente sólo debes poner una almohadilla `#` delante del título del capítulo y escribir en testo plano el resto. 
Puedes ver más cosas de la sintaxis RMarkdown [aquí](../mdbasic/). También he escrito varios artículos en el blog relacionados para ayudar a sacar documentos de calidad usando R:

 * [tinytex tener Latex desde R sin esfuerzo](../tinytex-tener-latex-desde-r-sin-esfuerzo)
 * [Editar documentos pdf a dos columnas](../editar-a-dos-columnas)
 * [Informes html con Rmarkdown](../informes-html-con-rmarkdown)
 
Si nos centraremos en un documento tipo libro, con cientos de páginas, figuras y texto organizado por capítulos lo recomendable es usar la librería [bookdown](https://bookdown.org/) junto con [TinyTeX](https://yihui.name/tinytex/). Casi todos los documentos con salida impresa en papel los realizo en RSTUDIO como *pdf* a partir de la plantilla por defecto de *bookdown* que puedes descargar en (<https://github.com/rstudio/bookdown>). La descomprimes en una carpeta y abres el proyecto de ejemplo en RSTUDIO.

Esta librería hace muchas cosas, pero lo básico es que junta en un mismo *pdf* final los documentos `*.Rmd` que encuentra en la carpeta raíz del proyecto por orden alfabético según sus nombres de fichero. Empieza con el fichero `index.Rmd`, y después añade los `*.Rmd` que encuentra. 

Las características generales de impresión las definimos en un encabezado YAML en el fichero `index.Rmd`. Esto nos permite que una vez escrito el libro puedo dedicar un tiempo a probar diferentes estilos, índices, pies o encabezados de página sin apenas modificar el documento bruto, tan solo tocando cosas en la configuración que se guarda en `index.Rmd`.

Vamos a ver un caso práctico.

### Ejemplo práctico

Voy a hacer un libro de ejemplo, para ello he descargado un texto sin ningún tipo de licencia o derechos pues se escribió hace 2000 años. Se trata del texto *Meditaciones* de *Marco Aurelio*, el emperador romano del siglo II conocido por ser además filósofo y ejemplo del **estoicismo**.

El libro original lo he descargado en texto plano y consta de 12 capítulos, pero además yo quiero añadir un prólogo y una breve biografía del Emperador antes del propio libro y al final otro capítulo de bibliografía y glosario de términos.

### Empecemos

Instalo en RSTUDIO la librería `bookdown` con `install.packages('bookdown')`, y abro el ejemplo base. Este contiene varios ficheros `*.Rmd` numerados. 

Como mi libro va a ser muy simple, me quedo solo con los ficheros `index.Rmd`, `01-intro.Rmd` y otro al que renombro `02-libro.Rmd` que contendrá el texto bruto descargado y borro los demás `*.Rmd`.

## Paso 2. Formato general

El archivo `_bookdown.yml` contiene algunas generalidades del libro final, como el *nombre de fichero final* que va a tener el archivo y que se generará en la carpeta `_book`. Si lo abrimos en RSTUDIO podemos editarlo: 

```
book_filename: "libro_meditaciones"
delete_merged_file: true
language:
  label:
    fig: 'Figura '
    tab: 'Tabla '
  ui:
    chapter_name: "Capítulo "
```

El libro lo he copiado directamente en el fichero `01-libro.Rmd`. Además como cada capítulo empieza con la palabra *LIBRO* me ha llevado poco tiempo buscar el comienzo de cada uno y añadir el símbolo de Rmarkdown de capítulo `#`. Si tuviese subcapítulos marcaríamos con doble `##`, y además podemos usar todo el *markdown* que conocemos o incluso meter chunks de código, aunque en este caso es solo literatura con alguna nota al pie que se ponen así `como meter una nota al pie^[Esto aparece en el pie de pagina]`.

![vista edicion libro](/post/2021-01-07-editar-un-libro-con-r/index.es_files/02_libro.png)

Simplemente con esto, podemos ejecutar el comando para imprimir libro de `bookdown` que es este: `bookdown::render_book('index.Rmd', bookdown::pdf_book(keep_tex = TRUE)) ` y nos generaría en el directorio `\_book` el pdf resultado con los parámetros por defecto. En este comando le ponemos que el primer fichero es el `index.Rmd` y nos da como resultado un pdf asi:

![Libro base](/post/2021-01-07-editar-un-libro-con-r/index.es_files/libro0_1.png)

Ha metido además una página simple de portada con el título y autor y un índice completo en las primeras páginas.
El formato por defecto del libro incluye unos encabezados de este estilo:

![Encabezado de pagina tipo báscia](/post/2021-01-07-editar-un-libro-con-r/index.es_files/encabezados.png)

Los parámetros básicos se los decimos en el encabezado YAML del primer fichero, esto es en `index.Rmd` y en nuestro caso he usado esto:

```
---
title: "Meditaciones"   
author: "Marcus Aurelius"
date: "Siglo II. d.c"
documentclass: book  # tipo documento: book, article
bibliography: [book.bib]  # para usar referencias bibliográficas
biblio-style: apalike
link-citations: yes
description: "Meditaciones"
lang: es
papersize: a5
fontsize: 11pt # tamaño puede ser 10, 11 o 12pt solo
fontfamily: avant # palatino, helvet, utopia, times, pifont, courier, bookman, newcent y avant
classoption: oneside # una cara
openright: yes # los capitulos siempren empiezan a la derecha en pag nueva
geometry: 
- tmargin=2.5cm
- bmargin=2.5cm
- lmargin=2.5cm
- rmargin=2cm
output:
  pdf_document: 
#    number_sections: no
#    df_print: kable
---
```

En el mismo fichero `index.Rmd` y tras el encabezado puedo escribir el prólogo como un capítulo más.

## Paso 3. Personalizar la salida

Para muchos será suficiente la salida y formatos por defecto, pero lo normal es que uno quiera poner algo especial, alguna cosa diferente y aquí la única forma es con *LaTeX*. 

*LaTeX* es el lenguaje de marcado definitivo de los editores, especialmente diseñado para textos y en realidad *Rmarkdown* y `bookdown` solo hacen de capas traductoras a *Latex*.

Para añadir cosas concretas podemos insertar código *Latex* directamente en el documento, pero para añadir formatos generales hay que hacerlo en el preámbulo, en la zona del encabezado *YAML*, vamos a ver varios ejemplos que lo entenderemos mejor.

### Preámbulo Latex

Para cambios generales debemos poner en el encabezado *YAML* de `index.Rmd` -encima de todo-, pero dentro de la marca de YAML `---` un preámbulo con el código *LaTeX* de los cambios, por ejemplo el siguiente sirve para cambiar el formato de los títulos de capítulo con la librería `fncychap` de *LaTeX* que tiene varias opciones distintas que pongo en comentarios:

```
---
header-includes:
- \usepackage[utf8]{inputenc} #para usar accentos
# Formato de titulo de capitulo
# Options: Sonny, Lenny, Glenn, Conny, Rejne, Bjarne, Bjornstrup
- \usepackage[Lenny]{fncychap}

...

---

```

El nuevo resultado al ejecutar en la consola el comando: `bookdown::render_book('index.Rmd', bookdown::pdf_book(keep_tex = FALSE))` es que cambia el formato de los capítulos a este:

![Nuevo formato capitulos](/post/2021-01-07-editar-un-libro-con-r/index.es_files/libro0_1_t2.png)

En este preámbulo podemos añadir muchas cosas, en *LaTeX*, aunque muchas están ya traducidas en bookdown y las hemos puesto en el YAML como el idioma, tamaño de letra, de página, geometría de la página etc. Por ejemplo es equivalente poner en el YAML: `lang: es` o en el preámbulo: `- \usepackage[spanish, es-tabla]{babel}`.

Voy a poner un ejemplo completo de encabezado YAML de `index.Rmd`, con preámulo LaTeX. Las líneas comentadas con `#` no se ejecutan, y sirven de comentarios o ejemplos de cambios. No voy a detallar el código completo, pero se trata de personalizar el encabezado, pie de página y título de capítulos. Otros comandos como `\AtBeginDocument{\renewcommand{\contentsname}{Índice}}` cambian el nombre por defecto del índice, lista de figuras, lista de tablas o pies de foto.

Uso comandos *LaTeX* sobre los que debes investigar por tí mismo, si quieres profundizar lee estos [apuntes de LaTeX](http://metodos.fam.cie.uva.es/~latex/apuntes/apuntes4.pdf) que aclaran muchos conceptos.

```
header-includes:
- \usepackage[utf8]{inputenc} #para usar accentos
- \usepackage{graphicx}
- \usepackage[labelfont=bf]{caption}
- \usepackage{float}
- \usepackage{afterpage} # agrega opcion de insertar página en blanco
## Formato de titulo de capitulo
- \usepackage{titlesec, blindtext, color}
- \definecolor{gray75}{gray}{0.75}  # define un estilo de color
- \newcommand{\hsp}{\hspace{20pt}}  # define un espacio fijo
# Define el formato de capitulo 
- \titleformat{\chapter}[hang]{\huge\bfseries}{\textcolor{gray75}{$\blacksquare$}\hsp}{0pt}{\huge\bfseries\MakeUppercase}
- \setcounter{secnumdepth}{0} #quita la numeración de capitulos
#- \setlength{\parindent}{1.5em} # indentacion de párrafos en la primera linea
- \addtolength{\headheight}{3pt}
## Formato encabezado y pie de paginas:
- \usepackage{fancyhdr} # para insertar encabezados y pies de pag personalizados
- \pagestyle{fancy} # establece estilo personalizado de encabezados
- \renewcommand{\chaptermark}[1]{\markboth{#1}{}} # para quitar num cap en leftmark
#- \renewcommand{\sectionmark}[1]{\markright{\arabic{section}.\#1}} # para quitar num cap en leftmark
- \renewcommand{\headrulewidth}{0.5pt} # añade línea de arriba en encabezado
- \renewcommand{\footrulewidth}{0.5pt} # linea abajo en pie de pag
#     thesection= num capitulo
#     leftmark= nombre capitulo completo
#     rightmark = nombre subcapitulo
- \lhead{\scriptsize \rightmark} # marca de subcapitulo en la parte izq arriba de encabezado
#- \chead{} # zona centro encabezado
- \rhead{\scriptsize \leftmark} 
#- \lfoot{\textit{www.gritos.org}} # zona izq pie pag
#- \cfoot{\includegraphics[height=0.08\textwidth]{imag/logo1.jpg}} # zona centro pie pag incluye imagen
#- \rfoot{\thepage}
## Formato nombres genericos de listas automáticas
## No activar babel pues coge los del idioma entonces
- \AtBeginDocument{\renewcommand{\contentsname}{Índice}}
- \AtBeginDocument{\renewcommand{\listfigurename}{Lista de figuras}}
- \AtBeginDocument{\renewcommand{\listtablename}{Lista de Tablas}}
- \AtBeginDocument{\renewcommand{\figurename}{Fig. }}
- \AtBeginDocument{\renewcommand{\tablename}{Tabla. }}
- \AtBeginDocument{\renewcommand{\chaptername }{Capítulo }}
title: "Meditaciones"   
author: "Marcus Aurelius"
date: "Siglo II. d.c"

...sigue con el YAML ...
---

```
Con este código básicamente conseguimos cambiar los encabezados y pies de página a como se muestra en la siguiente imagen. Verás que hay una línea que separa encabezado y pie y otros cambios.

![Cambios](/post/2021-01-07-editar-un-libro-con-r/index.es_files/encabezados2.png)

### Capítulos no numerados

La numeración romana que ves en la imagen anterior, solo aparece en las páginas de los capítulos que no se numeran. En este libro, los capítulos de *Prólogo* y *Biografía* primeros quiero que no se numeren, que sean  especiales, también los últimos capítulos *Bibliografía* y  *Glosario*. 

Para esto uso un truco de *LateX*, y es añadir directamente en el documento una linea= `\frontmatter` encima antes del inicio del capítulo 0:*Prólogo*, otra que ponga `\mainmatter` cuando empieza el libro delante del capítulo 1 en `01-libro.Rmd` y otra que pone `\backmatter` antes de los ultimos capítulos (*Bibliografía ..*). Esto divide en 3 partes el libro y solo numera la parte principal marcada a partir de `\mainmatter`.
(puedes verlo en la primera imagen del articulo como se pone).

El resultado es que el índice general queda personalizado, y que ninguno de los 3 capítulos señalados se numeran y si el resto de principales:

![Indice personalizado](/post/2021-01-07-editar-un-libro-con-r/index.es_files/indice001.png)

Respecto al formato de los capítulos también hemos añadido un cuadrado gris delante `$\blacksquare$` del nombre, pero podríamos poner una imagen o cualquier otra cosa.

![Nuevo formato capitulos](/post/2021-01-07-editar-un-libro-con-r/index.es_files/titu003.png)

También hemos cambiado el pie de foto a `Fig. +num ` con esta línea del preámbulo: `- \AtBeginDocument{\renewcommand{\figurename}{Fig. }}` y luce así:

![Pie de foto cambiado en preámbulo](/post/2021-01-07-editar-un-libro-con-r/index.es_files/imagen_foto.png)

## Insertar listados de índices

El índice general lo genera por defecto al principio del pdf, pero podemos insertar el listado de figuras `\listoffigures`, tablas `\listoftables` o referencias con el siguiente código en cualquier lugar (yo prefiero al final del doc en el fichero `03-referencias.Rmd` directamente al final):

``` 
<!-- pag sin encabezado para TOC de figuras -->
\begin{titlepage}
    \begin{scriptsize} 
        \listoffigures
    \end{scriptsize} 
\end{titlepage}

```
Este código anterior mete una lista de las figuras del libro, tanto las insertadas con sintaxis de Rmarkdown como las generadas con chunks de código. Por cierto, para insertar una foto o imagen lo más sencillo es usar markdown, pero también lo podemos hacer con R así con pleno control de forma:

```
'''{r fotor01, fig.cap='Imagen de Marco Aurelio en las monedas de época', out.width='50%', fig.asp=.75, fig.align='center',eval=TRUE, echo=FALSE}
knitr::include_graphics('fotos/Marcus_Aurelius.jpg')
'''
```

El comando *LaTeX* `titlepage` indica una página tipo portada sin encabezado ni pie de página. `<!--` es el símbolo de comentarios en *LaTeX*. Como vemos *LaTeX* es totalmente compatible con Markdown en el mismo fichero.

![Índice de figuras](/post/2021-01-07-editar-un-libro-con-r/index.es_files/indice_figuras.png)

Para insertar una hoja en blanco en cualquier lugar metemos este código *LaTeX*:

```
<!-- pag en blanco-->
\afterpage{\thispagestyle{empty}\null\newpage}

```

### Hojas especiales

Un buen libro tiene que llevar unas hojas especiales al principio que complementan la edición, por ejemplo estas:
 
 1. Portada
 1. Hoja en blanco
 1. Portadilla
 1. Dedicatoria
 1. Datos edición
 1. Indice
 1. Libro
 
Podemos hacerlas, escribiendo en el fichero `index.Rmd` con *LaTex* cada una de estas hojas. Os paso cómo lo he definido yo, justo debajo del encabezado *YAML* con el siguiente código. Cada llamada a `\begin{titlepage}` es una hoja nueva.
 
```
---
title: "Meditaciones"   
author: "Marcus Aurelius"
date: "Siglo II. d.c"

...sigue el YAML ...
---

<!-- pag sin encabezado para portada  -->
\begin{titlepage}
    \begin{center}
        \begin{LARGE}
            \textsc{MEDITACIONES\\
            Marco Aurelio\\ }
            \textsc{--- \\
             }
        \end{LARGE}
        \textsc{ \\}
        \includegraphics[width=0.8\linewidth]{fotos/Marcus_Aurelius.jpg}
    \end{center}
    \begin{flushright}
        \textit{Imperatoris animarum\\
        }
    \end{flushright}
\end{titlepage}

<!-- pag en blanco-->
\afterpage{\thispagestyle{empty}\null\newpage}

<!-- pag sin encabezado para dedicatoria-->
\begin{titlepage}
 \begin{flushright}
        \textit{No temas a la muerte,\\
                teme no haber empezado a vivir.\\ 
                ....\\}
 \end{flushright}
\end{titlepage}

<!--pagina de edición y licencia-->
\begin{titlepage}
\null\vfill
 \begin{flushleft}
       \textit{Meditaciones}\\
       ©Marco Aurelio\\
       Primera edición, Murcia 2020\\
       ---\\
       Contacto: \textit{fervilber@gmail.com }\\
            \noindent Este libro se distribuye bajo licencia de Attribution-NonCommercial-ShareAlike 4.0 International\\
            (CC BY-NC-SA 4.0)\\
      ---\\
      \includegraphics[width=0.3\linewidth]{imag/CreativeCommons-1024x358.png}
 \end{flushleft}
\end{titlepage}

\pagebreak
 
 ```

![Páginas creadas a mano con LaTeX](/post/2021-01-07-editar-un-libro-con-r/index.es_files/pag_especiales.png)
 
Si quieres ver el resultado final de ejemplo descargarlo [aquí Meditaciones de Marco Aurelio](/post/2021-01-07-editar-un-libro-con-r/index.es_files/meditaciones.pdf). 

## Paso 4. Impresión de ejemplares
Una vez tienes el fichero final en pdf, es muy fácil subirlo a editoriales en línea como [Bubok-este es mi libro de navidad-](https://www.bubok.es/libros/266691/Memorias-de-un-soldado-1919-24). Encargas los ejemplares que quieres y en unos días los recibes en casa por un precio que te sorprenderá.

Al final el coste de edición que es bastante, si lo haces tú, eso que ahorras.

Espero que os haya gustado, un saludo y Feliz 2021.

 
 
