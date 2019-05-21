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

En los dos  artículos anteriores he explicado cómo instalé R, RSTUDIO y Shiny en la nube de [Amazon Web Service](https://aws.amazon.com/es/) o AWS. El proceso, aunque aparentemente  fácil, me ha llevado muchos quebraderos de cabeza fundamentalmente por temas ajenos a R y relacionados nstalación en linux ubuntu, que no domino lo suficiente. 

## Problemas de instalación

Hay que tener alguna experiencia previa o echarle valor para enfrentarse a cada paso a una nueva contrariedad, por ejemplo me ha sido imposible instalar algunas librerías, quizás por dependencias que no he logrado sacar, problemas para que Shiny no reconociera la carpeta de trabajo del usuario etc...

Después de investigar y probar muchas cosas, en concreto funcionó bastante bien incrementar el tipo de instancia y pasar a una de pago con más capacidad RAM (de la *t2.micro* a *t2.small*),así conseguí cierta mejora, pero mi instalación de RSTUDIO y R seguía teniendo problemas, el principal: que no fui capaz de instalar la última versión de R, la 3.6 en Ubuntu.

Por alguna extraña razón y pese a intentarlo de cero en varias ocasiones, siempre finalizaba reinstalando la versión R 3.4, bastante vieja ya, en la que no podía usar bien mis aplicaciones de SIG. Quizás no conozco *Ubuntu* lo suficiente.


## AMI de AWS

Buscando soluciones me encontré de cara con las AMI del AWS. 

Una AMI (Amazon Machine Imagen) es una imagen virtual de una máquina completa e instalada, es decir contiene una copia no solo del sistema operativo, sino de los programas y aplicaciones que deseemos instalar, permisos usuarios ya montados etc.  Esta imagen o AMI proporciona la información necesaria para lanzar una instancia nueva rápidamente clon de la original.

Como detalla la propia web de amazon una AMI incluye lo siguiente:

  - Una plantilla para el volumen raíz de la instancia (por ejemplo, un sistema operativo, un servidor de aplicaciones y aplicaciones)
  - Permisos de lanzamiento que controlan qué cuentas de AWS pueden utilizar la AMI para lanzar instancias
  - Un mapeo de dispositivos de bloques que especifica los volúmenes que se van a adjuntar a la instancia cuando se lance

El caso es que otras personas que ya están trabajando con R y RSTUDIO en AWS han dejado voluntariamente copias de sus AMI, incluso con diferentes configuraciones y nos las dejan preparaditas para nuestro uso. Es como el que vende (en este caso regala) pan precocinado. Cuando quieres comer pan lo sacas del congelador y lo calientas en el horno. Las AMI son ese pan congelado, esperando su consumo directo. 

Además es una idea fantástica, pues pon que necesitas no un ordenador sino 20, pues en un rato los tienes, por ejemplopara dar un curso, y luego los desmontas , o que quieres un  cojonudoordenador, con 1 tera de RAM, pues lo montas en un rato también.

## ¿Cómo lo hacemos?

El proceso es básicamente el mismo que explicaba en el artículo [instalar-r-en-la-nube-con-amazon](../instalar-r-en-la-nube-con-amazon), pero se vuelve mucho más sencillo, ya que una vez arranca tienes todo configurado: el servidor de R, RSTUDIO y Shiny.

Para hacerlo solo tenemos que pinchar en el enlace de estas AMI y lanzar una desde nuestra cuenta de AWS.

Las que he probado han funcionado de maravilla, por lo que se nota que hay un trabajo de configuración de Linux detrás, y no he tenido ninguno de los problemas que pasé al crear mi propia instancia.

Las que he probado son las que proporcionan en esta página web:

 - <http://www.louisaslett.com/RStudio_AMI/>

Es suficiente con seleccionar la que queremos, en el centro de datos más cercano (menor latencia) a nosotros y nos abre el mismo proceso de configuración inicial de instancias que vimos en [instalar-r-en-la-nube-con-amazon](../instalar-r-en-la-nube-con-amazon). Seleccionamos el tipo de instancia etc y listo.

Tal y como explica en su web el usuario por defecto es *rtstudio* y la contraseña el identificador de identidad de la instancia que vemos desde el panel de configuración de AWS.

En la misma [web](http://www.louisaslett.com/RStudio_AMI/) nos indica una forma de cambiar las contraseñas con una librería en rstudio, aunque yo lo hice desde un terminal conectandome con *putty* a Ubuntu y poniendo `$ passwd`. Pones las contraseña actual y la nueva según indica la pantalla y listo.


Otra de las cosas que vienen por defecto en estas AMI es que la dirección de RSTUDIO está implicita y no es necesario escribir tras la ip el `:8787` ni el `:38383` para Shiny, y son de la forma `http://34.187.137.206/shiny/`  


Os animo a probar esta manera, vereis qué bien va y aprovechando la capa gratuita de AWS para empezar.



