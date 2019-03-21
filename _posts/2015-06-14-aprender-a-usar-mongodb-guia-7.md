---
id: 369
title: 'Aprender a usar MongoDB: Guía 7'
date: 2015-06-14T12:40:41+00:00
author: findemor
layout: post
guid: http://blog.findemor.es/?p=369
permalink: /2015/06/aprender-a-usar-mongodb-guia-7/
image: /wp-content/uploads/2015/06/mongodb.png
categories:
  - Desarrollo
  - Cursos
tags:
  - MongoDB
---
<p dir="ltr">
  Ya sabemos mucho acerca de mongo, hemos <a href="http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-2">insertado datos</a>, <a href="http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-6">añadido indices para hacerlo realmente rápido</a>, y <a href="http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-3">creado nuestras consultas</a> para utilizarlo en nuestra aplicación… pero en algún momento <strong>podríamos querer analizar los datos y entender qué tenemos ahí dentro</strong>, para sacar provecho de todas esa información y hacer las cosas aún mejor.
</p>

<p dir="ltr">
  Para el análisis de datos, agrupación, ordenaciones, proyecciones, y un montón de otros tratamientos, <strong>MongoDB nos entrega una herramienta realmente poderosa: Aggregation Framework</strong>. Hoy vamos a ver rápidamente un montón de operaciones que podríamos hacer con él.
</p>

<h2 dir="ltr">
  Anteriormente
</h2>

Guía 6: [Crear, manejar y entender los índices](http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-6 "índices en mongodb")

<h2 dir="ltr">
  Análisis de datos con el Framework de Agregación en MongoDB<!--more-->
</h2>

<p dir="ltr">
  <strong>Un ejemplo rápido</strong> de uso del Aggregation Framework
</p>

<p dir="ltr">
  <strong>Cargamos un conjunto de datos</strong> (codigos postales) en nuestra base de datos (la base de datos del ejemplo está disponible en la <a href="http://docs.mongodb.org/manual/tutorial/aggregation-zip-code-data-set/">documentación oficial</a>), para ello ejecutamos en una terminal:
</p>

> <p dir="ltr">
>   > curl http://media.mongodb.org/zips.json > zips.json<br /> > mongoimport -d test -c zips zips.json
> </p>

```js
connected to: 127.0.0.1  
2015-06-08T19:24:31.236+0200 check 9 29353  
2015-06-08T19:24:31.454+0200 imported 29353 objects
```


<p dir="ltr">
  <strong>Vemos que pinta tienen los datos</strong>, en la shell de mongo consultamos alguno de ellos:
</p>

> <p dir="ltr">
>   > db.zips.findOne()
> </p>

```js
{  
"_id": "01001",  
"city": "AGAWAM",  
"loc": [-72.622739, 42.070206],  
"pop": 15338,  
"state": "MA"  
}
```


<p dir="ltr">
  Vemos que <strong>son códigos postales, con su codigo de ciudad, las coordenadas, la población en ese código postal y el estado al que pertenecen</strong>.
</p>

<p dir="ltr">
  Ahora, sobre estos datos, <strong>podríamos lanzar una operación de agregación,</strong> por ejemplo <strong>para ver cuantos códigos postales hay en nuestra base de datos</strong> (que debería coincidir con el resultado de la operación count.
</p>

> <p dir="ltr">
>   > db.zips.aggregate([ {$group: { _id : 0, population:{$sum : 1} }} ])
> </p>

```js
{ "_id" : 0, "population" : 29353 }
```


> <p dir="ltr">
>   > db.zips.count()
> </p>

```js
29353
```


<h3 dir="ltr">
  Pipes en Aggregation
</h3>

<p dir="ltr">
  Si prestamos atención, nos daremos cuenta de que la operación aggregate recibe un array de operaciones, esto se debe a que el framework de agregación funciona como un pipe de Unix, es decir, se admite la construcción de transformaciones y agrupaciones en distintas etapas que se ejecutarán de forma serializada.
</p>

<p dir="ltr">
  <strong>Estas son las etapas admitidas</strong>:
</p>

<div dir="ltr">
  <table>
    <colgroup> <col width="103" /> <col width="380" /> <col width="118" /></colgroup> <tr>
      <td>
        <p dir="ltr">
          <strong>Etapa</strong>
        </p>
      </td>
      
      <td>
        <p dir="ltr">
          <strong>Descripción</strong>
        </p>
      </td>
      
      <td>
        <p dir="ltr">
          <strong>Multiplicidad</strong>
        </p>
      </td>
    </tr>
    
    <tr>
      <td>
        <p dir="ltr">
          <a href="#project"><strong><em>$project</em></strong></a>
        </p>
      </td>
      
      <td>
        <p dir="ltr">
          Cambia la forma del documento
        </p>
      </td>
      
      <td>
        <p dir="ltr">
          1:1
        </p>
      </td>
    </tr>
    
    <tr>
      <td>
        <p dir="ltr">
          <a href="#match"><strong><em>$match</em></strong></a>
        </p>
      </td>
      
      <td>
        <p dir="ltr">
          Filtra los resultados
        </p>
      </td>
      
      <td>
        <p dir="ltr">
          n:1
        </p>
      </td>
    </tr>
    
    <tr>
      <td>
        <p dir="ltr">
          <a href="#group"><strong><em>$group</em></strong></a>
        </p>
      </td>
      
      <td>
        <p dir="ltr">
          agregación (agrupación)
        </p>
      </td>
      
      <td>
        <p dir="ltr">
          n:1
        </p>
      </td>
    </tr>
    
    <tr>
      <td>
        <p dir="ltr">
          <a href="#sort"><strong><em>$sort</em></strong></a>
        </p>
      </td>
      
      <td>
        <p dir="ltr">
          ordenación
        </p>
      </td>
      
      <td>
        <p dir="ltr">
          1:1
        </p>
      </td>
    </tr>
    
    <tr>
      <td>
        <p dir="ltr">
          <a href="#skip"><strong><em>$skip</em></strong></a>
        </p>
      </td>
      
      <td>
        <p dir="ltr">
          salta N elementos
        </p>
      </td>
      
      <td>
        <p dir="ltr">
          n:1
        </p>
      </td>
    </tr>
    
    <tr>
      <td>
        <p dir="ltr">
          <a href="#skip"><strong><em>$limit</em></strong></a>
        </p>
      </td>
      
      <td>
        <p dir="ltr">
          elige N elementos
        </p>
      </td>
      
      <td>
        <p dir="ltr">
          n:1
        </p>
      </td>
    </tr>
    
    <tr>
      <td>
        <p dir="ltr">
          <a href="#unwind"><strong><em>$unwind</em></strong></a>
        </p>
      </td>
      
      <td>
        <p dir="ltr">
          normaliza arrays
        </p>
      </td>
      
      <td>
        <p dir="ltr">
          1:n
        </p>
      </td>
    </tr>
    
    <tr>
      <td>
        <p dir="ltr">
          <strong><em>$out</em></strong>
        </p>
      </td>
      
      <td>
        <p dir="ltr">
          cambia nombres, etc
        </p>
      </td>
      
      <td>
        <p dir="ltr">
          1:1
        </p>
      </td>
    </tr>
  </table>
</div>

<p dir="ltr">
  La multiplicidad se refiere a cuántos documentos obtenemos como resultado despues de aplicar la etapa a N documentos. Por ejemplo, en una ordenación, como tan solo cambia el orden de N documentos, el resultado siguen siendo N documentos pero ordenados de otro modo.
</p>

<p dir="ltr">
  Vamos a conocer los detalles de cada una de estas etapas.
</p>

<h4 dir="ltr" id="project">
  Proyección ($project 1:1)
</h4>

<p dir="ltr">
  La proyección permite modificar la representación de los datos, por lo que en general se emplea para darles una nueva forma con la que nos resulte más cómodo trabajar. Por ejemplo, permite elimiar claves, añadir nuevas claves, cambiar su forma, o usar algunas funciones simples de transformación como <strong>$toUpper</strong> (convertir en mayusculas), <strong>$toLower</strong> (convertir en minúsculas), <strong>$add</strong> (sumar) y <strong>$multiply</strong> (multiplicar).
</p>

<p dir="ltr">
  Se utiliza tambien en las primeras etapas para filtrar atributos y liberar memoria evitando procesar aquella información que no se va a necesitar.
</p>

```js
db.zips.aggregate([{  
$project: {  
_id: 0,  
'ciudad': {  
$toLower: "$city"  
},  
'poblacion': {  
'unidades': "$pop",  
'miles': {  
"$multiply": ["$pop", 0.001]  
}  
}  
}  
}])
```


<p dir="ltr">
  R<span style="line-height: 1.666666667;">esultado:</span>
</p>

```js
{  
"ciudad": "cushman",  
"poblacion": {  
"unidades": 36963,  
"miles": 36.963  
}  
}  
{  
"ciudad": "barre",  
"poblacion": {  
"unidades": 4546,  
"miles": 4.546  
}  
}  
...
```


<p dir="ltr">
  Nótese que poner un 0 en un valor de campo (como en el caso del _id), hace que el atributo se ignore en el resultado. Si se pone un 1, se utilizará exactamente el mismo valor que tiene en el documento original.
</p>

<h4 dir="ltr" id="match">
  Filtrado ($match n:1)
</h4>

<p dir="ltr">
  La etapa match permite filtrar los documentos para que en el resultado de la etapa solo esten aquellos que cumplen ciertos criterios. Permite filtrar antes o despues de agregar los resultados, en función del orden en que definamos esta etapa.
</p>

<p dir="ltr">
  Por ejemplo, devolver unicamente los codigos postales del estado de California (CA)
</p>

```js
db.zips.aggregate([{  
$match: {  
state: "CA"  
}  
}])
```


<h4 dir="ltr" id="group">
  Agrupación ($group n:1)
</h4>

<p dir="ltr">
  La agrupación permite agrupar distintos documentos según compartan el valor de uno o varios de sus atributos, y realizar operaciones de agregación sobre los elementos de cada uno de los grupos. Esta etapa es rica en operadores, vamos a ver algunos ejemplos.
</p>

<h5 dir="ltr">
  <strong>Operador $sum</strong>
</h5>

<p dir="ltr">
  Suma el valor de los atributos de los documentos del mismo grupo. Por ejemplo, para obtener, la población total de cada estado, y el número de ciudades que hay en el estado, para cada uno de los estados, basta por agrupar por estado y aplicar la clausula sum convenientemente.
</p>

```js
db.zips.aggregate([{  
$group: {  
_id: "$state",  
state_pop: {  
$sum: "$pop"  
},  
state_cities: {  
$sum: 1  
}  
}  
}])
```


<p dir="ltr">
  Resultado:
</p>

```js
{  
"_id": "WA",  
"state_pop": 4866692,  
"state_cities": 484  
}  
{  
"_id": "HI",  
"state_pop": 1108229,  
"state_cities": 80  
}  
…
```


<h5 dir="ltr">
  <strong>Operador $avg</strong>
</h5>

<p dir="ltr">
  Obtiene la media de los valores de un atributo en el grupo. Por ejemplo, para calcular la población media de las ciudades de un estado.
</p>

```js
db.zips.aggregate([{  
$group: {  
_id: "$state",  
avg_pop: {  
$avg: "$pop"  
}  
}  
}])
```


<p dir="ltr">
  Resultado:
</p>

```js
{  
"_id": "WA",  
"avg_pop": 10055.148760330578  
} {  
"_id": "HI",  
"avg_pop": 13852.8625  
} {  
"_id": "CA",  
"avg_pop": 19627.236147757256  
}…
```


<h5 dir="ltr">
  <strong>Operador $addToSet</strong>
</h5>

<p dir="ltr">
  Es un operador un tanto raro, y no tiene equivalencia en SQL. Su objetivo crear un array con todos los valores distintos que toma un atributo en un grupo de documentos. Por ejemplo, la siguiente operación daria como resultado un documento que tendría un atributo categorías con todas las ciudades de cada estado.
</p>

```js
db.zips.aggregate([{  
$group: {  
_id: "$state",  
cities: {  
$addToSet: "$city"  
}  
}  
}])
```


<h5 dir="ltr">
  <strong>Operador $push</strong>
</h5>

<p dir="ltr">
  Funciona <strong>igual que addToSet</strong> pero no garantiza que el elemento no vaya aparecer más de una vez si es que hay dos documentos con el mismo valor en el grupo.
</p>

<h5 dir="ltr">
  <strong>Operadores $max y $min</strong>
</h5>

<p dir="ltr">
  Permiten obtener el valor máximo o el mínimo del grupo de documentos para alguno de sus atributos. Por ejemplo, para obtener la mayor población para cada estado.
</p>

```js
db.zips.aggregate([{  
$group: {  
_id: "$state",  
max_pop: {  
$max: "$pop"  
}  
}  
}])
```


<h5 dir="ltr">
  Agrupación compuesta
</h5>

<p dir="ltr">
  Nos permite agrupar por más de un atributo, esto es, como si agrupamos por subcategorías. Por ejemplo, para obtener la población de cada una de las ciudades de un estado.
</p>

```js
db.zips.aggregate([{  
$group: {  
_id: {  
"state": "$state",  
"city": "$city"  
},  
state_pop: {  
$sum: "$pop"  
},  
state_cities: {  
$sum: 1  
}  
}  
}])
```


<p dir="ltr">
  Resultado:
</p>

```js
{  
"_id": {  
"state": "AK",  
"city": "CRAIG"  
},  
"state_pop": 1398,  
"state_cities": 1  
} {  
"_id": {  
"state": "AK",  
"city": "KETCHIKAN"  
},  
"state_pop": 14308,  
"state_cities": 2  
}…
```


<h5 dir="ltr">
  Agrupación múltiple
</h5>

<p dir="ltr">
  Es algo muy llamativo para los que vienen de SQL, ya que no existe y a veces es muy bienvenido. La agrupación múltiple nos permite ejecutar la fase de agregación más de una vez, es decir, la salida de la primera agregación es la entrada de la siguiente.
</p>

<p dir="ltr">
  Por ejemplo, si quisieramos, podríamos obtener la media de la población de cada ciudad, y luego obtener la media de las media para cada estado.
</p>

```js
db.zips.aggregate([{  
"$group": {  
_id: {  
state: "$state",  
city: "$city"  
},  
"average": {  
"$avg": "$pop"  
}  
}  
}, {  
"$group": {  
_id: "$_id.state",  
"average": {  
"$avg": "$average"  
}  
}  
}])
```


<p dir="ltr">
  Resultado:
</p>

```js
{  
"_id": "RI",  
"average": 12835.16868131868  
} {  
"_id": "FL",  
"average": 13097.2471511727  
}
```


<h4 dir="ltr" id="sort">
  Ordenación ($sort 1:1)
</h4>

<p dir="ltr">
  Ordena los documentos resultantes. Intentará ordenar en memoria (con un límite de 100mb por etapa del pipeline) y en caso de no tener memoria suficiente, realizará una ordenación en disco.
</p>

<p dir="ltr">
  En el ejemplo se muestra como podríamos obtener los documentos ordenados por población (en orden descendente) y estado (ascendente)
</p>

```js
db.zips.aggregate([{  
$sort: {  
pop: -1,  
state: 1  
}  
}])
```


<h4 dir="ltr" id="skip">
  Paginación ($skip y $limit)
</h4>

<p dir="ltr">
  En éste caso, a diferencia de cuando se utilizan en clausulas find, sí importará el orden en el que aparece la etapa en el pipeline. El resultado es que podremos ignorar los primero N elementos (skip) y obtener solo los N primero (limit).
</p>

<p dir="ltr">
  Por ejemplo, podemos ignorar los 10 primeros y devolver los 5 siguientes (es decir, obtendríamos los elementos del 11 al 15.
</p>

```js
db.zips.aggregate([{  
$skip: 10  
}, {  
$limit: 5  
}])
```


<h4 dir="ltr">
  Primero y último ($first y $last)
</h4>

<p dir="ltr">
  Nos permiten obtener el primer y el último elemento de un grupo. Para que esta operación tenga sentido debe combinarse con la ordenación… (de otro modo sería impredecible).
</p>

<p dir="ltr">
  Por ejemplo, a continuación usamos el pipeline para obtener la ciudad con mayor población de cada estado:
</p>

<p dir="ltr">
  1 - Obtenemos la población de cada ciudad de cada estado
</p>

<p dir="ltr">
  2 - Ordenamos por estado (ascendente) y población (descendente, la ciudad con mayor población quedará arriba en cada bloque de estados)
</p>

<p dir="ltr">
  3 - Agrupamos por estado, obteniendo el primero de cada grupo.
</p>

```js
db.zips.aggregate([  
//obtiene la poblacion de cada ciudad de cada estado  
{  
$group: {  
_id: {  
state: "$state",  
city: "$city"  
},  
population: {  
$sum: "$pop"  
}  
}  
},  
//ordenamos por estado, poblacion  
{  
$sort: {  
"_id.state": 1,  
"population": -1  
}  
},  
//agrupamos por estado, obtenemos el primero de cada grupo  
{  
$group: {  
_id: "$_id.state",  
city: {  
$first: "$_id.city"  
},  
population: {  
$first: "$population"  
}  
}  
}  
])
```


<p dir="ltr">
  Resultado:
</p>

```js
{  
"_id": "WV",  
"city": "HUNTINGTON",  
"population": 75343  
} {  
"_id": "WA",  
"city": "SEATTLE",  
"population": 520096  
}…
```


<h4 dir="ltr" id="unwind">
  Operación Unwind ($unwind)
</h4>

<p dir="ltr">
  Permite agrupar sobre elementos de un array, para “aplanarlo”. Por ejemplo, para el siguiente documento:
</p>

> <p dir="ltr">
>   { a:1, b:2, c:[pera, manzana]}
> </p>

<p dir="ltr">
  Después de aplicar unwind quedarían los siguientes documentos resultantes:
</p>

> <p dir="ltr">
>   {a:1, b:2, c:pera}<br /> {a:1, b:2, c:manzana}
> </p>

<p dir="ltr">
  Dado que esta etapa es la única cuya salida puede tener un mayor número de documentos que la entrada proporcionada, hay que usarla con cuidado para no sobrecargar la memoria, e intentar aplicar todos los filtros posibles antes a nuestros documentos para reducir los resultados potenciales. La sintaxis es la siguiente:
</p>

> <p dir="ltr">
>   {“$unwind”;:”;$c”;}
> </p>

<p dir="ltr">
  Después es posible recuperar el array empleando $push como se ha visto más arriba. También es posible hacer un doble $unwind (en pipelines consecutivos) para aplanar datos que tengan dos arrays, por ejemplo (haciendo el producto cartesiano de todos ellos)
</p>

<h3 dir="ltr">
  Limitaciones de Aggregation framework
</h3>

<p dir="ltr">
  Existen algunas características que hay que tener en consideración.
</p>

<li dir="ltr">
    <strong>Hay un límite de 100mb de tamaño de pipeline para cada etapa</strong> durante el procesado, a menos que establezcamos el atributo allowDiskUse en la ejecución, lo que lo hará más lento.
</li>

<li dir="ltr">
    <strong>Hay 16mb de límite de tamaño para la salida de cada pipeline</strong>, ya que se maneja como un único documento. Se puede evitar obteniendo un cursor y recorriendo el cursor elemento a elemento.
</li>

<li dir="ltr">
    <strong>Sharding</strong>: cuando se usa group o sort, todos los datos se reunen en un solo shard (el pricipal) antes de continuar procesando los pipes. Para eso se puede importar el mongo en Hadoop (con Hadoop connector), ya que con mapreduce tiene mejor escalabilidad.
</li>

<h3 dir="ltr">
  Otros ejemplos
</h3>

<p dir="ltr">
  Para comprender mejor el Aggregation Framework lo mejor es realizar algunos ejercicios, o analizar ejemplos para intentar averiguar como hacen lo que hacen. A continuación planteo algunos escenarios.
</p>

<h4 dir="ltr">
  Ejemplo 1
</h4>

<p dir="ltr">
  Obtener la población total de las ciudades cuyo nombre empieza por A o B
</p>

```js
db.zips.aggregate([{  
$project: {  
_id: 1,  
first_char: {  
$substr: ["$city", 0, 1]  
},  
pop: 1  
}  
}, {  
$match: {  
"first_char": {  
$in: ["A", "B"]  
}  
}  
}, {  
$group: {  
_id: 0,  
total: {  
$sum: "$pop"  
}  
}  
}])
```


<h4 dir="ltr">
  Ejemplo 2
</h4>

<p dir="ltr">
  Obtenemos la media de población de entre todas las ciudades de Nueva York y California cuya población supera los 25000 ciudadanos.
</p>

```js
db.zips.aggregate([{  
$match: {  
"state": {  
$in: ["CA", "NY"]  
}  
}  
}, {  
$group: {  
_id: {  
"state": "$state",  
"city": "$city"  
},  
sum_pop: {  
$sum: "$pop"  
}  
}  
}, {  
$match: {  
"sum_pop": {  
$gt: 25000  
}  
}  
}, {  
$group: {  
_id: null,  
avg: {  
$avg: "$sum_pop"  
}  
}  
}, ])
```


<h4 dir="ltr">
  Ejemplo 3
</h4>

<p dir="ltr">
  Obtenemos los usuarios que más y menos comentarios han hecho de entre todos los posts de un blog. Ojo al uso de la clausula unwind para crear un documento por cada comentario del cada post (que inicialmente estarían en un array dentro de el post correspondiente).
</p>

```js
db.posts.aggregate([{  
$unwind: "$comments"  
}, {  
$group: {  
_id: "$comments.author",  
count: {  
$sum: 1  
}  
}  
}, {  
$sort: {  
"count": 1  
}  
}, {  
$group: {  
_id: null,  
name_mas: {  
$last: "$_id"  
},  
number_mas: {  
$last: "$count"  
},  
name_menos: {  
$first: "$_id"  
},  
number_menos: {  
$first: "$count"  
}  
}  
}])
```


<h2 dir="ltr">
  Enlaces a la guía completa
</h2>

  * Guía 1: [Introducción a MongoDB, características clave](http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-1/ "Características básicas e introducción a Mongodb")
  * Guía 2: [Arrancar MongoDB e importar datos](http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-2/ "Iniciar el motor de MongoDB e insertar documentos")  ‎
  * Guía 3: [Comandos y operaciones esenciales](http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-3 "Operaciones fundamentales")
  * Guía 4: [Comandos y operaciones avanzadas](http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-4 "Comandos y operaciones avanzadas")
  * Guía 5: [Esquema de datos dirigido por la aplicación](http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-5 "Application Driven Schema")
  * Guía 6: [Crear, manejar y entender los índices](http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-6 "índices en mongodb")
  * Guía 7: Análisis de datos con el framework de agregación
  * Guía 8: [Replicación y Sharding](http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-8 "Replication and sharding")