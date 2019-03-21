---
id: 286
title: Prefab gratis para hacer transiciones FadeIn / FadeOut entre escenas en Unity 3D
date: 2013-10-25T14:53:23+00:00
author: findemor
layout: post
guid: http://blog.findemor.es/?p=286
permalink: /2013/10/prefab-gratis-para-hacer-transiciones-fadein-fadeout-entre-escenas-en-unity-3d/
image: /wp-content/uploads/2013/10/mazemaster-64x64.png
categories:
  - Desarrollo
  - How to
tags:
  - Unity
---
Hace poco he empezado a hacer algunas pruebas con Unity para hacer algún **juego en Android**. Mi experiencia está siendo muy positiva, porque hace largo tiempo trabajé con Div Game Studio y Phoenix, y me divertía muchísimo... y esto ha sido un reencuentro con el mundo del _indiegame_ genial.

Sin embargo, encuentro que aún hay muchas cosas que le faltan a Unity y que no se porqué no están ahí... una de las más sencillas es la posibilidad de **hacer transiciones para pasar de una escena a otra**, con un **simple Fade In o Fade Out desde negro**.

<!--more-->

Para completar mi prueba con Unity decidí hacer un sencillo **Prefab** y compartirlo con vosotros, por si a alguien le resulta de utilidad, **lo podéis descargar pinchando [AQUÍ](http://www.findemor.es/resources/downloads/UnitySceneFade.unitypackage "Findemor Unity FadeIn prefab")**.

Y podeis **verlo en marcha en el juego MazeMaster Skully** aquí:

<p style="text-align: center;">
  <strong><a title="Maze Master Skully free Android game" href="https://play.google.com/store/apps/details?id=com.devergence.mazemaster" target="_blank">https://play.google.com/store/apps/details?id=com.devergence.mazemaster</a></strong>
</p>

<div style="height: 320px;">
  <a style="float: left;" href="https://play.google.com/store/apps/details?id=com.devergence.mazemaster"><img class="size-medium wp-image-298" title="MazeMaster Skully free Android Game" alt="MazeMaster Skully free Android Game" src="http://blog.findemor.es/wp-content/uploads/2013/10/2013-10-22-21.57.46-300x168.png" width="400" height="250" /></a>
</div>

### Instalación (Importación)

Basta con ir en Unity al menú **Assets / Import Package / Custom Package** y elegir el fichero **UnitySceneFade.unitypackage que habeis descargado** para poder completar la instalación en vuestro proyecto.

### Uso

Una vez importado el proyecto, podéis **arrastrar el Prefab llamado SceneFade a vuestra jerarquía de objetos en la escena en que deseeis usarlo**.

Si seleccionáis el nuevo objeto que habéis añadido a vuestra escena podréis ver lo siguiente en el inspector:

[<img class="aligncenter size-medium wp-image-291" title="Inspector Scene Fade Unity" alt="Inspector Scene Fade Unity" src="http://blog.findemor.es/wp-content/uploads/2013/10/InspectorSceneFadeUnity-300x243.jpg" width="300" height="243" />](http://blog.findemor.es/wp-content/uploads/2013/10/InspectorSceneFadeUnity.jpg)

La descripción de los atributos es la siguiente:

  * **FadeOutExture** : textura que se mostrara al hacer el fadeOut, por defecto es negro.
  * **PlayOnAwake**: se va a ejecutar en cuanto se cargue la escena.
  * **StartFadingIn**: cuando PlayOnAwake está seleccionado, este check indica si hará fadeIn al empezar, o si por el contrario hará FadeOut.
  * **FadeInDuration** y **FadeOutDuration**: tiempo en segundos que dura la transición.

Os invito a hacer pruebas con la configuración que más os guste.

**Si quereis reproducir la transición desde código**, por ejemplo **al pulsar un botón**, basta con obtener la referencia del objeto instanciado SceneFade y ejecutar el método oportuno. Por ejemplo, para hacer FadeOut al pulsar el boton podeis hacer algo así:

```js
function OnMouseDown(){
  GameObject.Find("SceneFade").GetComponent(sceneFade).FadeOut();
  }
```


Si abrís el fichero **sceneFade.js** podeis encontrar **más métodos útiles** o modificarlos a vuestro antojo.

Un saludo!

_Y ahora unos tags en inglés para facilitar la busqueda a los usuarios extranjeros:_

## Free Unity 3D Prefab for scene FadeIn and FadeOut transitions

If you are looking for a free and simple (easy to use) Unity 3D Prefab for Fade In and Fade Out transition between scenes, you can try this:

  1. **Download prefab package from [HERE](http://www.findemor.es/resources/downloads/UnitySceneFade.unitypackage "Findemor Unity FadeIn prefab")**.
  2. Import **UnitySceneFade.unitypackage in your project** from Unity menu: **Assets / Import Package / Custom Package**
  3. **Drag SceneFade prefab** to your scene Hierarchy panel.
  4. Configure SceneFade object in Inspector for your own customization.

  * **FadeOutExture** : fade out texture
  * **PlayOnAwake**: Play when scene starts
  * **StartFadingIn**: if PlayOnAwake is selected, play FadeIn (if selected) or FadeOut at start
  * **FadeInDuration** and **FadeOutDuration**: duration (seconds) for transition

  1. Or play transitions from codebehind by doing:

```js
function OnMouseDown(){
  GameObject.Find("SceneFade").GetComponent(sceneFade).FadeOut();
  }
```


Read sceneFade.js for more methods.

Enjoy!