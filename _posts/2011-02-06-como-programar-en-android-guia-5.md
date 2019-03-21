---
id: 150
title: 'Como programar en Android: Guia 5'
date: 2011-02-06T19:49:14+00:00
author: findemor
layout: post
guid: http://blog.findemor.es/?p=150
permalink: /2011/02/como-programar-en-android-guia-5/
categories:
  - Desarrollo
  - Cursos
tags:
  - Android
  - Java
---
En esta nueva Guía aprenderemos como **realizar consultas sobre una Base de Datos SQLite en Android**, **añadiendo un fichero de Base de Datos a los recursos**.  
Antes de continuar con la lectura de esta guía, quizá te interesaría echar un vistazo a la [Guía 4](http://blog.findemor.es/2011/02/como-programar-en-android-guia-4/), en la que aprendimos a mostrar colecciones de datos en listas con items personalizados. Partiremos del proyecto de la Guía 4 para completar los pasos de la Guía 5.  
<!--more-->

#### ¿Qué vamos a hacer?

Vamos a crear un fichero de Base de datos **SQLite**, a añadirlo a los **recursos** de nuestro proyecto, y a **obtener una colección de registros** de la base de dos para **mostrarlo en nuestro ListView** de la Guia 4.

Para preparar fácilmente nuestra base de datos, vamos a utilizar un editor de SQLite, por ejemplo [SQLite Database Browser](http://sourceforge.net/projects/sqlitebrowser/), un editor GUI muy ligero y Open Source, disponible en Mac, Linux y Windows.

#### Paso 1 - Preparar nuestro fichero de Base de Datos SQLite

En la Guia 4, mostrabamos una lista de Libros, cada uno de los cuales tenia como información asociada: Titulo y Autor. Vamos a preparar una tabla en nuestra Base de datos que almacene esta información. Para eso podemos utilizar por ejemplo **SQLite Database Browser**. Es muy sencillo de utilizar, y asumo que todos entendemos como funciona una Base de Datos, así que vamos a ir muy rápido en este paso.

```
  
CREATE TABLE "Libros" ("_id" INTEGER PRIMARY KEY, "Titulo" TEXT, "Autor" TEXT);  
INSERT INTO "Libros" ("Titulo","Autor") VALUES ("El Silmarillion", "J.R.R. Tolkien");  
INSERT INTO "Libros" ("Titulo","Autor") VALUES ("Hoja de Niggle", "J.R.R. Tolkien");  
INSERT INTO "Libros" ("Titulo","Autor") VALUES ("Mitopoeia", "J.R.R. Tolkien");  
```


De esta forma creamos la Tabla que almacena la información de los libros, e insertamos 3 registros. Es evidente que hay modelos mejores para guardar estos datos, pero es suficiente para este ejemplo.

Hay que **destacar el campo _id** que es la clave primaria de nuestra tabla libros, (entero y autonumérico). Las claves primarias de nuestras tablas deben llamarse así para que el sistema Android la identifique como tal.

Sin embargo esto no es suficiente, y hay que realizar otro pequeño cambio a nuestra base de datos para que funcione en el dispositivo: **añadir una tabla de metadatos con información para la localización**.

```
  
CREATE TABLE "android_metadata" ("locale" TEXT DEFAULT 'es_ES');  
INSERT INTO "android_metadata" VALUES ('es_ES');  
```


<div id="attachment_155" style="width: 310px" class="wp-caption aligncenter">
  <a href="http://blog.findemor.es/wp-content/uploads/2011/02/DatabaseScreenshot1.jpg"><img class="size-medium wp-image-155" title="Así queda nuestra Base de Datos" src="http://blog.findemor.es/wp-content/uploads/2011/02/DatabaseScreenshot1-300x245.jpg" alt="Así queda nuestra Base de Datos" width="300" height="245" /></a>
  
 <p class="wp-caption-text">
    Así queda nuestra Base de Datos
  </p>
</div>

#### Paso 2 - Copiar nuestro archivo de Base de Datos

El fichero de Base de datos SQLite que hemos creado, debemos copiarlo dentro de la carpeta “**assets**”; de nuestro proyecto. En este ejemplo el fichero se llamará **TolkienLibraryDB**.

<div id="attachment_157" style="width: 160px" class="wp-caption aligncenter">
  <a href="http://blog.findemor.es/wp-content/uploads/2011/02/esqueleto-proyecto1.jpg"><img class="size-thumbnail wp-image-157" title="Esqueleto del proyecto" src="http://blog.findemor.es/wp-content/uploads/2011/02/esqueleto-proyecto1-150x150.jpg" alt="Esqueleto del proyecto" width="150" height="150" /></a>
  
 <p class="wp-caption-text">
    Esqueleto del proyecto
  </p>
</div>

#### Paso 3 - Extender SQLiteOpenHelper

Una vez que tenemos nuestro fichero de Base de Datos preparado y en la carpeta donde Android espera encontrar este tipo de recurso, es hora de implementar lo necesario para que sea accesible desde nuestra aplicación en ejecución.

Para esto vamos a crear una clase que **extienda la clase SQLiteOpenHelper**, una facilidad que nos proporciona android para manejar la creación y actualización de bases de datos, y escribiremos lo necesario para que quede así (haz clic para ver el código):

```java  
package com.example.tolkienlibrary;

import java.io.FileOutputStream;  
import java.io.IOException;  
import java.io.InputStream;  
import java.io.OutputStream;

import android.content.Context;  
import android.database.SQLException;  
import android.database.sqlite.SQLiteDatabase;  
import android.database.sqlite.SQLiteException;  
import android.database.sqlite.SQLiteOpenHelper;

/**  
* Clase que facilita el acceso a una Base de Datos SQLite creando la Base de datos a partir de un fichero  
* en la carpeta Assets  
* blog.findemor.es 06/02/2011  
**/  
public class BaseDatosHelper extends SQLiteOpenHelper {

//La carpeta por defecto donde Android espera encontrar la Base de Datos de tu aplicación  
private static String DB_PATH = "/data/data/com.example.tolkienlibrary/databases/";  
private static String DB_NAME = "TolkienLibraryDB";  
private SQLiteDatabase myDataBase;

private final Context myContext;

/**  
* Constructor  
*  
* Guarda una referencia al contexto para acceder a la carpeta assets de la aplicación y a los recursos  
* @param contexto  
**/  
public BaseDatosHelper(Context contexto) {

super(contexto, DB_NAME, null, 1);  
this.myContext = contexto;  
}

/**  
* Crea una base de datos vacía en el sistema y la sobreescribe con la que hemos puesto en Assets  
**/  
public void crearDataBase() throws IOException{

boolean dbExist = comprobarBaseDatos();

if(dbExist){  
//Si ya existe no hacemos nada  
}else{  
//Si no existe, creamos una nueva Base de datos en la carpeta por defecto de nuestra aplicación,  
//de esta forma el Sistema nos permitirá sobreescribirla con la que tenemos en la carpeta Assets  
this.getReadableDatabase();  
try {  
copiarBaseDatos();  
} catch (IOException e) {  
throw new Error("Error al copiar la Base de Datos");  
}  
}  
}

/**  
* Comprobamos si la base de datos existe  
* @return true si existe, false en otro caso  
**/  
private boolean comprobarBaseDatos(){  
SQLiteDatabase checkDB = null;  
try{  
String myPath = DB_PATH + DB_NAME;  
checkDB = SQLiteDatabase.openDatabase(myPath, null, SQLiteDatabase.OPEN_READONLY);  
}catch(SQLiteException e){  
//No existe  
}

if(checkDB != null){  
checkDB.close();  
}

return checkDB != null ? true : false;  
}

/**  
* Copia la base de datos desde la carpeta Assets sobre la base de datos vacía recién creada en la carpeta del sistema,  
* desde donde es accesible  
**/  
private void copiarBaseDatos() throws IOException{

//Abrimos la BBDD de la carpeta Assets como un InputStream  
InputStream myInput = myContext.getAssets().open(DB_NAME);

//Carpeta de destino (donde hemos creado la BBDD vacia)  
String outFileName = DB_PATH + DB_NAME;

//Abrimos la BBDD vacia como OutputStream  
OutputStream myOutput = new FileOutputStream(outFileName);

//Transfiere los Bytes entre el Stream de entrada y el de Salida  
byte[] buffer = new byte[1024];  
int length;  
while ((length = myInput.read(buffer))>0){  
myOutput.write(buffer, 0, length);  
}

//Cerramos los ficheros abiertos  
myOutput.flush();  
myOutput.close();  
myInput.close();  
}

/**  
* Abre la base de datos  
**/  
public void abrirBaseDatos() throws SQLException{  
String myPath = DB_PATH + DB_NAME;  
myDataBase = SQLiteDatabase.openDatabase(myPath, null, SQLiteDatabase.OPEN_READONLY);

}

/**  
* Cierra la base de datos  
**/  
@Override  
public synchronized void close() {  
if(myDataBase != null)  
myDataBase.close();

super.close();  
}

@Override  
public void onCreate(SQLiteDatabase db) {  
//No usamos este método  
}

@Override  
public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {  
//No usamos este método  
}

//Podemos añadir métodos públicos que accedan al contenido de la base de datos,  
//para realizar consultas, u operaciones CRUD (create, read, update, delete)

}

```


El código está comentado para que resulte autoexplicativo, pero hay que **destacar las variables DB_PATH y DB_NAME.**

  * **DB_PATH** contiene la carpeta de destino en la que Android coloca la Base de Datos de nuestra aplicación por defecto, y es de la forma /data/data/**TU_PACKAGE**/databases/ siendo **TU_PACKAGE** el nombre de tu package.
  * **DB_NAME** es el nombre del fichero de la Base de Datos que hemos colocado en Assets.

#### Paso 4 - Nuestra entidad Libro

En la Guía 4 implementamos nuestra entidad Libro en el controlador de la lista: la Actividad ControladorLista.

Vamos a quitar de ahí el código que define la entidad Libro y a crear una nueva clase “Libro.java”; donde recolocaremos ese código, de forma que quede claro que vamos a acceder a entidades de ese tipo tanto desde el Controlador de la Lista como desde la clase de acceso a la Base de Datos.

```java  
package com.example.tolkienlibrary;

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
```


#### Paso 5 - Implementar una operación de Consulta sobre los datos

En la clase **BaseDatosHelper** que acabamos de implementar, vamos a implementar un método que nos devuelva todos los registros de Libros que hay en nuestra tabla Libros.

Los datos se pueden devolver de varias formas, **lo más común** **es devolver un Cursor** a los datos obtenidos, algo que resulta muy cómodo para poblar de datos los controles ListView, por ejemplo. Sin embargo aquí vamos a **recorrer el Cursor** para ir instanciando **objetos de tipo Libro** (que implementamos en la Guia 4) y de esta forma veremos el ejemplo completo.

Añadimos el siguiente código a nuestra clase:

```java

//Podemos añadir métodos públicos que accedan al contenido de la base de datos,  
//para realizar consultas, u operaciones CRUD (create, read, update, delete)

private final String TABLE_LIBROS = "Libros";  
private final String TABLE_KEY_ID = "_id";  
private final String TABLE_KEY_TITULO = "Titulo";  
private final String TABLE_KEY_AUTOR = "Autor";

/*  
* Obtiene todos los libros desde la Base de Datos  
*/  
public ArrayList<Libro> GetLibros(){  
ArrayList<Libro> listaLibros = new ArrayList<Libro>();

Cursor c = myDataBase.query(TABLE_LIBROS,  
new String[] {TABLE_KEY_ID, TABLE_KEY_TITULO, TABLE_KEY_AUTOR},  
null, null, null, null, null);

//Iteramos a traves de los registros del cursor  
c.moveToFirst();  
while (c.isAfterLast() == false) {  
Libro libro = new Libro();  
libro.setTitulo(c.getString(1));  
libro.setAutor(c.getString(2));  
listaLibros.add(libro);  
c.moveToNext();  
}  
c.close();

return listaLibros;  
}  
```


#### Paso 6 - Consultar los datos para mostrarlos en la Lista

Vamos a reimplementar el evento **OnCreate** de la Actividad “**ControladorLista**”; de nuestro ejemplo de la Guia 4, para crear la Base de Datos al mostrar la lista (si no se ha creado anteriormente).

```java
  
public class ControladorLista extends ListActivity {

private class LibroAdapter extends ArrayAdapter<Libro> {

private ArrayList<Libro> items;

public LibroAdapter(Context context, int textViewResourceId,  
ArrayList<Libro> items) {  
super(context, textViewResourceId, items);  
this.items = items;  
}

@Override  
public View getView(int position, View convertView, ViewGroup parent) {  
View v = convertView;  
if (v == null) {  
LayoutInflater vi = (LayoutInflater) getSystemService(Context.LAYOUT_INFLATER_SERVICE);  
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

BaseDatosHelper miBBDDHelper;

public void crearBBDD() {  
miBBDDHelper = new BaseDatosHelper(this);  
try {  
miBBDDHelper.crearDataBase();  
} catch (IOException ioe) {  
throw new Error("Unable to create database");  
}  
}

/*\* Called when the activity is first created. \*/  
@Override  
public void onCreate(Bundle savedInstanceState) {  
super.onCreate(savedInstanceState);  
// Creamos la Base de datos  
crearBBDD();  
// Obtenemos la lista de Libros  
ArrayList<Libro> Libros = getItems();  
// Entregamos la lista de Libros al adaptador de la lista en el Layout  
// Lista.xml  
setListAdapter(new LibroAdapter(this, R.layout.lista_item, Libros));  
}

...  
```


En la **Guía 4** también implementamos en esta clase controladora un método que proporcionaba una lista de Libros cargados de datos: **public ArrayList<Libro> getItems()**

Vamos a **reimplementar** ese método para que **abra una conexión** con la Base de Datos, **consulte** los datos, y **cierre** la conexión.

```java  
/*  
* Obtiene una lista de libros  
*  
* @returns ArrayList<Libro> libros  
*/  
public ArrayList<Libro> getItems() {  
//Abrimos una conexión  
miBBDDHelper.abrirBaseDatos();  
//Consultamos los datos  
ArrayList<Libro> listaLibros = miBBDDHelper.GetLibros();  
//Cerramos la conexion  
miBBDDHelper.close();  
//Devolvemos los datos  
return listaLibros;  
}  
```


#### Paso 7 - Probar la aplicación

Ya hemos terminado y es hora de ejecutar la aplicación.

Aparentemente no debería tener diferencias con la de la Guía 4, excepto porque estamos obteniendo resultados distintos en nuestra lista. Esto se debe a que los datos se obtienen desde la Base de Datos, por lo que ahora podríamos implementar operaciones de inserción y edición de Libros, por ejemplo, de forma que un usuario pueda dar de alta nuevos libros de su biblioteca Tolkien!!

<div id="attachment_161" style="width: 310px" class="wp-caption aligncenter">
  <a href="http://blog.findemor.es/wp-content/uploads/2011/02/ScreenshotAppGuia5.jpg"><img class="size-medium wp-image-161" title="Resultado de la ejecución" src="http://blog.findemor.es/wp-content/uploads/2011/02/ScreenshotAppGuia5-300x210.jpg" alt="Resultado de la ejecución" width="300" height="210" /></a>
  
 <p class="wp-caption-text">
    Resultado de la ejecución
  </p>
</div>

#### Paso 8 - Descarga la aplicación...

El fichero de solución de la Guía 5, lo puedes descargar desde <a href="http://blog.findemor.es/resources/TolkienLibrary_Guia5_Solution.zip" target="_blank">este enlace</a>.

#### Paso 9 - Guías anteriores

Esta serie de Guías-Tutoriales para programar en Android está formada por los siguientes capítulos.

Espero que os hayan resultado útiles.

[<< IR A LA GUÍA ANTERIOR: Guia 4 - ListView ricas con celdas personalizadas e imágenes.](http://blog.findemor.es/2011/02/como-programar-en-android-guia-4)

[](http://blog.findemor.es/2011/02/como-programar-en-android-guia-4)<a style="text-align: right;" title="Inserción de registros en la base de datos" href="http://blog.findemor.es/2012/02/como-programar-en-android-guia-6/">IR A LA GUÍA SIGUIENTE: Guia 6 - Inserción de registros en la Base de Datos >></a>