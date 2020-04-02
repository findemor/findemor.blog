---
title: 'Impresión 3D y viseras para combatir el coronavirus'
date: 2020-03-24T12:40:48+00:00
author: findemor
layout: post
image: /wp-content/uploads/2020/03/24/portada.png
share-img: /wp-content/uploads/2020/03/24/portada.png
categories:
  - Impresion3D
tags:
  - Impresion3D
---

Con motivo de la lamentable situación provocada por la pandemia de COVID-19, y la escasez de productos comerciales para la protección contra el virus, la comunidad Maker se ha lanzado a colaborar en la medida que nos es posible para imprimir con equipos, tanto profesionales como aficionados, a diseñar, producir y enviar mascarillas, piezas para los respiradores mecánicos y viseras de PVC, entre otras cosas.

Hoy mismo ha enviado la Consejería de Sanidad la aprobación y validación de uno de los modelos en los que la comunidad ha trabajado incansablemente los últimos días, y la gente se ha puesto inmediatamente a manos a la obra.

## Enlaces de interés

Obviamente con este post solo intento aportar como he solucionado mis problemas y espero que el resultado sirva. La única fuente de verdad y punto de partida para quienes desean colaborar deben ser en primer lugar los canales oficiales. Además, este post podría quedar rápidamente obsoleto, tenlo en cuenta y saca provecho a lo que necesites.

* [WEB DE MAKERS COVIDMADRID (DISEÑO, LOGISTICA, ENVIOS, MATERIAL)...](https://covidmadrid.com/)
* [WEB DEL PROYECTO DE LA VISERA "OFICIAL" PARA HOSPITALES](https://wikifactory.com/+covidmakersmadrid/visera-hospitales)
* [GRUPO DE TELEGRAM DE FABRICACIÓN DE MADRID: @cv19_fab_info](https://web.telegram.org/#/im?p=@cv19_fab_info)


Si solo quieres el modelo, aqui lo tienes.
> __[DESCARGA MODELO OFICIAL MODIFICADO PARA ENDER 3 DESDE THINGVERSE](https://www.thingiverse.com/thing:4240059)__

## Modelo oficial

El modelo oficial pude obtenerse [ESTE ENLACE A LA WEB DE MAKERS COVIDMADRID](https://covidmadrid.com/). Si en tu caso puedes hacerlo, siempre intenta producir modelos oficiales en la última versión.


### Adaptaciones al modelo oficial y reglas de la casa

En mi caso, tengo una impresora Ender 3, con una cama caliente relativamente pequeña, y desde luego no es la mejor calibrada del mundo. No es lo ideal para producir el modelo oficial.

Sin embargo entiendo que si han validado este estoy seguro de que tiene ventajas de peso, y decidí intentarlo, por lo que hice numerosas modificaciones y pruebas sobre el modelo oficial hasta que consegui que mi impresora aceptase esta impresión con bastantes garantías de resultar funcional.

> __[DESCARGA MODELO OFICIAL MODIFICADO PARA ENDER 3 DESDE THINGVERSE](https://www.thingiverse.com/thing:4240059)__

[![DOWNLOAD](/wp-content/uploads/2020/03/24/download.jpg)](https://www.thingiverse.com/thing:4240059)


__Las modificaciones__ que he realizado sobre la verión 1.0 del modelo oficial espero que sean inocuas para su funcionalidad pero que le otorguen mayor robustez y que permitan que pueda ser impreso en modelos como la Ender 3 y por lo tanto llegar aun mayor número de personas, hago un repaso de los retoques:

* __Aumento del tamaño de los agujeros__ por los que hay que pasar los ejes en un factor de 1,08 para que no haya que forzar demasiado al montarla y aun así no se salga.
* __Añado algunos discos semicirculares en los puntos más delicados__ (4 en total) para facilitar la adhesion de las primeras capas, evitar el warping, etc. Con esta modificación es posible que aún así haya fallos en la primera capa y se despeguen algunos filamentos, pero he comprobado que tiene bastante tolerancia a los errores y al depositarse la segunda o tercera capa suelen resolver el problema. No obstante conviene vigilarlo al principio por si hay que "cortar algun pelillo" durante la impresion. No es necesario retirarlos después.
* __Añado soportes bajo los ejes.__ Como debo imprimir el modelo desmontado en dos piezas, es necesario poner soportes bajo los ejes, podemos hacer esto con Ultimaker Cura, por ejemplo, pero prefiero poner mis propios ejes a mano, me dan mejor resultado y son más solidos y se adhieren mejor a la mesa. Hay que retirarlo tras la impresion antes del montaje.
* Con estas modificación no hace falta Brim o Raft, por lo que entra en la cama perfectamente, y se despega lo justo.
* __He añadido un refuerzo rectangular en la parte más delicada__ de la patilla. La razón es que en esta zona se me estaban partiendo al intentar introducir el eje por el orificio de la visera durante el montaje. Es la única modificacion que creo que puede afectar a su funcionalidad, espero que no produzca rozaduras y que alargue la vida de la visera.

### Ubicación en la cama y tiempos de fabricado

En Ultimaker Cura, antes de generar el fichero .gcode, importé las dos piezas grandes del modelo y las coloqué de forma que entren en la cama. 
Esto incrementa el riesgo de que si algo falla tenga que tirar más material al parar la impresión, pero me hace estar atento a las primeras capas un mayor número de veces y me permite desatender más la impresión.

![Cura](/wp-content/uploads/2020/03/24/cura.png)

* Cada visera lleva unas 2h 40m sin contar las arandelas.
* 35g de material PLA.
* 11.86m de filamento PLA.

Las arandelas (segun el diseño oficial son necesarias) no las incluyo porque prefiero imprimirlas a parte. Las imprimo una cama llena de ellas y le cambio en Cura la propiedad __Horizontal Expansion__ a -0.5mm para facilitar su colocación sin que se parta. Esto hay que probar con cada impresora hasta que entra ajustado. Además las imprimo a una temperatura superior, 210ºC, para que se funda mejor el filamento.

### Propiedades de impresión

Utilizo las propiedades que recomiendan en la web de la visera oficial. Con las siguientes excepciones.

* Temperatura de la cama 60ºC
* Sin soportes ni capa de adhesión
* Horizontal Expansion 0mm salvo para las arandelas.
* Material > Flow al 85%

### Montaje

Un video vale más que mil palabras:

<iframe width="560" height="315" src="https://www.youtube.com/embed/oxOJ0z7WJ-g" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

### Fotografias del proceso

> Gracias a los añadidos de adhesión se puede ver que la primera capa soporta cierta cantidad de fallos que se resuelven al irse depositando capas posteriores

![Cama](/wp-content/uploads/2020/03/24/cama.png)
![Cama 2](/wp-content/uploads/2020/03/24/cama2.png)

> Piezas sueltas y arandelas, destaco con lapiz algunos de los añadidos al modelo original

![Piezas sueltas](/wp-content/uploads/2020/03/24/anadidos.jpg)

> Asi queda una vez montado

![Montado 1](/wp-content/uploads/2020/03/24/montado.png)
![Montado 2](/wp-content/uploads/2020/03/24/montado2.png)


## Modelo alternativo "media"

Lamentablemente el modelo escogido presenta algunas características que dificultan su impresión en algunas de las impresoras más modestas, como es mi caso. Por esta razón, algunos makers que no pueden imprimir el modelo "homologado" prefieren no quedarse parados y seguir fabricando el modelo de media visera, cuyo fichero .stl podeis descargar [EN ESTE ENLACE](/wp-content/uploads/2020/03/24/modelo_media_visera.stl).

Este modelo de "media visera" o "media" es más fácil y rápido de imprimir, aparentemente más robusto y más de "batalla". Además, siguen siendo útiles para otras personas expuestas que no trabajen en sanidad y que también necesitan protección.

Eso sí, también presenta desventajas, ya que debido a su forma y características no es apto para el uso por parte de los sanitarios durante varias horas, es más incómodo de manejar, peor ventilado, etc. Tened en cuenta el trabajo que los sanitarios deben realizar con esto puesto, y el número de horas que lo llevan encima...

## Feedback, consultas, sugerencias

Por supuesto esto solo lo comparto por si a alguien le viene bien y puede utilizarlo para resolver sus propios problemas para imprimir el modelo oficial (o cualquier otro). Úsalo como prefieras.

Cualquier sugerencia para mejorar el diseño o correcciones al mismo que se me hayan podido escapar son más que bienvenidas.

Igualmente, si tienes preguntas o crees que falta información puedes consultarme e intentaré ayudar.

Podéis contactarme en los comentarios o por [twitter @findemor](https://twitter.com/findemor).
