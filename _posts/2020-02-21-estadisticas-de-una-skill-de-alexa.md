---
title: 'Estadísticas de una skill de Alexa un año después'
date: 2020-02-21T12:40:48+00:00
author: findemor
layout: post
image: /wp-content/uploads/2020/02/fotnite-sounds.png
share-img: /wp-content/uploads/2020/02/fotnite-sounds.png
categories:
  - Desarrollo
tags:
  - Alexa
  - NodeJS
---

Hace este mes (02/2020) un año que Amazon presentaba un programa para desarrolladores en el que, por subir una skill para Alexa y conseguir cierto número de activaciones en un período de un mes, aspirabas a conseguir un Amazon Echo gratuito. Por aquel entonces había estado haciendo pruebas con el simulador y, como me siento muy cómodo desarrollando en NodeJS (el lenguaje de desarrollo nativo para las Skills de Alexa), decidí ponerme manos a la obra y hacerme con uno.

Como tenía muy poco tiempo libre en aquel momento, consideré que sería mejor no complicarme demasiado si quería acabar a tiempo, por lo que elegí entre las opciones más sencillas que se me ocurrían, y la decisión final fue hacer ~~la típica~~ una skill de reproducción de sonidos. Necesitaba sin embargo alcanzar un gran número de descargas, necesitaba algo que se posicionase fácilmente, así que como los jugadores de Fortnite no teniamos muchas opciones en este dispositivo... ¡pues ya lo tenia!

Me encantan las estadísticas, y hacer un poco de retrospectiva de vez en cuando sobre el trabajo que hacemos siempre aporta algún aprendizaje, así que me gustaría compartir con vosotros este pequeño análisis por si os ayudase, al menos para tener ciertas referencias, con las decisiones sobre vuestras propias skills.


# Fortnite Sounds (Sonidos Fortnite) para Alexa Amazon Echo

## _"Alexa, abre Sonidos Fortnite"_

Antes de nada... ¿de qué skill estoy hablando? Pues de "Sonidos Fortnite", si quieres probarla, saber en qué consiste, etc. Está disponible gratis en el mercado de Skills de Amazon.

> [https://www.amazon.es/devergence-Sonidos-Fortnite/dp/B07P8M1CQQ](https://www.amazon.es/devergence-Sonidos-Fortnite/dp/B07P8M1CQQ)

La skill cuenta con tan solo 43 valoraciones en este momento, con una nota media de 3,4 sobre 5 estrellas.

Ojo al dato, como veremos por las activaciones, esto significa que sin más motivación para ello, __solamente el 0,06% de los usuarios que la activaron entraron a valorar la aplicación__. De estos, un 23% hicieron una valoración muy negativa y un 44% una valoración muy positiva.

## Activaciones

Durante este año, ¡la skill se ha activado __más de 74000 veces__!, increible... de verdad, __muchas gracias a todos los usuarios__ por semejante acogida. :D

Unas 1400 descargas semanales desde el inicio, __con un pico de más de 4300, en la semana de navidad de 2019__. Imagino que fue un regalo estrella estas últimas navidades y que ese mismo dia o al siguiente estaría todo el mundo buscando que skills activar en sus aún resplandecientes dispositivos.

### Activaciones semanales

En la siguiente gráfica se muestran los usuarios que activaron la skill en global (acumulado) y por cada uno de los 4 idiomas en los que está disponible. 

He marcado algunas fechas importantes:
* __1:__ Publicación de la skill tras días de idas y venidas de un par de semanas con el proceso de certificación. Publico un tweet (en inglés) que tuvo unas 970 impresiones y 38 interacciones, la única publicidad que hice de la skill.
* __2:__ Presento la skill a un segundo programa de desarrolladores, ahora incluyendo APL para las pantallas de Echo Show, lo cual parece que tuvo un efecto increible en las activaciones.
* __3* (ver más abajo):__ Amazon incorpora la skill en el [programa de recompensas](https://developer.amazon.com/es-ES/alexa/alexa-skills-kit/rewards?ref_=pe_3594520_270786200) y comienza a realizar pagos mensuales basado en el número de interacciones de la skill. ¡Genial!
* __4:__ Amazon lanza el idioma nuevo es-US (Español de Estados Unidos) y añade automáticamente la skill a dicho mercado. Sin embargo no lo revisé en su momento y no era compatible, por lo que desgraciadamente no estaba funcionando bien para esos usuarios. :(
* __5* (ver más abajo):__ Lanzamiento de la campaña de Facebook Ads. Decidí probar la red de publicidad en Instagram e invertí 30 euros en una fugaz campaña sin ningún éxito, claro está. Hablo de él más abajo.
* __6:__ Amazon revisa periodicamente las skills y me notifica que en es-US contiene un error, por lo que realizo una nueva publicación corrigiéndolo.

Además hay dos dias importantes:
* A: Los dias 24 y 25 de Diciembre, Navidad, con muchísimas activaciones.
* B: El dia 6 de Enero, Dia de Reyes, con muchas activaciones en España.


![Activaciones totales](/wp-content/uploads/2020/02/fortnite-activation.jpg)

### Activaciones diarias

En la siguiente gráfica se muestra un período de tiempo con granularidad diaria. 

![Activaciones diarias](/wp-content/uploads/2020/02/fortnite-activaciones-diarias.jpeg)

El patrón de activación diario tiene un perfil de diente de sierra constante y predecible:
* Los picos altos corresponden a los sabados y domingos, invariablemente.
* Los valles entre ellos son los dias de diario, siendo el día más bajo el jueves, generalmente.

A medida que se acercaban la semana de navidad (desde el 15 de diciembre al 25) se produjo un incremento en el factor de activación. El enorme pico a la derecha del gráfico es precisamente el miércoles 25 de diciembre. No me lo quiero imaginar si el 25 hubiese caído en sabado. 

__Activaciones diarias por países__

La forma de activación es similar en los cuatro idiomas en los que está disponible la skill. Si bien es cierto que __en España las activaciones fueron habitualmente más altas los sábados (con muchas excepciones) y en Estados Unidos y Reino Unido los domingos__. Esta es la gráfica de activaciones diarias en cada país.

![Activaciones diarias por paises](/wp-content/uploads/2020/02/fortnite-activaciones-diarias-paises.jpeg)

### Programa de recompensas de Amazon (3* numerado en el diagrama de activaciones)

La verdad es que no voy a decir que no me agradase y me sorprendiese felizmente cuando me llegó el correo de Amazon avisandome de que iban a realizar un pago y que debía actualizar mi información de facturación, debido al _engage_ de la skill.

Eso sí, me pilló en mitad de la noche, en Islandia, en una caravana a punto de ser derribada por vientos huracanados, con malísima cobertura y teniendo que rellenar formularios bancarios en la pantalla del móvil. Yo, que no veo 3 en un burro. Pero aún así.. ¡Estupendus!.

Y bien, __¿Cuánto paga Amazon los desarrolladores por la activaciones e interacciones de las skills de Alexa?__

La página del [programa de recompensas](https://developer.amazon.com/es-ES/alexa/alexa-skills-kit/rewards?ref_=pe_3594520_270786200) nos informa de que se usan formulas complejas para determinar cuanto recibirá el desarollador cuando la aplicación ha alcanzado cierto nivel de éxito en entre los usuarios, y no dudo de que sea así, pero en la práctica, en mi caso ha sido:

* En 2019: 0,021€ por sesión o 0,003€ por utterance, según lo queramos calcular.
* En 2020: 0.011€ por ssesión o 0,001€ por utterance.

Lógicamente esto es un cálculo muy básico que he hecho haciendo un poco de ingeniería inversa en los números, sin conocer exactamente los periodos en los que calculan las interacciones ni qué factores tienen realmente en cuenta. Espero que aún siendo muy aproximados os valga para haceros una idea.

Además, esto solo ocurrió en los markets de UK y US, entiendo que en España no ha llegado a cruzar la barrera para entrar en el programa de recompensas.

### Instagram (5* numerado en el diagrama de activaciones)

En su momento decidí ver como funcionaba la publicación de anuncios en Instagram y experimenté con esta skill. Invertí tan solo 30€ en una campaña de un mes.

![Activaciones diarias por paises](/wp-content/uploads/2020/02/fortnite-ad.PNG)

El resultado fue:  
  * Coste 0,90€ por resultado
  * 33 clicks (45 clicks segun bitly, 3 instagram, 41 directos)
  * 3800 usuarios alcanzados

Creo que bitly esta contando como directos los clicks desde la app de Instagram y como Instagram los que vienen desde la web.

### Usuarios únicos

Casi 79400 usuarios únicos en total, frente a las 74000 activaciones... nunca he entendido (ni me he preocupado demasiado) por este dato. Tiendo a imaginar que hay unos 5400 usuarios que provienen de dispositivos compartidos entre varios miembros de la familia por ejemplo, donde la skill está activada.

## Sesiones

En cuanto a las sesiones, hubo más de 105200 sesiones en total, con un máximo de 5800 sesiones en una semana en la misma semana de navidad donde se produjo el pico de descargas, y una media semanal de 2000.

![Sesiones](/wp-content/uploads/2020/02/fortnite-sessions.PNG)

Realmente la skill no tiene mucha opciones y eso deja huella en las sesiones, la media de sesiones es de 1,3 diarias por usuario.

### Utterances 

Las utterances son las frases con las que el usuario ha interactuado con la skill. Hubo un sorprendente total de 763500. En la semana de navidad un pico de casi 42000 frases fueron interpretadas y manejadas por la skill. La media semanal es de 14683. 

WTF... ¡14700 personas accediendo cada semana! Menos mal que todo lo tengo montado en cloud y AWS se encarga de escalarlo todo por mi. Veamos los distintos __percentiles de latencia__ que está ofreciendo la función lambda en la que todo esto ocurre:

![Latencia](/wp-content/uploads/2020/02/fortnite-latencia.jpeg)

Nada mal, y ojo... ningún error. Tendría el día bueno cuando lo programé.

![Errores](/wp-content/uploads/2020/02/fortnite-responses.jpeg)

### Intents

Los intents serían algo así como las operaciones que la skill maneja. Varios utterances pueden invocar la misma Intent.

En el caso de esta Skill lamentablemente esto no va a aportar mucha información, porque lo unico que hace es reproducir sonidos, por lo que solo tiene realmente un único Intent que maneja este asunto. 

La consola permite examinar el Interaction Path. Si hubiera más de un Intent en la skills podría ser muy interesante, pero de esta forma básicamente nos demuestra la paciencia y persistencia que tiene la gente pidiendo sonidos de Fortnite a su dispositivo.

![Interaction path, inicio](/wp-content/uploads/2020/02/fortnite-interaction.PNG)

El Interation Path de la consola solo muestra datos de la última semana. En este caso vemos los siguientes puntos clave acerca de __cuántos sonidos reproduce cada usuario en una única sesión__:

* 2600 usuarios lanzaron la aplicación y solicitaron al menos dos sonidos. Una pequeña parte de ellos no respondieron (reprompt).
* 1955 pidieron al menos 3 sonidos.
* 1240 pidieron 5.
* 385 pidieron 10.
* 130 pidieron 15.
* 48 pidieron 21.
* 15 pidieron 32.
* 3 pidieron 46.
* Y 1 usuario, héroe, líder de masas, incognoscible dios de la perseverancia, __escuchó pacientemente 56 sonidos completos en una única sesión__, hasta quedar satisfecho.

![Interaction path, grafico](/wp-content/uploads/2020/02/fortnite-interaction-graph.PNG)

Aquí tenemos al genuino ganador cruzando la línea de meta:

![Interaction path, level 56](/wp-content/uploads/2020/02/fortnite-interaction-2.jpeg)

## Retención

La consola muestra la retención calculada semanalmente, y solo retiene 12 semanas de datos, así que solo puedo mostrar los datos más recientes. Una lástima. La verdad es que el dashboard tiene MUCHAS cosas que mejorar.

![Retención](/wp-content/uploads/2020/02/fortnite-retencion.jpeg)

Como puede verse en el diagrama anterior, el _engage_ de la skill no está nada trabajado y obviamente, se hace notar. Muchas veces he pensado en mejorar esto, pero la verdad es que tengo otro proyecto en mente (también para Alexa) mucho más ambicioso e interesante. Y tampoco tengo ganas de meter mucho las manos en una skill con la que Epic Games podría llamar a mi puerta cualquier día y pedirme amablemente que retire.


## Conclusión

Me ha llevado un rato compilar esta información, pero tenía ganas de hacerlo. ¡espero que os haya resultado útil e interesante!

Debo decir, por cierto, que Amazon finalmente me envió un Amazon Echo Plus de 2º Generación que desde entonces nos hace compañia desde su rinconcito del salón. 

También me gustaría agradecer a [@germanviscuso](https://twitter.com/germanviscuso), evangelista de Amazon Alexa, que supo transmitir su pasión por la [#VoiceFirst](https://twitter.com/search?q=%23VoiceFirst), que tanta paciencia tuvo conmigo en twitter y que me ha invitado a algún que otro taller en el que siempre he aprendido muchísimo y de los que salí entusiasmado por hacer más cosas relacionadas con las VUI. 

Si te interesa ver el código fuente de la skill o quieres saber más acerca de su desarrollo, o más información de las estadísticas, etc. o si quieres compartir tu propia experiencia o darme consejos para mejorar, no lo dudes: __¡deja un comentario con tu opinión!__
