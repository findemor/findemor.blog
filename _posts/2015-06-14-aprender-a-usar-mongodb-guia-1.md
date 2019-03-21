---
id: 355
title: 'Aprender a usar MongoDB: Guía 1'
date: 2015-06-14T12:38:53+00:00
author: findemor
layout: post
guid: http://blog.findemor.es/?p=355
permalink: /2015/06/aprender-a-usar-mongodb-guia-1/
image: /wp-content/uploads/2015/06/mongodb.png
categories:
  - Desarrollo
  - Cursos
tags:
  - MongoDB
---
<p dir="ltr">
  Hace algún tiempo completé uno de los fantásticos curso de <a href="https://university.mongodb.com/">mongo university</a> y me encantó. A veces, cuando utilizo mongo, me toca rebuscar entre los apuntes para recordar algún dato de los que habitualmente pueden caer en el olvido… y para evitarlo: a modo de nota personal, y para compartirlo con vosotros, he pensado en escribir un breve resumen del aprendizaje obtenido en dicho curso en una serie de varios posts. Espero que os guste, y no dudéis en hacer preguntas ¡o en comentar vuestras experiencias si también hicisteis el curso!
</p>

<h2 dir="ltr">
  MongoDB, introducción, propósito y conceptos clave.
</h2>

<!--more-->

<h3 dir="ltr">
  Instalación
</h3>

<p dir="ltr">
  Mongo puede instalarse en prácticamente cualquier plataforma, y el modo de lograrlo depende un poco de cada una y está bien documentada en <a href="http://docs.mongodb.org/manual/installation/">su página web</a>, por lo que no voy a decir gran cosa al respecto, salvo compartir que en mi experiencia, me resultó mucho más sencillo de instalar y utilizar en Mac OSX que en windows, aunque a día de hoy lo utilizo por igual en ambos entornos.
</p>

<h3 dir="ltr">
  Qué es MongoDB
</h3>

<p dir="ltr">
  MongoDB es una base de datos no relacional, o NO-SQL, de código abierto que proporciona un altísimo rendimiento, alta disponibilidad y escalado automático.
</p>

<p dir="ltr">
  En el núcleo de MongoDB están los documentos. Todo en mongo gira en torno a ellos, incluso la forma de configurar y obtener información del propio motor de base de datos será empleando documentos.
</p>

<h3 dir="ltr">
  Qué son los documentos
</h3>

<p dir="ltr">
  Podemos entender un documento como el equivalente a una fila en una base de datos basada en SQL… una estructura de datos compuesta por atributos (pares de clave-valor). Sin embargo, un documento puede ser mucho más rico que una única tabla de una base de datos SQL, puesto que de hecho puede almacenar relaciones y datos complejos como en SQL conseguiríamos a través de una colección de tablas y relaciones.
</p>

<p dir="ltr">
  Los documentos de MongoDB son similares a los ya tan habituales objetos JSON, pero de mayor complejidad puesto que cuentan con tipos adicionales para sus valores. A este formato se le llama <a href="http://es.wikipedia.org/wiki/BSON">BSON </a>puesto que se trata de una representación binaria de datos serializados.
</p>

<h4 dir="ltr">
  Ventajas de los documentos
</h4>

<p dir="ltr">
  Los documentos son objetos que corresponden directamente con tipos de datos nativos en una gran mayoria de lenguajes de programación, por lo que dichos objetos empleados en la programación pueden ser directamente almacenados o recuperados en la base de datos.
</p>

<p dir="ltr">
  Los documentos permiten incluir en sí mismos otros documentos (embedded documents) o arrays de documentos, lo que reduce drásticamente la necesidad de “joins” que habitualmente encarecen el tratamiento de datos en SQL.
</p>

<p dir="ltr">
  Finalmente, y no poco importante, los documentos en MongoDB admiten la definición dinámica de esquemas, por lo que cambiar la estructura de un objeto a almacenar en base de datos no requiere reconstruir ni alterar ningún modelo de datos.
</p>

<h3 dir="ltr">
  Características clave
</h3>

<li dir="ltr">
    Alto rendimiento del mecanismo de persistencia.

</li>

<li dir="ltr">
    Soporta un modelo de datos embebido que reduce drásticamente la actividad de accesos al disco o a memoria.

</li>

<li dir="ltr">
    Soporta índices para acelerar las consultas en gran medida.

</li>

<li dir="ltr">
    Gran disponibilidad, basada en la replicación, que proporciona seguridad ante fallos y redundancia de datos.

</li>

<li dir="ltr">
    Escalado automático que proporciona escalabilidad horizontal como parte de la funcionalidad nativa, (conocido como sharding, que distribuye los datos a través de un cluster de máquinas).

</li>

<h2 dir="ltr">
  Operaciones
</h2>

<p dir="ltr">
  MongoDB almacena datos en forma de documentos similares a JSON, denominados BSON. Un valor de un campo de un documento BSON puede ser cualquier tipo de los tipos admitidos por BSON, incluso podría ser otro documento o un array de documentos.
</p>

<p dir="ltr">
  MongoDB almacena todos los documentos en colecciones. Una colección es un grupo de documentos relacionados de algún modo relevante para la aplicación, y que comparten un índice común.
</p>

<p dir="ltr">
  Las colecciones, por lo tanto, podrían considerarse el homólogo a las tablas SQL pero con una gran diferencia: pueden almacenarse documentos con distintas estructuras en la misma colección, sin necesidad de especificar previamente la estructura que tendrán.
</p>

<p dir="ltr">
  Las operaciones admitidas en MongoDB son de lo más diversas y flexibles, en cualquiera de las acciones que pudiéramos desear realizar sobre los datos (consulta, actualización, borrado…). E incluso empleando ciertos frameworks podremos realizar agregaciones, transformaciones y secuencias de operaciones basadas en pipelines.
</p>

<h2 dir="ltr">
  Enlaces a la guía completa
</h2>

  * Guía 1: Introducción a MongoDB, características clave
  * Guía 2: [Arrancar MongoDB e importar datos](http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-2/ "Iniciar el motor de MongoDB e insertar documentos")  ‎
  * Guía 3: [Comandos y operaciones esenciales](http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-3 "Operaciones fundamentales")
  * Guía 4: [Comandos y operaciones avanzadas](http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-4 "Comandos y operaciones avanzadas")
  * Guía 5: [Esquema de datos dirigido por la aplicación](http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-5 "Application Driven Schema")
  * Guía 6: [Crear, manejar y entender los índices](http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-6 "índices en mongodb")
  * Guía 7: [Análisis de datos con el framework de agregación](http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-7 "Aggregation Framework")
  * Guía 8: [Replicación y Sharding](http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-8 "Replication and sharding")