---
id: 371
title: 'Aprender a usar MongoDB: Guía 8'
date: 2015-06-14T12:40:48+00:00
author: findemor
layout: post
guid: http://blog.findemor.es/?p=371
permalink: /2015/06/aprender-a-usar-mongodb-guia-8/
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
  Hasta la fecha hemos cubierto prácticamente todos los temas principales que pueden interesarnos para comenzar a trabajar con MongoDB, salvo uno: <strong>el sharding y replicación</strong>.
</p>

<h2 dir="ltr">
  Anteriormente
</h2>

Guía 7: [Análisis de datos con el framework de agregación](http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-7 "Aggregation Framework")

<h2 dir="ltr">
  Sharding y Replicación en MongoDB<!--more-->
</h2>

<h3 dir="ltr">
  Replicación
</h3>

<p dir="ltr">
  La replicación en MongoDB permite conseguir una <strong>altísima disponibilidad y tolerancia a fallos</strong>, mediante la creación de múltiples copias de <strong>mongods</strong>.
</p>

<p dir="ltr">
  <strong>Al conjunto de mongods se lo denomina “replica set”</strong>, donde uno de los mongods será primario y el resto secundarios. La elección del mongo primario es dinámica, y cambiará en función de la disponibilidad del primario actual (es transparente para los clientes conectados), aunque existen algunas peculiaridades y diferencias entre los procesos de escritura y lectura.
</p>

<p dir="ltr">
  Para que este cambio dinámico pueda llevarse a cabo, debe haber al menos 3 mongos, ya que cuando el primario se cae por cualquier motivo, el conjunto de réplicas restante debe ser mayoría respecto al total de mongods que hubiese inicialmente, a fin de que puedan decidir cual será el nuevo mongo primario.
</p>

<h4 dir="ltr">
  Elecciones de nodo primario
</h4>

<p dir="ltr">
  Puede haber distintos tipos de nodo:
</p>

<li dir="ltr">
  <p dir="ltr">
    <strong>Regular</strong>: Es el caso descrito arriba, y será un mongo que podrá actuar como primario o secundario.
  </p>
</li>

<li dir="ltr">
  <p dir="ltr">
    <strong>Árbitro</strong>: un nodo de tipo árbitro sólo sirve para participar en la votación (por ejemplo, resultaría útil si queremos desplegar un número par de réplicas, y necesitamos un nodo adicional para deshacer los empates en las decisiones).
  </p>
</li>

<li dir="ltr">
  <p dir="ltr">
    <strong>Delayed</strong>: solo puede actuar como nodo secundario (es una mezcla entre árbitro y regular).
  </p>
</li>

<li dir="ltr">
  <p dir="ltr">
    <strong>Hidden</strong>: no puede ser primario, y suele emplearse únicamente para almacenar analíticas.
  </p>
</li>

<h4 dir="ltr">
  Consistencia de escritura
</h4>

<p dir="ltr">
  Las escrituras siempre se realizan contra el nodo primario, a diferencia de las lecturas, que pueden ir tanto contra el primario como contra los secundarios. Sin embargo, si siempre usamos el primario será más consistente, ya que los datos estarán disponibles desde el momento en que se solicita su escritura, y no leeremos datos obsoletos durante el tiempo que tarde la propagación.
</p>

<p dir="ltr">
  MongoDB, frente a otros sistemas, no da consistencia eventual (salvo que leas de los nodos secundarios), sino continua (si se utiliza el nodo primario).
</p>

<h4 dir="ltr">
  Crear replicaset
</h4>

<p dir="ltr">
  A continuación se muestra un breve ejemplo de cómo crear un replicaset de 3 servidores, arrancando tres instancias de mongod, y después vinculandolos entre sí.
</p>

<p dir="ltr">
  Lanzar las instancias (en este caso todas están en la misma máquina)
</p>

> <p dir="ltr">
>   mkdir -p /data/rs1 /data/rs2 /data/rs3
> </p>
> 
> <p dir="ltr">
>   mongod &#8211;replSet rs1 &#8211;logpath &#8220;1.log&#8221; &#8211;dbpath /data/rs1 &#8211;port 27017 &#8211;fork<br /> mongod &#8211;replSet rs1 &#8211;logpath &#8220;3.log&#8221; &#8211;dbpath /data/rs2 &#8211;port 27018 &#8211;fork<br /> mongod &#8211;replSet rs1 &#8211;logpath &#8220;3.log&#8221; &#8211;dbpath /data/rs3 &#8211;port 27019 &#8211;fork
> </p>

<p dir="ltr">
  Ahora los tres servidores estan arrancados cada uno en su puerto, pero son independientes y hay que vincularlos para que se mantengan en contacto entre ellos. Para esto, ejecutamos en el mongo shell:
</p>

> <p dir="ltr">
>   > config = { _id: &#8220;rs1&#8221;, members: [<br /> { _id : 0, host: &#8220;127.0.0.1:27017&#8221;, priority: 0, slaveDelay: 5 },<br /> { _id : 1, host: &#8220;127.0.0.1:27018&#8221; },<br /> { _id : 2, host: &#8220;127.0.0.1:27019&#8221;}<br /> ]}
> </p>
> 
> <p dir="ltr">
>   > rs.initiate(config)<br /> > rs.status()
> </p>

<p dir="ltr">
  Hay que prestar atención al nombre rs1 que le dimos al atributo replSet (será el nombre del conjunto de réplicas).
</p>

<p dir="ltr">
  Además, podríamos haber utilizado los atributos SlaveDelay (que hace que arranque un tiempo después de lanzar el comando) o priority a 0, para evitar que un nodo sea primario.
</p>

<p dir="ltr">
  Ahora que los mongods están arrancados y configurados, <strong>podemos conectarnos a cualquiera de ellos</strong> (salvo al puerto 27017 ya que hemos forzado que no sea primario)
</p>

> <p dir="ltr">
>   > mongo &#8211;port 27018<br /> > rs.status()
> </p>

<p dir="ltr">
  Si quisiéramos <strong>permitir leer de los esclavos</strong> (no se puede por defecto) hay que activarlo:
</p>

> <p dir="ltr">
>   > db.slaveOk()
> </p>

<p dir="ltr">
  Podemos <strong>obtener información</strong> acerca del replicaset con varios comandos, por ejemplo:
</p>

> <p dir="ltr">
>   > rs.status()<br /> > rs.isMaster() //será true para el primario
> </p>

<p dir="ltr">
  O podemos <strong>apagar el servidor</strong>
</p>

> <p dir="ltr">
>   > db.shutdownServer()
> </p>

<h4 dir="ltr">
  Implicaciones de la replicación
</h4>

<li dir="ltr">
  <p dir="ltr">
    Los nodos son transparentes para los clientes conectados
  </p>
</li>

<li dir="ltr">
  <p dir="ltr">
    El driver que estemos usando, necesita conocer al menos a un servidor
  </p>
</li>

<li dir="ltr">
  <p dir="ltr">
    <strong>Write concern</strong>: puede pasar un tiempo hasta que la escritura esté disponible, y cuando se produce un fallo, puede que no se admita la escritura durante un breve tiempo.
  </p>
</li>

<li dir="ltr">
  <p dir="ltr">
    Hay preferencias de lectura que puedes cambiar
  </p>
</li>

<li dir="ltr">
  <p dir="ltr">
    Los errores pueden seguir ocurriendo, y el entorno distribuido esta preparado para resolver gran parte de ellos
  </p>
</li>

<h4 dir="ltr">
  Funcionamiento interno de la replicación
</h4>

<p dir="ltr">
  En el nodo primario existe una colección (de tipo capped) llamada oplog.rs. Esta colección contiene todas las operaciones que se realizan en el nodo primario, y se emplea para propagar las mismas operaciones al resto de nodos cada cierto tiempo (casi de inmediato).
</p>

<p dir="ltr">
  Si un servidor se cae antes de que todo su oplog se haya propagado, esas operaciones quedarán pendientes. Cuando más tarde el servidor retorne, se conectará al conjunto de replicas como un nodo secundario (ya que el conjunto habrá escogido a un nuevo nodo primario) y en ese momento detectará su oplog lleno y lo moverá al fichero rollback (donde podremos consultar todas esas operaciones que no llegaron a propagarse), pero no aplicará las operaciones al replicaset, por lo que esos datos podrían no ser consistentes.
</p>

<p dir="ltr">
  Es posible establecer los parametros de operación w=1 y j=1 para configurar el Write Concern, y que de este modo no devuelvan el Ok de la operación de escritura hasta que no se haya propagado tambien a alguno de los secundarios (y de este modo asegurar la consistencia de ciertas operaciones críticas).
</p>

<h4 dir="ltr">
  MongoDB driver
</h4>

<h5 dir="ltr">
  Conectarse usando un driver
</h5>

<p dir="ltr">
  Para conectarnos a un replicaset usando un driver, como podria ser el mongodb driver para nodejs, es posible establecer uno o varios de las instancias con la llamada siguiente:
</p>

> <p dir="ltr">
>   MongoClient.connect(&#8220;mongodb://localhost:30001,&#8221; +<br /> &#8220;localhost:30002,&#8221;+<br /> &#8220;localhost:30003/course&#8221;, function(err, db) {<br /> &#8230;<br /> })
> </p>

<p dir="ltr">
  Si no se especifica alguno de los nodos del conjunto no pasa nada, ya que <strong>será autodescubierto</strong> si al menos hemos especificado un nodo que sí esta disponible.
</p>

<p dir="ltr">
  Cuando se produzca un fallo, el driver de mongo-nodejs acumula un buffer de solicitudes y lo ejecuta en cuanto detecta que la conexión es válida y hay un primario de nuevo disponible.
</p>

<h5 dir="ltr">
  Write Concern en mongo driver
</h5>

<p dir="ltr">
  Es posible especificar ciertos atributos de las operaciones para determinar el grado de consistencia que esperamos durante la escritura, según los siguientes parámetros:
</p>

  * **w:1** -> espera a que la operación haya sido registrada en el principal y retorna una respuesta
  * **w:0** -> entrega la petición y devuelve un ok inmediato pero sin respuesta
  * **w:2** -> espera a que el primario y al menos un secundario conozcan el dato
  * **w:3** -> para dos secundarios y el primario… etc
  * **w:j** -> escribe en el journal, lo que permite estar seguro de que los datos serán persistentes
  * **w:majority** -> espera a que esté en la mayoría de nodos

<p dir="ltr">
  Para usarlo, hay que añadir lo siguiente en el connection string:
</p>

> <p dir="ltr">
>   &#8220;localhost:30003/course?w=1&#8221;
> </p>

<p dir="ltr">
  O bien podemos utilizar en una operación individual concreta mediante el objeto options:
</p>

> <p dir="ltr">
>   &#8220;insert({x:2},{w:3})&#8221;
> </p>

<h5 dir="ltr">
  Preferencias de lectura en Mongo Driver
</h5>

<p dir="ltr">
  Puedes establecer las preferencias de lectura como primario, secundario o nearest, bien sea al establecer la conexión o en una operación concreta. Veamos ambos casos:
</p>

> <p dir="ltr">
>   &#8220;localhost:30003/course?readPreference=secondary&#8221;
> </p>
> 
> <p dir="ltr">
>   { &#8216;readPreference&#8217; : ReadPreference.PRIMARY }
> </p>

<h3 dir="ltr">
  Sharding
</h3>

<p dir="ltr">
  El sharding, <strong>a diferencia de la replicación, tiene como objetivo mejorar la eficiencia y el rendimiento de las operaciones de consulta</strong> mediante el particionado de datos y su distribución troceada en distintos nodos denominados Shards.
</p>

<p dir="ltr">
  Cada Shard puede ser una única instancia de mongo o un replicaset indistintamente (lo que mejoraria la disponibilidad de cada uno de los shards). Los conjuntos de shards se ubican detrás de un <strong>mongos</strong>, que es el router que mantiene el pool de conexiones con cada shard.
</p>

<p dir="ltr">
  Cada Shard guarda ciertos trozos de datos de las colecciones, que se denominan “chunks”. Estos trozos se guardan en base a rangos de valores de ciertos atributos, con concreto de aquellos que forman la ShardKey o clave de Shard.
</p>

<p dir="ltr">
  La ShardKey es un atributo de nuestros documentos que, si presenta una segregación de valores adecuada (elevada), permite al Shard repartir los documentos que comparten su valor en un único shard, de forma que cuando pedimos al router un documento y especificamos su ShardKey, sabe exactamente en qué shard tiene que buscar ese documento. Si por el contrario no hubiésemos especificado una shardKey, el router tendría que lanzar la búsqueda en broadcast a todos los shards existentes, para que todos ellos intenten localizar los resultados coincidentes. Debido a esta razón, cuando se trabaja con sharding es extremadamente importante establecer una clave de shard en nuestras colecciones y especificarla siempre, tanto en las consultas como en las inserciones (para que el servidor pueda ubicar correctamente el nuevo documento).
</p>

<p dir="ltr">
  Además, otro datos interesante es que los mongos carecen de estado (stateless), así que podría haber más de uno y se comportarán como un conjunto de réplicas.
</p>

<h4 dir="ltr">
  Como desplegar un Sharding
</h4>

<p dir="ltr">
  Para desplegar un entorno de sharding, hay que lanzarlo <strong>del mismo modo que si fuera un conjunto de réplicas, añadiendo la clave &#8212;<em>shardsvr</em></strong> para indicarle que además de una réplica, va a ser un shard.
</p>

<p dir="ltr">
  Además, necesitaremos unos config-servers, que se lanzan igual pero su clave en este caso será<strong><em> &#8211;configsvr</em></strong>
</p>

<p dir="ltr">
  Luego ya podríamos <strong>arrancar el mongos</strong>:
</p>

> <p dir="ltr">
>   //ej. init_sharded_env.ae39ee9f8161.sh<br /> mongos &#8211;logpath &#8220;mongos-1.log&#8221; &#8211;configdb<br /> localhost:57040,localhost:57041,localhost:57042 &#8211;fork
> </p>

<p dir="ltr">
  Finalmente, hay que decirle a los servidores de config que<strong> conozcan al conjunto de shards</strong>.
</p>

> <p dir="ltr">
>   > db.adminCommand({ addshard: &#8230; })
> </p>

<p dir="ltr">
  Ya podemos <strong>consultar el estado de nuestros shards</strong>:
</p>

> <p dir="ltr">
>   > sh.status()
> </p>

<h4 dir="ltr">
  Implicaciones del sharding
</h4>

<li dir="ltr">
  <p dir="ltr">
    <strong>Todos los documentos deben incluir la shardKey</strong>
  </p>
</li>

<li dir="ltr">
  <p dir="ltr">
    La ShardKey no puede cambiar nunca
  </p>
</li>

<li dir="ltr">
  <p dir="ltr">
    Necesitas establecer un índice que comience con la ShardKey
  </p>
</li>

<li dir="ltr">
  <p dir="ltr">
    No puede tener ningún índice único (o clave única) que sea parte de la shardKey
  </p>
</li>

<p dir="ltr">
  Por ejemplo, si la clave de shard es “student_id, class”, entonces puedes tener un índice único sobre “student_id, class, otro_atributo”. Esto se debe a que los índices de cada shard están completos en cada shard, no se accede a ellos de forma conjunta.
</p>

<h4 dir="ltr">
  Para elegir una clave de shard
</h4>

<li dir="ltr">
  <p dir="ltr">
    Tienes que asegurarte de que hay <strong>suficiente cardinalidad</strong>, es decir, suficiente cantidad de valores distintos para que los chunks se generen correctamente.
  </p>
</li>

<li dir="ltr">
  <p dir="ltr">
    Puede ser monotamente creciente, pero si no tiene suficiente cardinalidad, al final todos los nuevos elementos irán al mismo shard. Esto puede causar problemas, por ejemplo si la clave de shard fuese una fecha, al final todos los elementos mayores al momento en que los rangos de clave se asignan a cada shard, serán enviados al último.
  </p>
</li>

<p dir="ltr">
  <strong>Un buen ejemplo</strong> es considerar qué accesos pueden ser paralelos. Por ejemplo, en un servidor de fotos, cada usuario podría ser un shardKey para paralelizar su acceso, ya que cada usuario accederá a sus propias fotos (empleará un único shard) y el resto accederán en paralelo al resto de shards, por lo que se evita colapsar un shard innecesariamente.
</p>

<h4 dir="ltr">
  Ejemplo de creación de Sharding y replica-set completo
</h4>

<p dir="ltr">
  A continuación se muestra un fragmento de la documentación de Mongo University donde se describen los pasos para generar un conjunto de replicas y sharding:
</p>

[js collapse=&#8221;true&#8221;]#  
Andrew Erlichson# 10 gen# script to start a sharded environment on localhost# clean everything up  
echo "killing mongod and mongos"  
killall mongod  
killall monogs  
echo "removing data files"  
rm &#8211; rf / data / config  
rm &#8211; rf / data / shard * #start a replica set and tell it that it will be a shord0  
mkdir &#8211; p / data / shard0 / rs0 / data / shard0 / rs1 / data / shard0 / rs2  
mongod&#8211;replSet s0&#8211;logpath "s0-r0.log"&#8211;dbpath / data / shard0 / rs0&#8211;port 37017&#8211;fork&#8211;shardsvr&#8211;smallfiles  
mongod&#8211;replSet s0&#8211;logpath "s0-r1.log"&#8211;dbpath / data / shard0 / rs1&#8211;port 37018&#8211;fork&#8211;shardsvr&#8211;smallfiles  
mongod&#8211;replSet s0&#8211;logpath "s0-r2.log"&#8211;dbpath / data / shard0 / rs2&#8211;port 37019&#8211;fork&#8211;shardsvr&#8211;smallfiles  
sleep 5# connect to one server and initiate the set  
mongo&#8211;port 37017 << &#8216;EOF&#8217;  
config = {  
_id: "s0",  
members: [{  
_id: 0,  
host: "localhost:37017"  
}, {  
_id: 1,  
host: "localhost:37018"  
}, {  
_id: 2,  
host: "localhost:37019"  
}]  
};  
rs.initiate(config)  
EOF# start a replicate set and tell it that it will be a shard1  
mkdir &#8211; p / data / shard1 / rs0 / data / shard1 / rs1 / data / shard1 / rs2  
mongod&#8211;replSet s1&#8211;logpath "s1-r0.log"&#8211;dbpath / data / shard1 / rs0&#8211;port 47017&#8211;fork&#8211;shardsvr&#8211;smallfiles  
mongod&#8211;replSet s1&#8211;logpath "s1-r1.log"&#8211;dbpath / data / shard1 / rs1&#8211;port 47018&#8211;fork&#8211;shardsvr&#8211;smallfiles  
mongod&#8211;replSet s1&#8211;logpath "s1-r2.log"&#8211;dbpath / data / shard1 / rs2&#8211;port 47019&#8211;fork&#8211;shardsvr&#8211;smallfiles  
sleep 5  
mongo&#8211;port 47017 << &#8216;EOF&#8217;  
config = {  
_id: "s1",  
members: [{  
_id: 0,  
host: "localhost:47017"  
}, {  
_id: 1,  
host: "localhost:47018"  
}, {  
_id: 2,  
host: "localhost:47019"  
}]  
};  
rs.initiate(config)  
EOF# start a replicate set and tell it that it will be a shard2  
mkdir &#8211; p / data / shard2 / rs0 / data / shard2 / rs1 / data / shard2 / rs2  
mongod&#8211;replSet s2&#8211;logpath "s2-r0.log"&#8211;dbpath / data / shard2 / rs0&#8211;port 57017&#8211;fork&#8211;shardsvr&#8211;smallfiles  
mongod&#8211;replSet s2&#8211;logpath "s2-r1.log"&#8211;dbpath / data / shard2 / rs1&#8211;port 57018&#8211;fork&#8211;shardsvr&#8211;smallfiles  
mongod&#8211;replSet s2&#8211;logpath "s2-r2.log"&#8211;dbpath / data / shard2 / rs2&#8211;port 57019&#8211;fork&#8211;shardsvr&#8211;smallfiles  
sleep 5  
mongo&#8211;port 57017 << &#8216;EOF&#8217;  
config = {  
_id: "s2",  
members: [{  
_id: 0,  
host: "localhost:57017"  
}, {  
_id: 1,  
host: "localhost:57018"  
}, {  
_id: 2,  
host: "localhost:57019"  
}]  
};  
rs.initiate(config)  
EOF# now start 3 config servers  
mkdir &#8211; p / data / config / config &#8211; a / data / config / config &#8211; b / data / config / config &#8211; c  
mongod&#8211;logpath "cfg-a.log"&#8211;dbpath / data / config / config &#8211; a&#8211;port 57040&#8211;fork&#8211;configsvr&#8211;smallfiles  
mongod&#8211;logpath "cfg-b.log"&#8211;dbpath / data / config / config &#8211; b&#8211;port 57041&#8211;fork&#8211;configsvr&#8211;smallfiles  
mongod&#8211;logpath "cfg-c.log"&#8211;dbpath / data / config / config &#8211; c&#8211;port 57042&#8211;fork&#8211;configsvr&#8211;smallfiles# now start the mongos on a standard port  
mongos&#8211;logpath "mongos-1.log"&#8211;configdb localhost: 57040, localhost: 57041, localhost: 57042&#8211;fork  
echo "Waiting 60 seconds for the replica sets to fully come online"  
sleep 60  
echo "Connnecting to mongos and enabling sharding"#  
add shards and enable sharding on the test db  
mongo << &#8216;EOF&#8217;  
db.adminCommand({  
addShard: "s0/" + "localhost:37017"  
});  
db.adminCommand({  
addShard: "s1/" + "localhost:47017"  
});  
db.adminCommand({  
addShard: "s2/" + "localhost:57017"  
});  
db.adminCommand({  
enableSharding: "test"  
})  
db.adminCommand({  
shardCollection: "test.grades",  
key: {  
student_id: 1  
}  
});  
EOF[/js]

<h2 dir="ltr">
  Conclusión
</h2>

A lo largo de toda esta guía, hemos visto todos los aspectos fundamentales de MongoDB de una forma bastante práctica. Evidentemente, si queréis utilizar MongoDB en un entorno de producción, tendréis que formaros más profundamente y consultar la documentación para conocer los pormenores de cada características de las que hemos visto, pero espero que lo aquí expuesto sea suficiente para despertar vuestro interés en esta tecnología, que además de resultar extremadamente útil en algunos casos, es muy divertida y rápida de utilizar.

¡Un saludo!

<h2 dir="ltr">
  Enlaces a la guía completa
</h2>

  * Guía 1: [Introducción a MongoDB, características clave](http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-1/ "Características básicas e introducción a Mongodb")
  * Guía 2: [Arrancar MongoDB e importar datos](http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-2/ "Iniciar el motor de MongoDB e insertar documentos")  ‎
  * Guía 3: [Comandos y operaciones esenciales](http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-3 "Operaciones fundamentales")
  * Guía 4: [Comandos y operaciones avanzadas](http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-4 "Comandos y operaciones avanzadas")
  * Guía 5: [Esquema de datos dirigido por la aplicación](http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-5 "Application Driven Schema")
  * Guía 6: [Crear, manejar y entender los índices](http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-6 "índices en mongodb")
  * Guía 7: [Análisis de datos con el framework de agregación](http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-7 "Aggregation Framework")
  * Guía 8: Replicación y Sharding