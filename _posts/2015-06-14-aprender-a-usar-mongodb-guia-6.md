---
id: 367
title: 'Aprender a usar MongoDB: Guía 6'
date: 2015-06-14T12:40:31+00:00
author: findemor
layout: post
guid: http://blog.findemor.es/?p=367
permalink: /2015/06/aprender-a-usar-mongodb-guia-6/
image: /wp-content/uploads/2015/06/mongodb.png
categories:
  - Desarrollo
  - Tecnología
tags:
  - bases de datos
  - mongodb
  - tecnología
---
<p dir="ltr">
  Ahora que ya hemos aprendido a <a href="http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-5">diseñar correctamente la estructura de los datos en MongoDB</a>, y que sabemos <a href="http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-3">realizar todo tipo de consultas</a>, seguro que se nos ocurren multitud de posibilidades con documentos anidados y estructuras complejas para que nuestra aplicación sea realmente rápida.
</p>

<p dir="ltr">
  Sin embargo, cuando tengamos una cantidad masiva de datos, por defecto MongoDB solo nos ofrecerá un alto rendimiento cuando las consultas utilicen filtros que utilicen el identificador (_id) del documento raiz, ya que de otro modo MongoDB deberá buscar sobre todos los elementos de la colección antes de empezar a procesar los resultados. En la publicación de hoy vamos a aprender a crear otros indices que nos permitan realizar operaciones eficientes sobre otros atributos, así como algunos detalles sobre su uso.
</p>

<h2 dir="ltr">
  Anteriormente
</h2>

Guía 5: [Esquema de datos dirigido por la aplicación](http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-5 "Application Driven Schema")

<h2 dir="ltr">
  Trabajar con índices en MongoDB<!--more-->
</h2>

<h3 dir="ltr">
  Tipos de índice
</h3>

<h4 dir="ltr">
  Indices simples (<a href="http://docs.mongodb.org/manual/core/index-single/">single field index</a>)
</h4>

<p dir="ltr">
  Los índices simples nos permiten definir un índice sobre un único campo. El orden (ascendente o descendente) en este caso, no es relevante, ya que mongoDB puede recorrerlo en cualquier dirección.
</p>

> <p dir="ltr">
>   > db.scores.ensureIndex({ score: 1 });
> </p>

<h4 dir="ltr">
  Indices compuestos (compound index)
</h4>

<p dir="ltr">
  Un caso más interesante es el <a href="http://docs.mongodb.org/manual/core/index-compound/">indice compuesto,</a> que nos permite establecer índices sobre varios atributos en conjunto. Por ejemplo:
</p>

> <p dir="ltr">
>   > db.scores.ensureIndex({ score: 1, username: -1 })
> </p>

<p dir="ltr">
  En este caso, el orden de los atributos cuando se crea el índice es relevante, ya que el índice ordenará primero por el primero y después por el segundo. Es decir, en el ejemplo anterior el indice ordena por puntuación, y por cada valor de puntuación, ordenará por nombre de usuario.
</p>

<p dir="ltr">
  El orden de ordenacion (ascendente o descendente) tambien es importante en este caso, ya que puede determinar cuando el índice se usará en una operación de ordenación.
</p>

<h4 dir="ltr">
  Indices multiclave (multikey index)
</h4>

<p dir="ltr">
  <a href="http://docs.mongodb.org/manual/core/index-multikey/">Este tipo de indice</a> permite indexar el contenido almacenado en los arrays. Si indexas un atributo que contiene un array, MongoDB creará automaticamente entradas separadas para cada elemento del array en la tabla de indices, y hace muy eficiente la búsqueda por valores de los arrays.
</p>

> <p dir="ltr">
>   > db.scores.ensureIndex({ favorites: 1 })
> </p>

<p dir="ltr">
  No se pueden crear índices multiclave sobre dos campos de tipo array en el mismo índice.
</p>

<h4 dir="ltr">
  Indices geoespaciales (geospatial index)
</h4>

<p dir="ltr">
  Para soportar consultas geoespaciales eficientes sobre datos de tipo coordenada, MongoDB soporta dos indices especiales: <a href="http://docs.mongodb.org/manual/core/2d/">2d indexes</a> para geometria de planos y <a href="http://docs.mongodb.org/manual/core/2dsphere/">2sphere indexes</a> para geometria esférica.
</p>

<p dir="ltr">
  Para poder utilizar estos indices, el atributo debe ser un objeto geolocalizado según la especificación <a href="http://geojson.org/">GeoJson</a> (en este caso un punto con las coordenadas como un array de Longitud y Latitud).
</p>

> <p dir="ltr">
>   > db.places.insert({ _id : 1, name: “mi casa”, loc: { type : “Point”, “coordinates” : [ -37.478, 4.4884 ] } })<br /> > db.scores.ensureIndex({ loc: “2dsphere” })
> </p>

<p dir="ltr">
  Ahora <strong>podríamos utilizar operadores de búsqueda, por ejemplo, por cercanía</strong>, especificando la distancia máxima en metros alrededor de un punto concreto con la misma notación:
</p>

> <p dir="ltr">
>   > db.places.find({ loc: {<br /> $near : {<br /> $geometry : { type: “Point”, coordinates : [ -130, 39 ] },<br /> $maxDistance : 10000<br /> } } });
> </p>

<h4 dir="ltr">
  Indices de texto (text indexes)
</h4>

<p dir="ltr">
  MongoDB proporciona un tipo de <a href="http://docs.mongodb.org/manual/core/index-text/">indice que soporta la busca de texto</a> en una colección. Estos índices no almacenan las “stop words” específicas de un lenguaje (por ejemplo, “de”, “la”, “o”) y simplifica (stem) las palabras para almacenar solo su forma “raiz”.
</p>

> <p dir="ltr">
>   > db.libros.ensureIndex({ titulo: “text” })<br /> > db.libros.find( { $text: { $search : “dog” } })
> </p>

<p dir="ltr">
  Podemos definir una proyección para obtener la relevancia del resultado en función del número de coincidencias que el índice de texto completo haya encontrado (ya que las búsquedas de este tipo no son filtros en el sentido estricto).
</p>

> <p dir="ltr">
>   > db.libros.find( { $text: { $search: “dog” } }, { score : { $meta: “textScore” }} )
> </p>

<h4 dir="ltr">
  Hashed Indexes
</h4>

<p dir="ltr">
  Se trata un tipo de índice especial para soportar <a href="http://docs.mongodb.org/manual/core/index-hashed/">sharding hash</a>.
</p>

<h3 dir="ltr">
  Propiedades de los índices
</h3>

<h4 dir="ltr">
  Indices únicos
</h4>

<p dir="ltr">
  Una propiedad unique para un indice hace que MongoDB rechace valores duplicados para ese atributo indexado dentro de una colección. Además de esta restricción, funcionan como cualquier otro índice.
</p>

> <p dir="ltr">
>   > db.scores.ensureIndex({ username: 1, score: -1 }, { unique: 1 })
> </p>

<p dir="ltr">
  Si quisiéramos eliminar los duplicados durante el proceso (ojo que puede resultar peligroso ya que implica perdida de datos):
</p>

> <p dir="ltr">
>   > db.scores.ensureIndex({ username: 1, score: -1 }, { unique: true, dropDups: true })
> </p>

<h4 dir="ltr">
  Indices dispersos (sparse indexes)
</h4>

<p dir="ltr">
  Una propiedad sparse en un indice hace que el índice solo contenga entradas para documentos que tienen el campo indexado (si el documento no tiene el campo que estamos indexando, no estará indexado).
</p>

<p dir="ltr">
  Es posible combinar sparse con unique para rechazar documentos que tengan valores duplicados para un campo pero ignore los documentos que no tienen ese atributo.
</p>

> <p dir="ltr">
>   > db.scores.ensureIndex({ username: 1, score: -1 }, { unique: 1, sparse: true })
> </p>

<h4 dir="ltr">
  Indices TTL (TTL indexes)
</h4>

<p dir="ltr">
  Son indices especiales de MongoDB que permiten eliminar documentos automaticamente de una colección despues de una cantidad de tiempo concreta. Esto es ideal para ciertos tipos de información como datos generados automaticamente, logs, sesiones…
</p>

<h3 dir="ltr">
  Uso de los indices
</h3>

<h4 dir="ltr">
  Consultas cubiertas
</h4>

<p dir="ltr">
  Cuando los criterios de filtro de una consulta y la proyección de los datos incluyen solo atributos indexados, MongoDB devolverá directamente los resultados desde el índice sin escanear ningún documento o acceder a la memoria para su lectura. A este tipo de consulta se la denomina Covered Query y puede ser realmente eficiente.
</p>

<p dir="ltr">
  <strong>Ejemplo</strong>:
</p>

> <p dir="ltr">
>   > db.scores.ensureIndex({ score: 1 })<br /> > db.scores.find({ score: { “$lt”: 30 } }, { score : 1, _id : 0 })
> </p>

<p dir="ltr">
  Creamos un índice sobre el atributo score. Después consultamos los documentos utilizando únicamente los atributos indexados en el filtro, y devolvemos únicamente los datos indexados empleando una proyección (la proyección es el segundo parametro de la operación find y determina qué campos queremos obtener como respuesta). Esta operación se considera una operación cubierta por el índice y será muy eficiente.
</p>

<h4 dir="ltr">
  Intersección de índices (<a href="http://docs.mongodb.org/manual/core/index-intersection/">index intersection</a>)
</h4>

<p dir="ltr">
  MongoDb puede emplear distintos índices para completar una operación. Si un índice puede emplearse para una parte de la condición de la query y otro índice puede completar otra parte de la condición, entonces MongoDB usará la intersección de ambos para obtener el resultado.
</p>

<h3 dir="ltr">
  Indices y sharding
</h3>

<p dir="ltr">
  Como veremos en el capitulo de sharding, MongoDB puede estar distribuido en varias máquinas, donde cada una de ellas almacena una parte de la totalidad de los datos. Para localizar un documento, MongoDB debe enviar la consulta en cada nodo, por lo que si no se sabe en cual está el documento, la consulta se realizará en todos ellos (broadcast query).
</p>

<p dir="ltr">
  Para evitar este comportamiento, se define una clave especial (clave de shard) que actuará como índice de nodos, y que permite determinar en que nodo está un documento concreto, ya que los datos están particionados siguiendo una lógica. Para aprovechar esta clave de shard, la clave debe estar incluida en todas las consultas que hagamos.
</p>

<h3 dir="ltr">
  Operaciones
</h3>

<h4 dir="ltr">
  Crear índices
</h4>

<p dir="ltr">
  Para crear un índice, emplearemos la operación ensureIndex especificando el criterio del índice tal y como se ha descrito antes
</p>

> <p dir="ltr">
>   > db.scores.ensureIndex({ username: 1, score: -1 });
> </p>

<p dir="ltr">
  Esta operación es bloqueante (a nivel de base de datos), si queremos evitarlo hay que usar la opción “background: true” que lo creará en segundo plano, aunque existe un límite de creación de un único índice cada simultáneamente.
</p>

<h4 dir="ltr">
  Listar los índices existentes en una colección
</h4>

> <p dir="ltr">
>   > db.scores.getIndexes()
> </p>

<h4 dir="ltr">
  Eliminar un índice existente
</h4>

> <p dir="ltr">
>   > db.scores.dropIndex({ username: 1, score: -1 })
> </p>

<h4 dir="ltr">
  Forzar el uso de un índice en una operación
</h4>

<p dir="ltr">
  Si por alguna razón MongoDB no utilizaría un índice cuyo uso deseamos forzar, podemos utilizar el siguiente comando, especificando el índice que queremos utilizar:
</p>

> <p dir="ltr">
>   > db.scores.find({ username: “findemor”}).hint({ username: 1, score: -1 })
> </p>

<p dir="ltr">
  Hay que tener en cuenta que los documentos que no existan en el índice, no serán listados, y que el orden del índice es muy importante, ya que hay que intentar quitar cuanto antes el mayor volumen de datos (es decir, los primero atributos del índice deberían ser los más segregados de la base de datos).
</p>

<h4 dir="ltr">
  Forzar que no se use ningún índice
</h4>

> <p dir="ltr">
>   > db.scores.find({ username: “findemor” }).hint({ $natural: 1 })
> </p>

<h3 dir="ltr">
  Analizar el uso de los índices
</h3>

<p dir="ltr">
  Podemos utilizar algunas operaciones para comprender cómo se procesará la consulta y analizar el uso de los índices existentes.
</p>

<p dir="ltr">
  <strong>Obtener una explicación de cómo se procesará la consulta</strong>
</p>

<p dir="ltr">
  Basta con aplicar la operación explain a la consulta que se desee. Esta operación ofrece muchísima información acerca de qué indices se utlizaron y como, cuantos elementos se recorrieron en la búsqueda, etc. Para conocer los detalles es recomendable leer la <a href="http://docs.mongodb.org/manual/reference/explain-results/">documentación oficial</a>.
</p>

> <p dir="ltr">
>   > db.places.find({ _id : 1 }, { _id : 1 }).explain()
> </p>

[js]{  
"cursor": "IDCursor",  
"n": 1,  
"nscannedObjects": 0,  
"nscanned": 1,  
"indexOnly": true,  
"millis": 0,  
"indexBounds": {  
"_id": [  
[  
1,  
1  
]  
]  
},  
"server": "MacBook-de-Findemor.local:27017"  
}[/js]

<p dir="ltr">
  En el ejemplo podemos ver que, <strong>al hacer una consulta Cubierta</strong> (es decir, la proyección y el filtro están en el índice, por lo que no hay que leer documentos del disco ni de la memoria), hemos obtenido 1 resultado (n), no se escaneó ningun documento en la colección (nscannedObjects), se escaneó solo un documento en el índice (nscanned), la consulta estaba cubierta (indexOnly), tardó 0 millisegundos…
</p>

<p dir="ltr">
  Hay que tener en cuenta que los datos obtenidos pueden diferir en funcion de la versión de mongo que tengais instalada (la mia es un poco antigua).
</p>

<p dir="ltr">
  <strong>Obtener información acerca de una colección</strong>
</p>

<p dir="ltr">
  Con la operación stats podemos conocer el numero de indices, el namespace, cantidad de documentos que contiene, tamaño de los indices…
</p>

> <p dir="ltr">
>   > db.places.stats()
> </p>

[js]{  
"ns": "test.places",  
"count": 2,  
"size": 224,  
"avgObjSize": 112,  
"storageSize": 8192,  
"numExtents": 1,  
"nindexes": 1,  
"lastExtentSize": 8192,  
"paddingFactor": 1,  
"systemFlags": 1,  
"userFlags": 1,  
"totalIndexSize": 8176,  
"indexSizes": {  
"\_id\_": 8176  
},  
"ok": 1  
}[/js]

<h3 dir="ltr">
  Latencias, Autolog y Profiling
</h3>

<p dir="ltr">
  Para terminar con esta publicación, vamos a ver como detectar malos rendimientos en las consultas y así poder ver que índices serían recomendables en nuestra base de datos.
</p>

<p dir="ltr">
  En MongoDB se registra automaticamente toda consulta que tome más de 100ms. Si queremos que estos resultados se escriban en la colección system.profile (además de mostrarse por consola, que es el comportamiento por defecto), tenemos que activar el profiler, que tiene tres modos de funcionamiento:
</p>

<li dir="ltr">
  <p dir="ltr">
    <strong></strong> : el profiler no registrará nada
  </p>
</li>

<li dir="ltr">
  <p dir="ltr">
    <strong>1</strong> : el profiler registrará las operaciones lentas
  </p>
</li>

<li dir="ltr">
  <p dir="ltr">
    <strong>2</strong> : el profiler registrará todas las operaciones
  </p>
</li>

<p dir="ltr">
  Para ello, tenemos que añadir las siguientes opciones al arrancar la instancia de mongo
</p>

> <p dir="ltr">
>   &#8211;profile 1 &#8211;slowms 20
> </p>

<p dir="ltr">
  O una vez arrancado, podemos obtener o establecer nuevos valores
</p>

> <p dir="ltr">
>   > db.getProfilingLevel()<br /> > db.getProfilingStatus()<br /> > db.setProfilingStatus(1,20)
> </p>

<p dir="ltr">
  Además existen varias utilidades para facilitarnos el diagnóstico de la base de datos
</p>

<li dir="ltr">
  <p dir="ltr">
    <strong>mongotop</strong> es un programa que nos permite qué colecciones están gastando más tiempo
  </p>
</li>

<li dir="ltr">
  <p dir="ltr">
    <strong>mongostat</strong> devuelve principalmente el número de índices que se pierden… aunque resulta dificil de comprender en ocasiones (por ejemplo, un valor del 0% puede ser perfecto o puede significar que no tengas ningún índice).
  </p>
</li>

<h2 dir="ltr">
  Enlaces a la guía completa
</h2>

  * Guía 1: [Introducción a MongoDB, características clave](http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-1/ "Características básicas e introducción a Mongodb")
  * Guía 2: [Arrancar MongoDB e importar datos](http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-2/ "Iniciar el motor de MongoDB e insertar documentos")  ‎
  * Guía 3: [Comandos y operaciones esenciales](http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-3 "Operaciones fundamentales")
  * Guía 4: [Comandos y operaciones avanzadas](http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-4 "Comandos y operaciones avanzadas")
  * Guía 5: [Esquema de datos dirigido por la aplicación](http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-5 "Application Driven Schema")
  * Guía 6: Crear, manejar y entender los índices
  * Guía 7: [Análisis de datos con el framework de agregación](http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-7 "Aggregation Framework")
  * Guía 8: [Replicación y Sharding](http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-8 "Replication and sharding")