---
id: 108
title: 'Como programar en Android: Guia 2'
date: 2011-01-26T22:05:52+00:00
author: findemor
layout: post
guid: http://blog.findemor.es/?p=108
permalink: /2011/01/como-programar-en-android-guia-2/
categories:
  - Desarrollo
  - Cursos
tags:
  - Android
  - Java
---
En esta guia vamos a aprender **cómo navegar entre dos Layouts (vistas) mediante la pulsación de un botón,** enviando parámetros de una vista a otra.

Antes de leer esta guia, quizá te interese echar un vistazo a [la anterior, donde explicábamos la estructura de una aplicación Android en Eclipse e implementábamos un sencillo Hola Mundo](http://blog.findemor.es/2011/01/como-programar-en-android-guia-1/). Vamos a **continuar con ese proyecto** para completar la Guia 2.

#### <!--more-->

#### ¿Qué vamos a hacer?

Vamos a continuar con el proyecto de la Guia 1 para explicar como crear una segunda vista, que se mostrará cuando pulsemos en el botón que vamos a colocar en la primera vista. Además, enviaremos un parámetro de la primera a la segunda vista.

#### Paso 1 - Crear el segundo Layout

Como todas las vistas, debe estar localizada en la carpeta de recursos /res/layout, así que vamos a esa carpeta y haciendo clic derecho en ella, elegimos New/Other.../AndroidXMLFile y damos a Next. En la ventana que abre rellenamos el campo “_File_”; dandole valor “lista.xml”; y pulsamos Finish.

Como en la ventana anterior teniamos seleccionado “LinearLayout”; como RootElement, nuestro Layout “lista.xml”; contendrá eso como único elemento.

```xml  
<?xml version="1.0" encoding="utf-8"?>  
<LinearLayout  
xmlns:android="http://schemas.android.com/apk/res/android"  
android:orientation="vertical"  
android:layout_width="fill_parent"  
android:layout_height="fill_parent">  
</LinearLayout>  
```


Dentro de este contendor LinearLayout, vamos a incluir un TextView donde se mostrará más adelante el parámetro enviado desde la primera vista. Queda así:

```xml  
<?xml version="1.0" encoding="utf-8"?>  
<LinearLayout  
xmlns:android="http://schemas.android.com/apk/res/android"  
android:orientation="vertical"  
android:layout_width="fill_parent"  
android:layout_height="fill_parent">  
<TextView android:id="@+id/campotexto"  
xmlns:android="http://schemas.android.com/apk/res/android"  
android:layout_width="wrap_content"  
android:layout_height="wrap_content"/>  
</LinearLayout>  
```


El simbolo “+”; en el atributo id del TextView indica al compilador que debe agregar un nuevo recurso, de forma que luego será accesible por código así _R.id.campotexto_ . Podemos aprender más sobre esto en la [documentación](http://developer.android.com/guide/topics/ui/declaring-layout.html).

#### Paso 2 - Incluir un botón en el primer Layout

Ahora que tenemos listo el segundo Layout, vamos a poner un botón en el primer Layout (recordemos que en la Guia 1 lo llamamos main.xml) que será el que abrirá la segunda vista de nuestra aplicación.

Abrimos el recurso xml res/layout/main.xml y en lugar del TextView que teníamos, vamos a poner un botón:

```xml  
<?xml version="1.0" encoding="utf-8"?>  
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"  
android:orientation="vertical"  
android:layout_width="fill_parent"  
android:layout_height="fill_parent"  
>  
<Button android:text="Ver Lista"  
android:id="@+id/Button02"  
android:layout_width="200px"  
android:textSize="15px"  
android:layout_height="55px">  
</Button>  
</LinearLayout>  
```


#### Paso 3 - Añadir la segunda Actividad

Recordemos que en un patrón MVC como este, cada Layout (Vista) está controlado por una Actividad.

Ya hemos diseñado el segundo Layout, ahora vamos a implementar su Actividad.

Hacemos clic derecho sobre nuestro paquete de código fuente src/com.example.tolkienlibrary y elegimos New/Class... y en la ventana que se abre le damos un nombre, en el campo “Name”; con valor “ControladorLista”; y pulsamos Finish.

Ahora abrimos nuestro recién creado fichero **ControladorLista.java**, que estará así:

```java
  
package com.example.tolkienlibrary;

public class ControladorLista {

}  
```


Como esta nueva clase debe ser una actividad, tendrá que heredar de la clase Activity. Mediante la palabra clave extends de java podemos realizar esta herencia.

Además, debemos sobreescribir el evento **onCreate** de la clase Actividad, que se ejecuta una vez cada vez que la Actividad se inicia, y que servirá para inicializar el estado de la Actividad y de su correspondiente Layout, que tambien debemos establecer en este método.

Ahora tendrá esta pinta:

```java
  
package com.example.tolkienlibrary;

import android.app.Activity;  
import android.os.Bundle;

public class ControladorLista extends Activity {  
/*\* Called when the activity is first created. \*/  
@Override  
public void onCreate(Bundle savedInstanceState) {  
super.onCreate(savedInstanceState);  
setContentView(R.layout.lista); //establecemos el Layout de esta Actividad  
}  
}  
```


Para añadir las referencias que faltan (los _imports_) en MAC OSX basta con pulsar **cmd+shift+O** o pasando el ratón por encima de los errores nos aparece un desplegable donde nos dicen soluciones al problema, entre ellos Importar las clases necesarias.

#### Paso 4 - Declarar la actividad en AndroidManifest.xml

Ya hablamos en la Guia 1 de que todas las actividades, entre otras cosas, deben ser declaradas en el fichero de Manifiesto, así pues, abrimos el AndroidManifest.xml y declaramos nuestra actividad “ControladorLista”; dentro del elemento “application”;. Hay que anteponer un simbolo “.”; al nombre de la clase. Vemos como queda nuestro fichero.

```xml  
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
<activity android:name=".ControladorLista"></activity>  
</application>  
<uses-sdk android:minSdkVersion="3" />  
</manifest>  
```


#### Paso 5 - Añadir el evento del botón

Vamos a añadir un _listener_ que atienda el evento que provoca pulsar sobre el botón. Para ello vamos a TolkienLibrary.java y escribimos el código necesario para dejarlo así:

```java
  
package com.example.tolkienlibrary;

import android.app.Activity;  
import android.content.Intent;  
import android.os.Bundle;  
import android.view.View;  
import android.widget.Button;

public class TolkienLibrary extends Activity {  
/*\* Called when the activity is first created. \*/  
@Override  
public void onCreate(Bundle savedInstanceState) {  
super.onCreate(savedInstanceState);  
setContentView(R.layout.main);

Button next = (Button) findViewById(R.id.Button02);  
next.setOnClickListener(new View.OnClickListener() {  
public void onClick(View view) {  
Intent myIntent = new Intent(view.getContext(), ControladorLista.class);  
startActivityForResult(myIntent, 0);  
}  
});  
}  
}  
```


Veamos que estamos haciendo:

  * Buscamos el botón cuyo id es _Button02_ dentro de los recursos de nuestro Layout (es el ID que le dimos en main.xml).
  * Una vez obtenido el objeto botón, le asignamos un listener para el evento OnClick
  * Dentro del Listener vamos a ejecutar un método que creará un objeto Intent sobre la actividad ControladorLista
  * Finalmente con startActivityForResult(myIntent, 0) ejecutamos la Actividad, y esperamos que esta nos devuelva un resultado.

A estas alturas nos estaremos preguntando que es eso de “Intent”;. Intent es un objeto que recopila información de interés para el sistema Android y que a efectos prácticos nos permite solicitar que se ejecute una Actividad. Como siempre, [aqui esta la documentación](http://developer.android.com/guide/topics/intents/intents-filters.html).

#### Paso 6 - Enviar parámetros a la segunda vista

Habíamos quedado en que ibamos a enviar algún parametro a la segunda vista. Para eso, en el evento del botón del nuestra actividad TolkienLibrary, vamos a construir un Bundle y a pasarselo a la vista, un Bundle no es más que una colección de claves/valor que la vista recibe cuando el sistema la restaura.

```java  
package com.example.tolkienlibrary;

import android.app.Activity;  
import android.content.Intent;  
import android.os.Bundle;  
import android.view.View;  
import android.widget.Button;

public class TolkienLibrary extends Activity {

final public static String MyKey = "mikey";

/*\* Called when the activity is first created. \*/  
@Override  
public void onCreate(Bundle savedInstanceState) {  
super.onCreate(savedInstanceState);  
setContentView(R.layout.main);

Button next = (Button) findViewById(R.id.Button02);  
next.setOnClickListener(new View.OnClickListener() {  
public void onClick(View view) {

// Cargamos el Bundle con la coleccion de objetos que pasaremos  
// a la siguiente vista  
Bundle bundle = new Bundle();  
bundle.putString(MyKey,  
"Esto lo hemos enviado desde la vista principal.");

// Creamos la vista de Lista de objetos y le pasamos la  
// colección de objetos a mostrar  
Intent myIntent = new Intent(view.getContext(),  
ControladorLista.class);  
myIntent.putExtras(bundle);

startActivityForResult(myIntent, 0);  
}

});  
}  
}  
```


Prestad atención a **MyKey**, que funciona como clave del parametro que estamos enviando en nuestro Bundle.

#### Paso 7 - Recibir el parámetro

Como quien va a recibir el parámetro es nuestra Actividad **ControladorLista** abrimos el fichero ControladorLista.java y escribimos lo siguiente:

```java  
package com.example.tolkienlibrary;

import android.app.Activity;  
import android.os.Bundle;  
import android.widget.TextView;

public class ControladorLista extends Activity {  
/*\* Called when the activity is first created. \*/  
@Override  
public void onCreate(Bundle savedInstanceState) {  
super.onCreate(savedInstanceState);  
setContentView(R.layout.lista); //establecemos el Layout de esta Actividad

Bundle extras = getIntent().getExtras();  
if (extras != null) {  
String parametro = extras.getString(TolkienLibrary.MyKey);

//utilizamos el parametro  
TextView controlTexto = (TextView) findViewById(R.id.campotexto);  
controlTexto.setText(parametro);  
}  
}  
}  
```


Una explicación al respecto:

Obtenemos desde el objeto Intent los Extras, mediante .getExtras().

Si hemos recibido alguno, entonces obtenemos el String que introducimos en la vista principal en la colección, mediante la clave MyKey.

Por ultimo buscamos en el Layout de la segunda vista el TextView cuyo id es “campotexto”; y establecemos su texto al parametro que hemos recibido.

#### Paso 8 - Responder a la vista principal

Ahora, como paso opcional, vamos a aprender a responder a la Actividad que nos llamó. Para ello vamos a incluir en el segundo Layout (lista.xml) y vamos a añadir otro botón.

```xml  
<?xml version="1.0" encoding="utf-8"?>  
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"  
android:orientation="vertical"  
android:layout_width="fill_parent" android:layout_height="fill_parent">  
<TextView android:id="@+id/campotexto"  
xmlns:android="http://schemas.android.com/apk/res/android"  
android:layout_width="wrap_content" android:layout_height="wrap_content" />  
<Button android:text="Volver" android:id="@+id/ButtonVolver"  
android:layout_width="200px" android:textSize="15px"  
android:layout_height="55px">  
</Button>  
</LinearLayout>  
```


Despues, como siempre, añadimos en la Actividad de este Layout el Listener que maneja el evento clic del botón:

```java  
package com.example.tolkienlibrary;

import android.app.Activity;  
import android.content.Intent;  
import android.os.Bundle;  
import android.view.View;  
import android.widget.Button;  
import android.widget.TextView;

public class ControladorLista extends Activity {  
/*\* Called when the activity is first created. \*/  
@Override  
public void onCreate(Bundle savedInstanceState) {  
super.onCreate(savedInstanceState);  
setContentView(R.layout.lista); //establecemos el Layout de esta Actividad

Bundle extras = getIntent().getExtras();  
if (extras != null) {  
String parametro = extras.getString(TolkienLibrary.MyKey);

//utilizamos el parametro  
TextView controlTexto = (TextView) findViewById(R.id.campotexto);  
controlTexto.setText(parametro);  
}

Button next = (Button) findViewById(R.id.ButtonVolver);  
next.setOnClickListener(new View.OnClickListener() {  
public void onClick(View view) {  
Bundle bundle = new Bundle();  
bundle.putString(TolkienLibrary.MyKey, "Este valor lo hemos enviado desde la segunda vista.");  
Intent mIntent = new Intent();  
mIntent.putExtras(bundle);  
setResult(RESULT_OK, mIntent);  
finish();  
}

});  
}  
}  
```


Vamos a ver que hemos hecho:

  * Hemos creado un Listener para el evento Clic del botón “ButtonVolver”;
  * En botón, creamos un Bundle, y usando por ejemplo la misma clave de antes “MyKey”;, almacenamos un par clave/valor.
  * Creamos un objetos Intent y le agregamos como Extras nuestro objeto Bundle cargado de información.
  * Mediante el método setResult, establecemos el resultado de esta actividad (recordemos que cuando invocamos a esta Actividad desde la Actividad principal, lo hicimos mediante la llamada **startActivityForResult** que espera un resultado por parte de la Actividad invocada) y entregamos el objeto Intent.
  * Llamamos a finish para forzar el fin de la Actividad actual.

De esta forma, estaríamos entregando también información de retorno a la Actividad TolkienLibrary al pulsar el botón.

Podemos ejecutar al fin, y veremos un botón, haciendo click sobre el se muestra el segundo Layout, y el texto es el parametro que enviamos desde la primera Actividad:

<div id="attachment_120" style="width: 310px" class="wp-caption aligncenter">
  <a href="http://blog.findemor.es/wp-content/uploads/2011/01/GuiaAndroid2Screenshot.jpg"><img class="size-medium wp-image-120" title="GuiaAndroid2Screenshot" src="http://blog.findemor.es/wp-content/uploads/2011/01/GuiaAndroid2Screenshot-300x210.jpg" alt="" width="300" height="210" /></a>
  
 <p class="wp-caption-text">
    Segundo Layout
  </p>
</div>

#### Paso 9 - Futuras Guías...

<a href="http://blog.findemor.es/2011/01/como-programar-en-android-guia-1" target="_self">Guia 1 - Hola Mundo en Android.</a>

[Guia 2 - Navegación entre Layouts con un botón, y paso de parámetros.](http://blog.findemor.es/2011/01/como-programar-en-android-guia-2/)

[Guia 3 - ListView o listas con scroll.](http://blog.findemor.es/2011/02/como-programar-en-android-guia-3)

[Guia 4 - ListView ricas con celdas personalizadas e imágenes.](http://blog.findemor.es/2011/02/como-programar-en-android-guia-4)

[Guia 5 - Acceso a base de datos (SQLLite).](http://blog.findemor.es/2011/02/como-programar-en-android-guia-5)