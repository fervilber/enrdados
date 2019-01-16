---
title: Geoposicionar fotografias
author: F.Vilber
date: '2019-01-16'
slug: geoposicionar-fotografías
categories:
  - mapas
tags:
  - markdown
  - mapas
  - SIG
  - R
  - CRS
description: ''
thumbnail: ''
toc: yes
---

## Objetivo
Crear un mapa en R en el que esté marcada la geo localización de cada una de las fotos que hemos hecho.

![geolocalizacion de fotografías](/img/mapa_fotos.png)

## Antecedentes
En mi trabajo hacemos muchas visitas a campo en las que recabar información, anotar, medir y sobre todo, hacer muchas fotografías.

Las fotos se seleccionan y editan para los informes. Si se trata de un par de fotos es poco trabajo, pero cuando hay que insertar 20, 50 o más la cosa se complica. Aunque para eso tenemos a *super R*.

## Metodología
Voy a exponer cómo lo hago, que es una de las miles de maneras posibles y seguramente a los que estén buscando un sistema les vendrá bien de partida.

Yo hago las fotos con mi movil, o con una cámara con GPS incorporado. Ahora esto está al alcance de cualquiera, solo hay que activar la´ubicación en el móvil, para que con cada foto se guarde como un dato más de los [Exif](https://es.wikipedia.org/wiki/Exchangeable_image_file_format). Normalmente las fotos almacenan -además de la imagen- multitud de metadatos que se guardan en un formato estándar llamado *Exif*, abreviatura en inglés de *formato de archivo de imagen intercambiable*.

Entre los datos están: fecha y hora, parámetros de configuración de la cámara y la **localización GPS**.

Tardé un tiempo en encontrar una librería que leyera bien estos datos en R, y no es casualidad que sea de nuestro buen amigo  [Dewey Dunnington](https://github.com/paleolimbot/exifr).

El proceso que sigo es el siguiente:

 1. descargo las imágenes a una carpeta
 2. Con R, cambio los nombres por otro más corto.
 3. leo con R los datos Exif de las fotos
 4. Omito las fotos que no tienen localización GPS almacenada.
 5. pintamos el mapa
 
## Código

Uso librerías de SIG como (`ggsn`, `sp` y `ggspatial`), para pintar uso ggplot y los complementos de `ggspatial` que permite añadir el fondo deseado.

También uso `ggrepel`, un paquete muy interesane para los que ponemos etiquetas, pues evita la sobreposición de estas, y mejora muchísimo la apariencia del mapa. Usando solo `ggplot` no quedan bien y se superponen unas a otras. Es bastante sencilla una vez que consigues un ejemplo.

El código es el siguiente:
Lo primero que hago es cambiar los nombre por defecto de las fotos que da la cámara:

```r
# Cambio de nombre de imagenes (jpg)
# damos la ruta de la carpeta con las imagenes
  ruta<-"imag/2019_01_rio/"
# Guardamos los nombres y ruta en una lista
  imagenes <- list.files(ruta, pattern = "*.jpg",full.names=TRUE,ignore.case = T)

# Renombrar los ficheros de la carpeta
# sprintf añade ceros a la izquierda hasta el 99, si es mas (999) poner %03d
  file.rename(imagenes,paste0(ruta,sprintf("%02d",seq_along(imagenes)),"_C.jpg"))
```
El bloque anterior nos ha cambiado el nombre a todas las imagenes (*.jpg) del directorio ruta. Ahora vamos a leer los metadatos y pintar el mapa

```r
# Lectura de metadatos de las imagenes
  library(ggplot2)
  library(sp)
  library(ggspatial)
  library(ggsn) # para escala y norte
  library(ggrepel) # para etiquetas 

  # leemos la imagenes y datos Exif
  library(exifr)
  # guardamos una variable lista con la ruta y nombre de los jpg
  imagenes <- list.files(ruta, pattern = "*.jpg",full.names=TRUE,ignore.case = T)
  
  # Leemos los datos exif
  dat <- read_exif(imagenes)

  # seleccionamos solo las columnas necesarias
  dat2 <- select(dat,
	        GPSLongitude, GPSLatitude,FileName)
  
  # Descartamos los que no tienen equiqueta de GPS
  dat2<-na.omit(dat2)
  
  # les cambio el nombre de la col 
  names(dat2)<-c("lon","lat","nombre")

  library(sf) # capa sf para usar la funcion de tabla a capa gis
  capap <- st_as_sf(x = dat2, 
                 coords = c("lon", "lat"),
                 crs = "+proj=longlat +datum=WGS84 +init=epsg:4326")

  # y finalmente pintamos los resultados
  ggplot(capap)+
    annotation_map_tile(type = "osm",zoom=14)+ # mapa de fondo base de OpenStreetMap
    ggtitle("Localizacion")+ # título
    labs(x="Longitud",y ="Latitud")+ # Ejes
    layer_spatial(capap,size = 3, col="red",alpha = 0.5)+ # capa de puntos de las fotos
    geom_text_repel(aes(x=dat2$lon, y=dat2$lat, label = dat2$nombre), # etiquetas
    size=2, col="blue",box.padding = unit(0.45, "lines"))+
    annotation_scale(location = "br")+ # pinta leyenda 
    annotation_north_arrow(location = "tr", which_north = "true", # pinta flecha norte
                           style = north_arrow_fancy_orienteering, 
                           height = unit(1.5, "cm"),
                           width = unit(1.5, "cm"))
```
La función `st_as_sf()` puede sustituirse con otras funciones similares de la librería `sp`, pero esta forma es más límpia.


El resultado final es el de la imagen de arriba, se puede personalizar todo lo que se quiera con algo más de trabajo. Espero que os sirva.


## CRS
Para posicionar datos x-y en un mapa se debe aportar el *CRS* (*Coordinate Reference System*) vamos, el *sistema de referencia* a la tabla inicial de datos. En este caso, al ser longitud-latitud la proyección es `+proj=longlat`.

Un sistema de coordenadas se compone de varios atributos, principalmente:

 1. Ellipsoide (describe la forma de la tierra del modelo)
 2. Datum (define el origen y orientación de los ejes de coordenadas)
 3. Proyección (pasa a 2D los datos en 3D anteriores)
 
Como puedes imaginar se pueden combinar a voluntad, aunque según la región del globo son más habituales unas que otras.

Una forma de identificar la combinación de los 3 atributos anteriores es mediante las denominaciones o códigos **EPSG** de (*European Petroleum Survey Group*). En nuestro caso usamos los datos de longitud y latitud : `+init=epsg:4326"`. por lo que simplemente podíamos haber introducido esto en la definición de la proyectión.

En España desde agosto del 2007 existe un sistema de referencia oficial que es el *ETRS89* para la peninsula y Baleares y el *REGCAN95* para la zona de Canarias. Los códigos EPSG correspondientes a estos sistemas son:

 * 4258, para coordenadas geográficas.
 * 258xx, para UTM, donde “xx” es el huso.

Pero se siguen usando mucho estos, en concreto en leaftlet uso EPSG:4326:

Códigos EPSG  | Descripción
 :--- | --- 
EPSG:3034 |	ETRS89/ETRS-LCC  Para la cartografía pan-Europeo a escalas <= 1:500.000 (IDEE)
EPSG:3035 |	ETRS89/ETRS-LAEA  Para representación y análisis estadístico pan-Europeos (IDEE)
EPSG:3395 |	WGS84  Mercator
EPSG:3785 |	Proyección Google Pseudo-Mercator  (WGS84)
EPSG:3857 |	Proyección WGS84 / Pseudo-Mercator
EPSG:4230 |	Coordenadas Geográficas ED50
EPSG:4258 |	Coordenadas Elipsoidales ETRS89 IDEE sin reproyectar
EPSG:4267 |	Coordenadas Geográficas North American Datum 1927 (NAD 27)
EPSG:4269 |	Coordenadas Geográficas North American Datum 1983 (NAD 83)
EPSG:4324 |	Coordenadas Geográficas WGS 72BE Transit Broadcast Ephemeris
EPSG:4326 |	Coordenadas Geográficas WGS84
EPSG:23028 |	Proyección UTM ED50 Huso 28 N
EPSG:23029 | Proyección UTM ED50 Huso 29 N
EPSG:23030 |	Proyección UTM ED50 Huso 30 N
EPSG:23031 |	Proyección UTM ED50 Huso 31 N
EPSG:25828 |	Proyección UTM ETRS89 Huso 28 N
EPSG:25829 |	Proyección UTM ETRS89 Huso 29 N, equivalente a EPSG:3041
EPSG:25830 |	Proyección UTM ETRS89 Huso 30 N, equivalente a EPSG:3042
EPSG:25831 |	Proyección UTM ETRS89 Huso 31 N, equivalente a EPSG:3043
EPSG:32628 |	Proyección UTM WGS84 Huso 28 N
EPSG:32629 |	Proyección UTM WGS84 Huso 29 N
EPSG:32630 |	Proyección UTM WGS84 Huso 30 N
EPSG:32631 |	Proyección UTM WGS84 Huso 31 N
EPSG:900913 | 	Proyección Google Mercator


Para ver todos los parametros pordemos ejecutar:

```r
CRS("+init=epsg:4326")
```

  CRS arguments:
 +init=epsg:4326 +proj=longlat +datum=WGS84 +no_defs +ellps=WGS84 +towgs84=0,0,0 

Que nos devuelve al completo los diferentes argumentos del sistema.