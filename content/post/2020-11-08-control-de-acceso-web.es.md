---
title: Control de acceso web
author: F.VilBer
date: '2020-11-08'
slug: control-de-acceso-web
categories:
  - diseño
  - Shiny
tags:
  - shiny
  - app
  - flexdashboard
description: 'control de accesos a la app desde flex'
thumbnail: '/post/2020-11-08-control-de-acceso-web.es_files/control_accesos1.png'
---

Una de las cosas por las que vale la pena aprender $$R$$ es por lo fácil que resulta hacer aplicaciones web dinámicas. Esto lo podéis comprobar en la serie de post que realicé sobre *shiny*, enfocado al desarrollo de app con la librería flexdashboard.

 * [I. Sintaxis y uso simple de flexdashboard](../Shiny-facil-con-flexdashboard)
 * [II. htmlwidgets](../Shiny-facil-con-flexdashboard-ii)
 * [III. crosstalk](../crosstalk)
 * [IV.App shiny con flexdashboard ](../shiny-facil-con-flexdashboard-iv)
 
La verdad es que yo no uso *Shiny*, porque siempre que tengo que hacer una aplicación o gráfica dinámica uso [flexdashboard](https://rmarkdown.rstudio.com/flexdashboard/) o flex para los amigos :-) . Para mi resulta muchísimo más sencillo, y es completamente funcional, incluso la salida y formato es mejor, queda más aparente con mucho menos tiempo de desarrollo y aprendizaje.

Lo malo es que si te sales de la norma o quieres hacer algunas cosas "raras", hacerlo en flex puede ser complicado. 

Esto fue lo que me pasó al intentar meter el **control de accesos a una web** en una app con  [flex](../shiny-facil-con-flexdashboard-iv). Me lo había preguntado una gran _errera_ de [UMUR](https://gauss.inf.um.es/umur/), y estuve buscando la solución, pero nada me funcionó. De esto hace ya unos meses, pero casualmente esta semana navegando por el océano de *Internet* descubrí la solución... vamos a explicarla.

## Control de accesos
La manera más sencilla de hacer el control de accesos a una app de *shiny* es usando la librería `shinymanager`. 
Para ello tenemos que hacer una tabla con los *nombres de usuarios - contraseña* válidos de entrada que se almacenará en el servidor. En la parte del cliente, antes de acceder a la app nos aparecerá una página de acceso donde nos preguntará en un formulario simple el *usuario* y *contraseña*.

Tiene algunas opciones de personalización, para poner textos o imágenes o logos tanto en la parte de arriba como abajo de la página de acceso, incluso hasta para elegir idioma (solo fr y pt, el español no está incluido). Algo así:

![control de acceso a la app de flex](/post/2020-11-08-control-de-acceso-web.es_files/control_accesos1.png)

## Como hacerlo en flex
El truco para que funcione en flex es crear un fichero `*.css` de los de formato e indicarle la ruta de acceso desde el encabezado YALM de la página de la app. Este fichero contiene el formato de la página de control de accesos, que se pone delante de la web y la tapa (oppacidad=1).

Pongamos que nombramos a este fichero `styles-auth.css`, el contenido del mismo es este:

```
.panel-auth {
  position: fixed;
  top:0;
  bottom: 0;
  left: 0;
  right: 0;
  background-color: #FFF;
  opacity: 1;
  z-index: 99997;
  overflow-x: hidden;
  overflow-y: scroll;
}

```

Este fichero lo ponemos en el mismo directorio de la app, por ejemplo.

Aparte de esto hay que introducir dos partes de código en la aplicación, el primero es para la definición de credenciales, que claramente debe ejecutarse en un `chunk` de código oculto, que no salga (`include=FALSE`). 

```
# define las credenciales de acceso.
credentials <- data.frame(
  user = c( "admin", "manager"), # mete los usuarios
  password = c("mmm333", "13245"), # mete las contraseñas
  stringsAsFactors = FALSE
)
```
Aparte en la pestaña o página que queramos pondremos el siguiente código, donde llamamos a las dos funciones de `shinimanager`: `auth_ui(id = "auth")` en la que podremos ampliar el formato e insertar cosas como veremos en el ejemplo y la `auth_server(id = "auth")`. Ambas deben referirse a la misma `id` y llamar a la función `check_credentials()` con referencia a la tabla que contiene las contraseñas y claves ( que hemos llamado `credencials` en nuestro ejemplo).

```
# forma resumida mínima de control de accesos
auth_ui(id = "auth")

auth <- callModule(
    module = auth_server,
    id = "auth",
    check_credentials = check_credentials(credentials) # data frame
)
``` 
Con esto ya tendríamos una app con acceso limitado por usuario/contraseña.

## Ejemplo completo

Os dejo un ejemplo completo más abajo, acordaros de poner en el mismo directorio el fichero de estilos `styles-auth.css` o indicar la ruta en el YAML, **sin este fichero no funciona** el control.

En el ejemplo he añadido más funciones de formato en `auth_ui()` para que veáis cómo poner un logo, o un mail de contacto al administrador en caso de que no puedan entrar a la web, pero esto no es necesrio y puedes reducirlo al código simplificado anterior.

Ojo, que por cuestiones de formato, las comillas que aparecen en este ejemplo en los chunk de código __NO SON VÁLIDAS EN R__, debes cambiar $'''$ por $```$.


````
---
title: "ejem_pass_web"
output:
  flexdashboard::flex_dashboard:
    css: styles-auth.css
    orientation: columns
    vertical_layout: fill
    theme: spacelab
runtime: shiny 
---

'''{r global, include=FALSE}
#####################################
# Ejemplo de control de accesos en felxdasboar shiny
# Autor: Fernando Villalba
###################################
library(datasets)
library(shinymanager)  # esto es para pass control
data(faithful) # datos

# define las credenciales de acceso.. (you can also use sqlite database)
credentials <- data.frame(
  user = c( "admin", "manager"),
  password = c("mmm333", "13245"),
  stringsAsFactors = FALSE
)

'''

# Pestaña1 {data-icon="fa-chart-area"}

'''{r control}
# control de acceso a la web shiny
auth_ui(id = "auth",
        # Añade imagen
      tags_top = 
        tags$div(
          tags$h4("Autorización de acceso ", style = "align:center"),
          tags$img(
            src = "https://enrdados.netlify.app/img/gritos.jpg", width = 100
        )
      ),
        tags_bottom = tags$div(
        tags$p(
          "Contactar con ",
          tags$a(
            href = "mailto:someone@example.com?Subject=Shiny%20aManager",
            target="_top", "administrator"
          )
        )
      ), 
      choose_language = F
    )
# Metemos el control de accesos
auth <- callModule(
    module = auth_server,
    id = "auth",
    check_credentials = check_credentials(credentials) # data frame
)
'''

## Entradas {.sidebar }

Tiempo de espera entre erupciones y duración de la erupción del
Old Faithful geyser en Yellowstone , Wyoming, EEUU.

'''{r}
selectInput("n_breaks", label = "Numero de barras:",
            choices = c(10, 20, 35, 50), selected = 20)

sliderInput("bw_adjust", label = "BAjuste de ancho de bandas:",
            min = 0.2, max = 2, value = 1, step = 0.2)
'''


## Gráfica

'''{r}
renderPlot({
  hist(faithful$eruptions, probability = TRUE, breaks = as.numeric(input$n_breaks),
       xlab = "Duration (minutes)", main = "Geyser Eruption Duration")

  dens <- density(faithful$eruptions, adjust = input$bw_adjust)
  lines(dens, col = "blue")
})
'''

``` 
 
