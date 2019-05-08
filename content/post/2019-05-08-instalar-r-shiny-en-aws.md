---
title: 'Instalar R Shiny en AWS '
author: F.VilBer
date: '2019-05-08'
slug: instalar-r-shiny-en-aws
categories:
  - R
  - programas
tags:
  - data minning
  - nube
  - programas
  - instalación
  - R
  - red
description: 'Problemas con R en AWS y procedimiento para instalar Shiny en la nube'
thumbnail: '/img/AWS/shiny_funciona.png'
toc: yes
---


Tras unos días de prueba de la instalación de R en la nube de amazon AWS, he pasado por fases de todo tipo. Al principio fue bastante bien, pero en cuanto quise instalar librerías de GIS o incluso algunas básicas todo fueron problemas.

Estas semanas de trabajo en AWS me ha dado cierta experiencia que me gustaría compartir, pues apenas he encontrado información en la web, y desde luego nada en Español.

## Instalación de Shiny Server
Cuando me metí en el lío de instalar R en la nube lo hice precisamente por Shiny. Me atraía mucho disponer de un servidor propio en el que poner las aplicaciones y documentos de R en línea. *Shiny* puede servir además documentos de *Rmarkdown*, por lo que es una herramienta  potente.

Lo primero para instalarlo es abrir una consola con [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) por ejemplo y conectarse al servidor de AWS con el usuario *ubuntu*.

Esto lo vimos en el artículo anterior [Instalar R en la nube de Amzaon](../instalar-r-en-la-nube-con-amazon) y lo usaremos muchas veces para acceder al servidor con una consola de comandos clásica (no en modo ventanas).

Una vez en la consola instalamos como administrador (`sudo`) la librería Shiny de R:

```
sudo su - -c "R -e \"install.packages('shiny', repos='http://cran.rstudio.com/')\""
```

Ahora instalamos el Shiny server, para lo que necesitamos el enlace a la versión más actual que veremos
[aquí](http://www.rstudio.com/products/shiny/download-server/). En nuestro caso es esta:

```
sudo apt-get install gdebi-core
wget https://download3.rstudio.org/ubuntu-14.04/x86_64/shiny-server-1.5.9.923-amd64.deb
sudo gdebi shiny-server-1.5.9.923-amd64.deb
```

Con esto ya tenemos Shiny en marcha en nuestro servidor, para probarlo podemos ir a la dirección que tenémos asignada en AWS y añadirle el puerto 3838. Este puerto lo dejamos ya liberado en el articulo anterior, pero si falla habrá que acceder a la consola de EC2 del AWS y abrirlo se encuentra en  *Security Groups*.

![Puertos abiertos en EC2](/img/AWS/aws_puertos.png)

Entonces abres la dirección `http://55.45.19.20:3838/` por ejemplo, que era la mia y aparece esto:

![prueba de Shiny](/img/AWS/default_app.png)

Si funciona la aplicación es que ya tenemos un servidor operativo de Shiny.


## Problemas de RAM
Aunque todo va bien, en cuanto tratas de instalar algunas librerías, verás que incluso al hacerlo desde código y con usuario administrador fallan muchas cosas. Esto es porque la instancia de EC2 gratuita la `t2.micro` viene con solo 1 Gb de RAM y R funciona todo en memoria RAM.

He visto soluciones intermedias que crean un espacio de disco dura para RAM virtual, pero mi conocimiento de ubuntu no ha sido suficiente para que esto funcione. 
El problema es que se quedaba la instancia parada y con bloqueo en la instalación de muchos paquetes como el simple `install.packages(“units”)`. 

Llegaba un momento en el que se paraba todo y había que reiniciar la instancia desde el panel de AWS.

La solución ha sido cambiar a una instancia superior, **pero ojo**, son ya de pago. Al parecer es poco aunque hasta fin de mes no lo tendré con coste real.

EC2 es muy flexible y el cambio de instancia a una superior con 2 Gb de RAM la `t2.small` ha sido casi inmediato. Paras la actual y cambias, hay mucha información en la propia AWS. Cuando reinicia lo hace con las nuevas características y ya no ha habido ningún problema al ejecutar la instalación de las librerías que daban problemas antes.

![cambiar de instancia en AWS](/img/AWS/cambi_instancia.png)


## Uso de Shiny Server
Ya con Shiny en marcha debemos ponernos al día y familiarizarnos con su configuración (en Ubuntu). Cosas fundamentales:

 - las app de Shiny se almacenan en la carpeta: `/srv/shiny-server/` Cualquier app que pongamos allí será operativa, pero ojo hay que instalar las librerías y paquetes en el usuario Shiny y no solo en administrador.
 - en mi caso solo funcionan las app que se sirven en dos ficheros: `ui.R` y `server.R` y no si la pongo en un solo fichero.No se la razón.
 - Los log que son útiles para ver que falta compilar librerías etc están en :`/var/log/shiny-server.log`
 - El fichero de configuración está en: `/etc/shiny-server/shiny-server.conf`
 - Para recargar Shiny debemos hacer en la consola: `sudo service shiny-server restart`
 
## Poner aplicaciones
Es importante saber que la aplicaciones de *Shiny* corren en el servidor *Ubuntu* bajo el usuario `shiny`, y por tanto solo se tendrá acceso a ciertas carpetas y librerías. Hay que tratar de instalar las librerías de R desde consola con el user administrador, para que estén disponibles para todos.

Otra opción que he hecho es crear un grupo de usuarios con privilegios, esto me permite por ejemplo arrancar RSTUDIO desde la web y almacenar directamente las aplicaciones de shiny en el servidor de Shiny, por lo que el flujo de trabajo es directo y reduce mucho el tiempo.

En este caso he creado un usuario llamado *fer* y junto con el user *shiny* le doy permisos de acceso a la carpeta `/srv/shiny-server`.

```
sudo groupadd shiny-apps
sudo usermod -aG shiny-apps fer
sudo usermod -aG shiny-apps shiny
cd /srv/shiny-server
sudo chown -R fer:shiny-apps .
sudo chmod g+w .
sudo chmod g+s .

```

## Cambiar fichero de configuración
Una de las primeras cosas que hay que hacer es decir a *shiny* que no borre los *log* de las app, pues son útiles para localizar los errores y fallos, eso lo hacemos editando el fichero de configuración que está en `/etc/shiny-server/shiny-server.conf` y cambiando un parámetro:

Lo podemos editar con el programa nano así:

```
sudo nano /etc/shiny-server/shiny-server.conf

```
Nos interesa cambiar la líneas: `directory_index on` a `directory_index off` para que no muestre el contenido de las carpetas por defecto, y también añadir al principio una línea nueva : `preserve_logs true;` que indica que guarde los ficheros de log que son muy útiles en el desarrollo de las aplicaciones.

Una vez hecho hay que reiniciar el servidor, por cierto para salir del user shiny se escribe `exit` y para reiniciar: `sudo systemctl reload shiny-server`

Para ver cualquier fichero de logs debemos ir a:

```
cd /var/log/shiny-server
ls
```

y elegir el fichero correspondiente y verlo con: `sudo tail miproyecto-shiny-20190505-080434-48979.log`.


## Clonar de githut
Otra opción bastante flexible para mover las aplicaciones a la carpeta del servidor es clonar el proyecto de githut, haremos un ejemplo:

```
# vamos al raiz
cd

# clonamos el
git clone https://github.com/fervilber/miproyecto.git

# nos vamos al directorio de shiny server
cd /srv/shiny-server

# copiamos un enlace directo a la carpeta clonada de git
sudo ln -s ~/miproyecto .

# vemo el contenido
ls
```

Con esto ya estaría disponible la app en el servidor en la web `http://55.45.19.20:3838/miproyecto`, aunque lo normal es que no funcione, porque falten algunas librerías.

Podemos ver los log de cada carpeta en `cd /var/log/shiny-server`, y editar el que corresponda con el editor *nano* o con `tail`, para ver solo el final: `sudo tail miproyecto-shiny-20190505-080434-48979.log`

Nos dirá seguramente que falta un paquete por lo que debemos instalarlo con el user: shiny.

```
# entramos con el usuario shiny
sudo su - shiny
# abrimos R e instalamos
R
install.packages("ggplot2")
```

Aunque en mi caso prefiero hacerlo desde el user administrador con:

```
sudo su - -c "R -e \"install.packages('ggplot2', repos='http://cran.rstudio.com/')\""
sudo su - -c "R -e \"install.packages('sf', repos='http://cran.rstudio.com/')\""
sudo su - -c "R -e \"install.packages('shinytheme', repos='http://cran.rstudio.com/')\""

```
para que estén disponibles a todos los usuarios.


![Instancia en marcha](/img/AWS/shiny_funciona.png)


## Poner ficheros Rmarkdown

Crearemos una carpeta y nombraremos el fichero como `index.rmd` además debemos poner en el encabezado o primeras lineas del fichero: `runtime: shiny`

Con esto se verá como `html` al poner la dirección web en el servidor.

