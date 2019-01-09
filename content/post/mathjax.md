---
title: MathJax
description: Ejemplo simple de MathJax
date: 2018-04-14
lastmod: 2018-04-15
mathjax: true
toc: true
categories:
  - "markdown"
tags:
  - "MathJax"
  - "markdown"
  - "matemáticas"
  - "sintaxis"
---

*MathJax* es una librería JavaScript que permite ver ecuaciones y notación matemática en casi cualquier navegador moderno.
Podemos usarla directamente en nuestros post de blogdown sin necesidad de acudir al formato Rmd, es decir con markdown puro de la siguiente forma:

<!--more-->

## Para permitir MathJax

Para permitir MathJax tenemos que añadir en el yaml el siguiente parámetro como 
`true`:

```yaml
mathjax: true
```

## Ejemplo de MathJax

**Entrada de código de ecuación**:

```
Cuando \\( a \ne 0 \\), hay dos soluciones a la ecuación

$$ ax^2 + bx + c = 0 $$

que son:

\\[ x = {-b \pm \sqrt{b^2-4ac} \over 2a} \\]
```

**Output**:

Cuando \\( a \ne 0 \\), ay dos soluciones a la ecuación

$$ ax^2 + bx + c = 0 $$

que son:

\\[ x = {-b \pm \sqrt{b^2-4ac} \over 2a} \\]
