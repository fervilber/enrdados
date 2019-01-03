---
title: MathJax
description: Simple MathJax example test article
date: 2018-04-14
lastmod: 2018-04-15
mathjax: true
tags:
  - "MathJax"
---

MathJax es una librería JavaScript que permite ver ecuaciones y notación matimática en casi cualquier navegador moderno.

<!--more-->

## How to enable MathJax

For enabling MathJax, add this to your content's frontmatter:

```yaml
mathjax: true
```

## MathJax example

**Input**:

```
When \\( a \ne 0 \\), there are two solutions to

$$ ax^2 + bx + c = 0 $$

and they are:

\\[ x = {-b \pm \sqrt{b^2-4ac} \over 2a} \\]
```

**Output**:

When \\( a \ne 0 \\), there are two solutions to

$$ ax^2 + bx + c = 0 $$

and they are:

\\[ x = {-b \pm \sqrt{b^2-4ac} \over 2a} \\]
