---
id: 122
title: 'Como programar en Android: Guia 3'
date: 2011-02-01T22:18:27+00:00
author: findemor
layout: post
guid: http://blog.findemor.es/?p=122
permalink: /2011/02/como-programar-en-android-guia-3/
categories:
  - Desarrollo
  - Cursos
tags:
  - Android
  - Java
---
Esta vez vamos a aprender a **crear vistas que contengan un control ListView**, es decir, que muestren una lista de elementos por la que podamos desplazarnos mediante un scroll cuando exceda la altura de la pantalla del dispositivo.

Antes de continuar con la lectura de esta guía, quizá te interese echar un vistazo [a la anterior, donde explicábamos como navegar entre Layouts](http://blog.findemor.es/2011/01/como-programar-en-android-guia-2/). **Vamos a partir de ese proyecto** para completar la Guia 3.

<!--more-->

#### ¿Qué vamos a hacer?

Vamos a continuar el proyecto de la Guia 2, para explicar como sustituir el contenido de nuestro **layout lista.xml**, que actualmente tiene un botón y un TextView, por un control ListView, y a cargar datos en esa lista.

Paso 1 - Editar el Layout lista.xml

En primer lugar, vamos a editar el archivo lista.xml que representa el Layout en el cual vamos a incluir nuestro ListView, una vez abierto, borramos su contenido y escribimos lo siguiente:

```xml  
<?xml version="1.0" encoding="utf-8"?>  
<LinearLayout  
xmlns:android="http://schemas.android.com/apk/res/android"  
android:layout_width="wrap_content"  
android:layout_height="wrap_content">  
<ListView android:layout_width="wrap_content"  
android:layout_height="wrap_content"  
android:id="@android:id/list"></ListView>  
<TextView android:layout_width="wrap_content"  
android:layout_height="wrap_content"  
android:text="@string/lista_vacia"  
android:id="@android:id/empty"></TextView>  
</LinearLayout>  
```


Lo que hemos hecho ha sido definir en nuestra vista un contenedor **LinearLayout**, en cuyo interior hay una lista “**ListView**”; y una etiqueta de texto “**TextView**“.

Hay que prestar atención al identificador tanto del ListView (**@android:id/list**) como del TextView (**@android:id/empty**), ambos son identificadores definidos por la plataforma Android, por eso anteponemos **@android en lugar de @+id** como hicimos en anteriores Guias. Al elegir estos identificadores, estamos indicando al sistema que **debe mostrar el TextView cuando la lista esté vacia** (de ahí lo de empty) y que muestre la lista en caso contrario.

Si guardamos ahora, Eclipse detectara que estamos usando un recurso que aún no hemos creado: el string “_lista_vacia_“, que representa el texto que mostrara el label TextView cuando la lista no tenga elementos. Para darle un valor, vamos a editar el fichero de recursos de cadena.

Como de costumbre abrimos res/values/**strings.xml** y definimos un valor para lista_vacia, ya sea en el diseñador o en el editor XML.

```xml  
<?xml version="1.0" encoding="utf-8"?>  
<resources>  
<string name="hello">Hola Arda!</string>  
<string name="app_name">Tolkien Library</string>  
<string name="lista_vacia">No hay elementos en la lista.</string>  
</resources>  
```


#### Paso 2 - Diseñar el Layout para cada elemento individual de la lista

Nuestra ListView va a mostrar una lista de elementos, para que el dispositivo sepa como dibujar cada uno de estos elementos, debemos diseñar el Layout.  Para eso vamos como siempre a **res/layout/** y creamos un nuevo archivo al que llamaremos **lista_item.xml.**

En nuestro ejemplo, cada uno de los elementos de la lista será únicamente el título de un libro, por lo que **lista_item.xml solo contendrá un TextView**.

```xml  
<?xml version="1.0" encoding="utf-8"?>  
<TextView  
xmlns:android="http://schemas.android.com/apk/res/android"  
android:layout_width="fill_parent"  
android:layout_height="fill_parent">  
</TextView>  
```


#### Paso 3 - Implementar la Actividad que maneja la lista

Como hemos hecho en otras ocasiones, ahora que tenemos creado nuestro Layout, vamos a implementar la Actividad que lo controla.

Si hemos completado la Guia 1, en la que teníamos un botón y un TextView, nuestra actividad ControladorLista será parecida a esta:

```java
  
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


Pues bien: ahora no tenemos dicho botón ni el TextView, así que para mayor comprensión de los cambios que vamos a realizar, vamos a eliminar esa parte del código y a partir desde una actividad más limpia:

```java  
public class ControladorLista extends Activity {  
/*\* Called when the activity is first created. \*/  
@Override  
public void onCreate(Bundle savedInstanceState) {  
super.onCreate(savedInstanceState);  
}  
}  
```


Nuestra actividad ControladorLista hereda de Activity, y lo primero que vamos a hacer **es cambiar esa herencia** por [ListActivity](http://developer.android.com/reference/android/app/ListActivity.html) que proporciona **métodos más apropiados** para trabajar con una lista de elementos.

Si en este momento ejecutamos nuestra aplicación para ver lo que llevamos hecho, veremos que cuando mostramos el segundo Layout (lista.xml) pulsando el botón “Ver Lista”;, se muestra el mensaje que nos indica que no hay elementos que mostrar, ya que la lista no tiene elementos.

<div id="attachment_127" style="width: 310px" class="wp-caption aligncenter">
  <a href="http://blog.findemor.es/wp-content/uploads/2011/02/appEmpty.jpg"><img class="size-medium wp-image-127" title="Sin elementos en la lista" src="http://blog.findemor.es/wp-content/uploads/2011/02/appEmpty-300x217.jpg" alt="Sin elementos en la lista" width="300" height="217" /></a>
  
 <p class="wp-caption-text">
    Sin elementos en la lista
  </p>
</div>

#### Paso 4 - Poblar el ListView

Ahora lo único que falta es meter elementos en la lista.

Lo normal en la práctica será cargarlo a partir de información almacenada en una Base de Datos SQLite, ya sea mediante un Cursor o Entidades, datos recogidos desde servicios web, etc. Sin embargo para este ejemplo vamos a crear una colección ArrayList de Java, pues no nos interesa la recolección de datos, sino la carga de los mismos en el ListView.

Para esto vamos a hacer dos cosas:

  * Primero implementamos la función **getItems()** que devuelve un ArrayList<String>, es decir, una colección de cadenas de texto, que en nuestro caso serán títulos de libros especialmente buenos.
  * Después, en el evento **onCreate** vamos a llamar a **getItems()**, y a pasarle la lista de elementos que nos devuelve al **adaptador de la lista**, invocando [setListAdapter](http://developer.android.com/reference/android/app/ListActivity.html#setListAdapter(android.widget.ListAdapter)), al que tambien debemos indicar que Layout debe usar para **mostrar cada elemento de la lista.**

```java  
package com.example.tolkienlibrary;

import java.util.ArrayList;

import android.app.ListActivity;  
import android.os.Bundle;  
import android.widget.ArrayAdapter;

public class ControladorLista extends ListActivity {  
/*\* Called when the activity is first created. \*/  
@Override  
public void onCreate(Bundle savedInstanceState) {  
super.onCreate(savedInstanceState);  
setContentView(R.layout.lista);  
//Obtenemos la lista de titulos  
ArrayList<String> Titulos = getItems();  
//Entregamos la lista de titulos al adaptador de la lista en el Layout Lista.xml  
setListAdapter(new ArrayAdapter<String>(this, R.layout.lista_item, Titulos));  
}

/*  
* Obtiene una lista de titulos de libros  
*  
* @returns ArrayList<String> titulos de libros  
*/  
public ArrayList<String> getItems()  
{  
ArrayList<String> MiLista = new ArrayList<String>();

MiLista.add("El Silmarillion");  
MiLista.add("El Señor de los Anillos");  
MiLista.add("Egidio, el granjero de Ham");

return MiLista;  
}  
}  
```


**Un truco:** Tal y como nos indican en la [documentación](http://developer.android.com/resources/tutorials/views/hello-listview.html), si queremos ahorrarnos el diseño del layout que emplearán los Items de la Lista (al que hemos llamado lista_item.xml) es posible usar diseños proporcionados por la plataforma Android, por ejemplo usando **android.R.layout.simple_list_item** en lugar de **R.layout.lista_item**. Sin embargo hemos hecho el diseño completo en este ejemplo, porque nos servirá para explicar la siguiente Guía, en la que mostraremos items más complejos.

Ahora ya podemos ejecutar nuestra aplicación, y veremos que a diferencia de antes, ahora la lista muestra una colección de Títulos.

<div id="attachment_126" style="width: 310px" class="wp-caption aligncenter">
  <a href="http://blog.findemor.es/wp-content/uploads/2011/02/appFilled.jpg"><img class="size-medium wp-image-126" title="Con elementos en la lista" src="http://blog.findemor.es/wp-content/uploads/2011/02/appFilled-300x217.jpg" alt="Con elementos en la lista" width="300" height="217" /></a>
  
 <p class="wp-caption-text">
    Con elementos en la lista
  </p>
</div>

#### Paso 5 - Futuras Guías...

¡Esto no se acaba aqui!

<a href="http://blog.findemor.es/2011/01/como-programar-en-android-guia-1" target="_self">Guia 1 - Hola Mundo en Android.</a>

[Guia 2 - Navegación entre Layouts con un botón, y paso de parámetros.](http://blog.findemor.es/2011/01/como-programar-en-android-guia-2/)

[Guia 3 - ListView o listas con scroll.](http://blog.findemor.es/2011/02/como-programar-en-android-guia-3)

[Guia 4 - ListView ricas con celdas personalizadas e imágenes.](http://blog.findemor.es/2011/02/como-programar-en-android-guia-4)

[Guia 5 - Acceso a base de datos (SQLLite).](http://blog.findemor.es/2011/02/como-programar-en-android-guia-5)