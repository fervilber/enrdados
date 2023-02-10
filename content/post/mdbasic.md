---
title: markdown básico
description: Ejemplos de markdown básico.
date: 2018-12-06
menu: main
toc: false
categories:
  - "markdown"
tags:
  - "formato"
  - "markdown"
  - "sintaxis"
---

El principal propósito de este artículo es tener a mano un resumen para los formatos básicos de markdown.

RSTUDIO pone a disposición de los usuarios una chuleta resumida muy bueno con gran parte de los formatos de sintaxis, pero creo conveniente ponerlo a mano también en el blog

<!--more-->

## Encabecados

Los encabezados se forman con con el símbolo almohadilla `#`. Repitiendo este símbolo sucesivamente hacemos los encabezados o títulos cada vez más pequeños.

```
# Título 1
## Título 2
### Título 3
#### Título 4
##### Título 5
###### Título 6
```

# Título 1
## Título 2
### Título 3
#### Título 4
##### Título 5
###### Título 6

***

## Listas

### Ordered List
```
1. Elemento 1
2. Elemento 2
3. Elemento 3
```

1. Elemento 1
2. Elemento 2
3. Elemento 3

### Unordered List

```
* Elemento 1
* Elemento 2
 * Subelemento 2.1
* Elemento 3
 * Subelemento 3.1
```

* Elemento 1
* Elemento 2
 * Subelemento 2.1
* Elemento 3
 * Subelemento 3.1


## Bloques de código

En md para meter un bloque de código lo podemos hacer encerrando la escritura entre las los símbolos tilde 3 veces:  

\`\`\`

 Esto es un bloque de código
 
\`\`\`

Cuya salida es:

```
esto es un bloque de código
```
Aunque depende de la configuración local, en muchos casos puede especificarse el lenguaje a continuación de las comillas como por ejemplo aquí:


\`\`\` javascript

var s = "JavaScript syntax highlighting";

alert(s);

\`\`\`

```javascript
var s = "JavaScript syntax highlighting";
alert(s);
```

En Rmd que es la versión adaptada de markdown que usa RSTUDIO lo haremos con el mismo símbolo y a continuación unas llaves `{}` indicando dentro de la llave el lenguaje y muchos otros parámetros.

## Tablas
Las tablas que no se generan dentro de código R, pueden hacerse directamente con el formato usando el símbolo `|`.

```
| ID  | Make      | Model   | Year |
| --- | --------- | ------- | ---- |
| 1   | Honda     | Accord  | 2009 |
| 2   | Toyota    | Camry   | 2012 |
| 3   | Hyundai   | Elantra | 2010 |
```
resultado:

| ID  | Make      | Model   | Year |
| --- | --------- | ------- | ---- |
| 1   | Honda     | Accord  | 2009 |
| 2   | Toyota    | Camry   | 2012 |
| 3   | Hyundai   | Elantra | 2010 |

Otro ejemplo

```
| Tables      | Are           | Cool         |
|:----------- |:-------------:| ------------:|
| align: left | align: center | align: right |
| align: left | align: center | align: right |
| align: left | align: center | align: right |
```

se crea esto:

| Tables      | Are           | Cool         |
|:----------- |:-------------:| ------------:|
| align: left | align: center | align: right |
| align: left | align: center | align: right |
| align: left | align: center | align: right |

Incluso puede usarse código md dentro de la tabla:

```
| Inline     | Markdown  | In                | Table      |
| ---------- | --------- | ----------------- | ---------- |
| *italics*  | **bold**  | ~~strikethrough~~ | `code`     |
```
| Inline     | Markdown  | In                | Table      |
| ---------- | --------- | ----------------- | ---------- |
| *italics*  | **bold**  | ~~strikethrough~~ | `code`     |


## Marcas de formato

 * *cursiva* = `*cursiva*`
 * **negrita** = `**negrita**`
 * ~~tachado~~ = `~~tachado~~`

## Enlaces
Hay varias formas de crear enlaces en md, la más simple es poner la url del enlace entre los símbolos de `<>`, así `<http://www.gritos.org>` cuya salida es esto <http://www.gritos.org>.

La otra forma es usando las llaves y paréntesis:

```
[texto el enlace](http://www.gritos.org)
```
queda así:
[texto el enlace](http://www.gritos.org)


## Insertar imagen
Insertar una imagen simple se hace así:

```
![titulo imagen](ruta/delaimagen.jpg)
```

En Rmd y si la salida usa $LaTeX$ podemos usar etiquetas como la siguiente:

```
![titulo imagen \lable{fig_23}](ruta/delaimagen.jpg)
```

## Línea horizontal
Para insertar una linea horizontal pondremos 3 símbolos seguidos: (cualquiera de los siguientes funciona):

 * `***`

***

 * `---` 
 
 --- 


## insertar un video de youtube
Para eso podemos insertar drectamente HTML en el código:

```
<a href="http://www.youtube.com/watch?feature=player_embedded&v=YOUTUBE_VIDEO_ID_HERE
" target="_blank"><img src="http://img.youtube.com/vi/YOUTUBE_VIDEO_ID_HERE/0.jpg" 
alt="IMAGE ALT TEXT HERE" width="240" height="180" border="10" /></a>
```

## Uso de $LaTeX$
Para usar código $$LaTeX$$ debemos insertar el mismo entre el símbolo de dolar `$`.Por ejemplo para inserar una flecha  `$\rightarrow$` es $\rightarrow$. 

