---
title: 'Instalar R en la nube con amazon'
author: F.VilBer
date: '2019-04-26'
slug: instalar-r-en-la-nube-con-amazon
categories:
  - R
  - programas
tags:
  - data minning
  - nube programas instalación
  - R
  - red
description: 'Procedimiento para instalar RSTUDIO Server en la nube de amazon AWS gratis'
thumbnail: '/img/AWS/aws.png'
toc: yes
---

Llevo tiempo con la idea de comprar un servidor-PC con *Shiny* para casa, la idea está, pero el tiempo para hacerlo no, así que en Semana Santa he aprovechado los días lluviosos para reciclar unos PCs viejos, de esos del 2003 e instalar Ubuntu, R y ponerlos en marcha.

Aunque me lo he pasado bien cacharreando, no he conseguido nada decente, esos ordenadores de 32 bits con 16 años, hay que poner [puppylinux](http://puppylinux.com/) y ya no tiran ni para ver un película. Como seguía lloviendo fuera, se me ocurrió probar eso de instalar RSTUDIO en la nube de amazon... 

Empecemos!!.

## AWS
[Amazon Web Service](https://aws.amazon.com/es/) o [AWS](https://aws.amazon.com/es/) es un servicio muy completo de computación en la nube. La verdad que es difícil hasta definirlo, porque es de estas cosas nuevas que revolucionan todo, pero todavía no las asimilamos muy bien. 

Para mi es como tener un PC sin tenerlo,es decir la máquina está en algún centro de datos de Amazon y la pantalla en mi casa, así de simple. Entonces con Internet puedo acceder a mi ordenador desde cualquier sitio y el mantenimiento, copia de seguridad y muchas otras cosas se las cedo a la empresa (Amazon). Esto tiene muchas ventajas, la más evidente es la escalabilidad, pues si veo que mi ordenador se ha quedado pequeño,.... con un simple click amplio las características y listo, sin necesidad de reinstalar nada de nada... es como tener coche nuevo siempre con renting.

A cambio le cedo todo el control de mis programas y datos a una empresa multinacional, lo que me puede dejar en pañales de un momento a otro... o no.

La experiencia de instalación ha sido más simple de lo que pensaba, aunque tien cierta dificultad. El servicio **AWS es gratuito en los primeros 12 meses** solo si usas la capa básica de computación, suficiente para este primer contacto de **coste 0**, gratis, free!!!..


### Amazon EC2
Dentro de AWS nosotros usaremos para esta prueba el *Amazon Elastic Compute Cloud* o (Amazon **EC2**) que es un servicio web que proporciona capacidad informática *elástica* en la nube, vamos, que se adapta a tus necesidades, que lo puedes ampliar o reducir a tu gusto y necesidad, un ordenador en la nube sin límite de potencia.

Hay otros muchos servicios el siguiente en complejidad sería S3 que además permiten almacenar datos dinámicos, pero empezaremos por EC2 y crear un servidor de R.

## Paso 1. Darse de alta
El primer paso es darse de alta en el [Amazon Web Service](https://aws.amazon.com/es/), lo que lleva un par de minutos. Hay que tener un teléfono móvil a mano y una cuenta de email.

Nos pedirán una tarjeta de crédito para verificar la identidad, aunque el uso que le vamos a dar a esta cuenta es totalmente **gratuito** hay muchos otros servicios de AWS que son de pago.

## Paso 2. Crear el primer servidor EC2
Una vez verificados y con el usuario y contraseña que hemos dado accedemos a la plataforma y buscamos en la pestaña de servicios la opción **EC2**.

Puedes buscar por ese nombre en una pantalla como esta:

![aws búsqueda](/img/AWS/aws-services.jpg)


Le damos en: *Launch Instance*. 

## Paso 3. Elegir sistema operativo AMI
Nos preguntará qué servidor vamos a montar. En amazon los llaman *AMI*  (Amazon Machine Image) que básicamente son preinstalaciones ya montadas con los principales sistemas operativos para servidores. Estas instalaciones predefinidas incluyen el sistema operativo y muchas aplicaciones básicas.

Uno de las AMI más sencillas y robustas en la de *Ubuntu Server*, que está en varias versiones. Seleccionamos y cliqueamos en la más moderna *Ubuntu Server 18.04 LTS*.

![aws](/img/AWS/aws_01.png)

Una vez elegido *Ubuntu*, hay que seleccionar en la siguiente pantalla las características del ordenador asignado, digamos el cuerpo del PC en la nube.
Nos marca por defecto *t2.micro* que es la opción gratuita y tiene una CPU con un núcleo y 1GB de RAM, si elegimos una más potente puede generar gastos en la tarjeta, así que para empezar esto es suficiente.

![aws AMI](/img/AWS/aws_02.png)

Sale después otra pantalla para configuración detallada, que la dejamos tal cual.

La siguiente pantalla nos permite seleccionar el almacenamiento, por defecto AWS otorga 8Gb de espacio, pero la cuenta gratuita parece que permite hasta 30Gb. 

![aws CPU](/img/AWS/aws_04.png)

EC2 permite cambiar estas cosas según las necesidades y si con posterioridad queremos ampliar o crear otro volumen de almacenamiento es super fácil desde el panel de control:

![aws disco](/img/AWS/aws_12.png)

Tras esta pantalla nos pregunta un par de pasos mas respecto a si queremos añadir tags o configurar los grupos de seguridad. 

Aquí en *Security Groups* tenemos que hacer algún cambio, aunque también lo podemos dejar para después en el panel de control. Lo principal es añadir además del puerto 22 que ya está por defecto, los puertos 80, 8787 pata RSTUDIO y  3838 para Shiny y abrir el puerto 80.

Como se indica en la siguiente imagen también se puede hacer desde el panel de control con posterioridad:

![aws puertos](/img/AWS/aws_18.png)

## Paso 4. Crear pareja de claves de seguridad
Este es un paso importante. Nos lo pregunta como último paso después de exponer el resumen de la instancia es esta ventana:

![aws](/img/AWS/aws_07.png)

Esta pareja de claves sirve para proteger el acceso al servidor, ya que, una vez en marcha está abierto a Internet, por lo que creamos una clave pública y otra privada que nos permitirá acceder, pero ojo!!!, no pierdas el fichero que se genera o te quedas sin acceso.
 
Escribe un nombre y descarga el fichero `*.pem` que se crea.

Tras esto ya tenemos el servidor disponible en AWS, y lo podemos ver aquí:

![aws](/img/AWS/aws_08.png)

## Paso 5. Acceder al servidor
Creo que esta es la parte mas liosa, a ver si logro explicarlo bien. 
Una vez que tenemos la instancia desde el panel de control vemos en la parte de arriba y junto al botón azul (ver imagen anterior) un botón *Connect*,si le damos nos sale la siguiente ventana con unos nombres que son los que nos permiten hacer la conexión.

![Direcciones y conexión](imag/AWS/connect.jpg)
 
Debemos fijarnos en el punto 4 que es la dirección que necesitamos para conectar al servidor con *putty.exe*.

Desde AWS nos recomiendan la aplicación [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) así que si estás en Windows, ve al enlace anterior y descarga dos programas *putty.exe*  y *puttygen.exe*.
Yo los descargué de su web <https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html>, el *exe* directamente ya que es un programa sencillo que en principio no necesita instalación.

### Crear fichero ppk
Primero usaremos *puttygen.exe* para transformar el fichero `*.pem` del par de claves de seguridad. Los pasos son:

  1. Ejecuta *puttygen.exe*.
  2. Elige la opción *RSA* en tipo de Key a generar. 
  3. selecciona carga (Load) el fichero `*.pem*` que guardaste en el último apartado del paso 4.
  4. Guarda el fichero que se genera `*.ppk`, pues lo necesitaremos para entrar al servidor con *putty.exe*.
  
![aws](/img/AWS/puttygen.jpg)

### Conectar con PuTTY al servidor
Una vez que tengamos este fichero y lo guardemos como oro en paño, abrimos *putty.exe*, y metemos la dirección que dijimos al inicio del paso 5 (la marcada como 4) en donde pone `Host Name`. En mi caso *ec2-35-180-33-25.eu-west-3.compute.amazonaws.com*. Marcamos el puerto 22 y la conexión tipo SSH, como pone en la siguiente imagen:

![aws](/img/AWS/aws_09.png)

Si nos fijamos hay un árbol de menú con opciones a la izquierda de la ventana de la aplicación. Uno de los puntos listados es `Connection --> SSH --> Auth`. Bajamos hasta ese nivel y cliqueamos en browser para abrir el fichero `*.ppk` que hemos creado y que contiene las claves de conexión.

![aws](/img/AWS/aws_13.png)

Hecho esto volvemos al menú anterior y click en `Open`, la conexión se establece y aparece una pantalla negra de código de comandos que indica que estamos ya en Ubuntu!!!.

## Paso 6.Entra y configurar Ubuntu
La ventana que nos aparece es el servidor, solo entraremos de esta manera para configurarlo, lo normal una vez hecho todo y abiertos *los puertos correctos* será ir directo a RSTUDIO o a Shiny.

Lo primero es que nos pide entrar con un usuario, el que hay por defecto es: *ubuntu*, y la clave la lee del `*.ppk`. 

![aws](/img/AWS/aws_10.png)

Ahora toca seguir las instrucciones para instalar [RSTUDIO](https://www.rstudio.com/products/rstudio/download-server/), que se describen en su web. En nuestro caso al ser Ubuntu 18 tendremos que escribir en la linea de comandos cada una de las siguientes lineas de código y ejecutar:

```
sudo apt-get install gdebi-core
wget https://download2.rstudio.org/server/bionic/amd64/rstudio-server-1.2.1335-amd64.deb
sudo gdebi rstudio-server-1.2.1335-amd64.deb
```

Yo antes me he metido en R desde consola (que ya viene instalado por defecto en Ubuntu) y he actualizado los paquetes así:

```
# actualizamos 
sudo apt-get update

# si R no estubiera instalado ejecutar:
sudo apt-get install r-base r-base-dev

#Entramos a R como usuario root
sudo -i R

# Actualizamos todos los paquetes
update.packages()

# salimos de R
q()
```
Nos irán saliendo cosas y de vez en cuando hay que pulsar `y` (yes) y continuar.
Con esto tendremos instalado 

### Crear nuevo usuario
Ojo que este paso me ha costado un rato, pues no podía entrar en RSTUDIO al no saber meter la clave del usuario root. Para solucionarlo, os recomiendo crear ahora en este paso un usuario nuevo que será con el que accedamos al RSTUDIO SERVER, para eso:

```
sudo adduser <escribe el nombre del nuevo usuario>

```
Te pedirá que introduzcas una nueva contraseña y listo. Este usuario /contraseña será el que usaremos para acceder a RSTUDIO desde el navegador web.

### Instalar compiladores
Si usas muchas librerías en R, quizás debas instalar los compiladores de fortran ... pues a veces son necesarios cuando descargas un nuevo paquete. Aprovechando que estas en la consola ejecuta esto:

```
sudo apt-get install g++
sudo apt-get install make
sudo apt-get install gfortran

```
Pues con esto ya estaría listo y en principio podemos ya cerrar y abrir el navegador para entrar en RSTUDIO SERVER.

## Paso 7. Entrar desde el navegador a RSTUDIO
Pues ya está todo listo, solo queda probar el funcionamiento. Abrimos un navegador y vamos a la dirección IP publica que nos indica en el panel de control de EC2.

Está en la descripción de la instancia actual y será algo así como: `IPv4 Public IP: 35.180.93.45`. Esta es la dirección de nuestro flamante servidor Ubuntu a la que hay que añadir el puerto de RSTUDIO, por lo que nos ponemos en la barra de direcciones y ponemos algo como: `http://35.180.93.45:8787/`

En la ventana del navegador saldrá esto, si todo va bien:

![ventana de apertura de RSTUDIO SERVER](/img/AWS/rstudio1.png)

Aquí metemos el usuario/ clave que añadimos en Ubuntu y nos abrirá directamente una versión completa de RSTUDIO en el navegador:

![ventana de trabajo de RSTUDIO SERVER](/img/AWS/aws_fin.png)

## Conclusión

Este proceso solo hay que hacerlo una vez, y ya tienes listo R y RSTUDIO en **cuanquier sitio con conexión a Internet y un navegador**. Va muy bien para ser un procesador de un solo núcleo y sólo 1Gb de RAM. La experiencia ha sido buena, aunque voy a seguir investigando y probandolo pues parece una opción buena de trabajo, siempre actualizado, facil de ampliar y disponible en cualquier sitio.

Dejo para otro artículo la instalación de Shiny, y por tanto de aplicaciones web en la nube.

Saludos!!





