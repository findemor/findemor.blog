---
id: 227
title: 'Cómo programar en Android: Guía 6'
date: 2012-02-22T22:14:48+00:00
author: findemor
layout: post
guid: http://blog.findemor.es/?p=227
permalink: /2012/02/como-programar-en-android-guia-6/
image: /wp-content/uploads/2012/02/findemor_blog_guia6_layout-64x64.png
categories:
  - Android
  - How to
  - Java
---
Muchos usuarios me habeis pedido que continuase con las Guías de programación en Android (que inicialmente estaba pensada como 5 guías básicas) incluyendo ejemplos de inserción y edición; y como vuestros deseos son órdenes, aquí está la Guía 6, que trata precisamente de eso: inserción de datos.

En esta Guía vamos a ver como **insertar datos en la Base de Datos SQLite en Android, desde un formulario diseñado en una actividad separada**. Antes de continuar con la lectura de esta guía, quizá te interesaría echar un vistazo a la [Guía 5](http://blog.findemor.es/2011/02/como-programar-en-android-guia-5/), en la que aprendimos a realizar las primeras consultas a una base de datos en este entorno. **Partiremos del proyecto de la [Guía 5](http://blog.findemor.es/2011/02/como-programar-en-android-guia-5/ "Guía 5")** para completar los pasos de la Guía 6.

<!--more-->

#### ¿Qué vamos a hacer?

Vamos a **crear una nueva actividad**, que controlará la **pantalla donde podremos introducir los datos** que se insertarán en la Base de datos SQLite. Después vamos a crear la **consulta de inserción en SQLite.**

 ****Finalmente vamos a **crear los botones y a realizar los cambios necesarios para integrar el ejemplo de esta Guía en el ejemplo completo** que teníamos en la Guía 5 (al final de la guía podíais descargar el código fuente completo).

#### Paso 1 &#8211; Creamos el layout para la pantalla de inserción

Lo primero que vamos a hacer es **crear el layout de la pantalla de inserción**, en la que indicaremos qué controles mostrará dicha pantalla y cómo se verán.

Como el ejemplo que estamos viendo consiste en una aplicación de tipo librería, y este layout tendrá todos los campos relacionados con un libro, lo vamos a llamar **edit.xml**.

**Ya hemos visto** en las guías anteriores como se creaba un layout xml, así que vamos directamente al código:

[sourcecode language=&#8221;xml&#8221; collapse=&#8221;true&#8221;]  
<?xml version="1.0" encoding="utf-8"?>  
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"  
android:layout\_width="fill\_parent"  
android:layout\_height="fill\_parent"  
android:orientation="vertical" >

<TextView  
android:id="@+id/lbTitulo"  
android:layout\_width="wrap\_content"  
android:layout\_height="wrap\_content"  
android:text="Titulo:"  
android:textAppearance="?android:attr/textAppearanceMedium" />

<EditText  
android:id="@+id/txTitulo"  
android:layout\_width="fill\_parent"  
android:layout\_height="wrap\_content" >

<requestFocus />  
</EditText>

<TextView  
android:id="@+id/lbAutor"  
android:layout\_width="wrap\_content"  
android:layout\_height="wrap\_content"  
android:text="Autor:"  
android:textAppearance="?android:attr/textAppearanceMedium" />

<EditText  
android:id="@+id/txAutor"  
android:layout\_width="fill\_parent"  
android:layout\_height="wrap\_content" >  
</EditText>

<Button  
android:id="@+id/btGuardar"  
android:layout\_width="fill\_parent"  
android:layout\_height="wrap\_content"  
android:text="Guardar" >  
</Button>

<Button  
android:id="@+id/btCancelar"  
android:layout\_width="fill\_parent"  
android:layout\_height="wrap\_content"  
android:text="Cancelar" >  
</Button>  
</LinearLayout>  
[/sourcecode]

A continuación una **explicación breve** del código:  
Tenemos un **LinearLayout como contenedor principal**, para mostrar el resto de componentes alineados verticalmente cada uno a continuación del anterior.

Como cada libro está representado por **dos valores (Titulo y Autor)** tendremos **dos cajas de texto de tipo EditText**, una para cada uno de los valores.

Para ayudar a los usuarios a interpretar para qué vale cada una de estas cajas, vamos a poner **un label (etiqueta) delante de cada una**, mostrando el nombre de lo que esperamos que el usuario escriba: **el label de tipo TextView lbTitulo antes de la caja txTitulo y el label TextView lbAutor delante de la caja txAutor**.

**Finalmente hay dos botones**, uno para **Guardar** lo que el usuario escriba en las cajas de texto, y otro para **cancelar** la operación y volver a la pantalla principal.

Así se verá aproximadamente:

<div id="attachment_241" style="width: 178px" class="wp-caption aligncenter">
  <a href="http://blog.findemor.es/wp-content/uploads/2012/02/findemor_blog_guia6_layout.png"><img class="size-medium wp-image-241 aligncenter" title="El nuevo layout de la Guia 6" src="http://blog.findemor.es/wp-content/uploads/2012/02/findemor_blog_guia6_layout-168x300.png" alt="El nuevo layout de la Guia 6" width="168" height="300" srcset="http://blog.findemor.es/wp-content/uploads/2012/02/findemor_blog_guia6_layout-168x300.png 168w, http://blog.findemor.es/wp-content/uploads/2012/02/findemor_blog_guia6_layout-84x150.png 84w" sizes="(max-width: 168px) 100vw, 168px" /></a>
  
  <p class="wp-caption-text">
    El nuevo layout de la Guia 6
  </p>
</div>

#### Paso 2 &#8211; Creamos la Actividad que controla la nueva pantalla

El **siguiente paso consiste en crear una actividad**, **que utilice el layout que hemos diseñado en el Paso 1** para controlar todas las tareas que se pueden realizar en ella.

Como **ya hemos visto como crear un Activity en guías anteriores**, y esta en particular no tiene nada nuevo (de hecho se parece mucho a las de la **Guía 5**), vamos a comentar rapidamente los métodos nuevos que se han implementado y su función, y después veremos el código completo de la Actividad.

**Creamos la clase Edit.java**, donde aparecerán los siguientes métodos:

  * **onCreate(&#8230;)**: el método onCreate de la Actividad se ejecutará cada vez que se inicie la Actividad. Por lo tanto, aprovecharemos este método para indicar que layout se va a mostrar, para llamar al método que inicia la Base de Datos, y para establecer los métodos que se ejecutarán cuando se pulse el botón Guardar o el botón Cancelar.
  * **crearBBDD()**: este método crea la base de datos o la inicia si ya existía. Es idéntico al empleado en la Guía 5.
  * **cancelar()**: el método cancelar termina la Actividad actual (cierra esta pantalla) y muestra la anterior en la tarea actual (la pantalla de inicio de nuestra aplicación).
  * **guardar()**: este método comprueba si los valores que el usuario ha escrito en el formulario son válidos. Si no lo son, muestra un mensaje de error; en caso de ser válidos solicita a la base de datos la inserción del registro de un nuevo libro, con los datos proporcionados por el usuario, y cierra esta actividad después. Además, si la base de datos generase algún error, lo mostrará en pantalla y no hará nada más.
  * **mostrarMensaje(String mensaje)**: este pequeño método permite mostrar un mensaje informativo para el usuario.
  * **comprobarGuardarValido()**: este método obtiene el valor de las cajas de texto (lo que ha escrito el usuario), y si este no ha escrito nada en alguna de ellas, considerará que no es válido y devolverá false; en caso contrario lo considerará válido y devolverá true. Se utiliza internamente en el método guardar().

Una vez enumerados y explicados los métodos, aquí podeis ver el **código completo de esta Actividad**:

[sourcecode language=&#8221;java&#8221; collapse=&#8221;true&#8221;]  
package com.example.tolkienlibrary;

import java.io.IOException;  
import android.app.Activity;  
import android.os.Bundle;  
import android.view.View;  
import android.widget.Button;  
import android.widget.TextView;  
import android.widget.Toast;

public class Edit extends Activity {

/*\* Called when the activity is first created. \*/  
@Override  
public void onCreate(Bundle savedInstanceState) {  
super.onCreate(savedInstanceState);  
setContentView(R.layout.edit);

// Creamos la Base de datos  
crearBBDD();

//Obtenemos la instancia de los botones y preparamos los eventos que lanzaran al pulsarse  
Button botonGuardar = (Button) findViewById(R.id.btGuardar);  
botonGuardar.setOnClickListener(new View.OnClickListener() {  
public void onClick(View view) {  
guardar();  
}  
});

Button botonCancelar = (Button) findViewById(R.id.btCancelar);  
botonCancelar.setOnClickListener(new View.OnClickListener() {  
public void onClick(View view) {  
cancelar();  
}  
});  
}

BaseDatosHelper miBBDDHelper;

public void crearBBDD() {  
miBBDDHelper = new BaseDatosHelper(this);  
try {  
miBBDDHelper.crearDataBase();  
} catch (IOException ioe) {  
throw new Error("Unable to create database");  
}  
}

private void cancelar()  
{  
//Cerramos la vista actual, por lo que Android mostrará la anterior, sin hacer nada más.  
this.finish();  
}

private void guardar()  
{  
if (comprobarGuardarValido()) {  
// El formulario es valido, por lo tanto podemos guardar

boolean bienHecho = false;

try {  
TextView cajaTitulo = (TextView) findViewById(R.id.txTitulo);  
TextView cajaAutor = (TextView) findViewById(R.id.txAutor);

String titulo = cajaTitulo.getText().toString();  
String autor = cajaAutor.getText().toString();

// Abrimos una conexión  
miBBDDHelper.abrirBaseDatos();  
// Consultamos los datos  
bienHecho = miBBDDHelper.InsertLibro(titulo, autor);  
// Cerramos la conexion  
miBBDDHelper.close();  
} catch (Exception ex) {  
// Algo ha ido mal, capturamos la excepcion  
bienHecho = false;  
}

if (!bienHecho)  
mostrarMensaje("Algo ha ido mal, no se guardó.");  
else  
finish(); //cerramos esta actividad

} else  
{  
//El formulario no es valido, no podemos guardar, mostramos un mensaje al usuario  
mostrarMensaje("Rellena todos los campos antes de guardar.");  
}  
}

/**  
* Muestra un mensaje al usuario  
* @param mensaje  
*/  
private void mostrarMensaje(String mensaje)  
{  
Toast.makeText(this, mensaje, 5000).show();  
}

/**  
* Comprueba que hay datos suficientes como para guardar  
* @return true si es valido, false si falta algun campo  
*/  
private boolean comprobarGuardarValido()  
{  
Boolean guardarEsValido = false; //empezamos suponiendo que es falso

//Obtenemos la instancia de la caja de texto  
TextView cajaTitulo = (TextView) findViewById(R.id.txTitulo);  
TextView cajaAutor = (TextView) findViewById(R.id.txAutor);

//Comprobamos las cajas existen  
if (cajaTitulo != null && cajaAutor != null)  
{  
//Comprobamos que el usuario ha escrito algo en ambas  
if (cajaTitulo.getText() != null && !cajaTitulo.getText().toString().equals(""))  
{  
//Titulo tiene algo, comprobamos tambien Autor  
if (cajaAutor.getText() != null && !cajaAutor.getText().toString().equals(""))  
{  
guardarEsValido = true; //ha pasado las comprobaciones  
}  
}

}

return guardarEsValido;  
}  
}  
[/sourcecode]

Si habéis leido el código con atención, quizá os hayáis dado cuenta de que **estamos utilizando el método .InsertLibro(titulo, autor)** de nuestro objeto de Base de Datos, para realizar la inserción, y que **éste método no se ha definido aún** en esta guia y tampoco en la Guía 5 (donde sí dimos forma a dicho objeto): lo vamos a hacer a continuación.

#### Paso 3 &#8211; Realizar la Inserción en la Base de Datos SQLite

En la **Guía 5 vimos cómo podíamos definir e implementar nuestro objeto de Base de Datos** SQLite en Android, y también cómo realizar consultas sobre él. A continuación **vamos a utilizar la implementación que hicimos en esa guía** para incluir un nuevo método muy sencillo que permite **insertar registros** (libros, en este caso) en nuestra tabla de librería.

Para hacer esto, **abrimos nuestra clase BaseDatosHelper.java** y **creamos el siguiente método al final del todo**:

[sourcecode language=&#8221;java&#8221; collapse=&#8221;true&#8221;]

public boolean InsertLibro(String titulo, String autor)  
{  
//Preparamos los valores que vamos a insertar  
ContentValues cv = new ContentValues();  
cv.put(TABLE\_KEY\_TITULO, titulo);  
cv.put(TABLE\_KEY\_AUTOR, autor);

//Realizamos la consulta  
long rowid = myDataBase.insert(TABLE_LIBROS,  
null,  
cv);

return rowid >= 0;  
}  
[/sourcecode]

Estamos usando **3 constantes que ya se definieron en la Guía 5**, y que corresponden a cadenas de texto:

  * **TABLE_LIBROS**: es el nombre de la tabla de la base de datos en la que vamos a realizar la inserción
  * **TABLE\_KEY\_TITULO**: es el nombre de la columna de la tabla en la que vamos a insertar el valor del titulo para este registro.
  * **TABLE\_KEY\_AUTOR**: es el nombre de la columna de la tabla en la que vamos a insertar el valor del autor para este registro.

Podeis observar como se realiza la inserción: **primero** se prepara un o**bjeto de tipo ContentValues** donde se colocan con el método **.put()** los valores para la nueva fila. El método put **recibe dos parámetros**: el **nombre** de la columna (el atributo) y el **valor** que debe almacenar.

**Después, se realiza un insert en el objeto de Base de Datos** pasandole este parámetro. El metodo **insert devolverá el identificador de fila** si ha realizado la inserción correctamente, y una excepción o un valor menor que cero en caso contrario.

#### Paso 4 &#8211; Integración en la Guía anterior: declarar la actividad

Vamos a empezar a integrar el nuevo código en el programa desarrollado en la Guía 5 para que funcione correctamente.

El primer paso, como siempre, consiste en declarar todas aquellas actividades nuevas que hayamos implementados. Como ya sabemos por guías anteriores, esto se hace en el archivo **AndroidManifest.xml**. Añadimos la siguiente línea, justo despues de donde teniamos definidas las otras actividades (**despues de &#8220;.ControladorLista&#8221;></activity>&#8221;**):

[sourcecode language=&#8221;xml&#8221; collapse=&#8221;true&#8221;]  
<activity android:name=".Edit"></activity>  
[/sourcecode]

#### Paso 5 &#8211; Integración en la Guía anterior: Acceso a la nueva actividad

Hemos creado la vista correctamente, definido e implementado su funcionamiento en la Actividad apropiada y hemos declarado su uso&#8230; pero no tenemos aún ningún sitio desde donde lanzar (ejecutar) esta nueva Actividad de inserción de libros.

En el ejemplo de la Guía 5, **utilizaremos la pantalla principal,** en la que solo teníamos un botón para acceder a la lista de libros, para añadir otro botón que realice esta tarea.

Para ello, **editamos el layout Main.xml** que teníamos en la Guía anterior, y **añadimos el botón btnInsertarLibro después del boton Button02 que ya teníamos**. El código para el nuevo botón es el siguiente:

[sourcecode language=&#8221;xml&#8221; collapse=&#8221;true&#8221;]  
<Button android:text="Insertar libro"  
android:id="@+id/btnInsertarLibro"  
android:layout_width="200px"  
android:textSize="15px"  
android:layout_height="55px">  
</Button>  
[/sourcecode]

#### Paso 6 &#8211; Integración con la Guía anterior: Comportamiento del botón

Ya tenemos el botón en el layout, ahora vamos a ir a la **clase TolkienLibrary.java**, que definía el comportamiento de la pantalla principal en la Guía 5, y vamos a definir su comportamiento.

Para lograr esto, **en el método onCreate**, justo antes del final, obtenemos la instancia del botón y **definimos su evento setOnClickListener()** para que inicie la **actividad Edit** cuando pulsemos en él.

[sourcecode language=&#8221;java&#8221; collapse=&#8221;true&#8221;]

Button botonInsertar = (Button) findViewById(R.id.btnInsertarLibro);  
botonInsertar.setOnClickListener(new View.OnClickListener() {  
public void onClick(View view) {  
//Abrimos la actividad de insertar libro  
Intent myIntent = new Intent(TolkienLibrary.this, Edit.class);  
startActivity(myIntent);  
}

});  
[/sourcecode]

Prestad atención a que **no estamos haciendo un finish()** de la Actividad actual cuando iniciamos la nueva, de este modo la Actividad de la pantalla principal **se mantendrá como anterior en la cola de actividades de la tarea**, y cuando cerremos la siguiente ventana volveremos aquí.

#### Paso 7 &#8211; Probar la aplicación

**¡Ya está! Hemos terminado.** Es hora de probarla.

Si todo ha ido bien, vereis que es prácticamente igual que el ejemplo de la Guía 5, salvo porque en la pantalla principal tenemos un nuevo botón que permite abrir la actividad Edit, que acabamos de crear, y en la que podemos insertar nuevos libros.

Si escribimos un titulo y un autor, y pulsamos guardar, se cerrara esa pantalla y volveremos a la principal. Si ahora pulsamos en el botón para ver la lista, podremos comprobar que el libro se ha registrado correctamente en la lista.

<div id="attachment_242" style="width: 310px" class="wp-caption aligncenter">
  <a href="http://blog.findemor.es/wp-content/uploads/2012/02/blog_findemor_tolkienlibrary_guia6.jpg"><img class="size-medium wp-image-242" title="Ejemplo completo de la Guia 6" src="http://blog.findemor.es/wp-content/uploads/2012/02/blog_findemor_tolkienlibrary_guia6-300x239.jpg" alt="Ejemplo completo de la Guia 6" width="300" height="239" /></a>
  
  <p class="wp-caption-text">
    Ejemplo completo de la Guia 6
  </p>
</div>

#### Paso 8 &#8211; Descarga el código

**Si tenéis algún problema** para interpretar el código, o quereis verlo en funcionamiento, **podeis descargar el proyecto** desde [este enlace](http://blog.findemor.es/resources/TolkienLibrary_Guia6_Solution.zip "Proyecto completo de la Guia 6").

#### Paso 9 &#8211; Guías anteriores

Durante esta guía se han nombrado varias veces **los capítulos anteriores** para resumir las explicaciones. Si lo considerais necesario, **no dudeis en consultarlos en los siguientes enlaces**, espero que os haya resultado útil<img src="http://blog.findemor.es/wp-includes/images/smilies/simple-smile.png" alt=":)" class="wp-smiley" style="height: 1em; max-height: 1em;" /> 

[<< IR a la GUÍA ANTERIOR: Guia 5 &#8211; Acceso a base de datos (SQLLite).  
](http://blog.findemor.es/2011/02/como-programar-en-android-guia-5)