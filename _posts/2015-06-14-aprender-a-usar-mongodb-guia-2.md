---
id: 357
title: 'Aprender a usar MongoDB: Guía 2'
date: 2015-06-14T12:39:28+00:00
author: findemor
layout: post
guid: http://blog.findemor.es/?p=357
permalink: /2015/06/aprender-a-usar-mongodb-guia-2/
image: /wp-content/uploads/2015/06/mongodb.png
categories:
  - Desarrollo
  - Cursos
tags:
  - MongoDB
---
<p dir="ltr">
  En esta segunda publicación acerca de MongoDB veremos como arrancar el motor, crear una base de datos y poblarla con datos.
</p>

<h2 dir="ltr">
  Anteriormente
</h2>

<p dir="ltr">
  Guía 1: <a title="Características básicas e introducción a Mongodb" href="http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-1/">Introducción a MongoDB, características clave</a>
</p>

<h2 dir="ltr">
  Iniciar MongoDB e importar o insertar datos
</h2>

<!--more-->

<h3 dir="ltr">
  Arrancar el motor de MongoDB
</h3>

<p dir="ltr">
  Una vez la base de datos esta instalada, podríamos configurarla para que arranque automaticamente como un servicio, pero yo prefiero arrancarla manualmente para que no consuma recursos durante el tiempo que no la estoy utilizando.
</p>

<p dir="ltr">
  Para eso, usando el terminal, entramos en la carpeta mongo que se ha creado al instalar mongodb, y ejecutamos el comando:
</p>

> mongod -dbpath ./db

<p dir="ltr">
  La aplicación mongod se arrancará y se mantendrá a la escucha en el puerto por defecto, y dará acceso a las bases de datos ubicadas bajo la carpeta db.
</p>

<h3 dir="ltr">
  Conectar a la base de datos
</h3>

<p dir="ltr">
  Para conectar y empezar a utilizar la base de datos, abrimos un nuevo terminal y ejecutamos la shell. Para ello, si durante la instalación hemos incluido mongo en el PATH, basta con ejecutar el comando:
</p>

> <p dir="ltr">
>   > mongo
> </p>

<h3 dir="ltr">
  Listar las bases de datos
</h3>

<p dir="ltr">
  Ahora que hemos ejecutado la shell, podemos empezar a lanzar comandos en ella. Lo primero será listar todas las bases de datos disponibles en nuestro mongo. Prueba a escribir en la shell el siguiente comando:
</p>

> <p dir="ltr">
>   > show dbs
> </p>

<p dir="ltr">
  Si nuestra instalación es nueva, mostrara una lista vacía, pero en otro caso se listará la lista completa de bases de datos disponibles en este entorno.
</p>

<h3 dir="ltr">
  Crear una base de datos
</h3>

<p dir="ltr">
  En MongoDB, lo más habitual será crear las bases de datos dinamicamente a medida que las usemos. Las bases de datos permiten agrupar colecciones, del mismo modo que las colecciones agrupan documentos. Y también del mismo modo, no es necesario preparar o crear una base de datos antes de que pueda ser usada, lo que se traduce en que ésta se creará automaticamente en el momento en que guardemos documentos en ellas, aunque no exista ni base de datos ni colección alguna previamente.
</p>

<p dir="ltr">
  Para comprobar esto, lanzaremos los siguientes comandos:
</p>

> <p dir="ltr">
>   > use testdb
> </p>
> 
> <p dir="ltr">
>   > db.users.insert({ name:”findemor” })
> </p>

<p dir="ltr">
  El primero de ellos determina en qué base de datos vamos a ejecutar los comandos posteriores,
</p>

<p dir="ltr">
  El segundo es el que realiza realmente el trabajo: ejecuta la inserción del documento BSON incluido entre los paréntesis, dentro de la colección users.
</p>

<p dir="ltr">
  Como no existían ni la base de datos testdb, ni la colección users, éstos se crean durante el proceso de inserción del documento.
</p>

<p dir="ltr">
  Podemos comprobarlo listando las bases de datos:
</p>

> <p dir="ltr">
>   > show dbs
> </p>

<p dir="ltr">
  Listando las colecciones
</p>

> <p dir="ltr">
>   > use testdb
> </p>
> 
> <p dir="ltr">
>   > show collections
> </p>

<p dir="ltr">
  Y listando los objetos de la colección
</p>

> <p dir="ltr">
>   > db.users.find()
> </p>

<p dir="ltr">
  Veremos que el objeto se ha insertado y que incluye un atributo adicional “_id”.
</p>

<h4 dir="ltr">
  Acerca del atributo identificador _id
</h4>

<p dir="ltr">
  Todos los documentos deben incluir al menos un identificador único, que debe llamarse _id. Si durante la creación del objeto éste no se especifica, MongoDB lo creará automáticamente.
</p>

<p dir="ltr">
  El valor _id de un documento debe ser único dentro de una colección, y sobre él se establece automáticamente un índice, lo que acelera las búsquedas por él.
</p>

<p dir="ltr">
  Además, siempre que sea único, su valor puede ser cualquiera que deseemos darle (basta con darle un valor específico durante la creación del documento), facilitando la integración con bases de datos existentes en otros sistemas de nuestra aplicación.
</p>

<h3 dir="ltr">
  Otras formas de población de datos
</h3>

<p dir="ltr">
  Para poblar con datos una base de datos, existen numerosas formas:
</p>

<li dir="ltr">
    Script de shell: la shell de mongo admite scripts Javascript, por lo que podríamos instanciar una variable dentro de un bucle y poblar una colección con datos de prueba.
</li>

```js
for (i = 0; i & lt; 10; ++i) {  
var doc = {  
name: "user-" + i;  
}

db.posts.insert(doc);  
}  
```


<li dir="ltr">
    Ejecutar el script creado en una herramienta externa:
</li>

> <p dir="ltr">
>   > load(“scripts/myusers.js”;)
> </p>

<li dir="ltr">
    Importar un documento que solo contenga Jsons: utilizando la herramienta mongoimport
</li>

> <p dir="ltr">
>   Mongoimport –d testdb –c users myusers.json
> </p>

<li dir="ltr">
    Desde una aplicación utilizando el driver correspondiente, por ejemplo con MongoClient driver:
</li>

```js
var MongoClient = require('mongodb').MongoClient;

MongoClient.connect('mongodb://localhost:27017/testdb, function(err, db) {  
if (err) throw err;  
var query = {  
'grade': 100  
};

function callback(err, doc) {  
if (err) throw err;  
console.dir(doc);  
db.close();

}  
});  
```


<h2 dir="ltr">
  Enlaces a la guía completa
</h2>

  * Guía 1: [Introducción a MongoDB, características clave](http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-1/ "Características básicas e introducción a Mongodb")
  * Guía 2: Arrancar MongoDB e importar datos  ‎
  * Guía 3: [Comandos y operaciones esenciales](http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-3 "Operaciones fundamentales")
  * Guía 4: [Comandos y operaciones avanzadas](http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-4 "Comandos y operaciones avanzadas")
  * Guía 5: [Esquema de datos dirigido por la aplicación](http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-5 "Application Driven Schema")
  * Guía 6: [Crear, manejar y entender los índices](http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-6 "índices en mongodb")
  * Guía 7: [Análisis de datos con el framework de agregación](http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-7 "Aggregation Framework")
  * Guía 8: [Replicación y Sharding](http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-8 "Replication and sharding")