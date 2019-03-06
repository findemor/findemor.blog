---
id: 455
title: Termostato remoto con Intel Edison y NodeJS
date: 2016-12-04T15:58:54+00:00
author: findemor
layout: post
guid: http://blog.findemor.es/?p=455
permalink: /2016/12/termostato-remoto-con-intel-edison-y-nodejs/
image: /wp-content/uploads/2016/12/IMG_20161204_142603-1560x738.jpg
categories:
  - API
  - Desarrollo
  - hobby
  - How to
  - Tecnología
tags:
  - hackathon
  - Intel Edison
  - IoT
  - nodejs
---
## Motivación

Hace algún tiempo participé en un hackathon de movilforum_ donde los evangelizadores de Intel anunciaban la placa <a href="https://software.intel.com/es-es/iot/hardware/edison" target="_blank">Intel Edison</a> (entre ellos mi amigo <a href="https://twitter.com/franbuitragoPa" target="_blank">@franbuitragoPa</a>).

El hackathon estaba orientado al desarrollo de proyectos relacionados con IoT (el famoso Internet de las Cosas) y mi compañero <a href="https://twitter.com/dennyf11" target="_blank">@dennyf11</a> y yo mismo nos pusimos a currar hombro con hombro y finalmente presentamos un proyecto para domótica del hogar al que bautizamos como proyecto Aurora.

La idea de partida consistía en un termostato que aprendiese automáticamente cuales eran los mejores patrones para calentar la casa en función del clima en el exterior y en el interior de la casa. En lugar de realizar un estudio de la distribución de las habitaciones y otras características, lo que haría sería aprender para minimizar el tiempo de encendido y proporcionar calorcito en las horas que estuviese programado. Con el tiempo, incluso, podría beneficiarse de la temperatura que emanase de las casas vecinas, si se trataba de un piso, ya que respondería a sus propios patrones de comportamiento e influiria en nuestro &#8220;clima&#8221;.

Después la verdad es que se nos empezó a &#8220;ir la pelota&#8221; y empezamos a diseñar una especie de &#8220;mente enjambre&#8221; que hiciera que los termostatos de todas las casas vecinas funcionasen de forma colaborativa para maximizar este efecto&#8230; y muchas cosas más.

<div id="attachment_462" style="width: 310px" class="wp-caption aligncenter">
  <a href="http://blog.findemor.es/wp-content/uploads/2016/12/hackathon.jpg" rel="attachment wp-att-462"><img class="size-medium wp-image-462" src="http://blog.findemor.es/wp-content/uploads/2016/12/hackathon-300x225.jpg" alt="Dani, Majs y yo en el hackathon" width="300" height="225" srcset="http://blog.findemor.es/wp-content/uploads/2016/12/hackathon-300x225.jpg 300w, http://blog.findemor.es/wp-content/uploads/2016/12/hackathon-768x576.jpg 768w, http://blog.findemor.es/wp-content/uploads/2016/12/hackathon-1024x768.jpg 1024w, http://blog.findemor.es/wp-content/uploads/2016/12/hackathon-1560x1170.jpg 1560w" sizes="(max-width: 300px) 100vw, 300px" /></a>
  
  <p class="wp-caption-text">
    Majs, Dani y yo en el hackathon
  </p>
</div>

Cuando el hackathon finalizó, nos llevamos cada uno un Intel Edison y un kit de groove a casa, y la vida cotidiana hundió nuestro proyecto en las profundidades más oscuras del armario mas recóndito de la casa&#8230;

Pero llegó el invierno, y miré el termostato instalado en mi casa (un botón analógico de encendido apagado con una rueda de temperatura y sin opciones de programación) y decidí rescatar la placa Intel Edison de su olvido y hacer un termostato programable y accesible via HTTP, para programarlo desde el móvil, por ejemplo.

<!--more-->

## El hardware

Una de las cosas que más me gustó, desde la perspectiva de aquellos a los que nos gusta cacharrear pero o bien no tenemos mucho tiempo, o nuestros conocimientos de electrónica no son envidiables, fue la placa del <a href="https://www.seeedstudio.com/Grove-starter-kit-plus-Intel-IoT-Edition-for-Intel-Galileo-Gen-2-and-Edison-p-1978.html#" target="_blank">Groove </a>con sus correspondientes sensores, que tambien pueden adquirirse sueltos.

Con estos pequeños componentes, que se enchufan en plan &#8220;plug&play&#8221;, prototipar o juguetear con el IoT es pan comido, y para la mayoría de los proyectos no es necesario ni siquiera pararse a dibujar esquemas. Para este proyecto necesitamos los siguiente:

  * Procesador Intel Edison + Placa Groove para prototipado
  * <a href="https://software.intel.com/en-us/node/557432" target="_blank">Display</a>
  * <a href="https://software.intel.com/en-us/iot/hardware/sensors/grove-temperature-sensor" target="_blank">Sensor de temperatura</a>
  * <a href="https://software.intel.com/en-us/iot/hardware/sensors/grove-button" target="_blank">Botón</a>
  * <a href="https://software.intel.com/en-us/iot/hardware/sensors/grove-relay" target="_blank">Relé</a>
  * <a href="https://www.seeedstudio.com/Grove-Rotary-Angle-Sensor-p-770.html" target="_blank">Sensor de rotación </a>

Además, necesitaremos algunos cables, clemas, un enchufe hembra para conectar la alimentación&#8230; y yo recomendaría añadir un botón de apagado de corriente, por ejemplo para reiniciar Intel Edison sin tener que desenchufarlo.

Conectamos el display a uno de los I2C de la placa,  botón al D8, sensor de rotación al A1, sensor de temperatura al A0, y el relé al D4. Al relé también habrá que conectar, por supuesto, el par de cables de control que van al termostato de casa y que controlan el encendido y apagado de los radiadores.

<div id="attachment_467" style="width: 310px" class="wp-caption aligncenter">
  <a href="http://blog.findemor.es/wp-content/uploads/2016/12/IMG_20161204_140923.jpg" rel="attachment wp-att-467"><img class="size-medium wp-image-467" src="http://blog.findemor.es/wp-content/uploads/2016/12/IMG_20161204_140923-300x209.jpg" alt="Vista del interior" width="300" height="209" srcset="http://blog.findemor.es/wp-content/uploads/2016/12/IMG_20161204_140923-300x209.jpg 300w, http://blog.findemor.es/wp-content/uploads/2016/12/IMG_20161204_140923-768x534.jpg 768w, http://blog.findemor.es/wp-content/uploads/2016/12/IMG_20161204_140923-1024x712.jpg 1024w, http://blog.findemor.es/wp-content/uploads/2016/12/IMG_20161204_140923-1560x1084.jpg 1560w" sizes="(max-width: 300px) 100vw, 300px" /></a>
  
  <p class="wp-caption-text">
    Vista del interior
  </p>
</div>

Para montarlo todo un poco más apañado, compre una caja de cables de esas que se empotran en la pared, lo suficientemente grande para que entrara todo, y lo atornille dentro. Los sensores y botones los dejé fuera, y para sujetarlos hice taladros en el plastico de la tapa de la caja, y pasé un clavo por los agujeros de las placas, poniendo una tuerca de metrica 3 entre la placa y el plastico, para separarlos un poco. Por el lado contrario de la tapa, corté parte del sobrante del clavo con un alicate y lo sujeté con unos toques de la pistola termofusible.

<div id="attachment_464" style="width: 310px" class="wp-caption aligncenter">
  <a href="http://blog.findemor.es/wp-content/uploads/2016/12/IMG_20161204_140740.jpg" rel="attachment wp-att-464"><img class="wp-image-464 size-medium" src="http://blog.findemor.es/wp-content/uploads/2016/12/IMG_20161204_140740-300x173.jpg" alt="Vista frontal exterior" width="300" height="173" srcset="http://blog.findemor.es/wp-content/uploads/2016/12/IMG_20161204_140740-300x173.jpg 300w, http://blog.findemor.es/wp-content/uploads/2016/12/IMG_20161204_140740-768x443.jpg 768w, http://blog.findemor.es/wp-content/uploads/2016/12/IMG_20161204_140740-1024x591.jpg 1024w, http://blog.findemor.es/wp-content/uploads/2016/12/IMG_20161204_140740-1560x900.jpg 1560w" sizes="(max-width: 300px) 100vw, 300px" /></a>
  
  <p class="wp-caption-text">
    Vista frontal exterior
  </p>
</div>

<div id="attachment_466" style="width: 310px" class="wp-caption aligncenter">
  <a href="http://blog.findemor.es/wp-content/uploads/2016/12/IMG_20161204_142603.jpg" rel="attachment wp-att-466"><img class="size-medium wp-image-466" src="http://blog.findemor.es/wp-content/uploads/2016/12/IMG_20161204_142603-300x142.jpg" alt="Detalle de clavos y tuercas" width="300" height="142" srcset="http://blog.findemor.es/wp-content/uploads/2016/12/IMG_20161204_142603-300x142.jpg 300w, http://blog.findemor.es/wp-content/uploads/2016/12/IMG_20161204_142603-768x363.jpg 768w, http://blog.findemor.es/wp-content/uploads/2016/12/IMG_20161204_142603-1024x484.jpg 1024w, http://blog.findemor.es/wp-content/uploads/2016/12/IMG_20161204_142603-1560x738.jpg 1560w" sizes="(max-width: 300px) 100vw, 300px" /></a>
  
  <p class="wp-caption-text">
    Detalle de clavos y tuercas
  </p>
</div>

&nbsp;

## El software

Las placas Intel Edison mueven una versión concreta de Linux Yocto, y entre otras cosas puede ejecutar aplicaciones escritas en NodeJS, una tecnología que a mi personalmente me encanta e incluso me divierte. Así que no lo dude dos veces y lo escogí para desarrollar mi programa.

El programa (en su versión inicial) es muy sencillo, basicamente se trata de un API REST, que permite establecer una programación que encienda y apague lo que quiera que haya enchufado al relé (en este caso nuestros radiadores), aunque podrían ser luces, una electroválvula, etc.

Puede configurarse en 3 modos:

  * ON: modo manual, le especificamos la temperatura minima deseada y si se baja de ella, se enciende el rele.
  * OFF: apagado manual, no se encenderá independientemente de la temperatura.
  * SCHEDULE: se encenderá si hay algun programa que coincida con la fecha actual y si la temperatura de la casa es inferior a la que determine ese programa concreto. El programa puede configurarse de forma que se repita los días que se desee (solo se tendrá en cuenta la hora), o para un día específico (una excepción).

En segundo plano, cada X tiempo, comprobará en función del modo en que esté actualmente seleccionado, si se cumplen las condiciones, y se pondrá en marcha o se detendrá paso de corriente por el relé.

Los programas se almacenan usando una base de datos muy sencillita: nedb.

El display mostrará el estado actual, indicando el modo mediante un texto, así como la temperatura interior, exterior y la temperatura deseada. Además, cambiará de color en función de si está en marcha o no:

  * Azul: está en modo SCHEDULE pero no hay ningun programa que coincida ahora mismo.
  * Rojo: el relé esta encendido.
  * Amarillo: esta encendido pero el rele esta apagado, standby.
  * Pantalla apagada: apagado permanente, no se encenderá.

Para obtener la temperatura en el exterior, se conecta al servicio web de <a href="http://darksky.net" target="_blank">darksky.net</a>. Hay que establecer las coordenadas y el API-KEY en el fichero de configuración config.json en la raiz del proyecto.

Una vez configurado el proyecto en el config.json, podemos cargarlo en nuestra placa Intel Edison utilizando el IDE de Intel IoT, y este será accesible mediante el puerto 8000 (tambien configurable).

### Testing unitario

Incluí en el proyecto algunas pruebas unitarias para verificar que las programaciones se calculaban correctamente, si se desea lanzar fuera de la placa (por ejemplo ejecutarlo en Windows), algunas dependencias propias de Intel Edison no funcionará, por lo que se utilizarán conectores &#8220;fake&#8221; para simular los sensores. Para activar esta opción y poder lanzar las pruebas, hay que cambiar &#8220;node\_env&#8221;: &#8220;production&#8221; por &#8220;node\_env&#8221;: &#8220;development&#8221; en el fichero de configuración.

Despues, lanzando un &#8220;mocha&#8221; podremos comprobar la validez de las pruebas unitarias.

No te olvides de volver a poner &#8220;production&#8221; si quieres ejecutarlo dentro de la placa y leer los sensores&#8230;

## Funcionamiento

Dentro de la carpeta del proyecto, en la carpeta doc, está disponible la especificación del API en formato RAML, que es fácil de entender y para todos los públicos.

Además, hay más ejemplos para cada llamada y cada caso en &#8220;doc/api-ex&#8221;

Esta es la pinta que tiene la documentación, <a href="https://rawgit.com/findemor/aurora-thermostat/master/doc/doc.html" target="_blank">que podéis consultar aquí</a>:

<a href="http://blog.findemor.es/wp-content/uploads/2016/12/api-aurora.png" rel="attachment wp-att-459"><img class="aligncenter size-medium wp-image-459" src="http://blog.findemor.es/wp-content/uploads/2016/12/api-aurora-300x176.png" alt="doc " width="300" height="176" srcset="http://blog.findemor.es/wp-content/uploads/2016/12/api-aurora-300x176.png 300w, http://blog.findemor.es/wp-content/uploads/2016/12/api-aurora-768x452.png 768w, http://blog.findemor.es/wp-content/uploads/2016/12/api-aurora-1024x602.png 1024w, http://blog.findemor.es/wp-content/uploads/2016/12/api-aurora.png 1025w" sizes="(max-width: 300px) 100vw, 300px" /></a>

Si nos conectamos al API usando Postman, por ejemplo, podemos añadir programas, modificarlos, borrarlos, y cambiar o consultar el estado del termostato. ¡Es muy fácil!

Tened en cuenta que para acceder, hay que añadir el atributo Authorization a la cabecera con el api-key, que es configurable en el config.json, y que en la demo es simplemente &#8220;pass&#8221;.

## Conclusiones y código

Bueno, pues llegamos al final: este proyecto pretendía ser un ejemplo de lo fácil que es hacer algo así con Intel Edison + Groove + NodeJS. Creo que está bastante chulo y en mis pruebas ha funcionado correctamente. Sin embargo, si alguno se anima a reproducirlo, no me hago responsable de la que lieis en casa! pero tened cuidado al manipular cables de corriente, no vayais a tener un disgusto.

<div id="attachment_463" style="width: 235px" class="wp-caption aligncenter">
  <a href="http://blog.findemor.es/wp-content/uploads/2016/12/IMG_20161120_132840.jpg" rel="attachment wp-att-463"><img class="size-medium wp-image-463" src="http://blog.findemor.es/wp-content/uploads/2016/12/IMG_20161120_132840-225x300.jpg" alt="Así estaba colgado provisionalmente y enchufado al control de los radiadores de mi casa" width="225" height="300" srcset="http://blog.findemor.es/wp-content/uploads/2016/12/IMG_20161120_132840-225x300.jpg 225w, http://blog.findemor.es/wp-content/uploads/2016/12/IMG_20161120_132840-768x1024.jpg 768w, http://blog.findemor.es/wp-content/uploads/2016/12/IMG_20161120_132840-1560x2080.jpg 1560w" sizes="(max-width: 225px) 100vw, 225px" /></a>
  
  <p class="wp-caption-text">
    Así estaba colgado provisionalmente y enchufado al control de los radiadores de mi casa
  </p>
</div>

Me he quedado (de momento) con ganas de añadirle más cosas al proyecto, como registrar de forma remota (en una base de datos de mongodb, por ejemplo) el estado cada cierto tiempo, para luego hacer algunos análisis de consumo, enfrentandolo a la temperatura exterior, y otros insights.

Además, y sobre todo, esto pide un cliente WEB como el comer&#8230; lo mismo me lanzo a desarrollar alguno en Angular, que hace tiempo que no lo toco, por que claro&#8230; entrar con Postman está bien para un rato, pero no es para todos los públicos.

En fin, si os animáis, no dudéis en descargar el código fuente desde el repositorio de GitHub:

> <a href="https://github.com/findemor/aurora-thermostat" target="_blank">https://github.com/findemor/aurora-thermostat</a>

¡Y colaborad en crear el termostato definitivo para vuestros hogares!