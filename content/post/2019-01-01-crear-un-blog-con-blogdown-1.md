---
title: Crear un blog con blogdown (1)
author: F.VilBer
date: '2019-01-01'
categories:
  - editar documentos
tags:
  - blogdown
  - R
slug: crear-un-blog-con-blogdown-1
toc: true
---

# Antes
Llevo ya varias semanas con la idea de crear un blog con la herramienta blogdown de Yihue Xie, la cuestión de si cambiar a blogdown no es simple, aunque tienen muchas cosas buenas, yo me apaño bastante bien con otras herramientas como docuwiki que me gustan mucho, me aportasn flexibilidad y puedo editar desde cualquier sitio, pero el problema es que para usar especificamente con R hay que pasar las gráficas, tablas y datos a estilos diferentes y eso con blogdown lo tienes de inmediato.

También está el tema de las plantillas, que con el uso de Hugo facilita mucho las cosas, y permite usar muchas cosas actualizadas que ya no es facil en docuwiki.

# Dificultades
No ha sido facil empezar con blogdown, muchas cosas no han funcionado a la primera, y solo despues de actualizar tanto R como RSTUDIO he logrado que fuese bien. Tampoco es sencillo enfrentarse con Hugo al pricipio. Las plantillas son fantásticas, pero complejas, con especificaciones propias de cada una, por lo que no todas son complatibles con blogdown ni con R.

Me temo que solo despues de un tiempo de préctica podré sacar conclusiones de si ha sido conveniente el cambio.

# Blogdown 


`Blogdown` es una librería de R que permite gestionar por completo un blog y publicarlo. Está pensado especialmente para usuarios y desarrolladores que usan el lenguaje R, pero no es solo para ellor pues podrías gestionar un blog de cocina con esta librería de la misma forma.

Las ventajas que aporta para un usario que no sepa R son menos que las que obtiene alguien que sepa programar en R, pues puede exprimir mucho más sus posibilidades y generar gráficos, informes o scripts de código que automáticamente se suben al blog.

# Empecemos
Doy por hecho que usas RSTUDIO o R.

## instalar librerías
Primeor bajamos blogdown y lo instalamos, quizás tengamos problemas, yo tuve que actualizar tanto R como RSTUDIo para que esto funcionase.

```
install.packages(“blogdown”)
blogdown::install_hugo()
```
 Despues de que se instale cargamos HUGO que es otro programa que gestiona las plantillas web libres que podemos usar con blogdown. Para ver todas las plantillas disponibles echa un ojo a su web <https://themes.gohugo.io/>
 
 Aunque no todos son compatibles. En el libro de blogdown de los autores dice que algunas que funcionan bien. Yo probé muchas y pocas son 100% compatibles.
 
Una vez que has seleccionado un tema, (theme) puedes probar a instalarlo en local y ver si funciona con blogdown copn el siguiente comando. fijate que escribo el usuario y theme de la web github del desarrollador en este caso Vimux y el theme mainroad

```
blogdown::install_theme("htr3n/hyde-hyde", theme_example = TRUE)
```

## ver la prueba
Una vez descargado el theme, si tenemos el RSTUDIO actualizado nos sale un menu de Addins en el que se encuentra un listado de comandos. Entre ellos `server-site`. En todo caso podemos ejecutarlo desde la ventana de comandos:


```
blogdown:::serve_site()
```

Y nos arranca el servidor de prueba local del blog. que podemos ver en la ventana de RSTUDI Viewer o en el navegador poniendo la dirección local:<http://127.0.0.1:4321//>

# Personalizar

Si todo ha ido bien, veremos un ejemplo de web, con las páginas por defecto del `theme` seleccionado, que nos toca personalizar. Esto ya dependerá de cada plantilla, pero en lineas generales existe siempre un fichero en el raiz de nuestro proyecto actual que será `config.toml`. En este fichero están lo parámetros de configuración de la plantilla (theme), como el idioma, el título del blog, subtitulos, menús, enlaces principales, activar o no el resaltado de sintaxis etc.

Por ejemplo si usamos una plantilla sencilla como *Amnix* el fichero `config.toml` inicial es:

```
baseURL = "/"
languageCode = "es"
title = "RqR"
theme = "Amnix"
paginate = 10
ignoreFiles = ["\\.Rmd$", "\\.Rmarkdown$", "_files$", "_cache$"]
defaultContentLanguage = "es"
[Params]
  description = "Responsive and configurable Hugo theme"
  homeLayout = "2 columns + sidebar"
  listLayout = "2 columns + sidebar"
  singleLayout = "content + sidebar"
  mainSections = ["post"]

[Params.Widgets]
  recent = true
  recentNum = 5
  recentDate = true
  categories = true
  tags = true

[Params.Share]
  facebook = true
  twitter = true
  reddit = true

```

Si hemos descargado la plantilla desde R con blogdown con la opcion descargar ejemplo (`blogdown::install_theme("htr3n/hyde-hyde", theme_example = TRUE`), además se nos habrá creado una carpeta como esta `/themes/nombretheme/exampleSite/` donde están todos los ficheros que conforman una web de ejemplo.

# Crear una cuenta en Netlify
Para publicar el blog automáticamente lo más sencillo es usar la web de [Netlify](https://www.netlify.com/). En ella podemos conectar a gitHub y automaticamente nos desarrollará los ficheros deploy cada vez que actualicemos la carpeta de gitHub origen.

Veamos los pasos, que son relativamente sencillos, primero:

 1. Crea un usuario.. sigue las instrucciones en su web
 2. Elige el método para actualizar tu página **deploy method** entre manual o Git

La manera más eficiente de actualizar la página web es haciendo uso de GitHub. Esta es la razón por la que hemos empezado creando el proyecto primero en git. 

No obstante tambien lo podrías hacer volcando la carpeta *public* que se creará cada vez que renderices el blog en local y llevarla manualmente a [Netlify](https://www.netlify.com/).

Una vez que estás logeado en [Netlify](https://www.netlify.com/) accede al menú deploy y marca dentro de `Build & Deploy` tu repository origen de gitHub. Es decir la carpeta de origen de los ficheros como esta: `github.com/tu_user/tu_proyecto`.


## cambiar la versión de Hugo
En mi caso al usar una plantilla de HUGO no muy conocida he tenido que pelearme con otras cosas, principalmente que no funciona con versiones de HUGO antigüas, por lo que tuve que crear unas variables en la misma web de Netlify para que funcionase.

En mi caso para obligar a Netlify a usar HUGO 0.43 creé las siguientes variables, dentro de la misma página de configuración Deploy hay un apartado que dice :*Build environment variables*. Ahí pones lo siguiente:


![hugo config variables](/post/2019-01-01-crear-un-blog-con-blogdown-1_files/configHUGO.png)


¡Listo! con esto ya me funcionó bastante bien

Para añadir nuevos post y configurar a tu gusto todas las características lee el manual de Blogdown.

## themes
 Algunos themes que he probado y funcionan son:

 * [hyde-hyde](https://themes.gohugo.io/hyde-hyde/)
 * [mainroad](https://themes.gohugo.io/mainroad/)
 * [amnix](https://github.com/Vimux/Amnix)


# Enlaces

* <https://bookdown.org/yihui/blogdown/netlify.html>
* <https://notes.peter-baumgartner.net/tutorial/blogdown-tutorial-part-3/>
* <https://alison.rbind.io/post/up-and-running-with-blogdown/>
