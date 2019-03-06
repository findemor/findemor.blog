---
id: 365
title: 'Aprender a usar MongoDB: Guía 5'
date: 2015-06-14T12:40:20+00:00
author: findemor
layout: post
guid: http://blog.findemor.es/?p=365
permalink: /2015/06/aprender-a-usar-mongodb-guia-5/
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
  En publicaciones anteriores hemos aprendido a utilizar <a href=" http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-3">multitud de comandos y operadores</a> para consultar y <a href="http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-4">manipular nuestros datos</a> en MongoDB… pero aún no hemos visto una de las cosas que más diferencias tienen en una base de datos noSQL frente a un entorno tradicional SQL o relacional: <strong>el hecho de que el esquema de datos está determinado por la aplicación</strong> (es decir, por el uso que se va a hacer de los mismos) y no por su propio esquema o modelo de dominio. Este será el tema que abordemos en este post.
</p>

<h2 dir="ltr">
  Anteriormente
</h2>

Guía 4: [Comandos y operaciones avanzadas](http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-4 "Comandos y operaciones avanzadas")

<h2 dir="ltr">
  Esquema dirigido por la aplicación (Application-driven Schema)<!--more-->
</h2>

<h3 dir="ltr">
  Tercera forma normal (SQL) vs Application-driven Schema
</h3>

<p dir="ltr">
  En MongoDB, como en otras bases de datos noSQL es más importante cómo se utilizan los datos, qué datos son consultados como un conjunto, y cómo son más utilizados en operaciones de lectura o de escritura. Esto supone una gran diferencia con una base de datos relacional, donde la información trata de conservarse de una forma agnóstica a la aplicación, eliminando redundancias y obviando cómo se utilizará la información para favorecer un correcto diseño del esquema.
</p>

<p dir="ltr">
  En las aplicaciones modernas, donde se requiere una gran escalabilidad y velocidad para recuperar datos, ese requisito nos obliga (cuando trabajamos con SQL) a realizar verdaderas virguerias para que las consultas e inserciones funcionen con fluidez, y a veces entramos en esa vaga zona donde no está claro si la “desnormalización” del modelo es adecuada o si supone un peligro para la futura consistencia de los datos.
</p>

<p dir="ltr">
  En las bases de datos noSQL, al emplearse los datos conforme a cómo serán utilizados en la aplicación y distanciarse de la Tercera forma normal de SQL, se asume que será responsabilidad de la aplicación (y del desarrollador) mantener la consistencia e integridad de los datos, pero a cambio se rompe finalmente con las barreras establecidas por esa estricta normalización y permite trabajar con modelo dinámicos, carentes de esquema, fácilmente integrables con los distintos elementos de la arquitectura de un software concreto, y sobre todo drásticamente más escalable y veloz.
</p>

<h3 dir="ltr">
  Características un Esquema dirigido por la aplicación en MongoDB
</h3>

<li dir="ltr">
  <p dir="ltr">
    <strong>Documentos enriquecidos</strong>: en mongo, los documentos no se fragmentan para almacenar sus datos en distintas tablas con diferentes relaciones de cardinalidad entre sí, sino que un documento es un conjunto atómico de datos, que contiene toda la información relevante según la aplicación que necesite procesarlo.
  </p>
</li>

<li dir="ltr">
  <p dir="ltr">
    <strong>Pre-join o datos embebidos</strong>: En mongo, un documento puede incluir otros documentos embebidos en su interior… sería el equivalente a las relaciones entre tablas de SQL. En el documento esto se traduce en que uno de sus atributos es un array de otros documentos.
  </p>
</li>

<li dir="ltr">
  <p dir="ltr">
    <strong>No hay joins</strong>: Los JOINS o cruces de datos en SQL escalan realmente mal, y una de las cosas que se busca al decidirse a utilizar una base de datos noSQL es un alto rendimiento. Debido a esto no existen joins en mongo… en su lugar, el diseñador de la base de datos y el desarrollador de la aplicación trabajan conjuntamente para que los datos se almacenen y se manipulen exactamente como la aplicación los necesita, sin tener que transformarlos o realizar complicadas proyecciones.
  </p>
</li>

<li dir="ltr">
  <p dir="ltr">
    <strong>No hay constraints</strong>: Es decir, no hay ForeignKeys, Checks constraints, etc. La solución a esta carencia es emplear datos embebidos para establecer cualquier tipo de relación o catálogo.
  </p>
</li>

<li dir="ltr">
  <p dir="ltr">
    <strong>Operaciones atómicas</strong>: En mongo, como veremos más adelante, no hay transacciones, pero en su lugar, todas las operaciones de manipulacion de datos son atómicas a nivel de documento, por lo que la alteración que hagamos a un documento nunca será parcial o estará disponible hasta haberse completado.
  </p>
</li>

<li dir="ltr">
  <p dir="ltr">
    <strong>Sin declaración de esquema</strong>: una de las mayores virtudes de mongo es que no necesita que el esquema sea declarado o definido de ningún modo antes de poder empezar a usarlo. Simplemente añade un documento a una colección inexistente y ésta se creará. Añade un documento totalmente distinto a la misma colección y se almacenará en ella y podrá ser consultado en conjunto o por separado en base a sus atributos. Esto permite que si la aplicación cambia, la integración sea trivial y prácticamente podamos olvidarnos de pesados scripts de transformación de base de datos.
  </p>
</li>

<p dir="ltr">
  <strong>En la tercera forma normal, el mundo relacional de SQL nos marca 3 objetivos:</strong>
</p>

<p dir="ltr">
  1 &#8211; Liberar a la base de datos de anomalías de modificación de los datos: por ejemplo, datos duplicados.
</p>

<p dir="ltr">
  2 &#8211; Minimizar el rediseño al extender la base de datos: si la base de datos debe crecer añadiendo nuevas entidades, no deberían verse afectadas las tablas anteriores… sin embargo, en el mundo real esto rara vez se consigue y las nuevas necesidades de la aplicación obligan a construir consultas muy complejas y poco eficientes o bien a rediseñar el modelo.
</p>

<p dir="ltr">
  3 &#8211; Evitar cualquier tendencia de patrón de uso de la aplicación: la estructura de los datos es independiente del uso que se haga de ellos. Pero ojo, que hacer esto tiene ventajas e inconvenientes… tantos como hacer exactamente lo contrario.
</p>

<p dir="ltr">
  En MongoDB, <strong>vamos a respetar los puntos 1 y 2</strong>, intentando evitar en la medida de lo posible el empleo de documentos embebidos (que dificultan la consistencia de la información), pero en el caso del punto 3, vamos a hacer exactamente lo contrario, donde aunque se viola el principio no se pone en peligro la consistencia ni la integridad de los datos.
</p>

<p dir="ltr">
  <strong>Veamos un ejemplo con un Post</strong>, que puede recibir comentarios y que esta categorizado con una serie de tags.
</p>

[js]{  
"_id": 1,  
"author": "findemor",  
"title": "este es el titulo",  
"body": "esto es el contenido de un post",  
"comments": [{  
"body": "esto es un comentario",  
"email": "john@doe.com",  
"author": "john"  
}, {  
"body": "otro comentario",  
"email": "jane@doe.com",  
"author": "jane"  
}],  
"date": ISODate("2015-12-05T03:23:33.841Z"),  
"permalink": "este\_es\_el_titulo",  
"tags": ["tecnologia", "programacion"]  
}[/js]

<h3 dir="ltr">
  Vivir en un mundo sin constraints.
</h3>

<p dir="ltr">
  Una regla que nos facilita iniciarnos en el mundo noSQL después de mucho bagaje en SQL es: “Si algo tiene la forma que tendría en una base de datos relacional, probablemente no lo estás enfocando bien”.
</p>

<p dir="ltr">
  [Tweet &#8220;En MongoDB, si algo tiene la forma que tendría en SQL, probablemente no lo has enfocado bien.&#8221;]
</p>

<p dir="ltr">
  En noSQL, la estructura de los datos forma parte del diseño de la aplicación, por lo que si el diseño no es correcto, obligarán a manipular grandes volumenes de datos en la memoria durante la ejecución de la aplicación.
</p>

<p dir="ltr">
  Veamos un esquema de datos alternativo al anterior para almacenar posts y comentarios, en este caso más parecido a SQL (relacional)
</p>

<div dir="ltr">
  <table>
    <colgroup> <col width="*" /> <col width="*" /> <col width="*" /></colgroup> <tr>
      <td>
        <p dir="ltr">
          <strong>Posts</strong>
        </p>
      </td>
      
      <td>
        <p dir="ltr">
          <strong>Comments</strong>
        </p>
      </td>
      
      <td>
        <p dir="ltr">
          <strong>Tags</strong>
        </p>
      </td>
    </tr>
    
    <tr>
      <td>
        {<br /> _id: 1,<br /> title: “&#8211; &#8211; ”,<br /> body: “&#8211; &#8211; ”,<br /> author: “&#8211; &#8211;”,<br /> date: “&#8211; &#8211; ”<br /> }
      </td>
      
      <td>
        {<br /> _id: 3,<br /> post_id: 1,<br /> author: “&#8211; &#8211; ”,<br /> author_email: “&#8211; &#8211; ”,<br /> order: 0<br /> }
      </td>
      
      <td>
        {<br /> _id: 1,<br /> tag: “&#8211; &#8211; ”,<br /> posts: [1]<br /> }
      </td>
    </tr>
  </table>
</div>

<p dir="ltr">
  Como podréis imaginar, listar un post en un esquema de este tipo, nos obliga a obtener el post, despues recorrer los comentarios y consultar cada uno de ellos de la base de datos… y lo mismo con los tags. Eso son muchas operaciones de entrada-salida, se rompe la atomicidad de la operación y es muy ineficiente en procesamiento… Podría ser deseable si vamos a realizar un gran número de actualizaciones de los datos (por ejemplo si los usuarios van a editar con mucha frecuencia sus comentarios… cosa que evidentemente no va a ocurrir en este caso). Si estamos dispuestos a pagar este coste a cambio de asegurar la consistencia de los datos, sería la alternativa válida.
</p>

<h3 dir="ltr">
  Viviendo sin transacciones
</h3>

<p dir="ltr">
  Como hemos dicho antes, mongo no soporta transacciones (que se emplean en SQL para ofrecer atomicidad, consistencia, aislamiento y durabilidad). Sin embargo, tenemos operaciones atómicas, es decir: al trabajar en un único documento, la operación se completará antes de que alguien pueda ver el cambio.
</p>

<p dir="ltr">
  <strong>Hay tres aproximaciones</strong> distintas.
</p>

<p dir="ltr">
  1 &#8211; <strong>Reestructurar el código</strong> para utilizar un único documento en las operaciones que deban ser atómicas, beneficiandote de su atomicidad para simular la transacción.
</p>

<p dir="ltr">
  2 &#8211;<strong> Implementar por sofware una especie de bloqueo</strong> (semáforo, find and modify…).
</p>

<p dir="ltr">
  3 &#8211; <strong>Tolerancia</strong>: hay aplicaciones que se pueden permitir cierta cantidad de inconsistencia, como actualizar el muro de facebook, etc. donde la inconsistencia de los datos no es crítica si no es grave.
</p>

<p dir="ltr">
  En realidad,<strong> la segunda aproximación es la más habitual en el mundo real</strong>, ¡incluso al trabajar con entornos SQL! Imaginaos una transferencia de dinero entre dos cuentas bancarias de distintas entidades. Evidentemente la operación no se completa en su totalidad en un único sistema relacional, ya que ambos bancos tendrás bases de datos distintas, sino que coordinan la operación con mecanismos de este tipo.
</p>

<h3 dir="ltr">
  Relaciones en MongoDB
</h3>

<p dir="ltr">
  No hay relaciones en MongoDB, pero hay distintas aproximaciones para los distintos tipos de cardinalidad que podamos encontrar. Vamos a verlos uno por uno.
</p>

<h4 dir="ltr">
  Relaciones 1 a 1
</h4>

<p dir="ltr">
  Este tipo de relación se da, por ejemplo, entre un empleado y su curriculum (o entre un paciente y su historia médica, un edificio y su plano…).
</p>

<p dir="ltr">
  Podemos vincular el identificador del curriculum en el empleado, o al revés. O podemos embeber un documento dentro del otro y tener toda la información en un único documento.
</p>

<p dir="ltr">
  Hay que tener en consideración como va a ser leido (por ejemplo ¿siempre va a desearse leer el curriculum?) o cómo va a crecer (si va a crecer todo el tiempo, podría superar el límite de tamaño máximo para un documento, que son 16mb… en cuyo caso habría que fragmentarlo), y finalmente hay que considerar la atomicidad… si no podemos permitir ningún tipo de inconsistencia, hay que unificar el documento.
</p>

<h4 dir="ltr">
  Relaciones 1 a N (entendiendo que N son Muchos)
</h4>

<p dir="ltr">
  Este tipo de relación se daría, por ejemplo, entre una Ciudad y sus Ciudadanos.
</p>

<p dir="ltr">
  Si metiesemos a las personas en el documento inicial, evidentemente el documento podría superar el límite de tamaño permitido (16mb).
</p>

<p dir="ltr">
  Si metemos la ciudad en cada uno de los documentos de persona, vamos a tener una gran posibilidad de que los datos sean inconsistentes si tratamos de actualizar la información de la ciudad.
</p>

<p dir="ltr">
  Una forma válida sería enlazar los N ciudadanos hacia el 1 (ciudad), es decir, dos colecciones con el id de ciudad en un atributo del documento persona.
</p>

<h4 dir="ltr">
  Relaciones 1 a N (entendiendo que N son Pocos)
</h4>

<p dir="ltr">
  Por el contrario al caso anterior, en ocasiones N son previsiblemente pocos. Por ejemplo, no hay mcuhos comentarios en un mismo Post (podría ser del orden de 1 a 10…), y como cada comentario está unicamente vinculado a un único Post y rara vez va a ser editado, parece una solución perfecta embeber los N en el 1.
</p>

<h4 dir="ltr">
  Relaciones N a N (Pocos a Pocos o Muchos a Muchos)
</h4>

<p dir="ltr">
  Este tipo de relación, que se daría entre Libros y Autores, o entre Estudiantes y Profesores… es importante analizarla en cada caso concreto, porque en realidad (en estos ejemplos por ejemplo) podrían entenderse como relaciones Pocos a Pocos en gran parte de los casos del mundo real.
</p>

<p dir="ltr">
  Una opción sería, por ejemplo, embeber los identificadores de cada uno de ellos en un array de identificadores… pero no es lo ideal, ya que suele crear una vulnerabilidad de inconsistencia a la hora de crear los datos en ambas direcciones, ya que no se mantienen juntos (aunque desde el punto de vista del rendimiento, podría ser interesante).
</p>

<div dir="ltr">
  <table>
    <colgroup> <col width="*" /> <col width="*" /></colgroup> <tr>
      <td>
        <p dir="ltr">
          <strong>Libros</strong>
        </p>
      </td>
      
      <td>
        <p dir="ltr">
          <strong>Autores</strong>
        </p>
      </td>
    </tr>
    
    <tr>
      <td>
        {<br /> _id: 12,<br /> titulo: “El señor de los anillos”,<br /> authors: [27]<br /> }
      </td>
      
      <td>
        {<br /> _id: 27,<br /> author_name: “J.R.R.Tolkien”,<br /> books: [12, 7, 13]<br /> }
      </td>
    </tr>
  </table>
</div>

<p dir="ltr">
  Otra opción es, en lugar de un array de identificadores, embeber el Libro dentro del Autor, pero esto nos hará duplicar libros y será vulnerable en caso de actualización.
</p>

<p dir="ltr">
  Finalmente, la alternativa en este caso concreto, sería tener ambos objetos como objetos de primera clase de un único documento general.
</p>

<h3 dir="ltr">
  Multikeys
</h3>

<p dir="ltr">
  MongoDB es extremadamente eficiente cuando realiza búsquedas de documentos por su identificador… pero cuando el número de documentos en una colección crece mucho, buscar por el valor de otros atributos suele ser penalizador. Sin embargo, MongoDB nos permite crear multiples índices en un mismo documento (ensureIndex) para realizar búsquedas eficientes por esos otros atributos, como veremos en futuros posts.
</p>

<h3 dir="ltr">
  Árboles en MongoDB
</h3>

<p dir="ltr">
  Por ejemplo, entre productos y categorias.
</p>

<div dir="ltr">
  <table>
    <colgroup> <col width="*" /> <col width="*" /></colgroup> <tr>
      <td>
        <p dir="ltr">
          <strong>Producto</strong>
        </p>
      </td>
      
      <td>
        <p dir="ltr">
          <strong>Categoría</strong>
        </p>
      </td>
    </tr>
    
    <tr>
      <td>
        {<br /> _id: 2,<br /> category: 7,<br /> product_name: “Nexus 5”<br /> }
      </td>
      
      <td>
        {<br /> _id: 7,<br /> category_name: “smartphone”,<br /> parent_id: 6<br /> }
      </td>
    </tr>
  </table>
</div>

<p dir="ltr">
  En el ejemplo, ponemos el identificador de la categoría padre, lo cual sería suficiente para diseñar un árbol y es conceptualmente sencillo. Sin embargo, para obtener la jerarquía tendríamos que recorrer todos los elementos.
</p>

<p dir="ltr">
  Si queremos facilitar la obtención de todos los hijos o ancestros, otra aproximación es poner un array como todos los ancestros.
</p>

<div dir="ltr">
  <table>
    <colgroup> <col width="*" /> <col width="*" /></colgroup> <tr>
      <td>
        <p dir="ltr">
          <strong>Producto</strong>
        </p>
      </td>
      
      <td>
        <p dir="ltr">
          <strong>Categoría</strong>
        </p>
      </td>
    </tr>
    
    <tr>
      <td>
        {<br /> _id: 2,<br /> category: 7,<br /> product_name: “Nexus 5”<br /> }
      </td>
      
      <td>
        {<br /> _id: 7,<br /> category_name: “smartphone”,<br /> parent_id: 6,<br /> ancestors: [3, 6, 8]<br /> }
      </td>
    </tr>
  </table>
</div>

<p dir="ltr">
  Lo que nos permitiría obtener todos los descendientes de la categoría smartphone con una sola operación:
</p>

> <p dir="ltr">
>   > db.categorias.find({ ancestors : 7 })
> </p>

<h4 dir="ltr">
  Beneficios de embeber datos
</h4>

<p dir="ltr">
  La principal ventaja es el rendimiento… los datos se acceden en el disco de una forma mucho más eficiente, se reducen el lecturas, etc. En el caso de la escritura ocurre lo mismo, que es muchísimo más eficiente, y tan solo en el caso de que el documento crezca habrá que reubicarlo.
</p>

<h4 dir="ltr">
  Cuándo desnormalizar los datos
</h4>

<p dir="ltr">
  Es normal pensar que si utilizamos documentos embebidos de mongo estaremos desnormalizando los datos y generando problemas por no respetar la tercera forma normal. Pero en realidad, mientras no dupliquemos los datos, no crearíamos vulnerabilidades.
</p>

  * En 1 a 1 es perfectamente viable embeber los datos de forma segura porque no se duplican los datos.
  * En relaciones 1 a muchos, funciona bien si embebes los muchos en los unos.
  * En relaciones muchos a muchos, hay que vincularlos a través de un array de identificadores en los documentos. Por algunas razones podrías querer embeber documentos, pero duplicarías mucho contenido.

<h3 dir="ltr">
  Conclusión
</h3>

<p dir="ltr">
  Al principio puede haber una cantidad respetable de miedo a los problemas de consistencia que puede generarse con este tipo de base de datos, pero una vez superemos esa barrera, y aprendamos a diseñar nuestros datos para que encajen perfectamente en nuestra aplicación, el miedo desaparecerá para dejar paso al tremendo rendimiento y flexibilidad de MongoDB.
</p>

<p dir="ltr">
  En futuras publicaciones veremos cómo definir y trabajar con índices, analizar consultas, manejar el framework de agregación para analizar los datos y construir réplicas de MongoDB en un entorno distribuido.
</p>

<h2 dir="ltr">
  Enlaces a la guía completa
</h2>

  * Guía 1: [Introducción a MongoDB, características clave](http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-1/ "Características básicas e introducción a Mongodb")
  * Guía 2: [Arrancar MongoDB e importar datos](http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-2/ "Iniciar el motor de MongoDB e insertar documentos")  ‎
  * Guía 3: [Comandos y operaciones esenciales](http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-3 "Operaciones fundamentales")
  * Guía 4: [Comandos y operaciones avanzadas](http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-4 "Comandos y operaciones avanzadas")
  * Guía 5: Esquema de datos dirigido por la aplicación
  * Guía 6: [Crear, manejar y entender los índices](http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-6 "índices en mongodb")
  * Guía 7: [Análisis de datos con el framework de agregación](http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-7 "Aggregation Framework")
  * Guía 8: [Replicación y Sharding](http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-8 "Replication and sharding")