---
title: Instalar R en la nube 2 La forma facil
author: F.VilBer
date: '2019-05-20'
slug: instalar-r-en-la-nube-2-la-forma-facil
categories:
  - R
tags:
  - instalación
  - nube programas instalación
  - nube
  - R
description: ''
thumbnail: ''
toc: yes
---

En los dos anteriores artículos he explicado cómo instalé R, RSTUDIO y Shiny en la nube de amazon web service AWS. El proceso, aunque aparentemente fue fácil, me ha llevado muchos quebraderos de cabeza, fundamentalmente por temas de instalación en linux ubuntu, que no domino lo suficiente. 

## Problemas de instalación

Hay que tener alguna experiencia previa o echarle valor para enfrentarse a cada paso a una nueva contrariedad, que por ejemplo me hacía imposible instalar algunas librerías, o que Shiny no reconociera la carpeta de trabajo del usuario etc...

Después de investigar y probar cosas, como incrementar la instancia y pasar a una de pago con más capacidad (de la *t2.micro* a *t2.small*), conseguí ciertamente mejorar mucho, pero mi instalación de RSTUDIO y R seguía teniendo problemas, el principal: que no fui capaz de instalar la última versión de R, la 3.6 en Ubuntu.

Por alguna extraña razón y pese a intentarlo de cero en varias ocasiones, siempre finalizaba reinstalando la versión R 3.4, bastante vieja ya en la que no podía usar bien mis aplicaciones de SIG.


## AMI de AWS

buscando soluciones me encontré de cara con las AMI. 

Una AMI (Amazon Machine Imagen) es una imagen virtual de una máquina completa e instalada, es decir contiene una copia no solo del sistema operativo, sino de los programas y aplicaciones que deseemos instalar.  Esta imagen o AMI proporciona la información necesaria para lanzar una instancia nueva rápidamente clon de la original.

Como detalla la propia web de amazon una AMI incluye lo siguiente:

  - Una plantilla para el volumen raíz de la instancia (por ejemplo, un sistema operativo, un servidor de aplicaciones y aplicaciones)
  - Permisos de lanzamiento que controlan qué cuentas de AWS pueden utilizar la AMI para lanzar instancias
  - Un mapeo de dispositivos de bloques que especifica los volúmenes que se van a adjuntar a la instancia cuando se lance

El caso es que otras personas que ya están trabajando con R y RSTUDIO en AWS han dejado voluntariamente copias de sus AMI, incluso con diferentes configuraciones y nos las dejan preparaditas para nuestro uso. Es como el que vende (en este caso regala) pan precocinado. Cuando quieres comer pan lo sacas del congelador y lo calientas en el horno. Las AMI son ese pan congelado, esperando su consumo directo.

## ¿Cómo lo hacemos?

El proceso se vuelve mucho más sencillo, y para crear una servidor de R, RSTUDIO y Shiny solo tenemos que pinchar en el enlace de estas AMI y lanzar una desde nuestra cuenta de AWS.

Las que he probado han funcionado de maravilla, por lo que se nota que hay un trabajo de configuración detrás, y no he tenido ninguno de los problemas que tuve a hace mi propia instancia.

Las que he probado son las que proporcionan en esta página web:

 - <http://www.louisaslett.com/RStudio_AMI/>

Es suficiente con seleccionar la que queremos, en el centro más cercano (menor latencia) a nosotros y nos abre el mismo proceso de configuración inicial de instancias que vimos en [instalar-r-shiny-en-aws](../instalar-r-shiny-en-aws). Seleccionamos el tipo de instancia etc y listo.

Tal y como explica en su web el usuario es *rtstudio* y la contraseña el código de identidad de la instancia que podremos cambiar desde terminal o incluso dese RSTUDIO.

