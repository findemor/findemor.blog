---
id: 92
title: 'Como programar en Android: Guia 1'
date: 2011-01-24T16:30:03+00:00
author: findemor
layout: post
guid: http://blog.findemor.es/?p=92
permalink: /2011/01/como-programar-en-android-guia-1/
categories:
  - Android
  - Desarrollo
  - Java
tags:
  - Android
  - Java
---
Hace poco he empezado a interesarme por el desarrollo de aplicaciones en Android motivado por la necesidad de cierta utilidad para mi Nexus One que, tras buscar en el Android Market, no encontré. Como no tengo experiencia con el lenguaje de programación Java, y tampoco tiempo ni ganas de estudiar más libros de sintaxis, Apis y Frameworks, decidí aprender a lo bruto: dandome directamente cabezazos contra el teclado, previa búsqueda por google claro.

Finalmente creo que he reunido algunos conocimientos para escribir una guia que me sirva para recordar lo aprendido en el futuro, y de paso tambien a aquellos de vosotros que lo necesiteis. Así pues, empezamos con la Guia 1.

<!--more-->

#### **¿Qué vamos a hacer?**

**  
** 

En la primera guía vamos a hacer el clásico &#8220;Hola Mundo&#8221;, como no podría ser de otra manera.

#### **Paso 1 &#8211; Instalar el SDK de Android**

**  
** 

Dependiendo de tu sistema operativo, la instalación puede ser diferente. En mi caso estoy empleando como IDE Eclipse para Mac en Inglés. No voy a describir el proceso de instalación del SDK de Android porque hay miles de tutoriales&#8230; en su momento yo seguí el oficial y es muy completo, esta aqui: <a href="http://developer.android.com/sdk/installing.html" target="_blank">http://developer.android.com/sdk/installing.html</a>

#### **Paso 2 &#8211; Crear un nuevo proyecto**

**  
** 

Una vez con Eclipse instalado, vamos a generar un nuevo proyecto que nos servirá de base para todas las guias que voy a hacer aquí. En eclipse vamos a File / New / Other&#8230; y en la ventana que se abre elegimos Android / Android Project, pulsamos Next y completamos los campos:

_Project name_: &#8220;TolkienLibrary&#8221; Será el nombre de nuestro proyecto.

_Application name_: &#8220;Tolkien Library&#8221; Nombre de la aplicación una vez instalada.

_Package name_: &#8220;com.example.tolkienlibrary&#8221; Namespace de nuestro proyecto.

_Create Activity_: &#8220;TolkienLibrary&#8221; Nombre de la actividad en el dispositivo.

_Min SDK Version_: &#8220;3&#8221; Version mínima para ejecutar la aplicación.

y pulsamos Finish.

#### **Paso 3 &#8211; Estructura del proyecto**

**  
** 

Ahora podemos estudiar brevemente la estructura que nos ha generado Eclipse para nuestro nuevo proyecto, vemos las siguientes carpetas:

src: contiene el código fuente de nuestra aplicación.

gen: ficheros generados por la aplicación Java.

Android: carpeta que contiene las librerias de Android.

Assets: carpeta de recursos de nuestra aplicación, por ejemplo ficheros HTML para navegación offline, ficheros de bases de datos, etc. Se comporta como una carpeta del sistema, así que puede ser listada, etc.

res: carpeta de recursos, dividida en subcarpetas, cada una con un proposito distinto.

res/drawable: imagenes para la aplicación.

res/layout: ficheros XML que definen la interfaz de usuario de nuestra aplicación o Vistas.

res/values: puede contener varias cosas, en este momento solo tenemos strings.xml, un fichero donde especificamos las cadenas de texto que vamos a mostrar en nuestra interfaz, para que sea facil cambiarlas o localizarlas.

AndroidManifest.xml: este fichero es muy importante, porque contiene información de la aplicación que el dispositivo necesita para iniciarla una vez instalada.

#### **Paso 4 &#8211; Conocer el AndroidManifest.xml**

**  
** 

Como señalaba en el paso anterior, conocer el archivo de manifiesto es muy importante, si abrimos el que tenemos ahora mismo con un editor XML o desde la vista de XML de Eclipse, vemos que contiene lo siguiente:

[sourcecode language=&#8221;xml&#8221;]  
<?xml version="1.0" encoding="utf-8"?>  
<manifest xmlns:android="http://schemas.android.com/apk/res/android"  
package="com.example.tolkienlibrary"  
android:versionCode="1"  
android:versionName="1.0">  
<application android:icon="@drawable/icon" android:label="@string/app_name">  
<activity android:name=".TolkienLibrary"  
android:label="@string/app_name">  
<intent-filter>  
<action android:name="android.intent.action.MAIN" />  
<category android:name="android.intent.category.LAUNCHER" />  
</intent-filter>  
</activity>

</application>  
<uses-sdk android:minSdkVersion="3" />  
</manifest>  
[/sourcecode]

Lo primero que hace es indicar el namespace de nuestra aplicación (package), y la versión de código y aplicación (versionCode y versionName) que son importantes para cuando vayamos a realizar actualizaciones de nuestra aplicación en el Market, versionCode sin embargo solo nos permite conocer que version de código estamos manejando durante el desarrollo, por lo que simplemente habría que aumentarlo en 1 cuando hagamos una Release.

Despues se indican el icon de la aplicación (que vemos que esta extrayendo de la carpeta de res/drawable) y el app_name, o nombre de la aplicación, que está sacando de res/values/strings.xml. Estos campos son obligatorios.

Dentro del elemento aplicación, debemos declarar las Actividades que nuestro código implementa. Este paso es muy importante, si no se declaran el dispositivo mostrara una excepción y la aplicación no funcionará. Para cada Activity se define al menos el nombre mediante android:name=&#8221;.TolkienLibrary&#8221; en nuestro caso, que corresponde al nombre de nuestra actividad anteponiendo un punto. Además, dentro del elemento Actividad, vemos que se define el subelemento <intent-filter> que nos permite indicar que esta va a ser la Actividad que el dispositivo debe arrancar al inicio de la aplicación.

#### **Paso 5 &#8211; Nuestro código fuente**

**  
** 

Vamos a la carpeta src y desplegamos hasta encontrar TolienLibrary.java y lo abrimos. Vemos lo siguiente:

[sourcecode language=&#8221;java&#8221;]  
package com.example.tolkienlibrary;

import android.app.Activity;  
import android.os.Bundle;

public class TolkienLibrary extends Activity {  
/*\* Called when the activity is first created. \*/  
@Override  
public void onCreate(Bundle savedInstanceState) {  
super.onCreate(savedInstanceState);  
setContentView(R.layout.main);  
}  
}  
[/sourcecode]

En este punto hay que tener en cuenta lo siguiente: estamos trabajando con un patrón MVC (Modelo-Vista-Controlador). En Android, cada pantalla que el usuari puede ver de nuestra aplicación (o Vista) se conoce como Layout. Cada Layout (y los eventos que en él se producen, valor de cada uno de los campos que lo componen, etc) está controlado por una Actividad. Así pues, es en las actividades donde debemos capturar los eventos del ciclo de vida de un Layout y realizar las operaciones pertinentes.

En la el código anterior podemos ver que Eclipse nos ha generado una clase &#8220;TolkienLibrary&#8221; que implementa una Actividad (clase Activity). En el evento _onCreate_ de esta Actividad se llama al evento de la clase padre y después se establece el layout que va a mostrar esta Actividad mediante _setContentView(R.layout.main);_ a partir de un Layout definido en nuestros ficheros de recursos, y cuyo identificador es **main**.

#### Paso 6 &#8211; El Layout

En el paso anterior hemos visto que la Actividad esta indicando la vista o Layout que va a motrar, y que obtenia mediante R.layout.main.

Este identificador indica que se trata de un Layout de nuestra colección de recursos (R) y que se llama main. Entonces, segun vimos en la estructura de nuestra aplicación en el **Paso 3**, vamos a la carpeta de recursos en res/layout/ y efectivamente nos encontramos con el layout main.xml.

Si hacemos doble click en el archivo, se abrirá el editor gráfico, que personalmente no me gusta, así que vamos a la vista XML (en la parte inferior de la ventana, junto a Graphical Layout), y observamos su contenido.

[sourcecode language=&#8221;xml&#8221;]  
<?xml version="1.0" encoding="utf-8"?>  
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"  
android:orientation="vertical"  
android:layout\_width="fill\_parent"  
android:layout\_height="fill\_parent"  
>  
<TextView  
android:layout\_width="fill\_parent"  
android:layout\_height="wrap\_content"  
android:text="@string/hello"  
/>  
</LinearLayout>

[/sourcecode]

Vemos que contiene dos elementos: **LinearLayout** y **TextView**.

En este momento no voy a dedicar mucho tiempo a explicar los distintos Widgets que pueden incluir las vistas, en el <a rel="nofollow" href="http://www.ubicuos.com/2010/05/11/diseo-de-interface-con-android-manejo-de-layouts/" target="_blank">Blog de Ubicuos</a> teneis un post muy atractivo para un primer contacto.

Tan solo decir que LinearLayout es un contenedor que mostrará los componentes que contiene alineados uno detrás de otro, y sus parametros indican que ocupará toda la pantalla y que la alineación se hará verticalmente. LinearLayout contiene un TextView que muestra un texto, y que este texto está siendo extraido de otro fichero de recuros, el que contiene las cadenas localizadas: strings.xml.

Esto se hace mediante **@string/** para indicar que vamos a buscar el el fichero de cadenas localizadas string.xml, seguido de **hello**, que es la clave de la cadena que deseamos mostrar en este TextView.

#### Paso 7 &#8211; Fichero de cadenas localizadas Strings.xml

Para abrir el fichero vamos a res/values/strings.xml y vemos que se trata de una colección de &#8220;claves/valor&#8221; para nuestras cadenas localizadas.

La cadena que estabamos usando en el Layout del paso anterior tenia la clave **hello**, y si hacemos clic en ella, a la derecha nos aparece su contenido:

_Name:_ &#8220;hello&#8221; es la clave

_Value:_ &#8220;Hello World, TolkienLibrary!&#8221; es el valor que se mostrará al referirse a esta clave.

Vamos a cambiar el mensaje &#8220;Hello World, TolkienLibrary&#8221; por &#8220;Hola Arda!&#8221;

Ademas de este cambio, tambien resulta igual de sencillo crear nuevas claves o eliminarlas, mediante los botones Add y Remove, o directamente editando el contenido xml del fichero.

[sourcecode language=&#8221;xml&#8221;]  
<?xml version="1.0" encoding="utf-8"?>  
<resources>  
<string name="hello">Hola Arda!</string>  
<string name="app_name">Tolkien Library</string>  
</resources>

[/sourcecode]

#### Paso 8 &#8211; Ejecutar el proyecto

Pues con esto hay tendriamos nuestro particular Hola Mundo (en este caso Hola Arda, un mundo mucho más épico que el nuestro, dónde va a parar), preparado para ejecutarse, tan solo vamos a Run/Run y elegimos AndroidApplication en la ventana que aparece.

Esto lanzará el emulador que hayamos configurado durante la instalación de Eclipse, y veremos algo como esto:

#### [<img class="aligncenter size-medium wp-image-104" title="Hola Arda Screenshot" src="http://blog.findemor.es/wp-content/uploads/2011/01/HolaArdaScreenshot-300x210.jpg" alt="" width="300" height="210" />](http://blog.findemor.es/wp-content/uploads/2011/01/HolaArdaScreenshot.jpg)Paso 9 &#8211; Futuras guías&#8230;

Esta primera guía era una simple toma de contacto con un proyecto de Android en Eclipse, en futuras guias veremos más cosas que nos permitirán desarrollar aplicaciones más ricas (que por otro lado&#8230; no es dificil superar lo que tenemos hecho hasta ahora ).

<a href="http://blog.findemor.es/2011/01/como-programar-en-android-guia-1" target="_self">Guia 1 &#8211; Hola Mundo en Android.</a>

[Guia 2 &#8211; Navegación entre Layouts con un botón, y paso de parámetros.](http://blog.findemor.es/2011/01/como-programar-en-android-guia-2/)

[Guia 3 &#8211; ListView o listas con scroll.](http://blog.findemor.es/2011/02/como-programar-en-android-guia-3)

[Guia 4 &#8211; ListView ricas con celdas personalizadas e imágenes.](http://blog.findemor.es/2011/02/como-programar-en-android-guia-4)

[Guia 5 &#8211; Acceso a base de datos (SQLLite).](http://blog.findemor.es/2011/02/como-programar-en-android-guia-5)