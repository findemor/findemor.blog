---
id: 363
title: 'Aprender a usar MongoDB: Guía 4'
date: 2015-06-14T12:40:03+00:00
author: findemor
layout: post
guid: http://blog.findemor.es/?p=363
permalink: /2015/06/aprender-a-usar-mongodb-guia-4/
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
  Anteriormente hemos visto como <a href="http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-2">introducir datos en la base de datos</a> MongoDB, y cómo realizar <a href=" http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-3">todo tipo de consultas</a> para obtener los datos almacenados con distintos tipos de filtro.
</p>

<p dir="ltr">
  En el post de hoy, vamos a trabajar en las operaciones que quedan, como las actualizaciones, borrado de datos, paginación y consultas por cursor.
</p>

<h2 dir="ltr">
  Anteriormente
</h2>

Guía 3: [Comandos y operaciones esenciales](http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-3 "Operaciones fundamentales")

<h2 dir="ltr">
  Operaciones avanzadas en MongoDB<!--more-->
</h2>

<h3 dir="ltr">
  Paginación
</h3>

<p dir="ltr">
  Obtener datos con paginación: en el ejemplo, podemos ver cómo obtener la segunda página de resultados, considerando que nuestras páginas fuesen de 2 elementos únicamente. Para ello, utilizamos la operación skip (que determina cuántos elementos se van a ignorar de los resultados obtenidos) y la operación limit (que especifica cuántos elementos deseamos obtener del nuevo conjunto de resultados).
</p>

<p dir="ltr">
  <strong>El orden es irrelevante</strong>, ya que primero se aplicará el filtro (si existe), después skip, y finalmente limit.
</p>

> <p dir="ltr">
>   > db.scores.find().skip(2).limit(2)
> </p>

[js]{ "_id" : 3, "username" : "franbuipa", "score" : 30 }  
{ "_id" : 4, "username" : "cocinero", "favorites" : [ "cocinar", "postres" ], "address" : { "country" : "es", "city" : "madrid" } }[/js]

<h3 dir="ltr">
  Ordenar resultados en MongoDB
</h3>

<p dir="ltr">
  Podemos añadir la operación sort para ordenar los resultados por el valor de sus atributos. Para utilizar el orden natural, hay que utilizar el 1 positivo, y para invertir los resultados, un 1 negativo. Por supuesto podemos combinarlo con skip o limit, y el orden sigue siendo relevante, primero se aplica el filtro, luego sort, después skip y finalmente limit.
</p>

> <p dir="ltr">
>   > db.scores.find().sort({ _id : 1 }).limit(2)
> </p>

[js]{ "_id" : 1, "username" : "findemor", &#8230; }  
{ "_id" : 2, "username" : "eden4ever", &#8230; }[/js]

> <p dir="ltr">
>   > db.scores.find().sort({ _id : -1 }).limit(2)
> </p>

[js]{ "_id" : 5, "username" : "deportista", &#8230; }  
{ "_id" : 4, "username" : "cocinero", &#8230; }[/js]

<h3 dir="ltr">
  Manejo de cursores en MongoDB
</h3>

<p dir="ltr">
  Como ya hemos visto, la shell de mongo funciona como una consola javascript, por lo que podemos escribir pequeños programas, o iterar sobre cursores, simplemente declarándolo como una variable javascript.
</p>

> <p dir="ltr">
>   > var c = db.scores.find().sort({ username : -1 }).limit(2)<br /> > c.hasNext()
> </p>

[js]true[/js]

> <p dir="ltr">
>   > c.next()
> </p>

[js]{ "_id" : 3, "username" : "franbuipa", "score" : 30 }[/js]

<h3 dir="ltr">
  Actualización de documentos en MongoDB
</h3>

<p dir="ltr">
  Para actualizar documentos, basta con utilizar la operación update con dos parámetros. El primero permite especificar el filtro que determina qué documentos serán actualizados (pero ojo, que solo se actualizará el primer elemento que coincida y se detendrá la operación, comportamiento que se puede modificar como veremos más abajo). El segundo parámetro es la actualización que se va a realizar.
</p>

<p dir="ltr">
  Es importante tener en cuenta que la operación update actualizará el documento en su totalidad, para comprenderlo, prestad atención a lo que pasa con el atributo finished del siguiente documento cuando se actualiza su score.
</p>

> <p dir="ltr">
>   > db.scores.find({ _id : 1 })
> </p>

[js]{ "_id" : 1, "username" : "findemor", "score" : 50, "finished" : true }[/js]

> <p dir="ltr">
>   > db.scores.update({ _id : 1 }, { _id : 1, username : &#8220;findemor&#8221;, score : 60 })
> </p>

[js]WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })[/js]

> <p dir="ltr">
>   > db.scores.find({ _id : 1 })
> </p>

[js]{ "_id" : 1, "username" : "findemor", "score" : 60 }[/js]

<p dir="ltr">
  <strong>Podemos evitar este efecto</strong>, como se muestra a continuación.
</p>

<p dir="ltr">
  <strong>Actualizar un atributo sin modificar el resto del elemento</strong> (si el atributo existe, se modifica, en otro caso se añade).
</p>

> <p dir="ltr">
>   > db.scores.find({ _id : 1 })
> </p>

[js]{ "_id" : 1, "username" : "findemor", "score" : 60 }[/js]

> <p dir="ltr">
>   > db.scores.update({ _id : 1 }, { $set : { finished : true }})
> </p>

[js]WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })[/js]

> <p dir="ltr">
>   > db.scores.find({ _id : 1 })
> </p>

[js]{ "_id" : 1, "username" : "findemor", "score" : 60, "finished" : true }[/js]

<p dir="ltr">
  Se pueden realizar otras acciones trabajando con atributos independientes.
</p>

<p dir="ltr">
  <strong>Incrementar el valor</strong> (si no existe, lo crea con el valor especificado)
</p>

> <p dir="ltr">
>   > db.scores.update({ _id : 1 }, { $inc : { score : 1 }})
> </p>

[js]WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })[/js]

> <p dir="ltr">
>   > db.scores.find({ _id : 1 })
> </p>

[js]{ "_id" : 1, "username" : "findemor", "score" : 61, "finished" : true }[/js]

<p dir="ltr">
  <strong>Eliminar una propiedad</strong> (o atributo) de un elemento.
</p>

> <p dir="ltr">
>   > db.scores.update({ _id : 1 }, { $unset : { finished : 1 }})
> </p>

[js]WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })[/js]

> <p dir="ltr">
>   > db.scores.find({ _id : 1 })
> </p>

[js]{ "_id" : 1, "username" : "findemor", "score" : 61 }[/js]

<p dir="ltr">
  <strong>UPSERT en MongoDB</strong>, actualiza el documento, y si no existe, lo crea aprovechando los datos del update como definición del nuevo documento.
</p>

> <p dir="ltr">
>   > db.scores.find({ _id : 10 }).count()
> </p>

[js]0[/js]

> <p dir="ltr">
>   > db.scores.update({ _id : 10 }, { $set : { score : 74 } }, { upsert : true })
> </p>

[js]WriteResult({ "nMatched" : 0, "nUpserted" : 1, "nModified" : 0, "_id" : 10 })[/js]

> <p dir="ltr">
>   > db.scores.find({ _id : 10 })
> </p>

[js]{ "_id" : 10, "score" : 74 }[/js]

<h4 dir="ltr">
  Actualización masiva de documentos
</h4>

<p dir="ltr">
  Cuando se realiza un update, <strong>se modificará el primer elemento coincidente y se detendrá la operación</strong>.<strong> Si queremos realizar una actualización masiva, hay que utilizar el parametro multi</strong>. Prestad atención el valor de nModified (numero de documentos modificados) en la respuesta de la operación, tras las siguientes operaciones.
</p>

> <p dir="ltr">
>   > db.scores.update({}, { $set : { finished : true }})
> </p>

[js]WriteResult({ "nMatched" : 1, "nUpserted" : 0,"nModified" : 1 })[/js]

> <p dir="ltr">
>   > db.scores.update({}, { $set : { finished : true }}, <strong>{ multi : true }</strong>)
> </p>

[js]WriteResult({ "nMatched" : 6, "nUpserted" : 0, "nModified" : 5 })[/js]

<h4 dir="ltr">
  Actualizando atributos de tipo Array (listas)
</h4>

<p dir="ltr">
  Los arrays admiten numerosas operaciones específicas que proporcionan una enorme flexibilidad de actualización en MongoDB, vamos a repasar rápidamente algunas de ellas.
</p>

<p dir="ltr">
  Primero <strong>veamos qué arrays tenemos</strong> en nuestra colección, para que podáis seguir la ejecución de las operaciones.
</p>

> <p dir="ltr">
>   > db.scores.find({ favorites : { $exists : 1 }}).pretty()
> </p>

[js]{  
"_id": 4,  
"username": "cocinero",  
"favorites": ["cocinar", "postres"],  
"address": {  
"country": "es",  
"city": "madrid"  
},  
"finished": true  
} {  
"_id": 5,  
"username": "deportista",  
"favorites": ["running"],  
"address": {  
"country": "es",  
"city": "toledo"  
},  
"finished": true  
}[/js]

<p dir="ltr">
  <strong>Modificar un elemento concreto (por posición)</strong> <strong>dentro de un array</strong>.
</p>

> <p dir="ltr">
>   > db.scores.update({ _id:4 }, { $set : { &#8220;favorites.1&#8221; : &#8220;comer&#8221; }})
> </p>

[js]WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })[/js]

> <p dir="ltr">
>   > db.scores.findOne({ _id:4 })
> </p>

[js]{  
"_id": 4,  
"username": "cocinero",  
"favorites": ["cocinar", "comer"],  
"address": {  
"country": "es",  
"city": "madrid"  
},  
"finished": true  
}[/js]

<p dir="ltr">
  <strong>Añadir un elemento al array por la derecha</strong>
</p>

> <p dir="ltr">
>   > db.scores.update({ _id:4 }, { $push : { favorites : &#8220;cazar&#8221; }})
> </p>

[js]WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })[/js]

<p dir="ltr">
  <strong>Eliminar un elemento del array por la izquierda</strong> (para que fuese por la derecha, habria que sustituir -1 por 1 positivo)
</p>

> <p dir="ltr">
>   > db.scores.update({ _id:4 }, { $pop : { favorites : -1 }})
> </p>

[js]WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })[/js]

<p dir="ltr">
  <strong>Veamos como va quedando nuestro documento</strong> para comprender las acciones anteriores…
</p>

> <p dir="ltr">
>   > db.scores.findOne({ _id : 4 })
> </p>

[js]{  
"_id": 4,  
"username": "cocinero",  
"favorites": ["comer", "cazar"],  
"address": {  
"country": "es",  
"city": "madrid"  
},  
"finished": true  
}[/js]

<p dir="ltr">
  <strong>Añadir todos los elementos especificados al Array</strong>
</p>

> <p dir="ltr">
>   > db.scores.update({ _id : 4 }, { $pushAll : { favorites : [ &#8220;a&#8221;, &#8220;b&#8221;, &#8220;c&#8221; ]}})
> </p>

[js]WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })[/js]

> <p dir="ltr">
>   > db.scores.findOne({ _id : 4 })
> </p>

[js]{  
"_id": 4,  
"username": "cocinero",  
"favorites": ["comer", "cazar", "a", "b", "c"],  
"address": {  
"country": "es",  
"city": "madrid"  
},  
"finished": true  
}[/js]

<p dir="ltr">
  <strong>Eliminar todos los elementos coincidentes del Array</strong>. También podríamos usar el operador pull para eliminar solo un elemento cualquiera especificando su valor.
</p>

> <p dir="ltr">
>   > db.scores.update({ _id : 4 }, { $pullAll : { favorites : [ &#8220;a&#8221;, &#8220;b&#8221;, &#8220;c&#8221; ]}})
> </p>

[js]WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })[/js]

> <p dir="ltr">
>   > db.scores.findOne({ _id : 4 })
> </p>

[js]{  
"_id": 4,  
"username": "cocinero",  
"favorites": ["comer", "cazar"],  
"address": {  
"country": "es",  
"city": "madrid"  
},  
"finished": true  
}[/js]

<p dir="ltr">
  <strong>Añadir un elemento al array únicamente si no existe ya</strong>.
</p>

> <p dir="ltr">
>   > db.scores.update({ _id : 4 }, { $addToSet : { favorites : &#8220;cazar&#8221; }})
> </p>

[js]WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 0 })[/js]

<h3 dir="ltr">
  Eliminar datos y colecciones en MongoDB
</h3>

<p dir="ltr">
  <strong>Para eliminar documentos</strong>, podemos determinar un criterio de filtro para eliminar todos los coincidentes del mismo modo que lo hacemos al utilizar el comando find.
</p>

> <p dir="ltr">
>   > db.scores.remove({ _id : 4 })
> </p>

[js]WriteResult({ "nRemoved" : 1 })[/js]

<p dir="ltr">
  <strong>Eliminar una colección</strong> (y todos los documentos que contiene)
</p>

> <p dir="ltr">
>   > db.scores.drop()
> </p>

[js]true[/js]

> <p dir="ltr">
>   > show collections
> </p>

[js]system.indexes[/js]

<h2 dir="ltr">
  Conclusión
</h2>

<p dir="ltr">
  Tras las operaciones descritas desde la última publicación, hemos visto multitud de operadores para trabajar con datos dentro de MongoDB. Pero MongoDB va mucho más allá, y para usarlo eficientemente necesitamos conocer aún tres elementos relevantes:
</p>

<li dir="ltr">
  <p dir="ltr">
    Cómo trabajar con esquemas dirigidos por la aplicación (frente a la tradicional Tercera Forma Normal de SQL).
  </p>
</li>

<li dir="ltr">
  <p dir="ltr">
    Definir índices para acelerar las consultas en MongoDB.
  </p>
</li>

<li dir="ltr">
  <p dir="ltr">
    Cómo utilizar el framework de agregación para agrupar y analizar datos.
  </p>
</li>

<li dir="ltr">
  <p dir="ltr">
    Trabajar con réplicas y Shards (Sharding).
  </p>
</li>

<p dir="ltr">
  Todo esto lo veremos en las siguientes publicaciones.
</p>

<h2 dir="ltr">
  Enlaces a la guía completa
</h2>

  * Guía 1: [Introducción a MongoDB, características clave](http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-1/ "Características básicas e introducción a Mongodb")
  * Guía 2: [Arrancar MongoDB e importar datos](http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-2/ "Iniciar el motor de MongoDB e insertar documentos")  ‎
  * Guía 3: [Comandos y operaciones esenciales](http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-3 "Operaciones fundamentales")
  * Guía 4: Comandos y operaciones avanzadas
  * Guía 5: [Esquema de datos dirigido por la aplicación](http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-5 "Application Driven Schema")
  * Guía 6: [Crear, manejar y entender los índices](http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-6 "índices en mongodb")
  * Guía 7: [Análisis de datos con el framework de agregación](http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-7 "Aggregation Framework")
  * Guía 8: [Replicación y Sharding](http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-8 "Replication and sharding")