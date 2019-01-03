---
title: R Mapas
date: 2018-04-09
menu: main
---

# Chuleta rápida para hacer mapas con R

Olga está conmigo

## enlaces recomendados para mapeo con R

 - [https://geocompr.robinlovelace.net/intro.html](https://geocompr.robinlovelace.net/intro.html)
 - [hoja resumen a imprimirse siempre](https://www.maths.lancs.ac.uk/~rowlings/Teaching/UseR2012/cheatsheet.html)
 - [ejemplos devarias librerias](https://bhaskarvk.github.io/user2017.geodataviz/notebooks/02-Static-Maps.nb.html)
 - [https://paleolimbot.github.io/ggspatial/](https://paleolimbot.github.io/ggspatial/)
 
## Crear mapa estático

El objetivo es crear un mapa estático, en imagen, y representar varios puntos de coordenadas.
Usaremos la librería *ggspatial* que es una versión de ggplot especial para hacer mapas con base de mapas libres, como los osm de openlayer.

Los datos que usaremos de entrada son coordenadas de dos puntos de la ciudad de Torrevieja, Alicante.
Las funciones de ggspatial son dos una `ggosm()` pinta mapas con base, pero es más interesante usar la funcion `ggplot` habitual y añadir como una geometría más el tipo de fondo mediante la fucnión `geom_osm` que se usa siempre dentro de `ggplot` .


Fondos libres o bases cartográficas que podemos usar son la mismas que las definidas en la función `osm.types()` de la librería `rosm`:

 - "osm"
 - "opencycle"
 - "hotstyle"
 - "loviniahike"
 - "loviniacycle"
 - "hikebike"
 - "osmgrayscale"
 - "stamenbw"
 - "stamenwatercolor"
 - "osmtransport"
 - "thunderforestlandscape"
 - "thunderforestoutdoors"
 - "cartodark"
 - "cartolight"
 
 
Veamos un ejemplo

```{r mapa_estatico1}
suppressPackageStartupMessages(library(sp))
#library(sp)
library(ggplot2)
library(ggspatial)


## Creamos capa de puntos como data frame
torrevieja <- data.frame(lon = c(-0.69,-0.67), lat = c(37.98,37.97))
# Convertimos la  tabla de puntos en capa espacial con sp
capa_puntos<-SpatialPoints(torrevieja)
str(capa_puntos)
# Pintamos el mapa generico que se define con las coordenadas anteriroes
# pero no pinta los puntos
ggosm(capa_puntos) + ggtitle("Plano Torrevieja")

# Pintamos el mapa con los puntos

ggosm(type = "osm") +  # pinta el mapa base
    ggtitle("Plano Torrevieja con puntos de interés")+
    geom_spatial(capa_puntos,size = 4, col="red",alpha = 0.6,lty = 2)+# pinta los puntos
    coord_map() # pinta leyenda 

# la funcion ggosm es igual a geom_osm
ggplot(capa_puntos)+
    geom_osm(type = "stamenwatercolor")+  # pinta el mapa base
    ggtitle("Plano Torrevieja con puntos de interés y escala")+
    geom_spatial(capa_puntos,size = 5, col="blue",alpha = 0.3)+# pinta los puntos
    annotation_scale() # pinta leyenda 

```
## preattymap

```{r}
#install.packages("prettymapr")
library(prettymapr)
library(rosm)
# source="dsk" to use the Data Science Toolkit que evita tener que darse de alta en las APIs
prettymap(osm.plot(searchbbox("Murcia")),source="dsk")

# plano con puntos a mano
torrevieja <- data.frame(lon = c(-0.69,-0.67), lat = c(37.98,37.97))

prettymap({
  osm.plot(searchbbox("Torrevieja"),zoom=13)
  osm.points(torrevieja$lon, torrevieja$lat, pch=18, cex=2)
},scale.pos = "topleft",
 arrow.pos="topright",
 drawarrow=T,arrow.scale=0.5,
 source="dsk")

```

## Ejemplo sencillo de mapa fondo con plot

Mapa sencillo con prettymapr
```{r}

library(prettymapr)
torrevieja <- data.frame(lon = c(-0.69,-0.67), lat = c(37.98,37.97))
# crea una caja de pintar makebbox(norte lat, este lon, sur lat, west lon)
#cajamapa <- makebbox(max(torrevieja$lat),max(torrevieja$lon),min(torrevieja$lat),min(torrevieja$lon))
cajamapa <- makebbox(37.98,-0.67,37.97,-0.69)

osm.plot(cajamapa, type="stamenbw")
prettymap(osm.plot(cajamapa, type="osmtransport"), scale.style="ticks", scale.tick.cex=0)

```

## pintar posicion de fotografías
Vamos a leer los datos EFIX de unas fotos y pinta la posición de las mismas en una mapa estático.

```{r}
# cargamos librería que leer exif
library(exifr)

# cargamos una lista con las imagenes
# ojo que es sensible a mayusculas o minusculas
imagenes <- list.files("imag/", pattern = "*.jpg",full.names=TRUE,ignore.case = T)
dat <- exifr(imagenes)
str(dat)

library(dplyr)
# seleccionamos la columnas justas
dat2 <- select(dat,
	GPSLongitude, GPSLatitude)
# pintamos los puntos de posicion de las fotos
plot(dat2$GPSLongitude, dat2$GPSLatitude)

# pintamos con fondo
#capap<-SpatialPointsDataFrame(dat2, dat[,"ModifyDate"])

require(sf)
capap <- st_as_sf(x = dat2, 
                        coords = c("GPSLongitude", "GPSLatitude"),
                        crs = "+proj=longlat +datum=WGS84")
#plot(capap)
#str(capap@data)
# esto es porque da problemas con otros nombres
#names(capap@data)<-c("x","y")

library(ggplot2)
library(ggspatial)
# la funcion ggosm es igual a geom_osm
ggplot(capap)+
    geom_osm(type = "stamenwatercolor")+  # pinta el mapa base
    ggtitle("Plano Torrevieja con puntos de interés y escala")+
    geom_spatial(capap,size = 5, col="blue",alpha = 0.3)+
geom_text(aes(x=dat2$GPSLongitude, y=dat2$GPSLatitude, label = dat[,"FileName"]), size=3, col="blue")+ 
    annotation_scale()

# pinta los puntos
    geom_text(aes(x=capap$GPSLongitude+0.002, y = capap$GPSLatitude, label = dat[,"FileName"]), size=3, col="blue")+ 
    annotation_scale() # pinta leyenda 
str(capap)
# otros graficos
#caja<-bbox(capap) # no funciona no se por qué
caja<-sp::bbox(cbind(dat2$GPSLongitude, dat2$GPSLatitude))

#osm.plot(caja)

prettymap({
  osm.plot(caja, type="osm")
  osm.points(dat2$GPSLongitude, dat2$GPSLatitude, pch=18, cex=2)
},scale.pos = "topleft",
 arrow.pos="topright",
 drawarrow=T,arrow.scale=0.5,
 source="dsk")


```