---
id: 137
title: 'Como programar en Android: Guia 4'
date: 2011-02-03T01:17:37+00:00
author: findemor
layout: post
guid: http://blog.findemor.es/?p=137
permalink: /2011/02/como-programar-en-android-guia-4/
categories:
  - Android
  - Desarrollo
  - Java
tags:
  - Android
  - Java
---
En la Guía 4 vamos a aprender a **crear un _Custom ListView Item_**, es decir, a personalizar los elementos de nuestras listas para mostrar una información más rica y adaptada a nuestras necesidades.

Antes de continuar la lectura de la guía, quizá te interese leer [la anterior, en la que aprendíamos a crear una vista con un ListView](http://blog.findemor.es/2011/02/como-programar-en-android-guia-3/). **Partiremos del proyecto de la Guía 3 para los ejemplos de la nueva Guía**.

<!--more-->

#### ¿Qué vamos a hacer?

Vamos a continuar el proyecto de la Guia 3, en el que teníamos una lista de títulos de libro, de forma que ahora la lista muestre para cada libro: una imagen, un titulo y su autor.

#### Paso 1 &#8211; Preparar nuestro Layout lista_item.xml

En la Guía 3 estábamos mostrando en el ListView una lista de títulos (cadenas de texto) cada uno de los cuales necesitaba un único TextView para representar esa información. Es por eso el Layout lista_item.xml, que es el diseño de cada elemento de la lista, solo contenia un control TextView.

En el ejemplo de hoy vamos a mostrar una imagen, un campo de texto para el título, y un campo de texto para el nombre del autor. Por lo tanto, necesitaremos un control ImageView para la imagen, y dos TextView para el titulo y autor.

Como siempre que vamos a colocar más de un control en el layout, necesitaremos tambien controles contenedores para anidarlos.

Despues de editar nuestro archivo lista_item.xml quedará algo así:

[sourcecode language=&#8221;xml&#8221; highlight=&#8221;18,28&#8243;]  
<?xml version="1.0" encoding="utf-8"?>  
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"  
android:layout\_width="fill\_parent"  
android:layout_height="?android:attr/listPreferredItemHeight"  
android:padding="5dip">  
<ImageView  
android:id="@+id/icono"  
android:layout\_width="wrap\_content"  
android:layout\_height="fill\_parent"  
android:layout_marginRight="5dip"  
android:src="@drawable/icon" />  
<LinearLayout  
android:orientation="vertical"  
android:layout_width="0dip"  
android:layout_weight="1"  
android:layout\_height="fill\_parent">  
<TextView  
android:id="@+id/titulo"  
android:layout\_width="fill\_parent"  
android:layout_height="0dip"  
android:layout_weight="1"  
android:gravity="center_vertical"  
/>  
<TextView  
android:layout\_width="fill\_parent"  
android:layout_height="0dip"  
android:layout_weight="1"  
android:id="@+id/autor"  
android:singleLine="true"  
android:ellipsize="marquee"  
/>  
</LinearLayout>  
</LinearLayout>

[/sourcecode]

Si nos fijamos bien, veremos que lo que hemos hecho es definir un **LinearLayout**, que por defecto mostrara los elementos que contenga uno al lado del otro, dentro tenemos un **ImageView**, que por lo tanto aparecerá a la izquierda, y **otro LinearLayout**, que aparecerá a continuación de la imagen, a su derecha. Este segundo LinearLayout tiene orientación vertical, por lo que los **dos TextView** que contiene aparecerán uno encima de otro (y a la derecha de la imagen). El resto de atributos sirven para personalizar aún más el diseño, como son alturas de los elementos, margenes, etc, pero ahora mismo podemos obviarlos.

#### Paso 2 &#8211; Preparar los objetos que aparecerán en la lista

En la Guía 3, el control ListView recibía una lista de títulos (cadenas de texto), para lo que usábamos un _ArrayList<String>._ Para el ejemplo de hoy necesitamos que nuestra entidad **Libro** contenga más información: un titulo y su autor, ya que esta entidad será lo que vamos a mostrar en cada celda de nuestro ListView.

En nuestro archivo **ControladorLista.java**, donde tenemos implementada la Actividad que controla la lista, vamos a crear a nivel de clase, una **entidad Libro**, con dos propiedades.

[sourcecode language=&#8221;java&#8221;]  
public class Libro {  
private String Titulo = "";  
private String Autor = "";

public String getTitulo() {  
return Titulo;  
}

public String getAutor() {  
return Autor;  
}

public void setTitulo(String titulo) {  
Titulo = titulo;  
}

public void setAutor(String autor) {  
Autor = autor;  
}  
}  
[/sourcecode]

También vamos a modificar el **método getItems()** para que genere y devuelva una lista de entidades Libro.

[sourcecode language=&#8221;java&#8221;]  
/*  
* Obtiene una lista de libros  
*  
* @returns ArrayList libros  
*/  
public ArrayList<Libro> getItems() {  
ArrayList<Libro> MiLista = new ArrayList<Libro>();

// Creamos los objetos Libro  
Libro libro1 = new Libro();  
libro1.setTitulo("El Silmarillion");  
libro1.setAutor("J.R.R. Tolkien");

Libro libro2 = new Libro();  
libro2.setTitulo("El Señor de los Anillos");  
libro2.setAutor("J.R.R. Tolkien");

Libro libro3 = new Libro();  
libro3.setTitulo("Los propios dioses");  
libro3.setAutor("Isaac Asimov");

// Añadimos los libros a la lista  
MiLista.add(libro1);  
MiLista.add(libro2);  
MiLista.add(libro3);

return MiLista;  
}  
[/sourcecode]

#### Paso 3 &#8211; Implementar un CustomAdapter para el ListView

En la Guía 3, vimos que para que el ListView poblase sus elementos a partir de una lista de cadenas de texto, bastaba con invocar el método **setListAdapter** al que pasabamos un **ArrayAdapter<String>** y el recurso que iba a actuar como layout para los elementos de nuestra lista.

Este paso **era así de sencillo** porque el adaptador por defecto de la lista, al recibir una lista de Strings, y teniendo en su Layout un unico TextView, sabe facilmente que lo que debe hacer es usar el TextView para mostrar el String correspondiente. Pero hoy queremos mostrar una entidad Libro, que es mas compleja, por lo que tendremos que **implementar nuestro propia clase Adapter**, a la que llamaremos **LibroAdapter**, y que **heredará de ArrayAdapter<Libro>**, donde podremos decirle en que control de Layout debe mostrar cada atributo del objeto Libro.

[sourcecode language=&#8221;java&#8221; highlight=&#8221;1,7,11,17,19,20,22,25&#8243;]  
private class LibroAdapter extends ArrayAdapter<Libro> {

private ArrayList<Libro> items;

public LibroAdapter(Context context, int textViewResourceId, ArrayList<Libro> items) {  
super(context, textViewResourceId, items);  
this.items = items;  
}

@Override  
public View getView(int position, View convertView, ViewGroup parent) {  
View v = convertView;  
if (v == null) {  
LayoutInflater vi = (LayoutInflater) getSystemService(Context.LAYOUT\_INFLATER\_SERVICE);  
v = vi.inflate(R.layout.lista_item, null);  
}  
Libro libro = items.get(position);  
if (libro != null) {  
TextView ttitulo = (TextView) v.findViewById(R.id.titulo);  
TextView tautor = (TextView) v.findViewById(R.id.autor);  
if (ttitulo != null) {  
ttitulo.setText(libro.getTitulo());  
}  
if (tautor != null) {  
tautor.setText(libro.getAutor());  
}  
}  
return v;  
}  
}  
[/sourcecode]

Prestemos atención a lo que estamos haciendo:

  * En el constructor, recibimos los parametros que requiere nuestra clase padre ArrayAdapter: **contexto** y **layout** donde mostraremos cada elemento de la lista.
  * También recibimos dicha lista (a la que llamaremos _**items**_), y la almacenamos en una variable a nivel de clase de tipo **ArrayList<Libro>.**
  * Sobreescribimos el método **getView()** de la clase padre ArrayAdapter. Este método se ejecutará una vez por cada elemento que vayamos a mostrar en la lista, y recibirá el indice de dicho elemento en la lista en el parametro **posicion**. Es decir, si en la lista cargamos 3 elementos, este método se ejecutará **3 veces**, uno por cada elemento, y se encarga de preparar y cargar la información en el Layout de nuestros elementos, es decir **lista_item.xml.** Por lo tanto lo que haremos en este método será en líneas generales, **obtener el Libro** que corresponde a la llamada actual, **obtener los controles TextView** del Layout en los que vamos a mostrar la información, y **cargar el atributo del Libro** apropiado en cada uno.

> **_Nota_**: Además, bajo ciertas circunstancias, aunque nuestra lista lista solo tenga por ejemplo 3 elementos, para representar la vista en el dispositivo, el sistema puede requerir **la ejecución de este método 3*N veces**. Siendo N un valor variable que depende de la **anidación de controles** en nuestra vista y del atributo **layout_width** y **layout_height** de los mismos, si deben calcularse dinamicamente (con **wrap_content** por ejemplo). Pero ya veremos la solución a este problema en futuros posts.

#### Paso 4 &#8211; Poblar la lista con los objetos Libro

Una vez que tenemos esto, solo falta cargar la lista de Libros en el adaptador de la lista, en el evento onCreate de la Actividad.

Para esto empleamos como siempre el método setListAdapter, al que pasamos la lista de libros, pero esta vez usaremos nuestro adaptador personalizado.

[sourcecode language=&#8221;java&#8221;]  
/*\* Called when the activity is first created. \*/  
@Override  
public void onCreate(Bundle savedInstanceState) {  
super.onCreate(savedInstanceState);

// Obtenemos la lista de Libros  
ArrayList<Libro> Libros = getItems();  
// Entregamos la lista de Libros al adaptador de la lista en el Layout Lista.xml  
setListAdapter(new LibroAdapter(this, R.layout.lista_item, Libros));  
}  
[/sourcecode]

##### Paso 5 &#8211; Ejecutar la aplicación

Nuestra Actividad controladora de la Lista, después de todos estos cambios, queda así (pulsa para desplegar):

[sourcecode language=&#8221;java&#8221; collapse=&#8221;true&#8221;]  
package com.example.tolkienlibrary;

import java.util.ArrayList;

import android.app.ListActivity;  
import android.content.Context;  
import android.os.Bundle;  
import android.view.LayoutInflater;  
import android.view.View;  
import android.view.ViewGroup;  
import android.widget.ArrayAdapter;  
import android.widget.TextView;

public class ControladorLista extends ListActivity {

public class Libro {  
private String Titulo = "";  
private String Autor = "";

public String getTitulo() {  
return Titulo;  
}

public String getAutor() {  
return Autor;  
}

public void setTitulo(String titulo) {  
Titulo = titulo;  
}

public void setAutor(String autor) {  
Autor = autor;  
}  
}

private class LibroAdapter extends ArrayAdapter<Libro> {

private ArrayList<Libro> items;

public LibroAdapter(Context context, int textViewResourceId, ArrayList<Libro> items) {  
super(context, textViewResourceId, items);  
this.items = items;  
}

@Override  
public View getView(int position, View convertView, ViewGroup parent) {  
View v = convertView;  
if (v == null) {  
LayoutInflater vi = (LayoutInflater) getSystemService(Context.LAYOUT\_INFLATER\_SERVICE);  
v = vi.inflate(R.layout.lista_item, null);  
}  
Libro libro = items.get(position);  
if (libro != null) {  
TextView ttitulo = (TextView) v.findViewById(R.id.titulo);  
TextView tautor = (TextView) v.findViewById(R.id.autor);  
if (ttitulo != null) {  
ttitulo.setText(libro.getTitulo());  
}  
if (tautor != null) {  
tautor.setText(libro.getAutor());  
}  
}  
return v;  
}  
}

/*\* Called when the activity is first created. \*/  
@Override  
public void onCreate(Bundle savedInstanceState) {  
super.onCreate(savedInstanceState);

// Obtenemos la lista de Libros  
ArrayList<Libro> Libros = getItems();  
// Entregamos la lista de Libros al adaptador de la lista en el Layout Lista.xml  
setListAdapter(new LibroAdapter(this, R.layout.lista_item, Libros));  
}

/*  
* Obtiene una lista de libros  
*  
* @returns ArrayList<Libro> libros  
*/  
public ArrayList<Libro> getItems() {  
ArrayList<Libro> MiLista = new ArrayList<Libro>();

// Creamos los objetos Libro  
Libro libro1 = new Libro();  
libro1.setTitulo("El Silmarillion");  
libro1.setAutor("J.R.R. Tolkien");

Libro libro2 = new Libro();  
libro2.setTitulo("El Señor de los Anillos");  
libro2.setAutor("J.R.R. Tolkien");

Libro libro3 = new Libro();  
libro3.setTitulo("Los propios dioses");  
libro3.setAutor("Isaac Asimov");

// Añadimos los libros a la lista  
MiLista.add(libro1);  
MiLista.add(libro2);  
MiLista.add(libro3);

return MiLista;  
}  
}  
[/sourcecode]

Y con esto hemos acabado.

#### Paso 6 &#8211; Descarga el proyecto

Puedes descargar el código fuente del proyecto completo que hemos realizado en ésta guía [desde aquí](http://blog.findemor.es/resources/TolkienLibrary_Guia4_Solution.zip "desde aquí")

#### Paso 7 &#8211; Aún hay más&#8230;

Si queréis hacer una lista aún más avanzada, y usar un fragmento para distinguir y destacar completamente la funcionalidad y el aspecto de vuestro primer elemento de la lista (algo muy habitual) podeis ver la guia aquí:

[Incluir un Fragmento en una ListView](http://blog.findemor.es/2014/07/como-incluir-un-fragment-en-un-listview-de-android/ "Cómo incluir un Fragment en un ListView de Android")

O podéis leer las otras guías:

<a href="http://blog.findemor.es/2011/01/como-programar-en-android-guia-1" target="_self">Guia 1 &#8211; Hola Mundo en Android.</a>

[Guia 2 &#8211; Navegación entre Layouts con un botón, y paso de parámetros.](http://blog.findemor.es/2011/01/como-programar-en-android-guia-2/)

[Guia 3 &#8211; ListView o listas con scroll.](http://blog.findemor.es/2011/02/como-programar-en-android-guia-3)

[Guia 4 &#8211; ListView ricas con celdas personalizadas e imágenes.](http://blog.findemor.es/2011/02/como-programar-en-android-guia-4)

[Guia 5 &#8211; Acceso a base de datos (SQLLite).](http://blog.findemor.es/2011/02/como-programar-en-android-guia-5)