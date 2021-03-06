---
id: 361
title: 'Aprender a usar MongoDB: Guía 3'
date: 2015-06-14T12:39:47+00:00
author: findemor
layout: post
guid: http://blog.findemor.es/?p=361
permalink: /2015/06/aprender-a-usar-mongodb-guia-3/
image: /wp-content/uploads/2015/06/mongodb.png
categories:
  - Desarrollo
  - Cursos
tags:
  - MongoDB
---
<p dir="ltr">
  En posts anteriores <a href="http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-1">hemos visto una introducción a mongodb</a> y cómo se pone en marcha, e incluso como se puede <a href="http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-2">hacer una importación de datos</a>.
</p>

<p dir="ltr">
  En esta nueva publicación vamos a empezar a trastear con la base de datos, introduciendo algunos datos y jugando con ellos para aprender algunos comandos esenciales. Es un post largo, ya que es un listado de operaciones, ¡pero espero que sea útil!
</p>

<h2 dir="ltr">
  Anteriormente
</h2>

Guía 2: [Arrancar MongoDB e importar datos](http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-2/ "Iniciar el motor de MongoDB e insertar documentos")  ‎

<h2 dir="ltr">
  Comandos esenciales en MongoDB<!--more-->
</h2>

<h3 dir="ltr">
  Insertar documentos
</h3>

<p dir="ltr">
  Vamos a insertar algunas puntuaciones de prueba para trabajar con ellas.
</p>

> <p dir="ltr">
>   > db.scores.insert({ _id: 1, username : “findemor”;, score : 50, finished : true })<br /> > db.scores.insert({ _id: 2, username : “eden4ever”;, score : 80, finished : false })<br /> > db.scores.insert({ _id: 3, username : “franbuipa”;, score : 30 })
> </p>

<p dir="ltr">
  Hay que prestar atención a que estamos incluyendo el atributo _id, por lo que no se generará uno automáticamente, sino que utilizará el que le especifiquemos. Además, uno de los documentos no incluye el atributo finished, lo que nos demuestra que la estructura de los documentos no tiene porque ser idéntica… de hecho, podrían no parecerse en absoluto.
</p>

<h4 dir="ltr">
  Crear una colección explicitamente
</h4>

<p dir="ltr">
  Las colecciones se crean automaticamente si no existían al insertar documentos. Sin embargo, es posible crearlas de forma explícita para determinar sus propiedades. Un buen ejemplo es la posibilidad de crear colecciones limitadas (<a href="http://docs.mongodb.org/manual/core/capped-collections/">capped collections</a>) muy empleadas, por ejemplo, para registrar logs, ya que podemos determinar un máximo de elementos y luego irá borrando los más antiguos en las nuevas inserciones.
</p>

> <p dir="ltr">
>   > db.createCollection(“log”, { capped: true, max: 1000 })
> </p>

### Consulta

<p dir="ltr">
  <strong>Listar todos los documentos de la colección.</strong> Si quisiéramos que la salida se muestre estructurada para facilitar la lectura (en caso de que los documentos sean muy largos) podemos usar el método pretty sobre la operación.
</p>

> <p dir="ltr">
>   > db.scores.find()
> </p>

```js
{ "_id" : 1, "username" : "findemor", "score" : 50, "finished" : true }  
{ "_id" : 2, "username" : "eden4ever", "score" : 80, "finished" : false }  
{ "_id" : 3, "username" : "franbuipa", "score" : 30 }  
```


<p dir="ltr">
  <strong>Listar alguno de los documentos</strong> de la colección (al azar). Suele utilizarse para hacerse una idea de la estructura de los documentos que contiene.
</p>

> <p dir="ltr">
>   > db.scores.findOne()
> </p>

```js
{ "_id" : 1, "username" : "findemor", "score" : 50, "finished" : true }
```


<p dir="ltr">
  <strong>Listar los elementos por valor de sus atributos</strong>: basta con especificar el valor de aquellos parámetros que queramos filtrar.
</p>

> <p dir="ltr">
>   > db.scores.find({ username : “findemor”; })
> </p>

```js
{ "_id" : 1, "username" : "findemor", "score" : 50, "finished" : true }
```


<p dir="ltr">
  <strong>Listar por rango de valores</strong>: en este caso documentos donde el score sea mayor que 20 y menor o igual a 50.
</p>

> <p dir="ltr">
>   > db.scores.find({ score : { $gt : 20, $lte: 50 }})
> </p>

```js
{ "_id" : 1, "username" : "findemor", "score" : 50, "finished" : true }  
{ "_id" : 3, "username" : "franbuipa", "score" : 30 }
```


<p dir="ltr">
  <strong>Listar elementos que no incluyen (o que incluyen) un atributo</strong>. En este caso listamos el documento que no tiene el atributo finished.
</p>

> <p dir="ltr">
>   > db.scores.find({ finished : { $exists : false } })
> </p>

```js
{ "_id" : 3, "username" : "franbuipa", "score" : 30 }
```


<p dir="ltr">
  <strong>Listar elementos usando expresiones regulares</strong>: cuyo username contenga la cadena uip
</p>

> <p dir="ltr">
>   > db.scores.find({ username : { $regex : “uip”; }})
> </p>

```js
{ "_id" : 3, "username" : "franbuipa", "score" : 30 }
```


<p dir="ltr">
  <strong>Encontrar elementos por tipo de dato de los atributos</strong>: en el ejemplo, obtenemos los documentos donde el atributo finished es booleano. El tipo del atributo se identifica según la <a href="http://docs.mongodb.org/manual/reference/operator/query/type/">especificación de BSON</a>.
</p>

> <p dir="ltr">
>   > db.scores.find({ finished : { $type : 8 }})
> </p>

```js
{ "_id" : 1, "username" : "findemor", "score" : 50, "finished" : true }  
{ "_id" : 2, "username" : "eden4ever", "score" : 80, "finished" : false }
```


<p dir="ltr">
  <strong>Hacer una agrupación OR de dos consultas</strong>, en este caso elementos con _id = 1 OR _id = 2
</p>

> <p dir="ltr">
>   > db.scores.find({ $or : [ { _id : 1 }, { _id : 2 } ] })
> </p>

```js
{ "_id" : 1, "username" : "findemor", "score" : 50, "finished" : true }  
{ "_id" : 2, "username" : "eden4ever", "score" : 80, "finished" : false }
```


<p dir="ltr">
  <strong>Contar el número de documentos de la colección</strong>.
</p>

> <p dir="ltr">
>   > db.scores.count()
> </p>

```js
3
```


<p dir="ltr">
  <strong>También podemos aplicar filtros</strong>.
</p>

> <p dir="ltr">
>   > db.scores.count({ finished : true })
> </p>

```js
1
```


<h3 dir="ltr">
  Trabajando con estructuras anidadas y arrays
</h3>

<p dir="ltr">
  Para poder tener datos con los que trabajar, <strong>vamos a insertar un par de documentos</strong> un poco más ricos en elementos.
</p>

```js
db.scores.insert({  
_id: 4,

username: "cocinero",

favorites: ["cocinar", "postres"],

address: {

country: "es",

city: "madrid"

}  
})

WriteResult({  
"nInserted": 1  
})

> db.scores.insert({

_id: 5,

username: "deportista",

favorites: ["running"],

address: {

country: "es",

city: "toledo"

}

})

WriteResult({  
"nInserted": 1  
})
```


<p dir="ltr">
  <strong>Consultar por coincidencias en un array</strong>: listamos los documentos cuyo array de favoritos contenga el elemento postres… evidentemente será el usuario cocinero.
</p>

> <p dir="ltr">
>   > db.scores.find({ favorites : “postres”; }).pretty()
> </p>

```js
{  
"_id": 4,  
"username": "cocinero",  
"favorites": [  
"cocinar",  
"postres"  
],  
"address": {  
"country": "es",  
"city": "madrid"  
}  
}
```


<p dir="ltr">
  Obtener los documentos que<strong> contengan en el array todos los elementos especificados</strong>.
</p>

> <p dir="ltr">
>   > db.scores.find({ favorites : { $all : [“postres”;, “cocinar”;] }}).pretty()
> </p>

```js
{  
"_id": 4,  
"username": "cocinero",  
"favorites": ["cocinar", "postres"],  
"address": {  
"country": "es",  
"city": "madrid"  
}  
}
```


<p dir="ltr">
  Listar los documentos que <strong>contengan en el array alguno de los elementos especificados</strong>.
</p>

> <p dir="ltr">
>   > db.scores.find({ favorites : { $in : [“cocinar”;] }}).pretty()
> </p>

```js
{  
"_id": 4,  
"username": "cocinero",  
"favorites": ["cocinar", "postres"],  
"address": {  
"country": "es",  
"city": "madrid"  
}  
}
```


<p dir="ltr">
  <strong>Listando los elementos por valor de alguno de los atributos del documento embebido</strong>. En este caso, documentos cuya ciudad es madrid.
</p>

> <p dir="ltr">
>   > db.scores.find({ “address.city”; : “madrid”; }).pretty()
> </p>

```js
{  
"_id": 4,  
"username": "cocinero",  
"favorites": ["cocinar", "postres"],  
"address": {  
"country": "es",  
"city": "madrid"  
}  
}
```


<h2 dir="ltr">
  Ejemplos utilizando el driver de MongoDB para nodejs
</h2>

<p dir="ltr">
  En el ejemplo a continuación se muestra como establecer una conexión a mongo en nodejs y como realizar algunas operaciones (consulta, conteo, borrado…). Ya que este post no trata de node, solo tiene caracter ilustrativo.
</p>

<p dir="ltr">
  Estos ejemplos han sido extraidos de la excelente formación de <a href="https://university.mongodb.com/">Mongo University</a> la cual os recomiendo completar si quereis iniciaros en MongoDB en alguna de las tecnologías disponibles.
</p>

<h3 dir="ltr">
  Ejemplo 1
</h3>

<p dir="ltr">
  Procesar las fotografías de caratula de album, cuyo album no existe en la base de datos.
</p>

```js
var client = require('mongodb')
    .MongoClient;
client.connect('mongodb://localhost:27017/h7', function(err, db) {
    if (err) throw err;
    var albums = db.collection('albums');
    db.collection('images', function(err, images) {
        if (err) throw err;
        images.find({}, {
            '_id': true
        }, function(err, cursor) {
            if (err) throw err;
            var count = cursor.count(function(err, count) {
                console.dir('num images: ' + count);
                // iterate over each image  
                cursor.each(function(err, item) {
                    if (item !== null) {
                        // attempt to find an album containing the photo, if not prune  
                        albums.findOne({
                            images: item._id
                        }, function(err, doc) {
                            if (err) throw err;
                            if (doc == null) {
                                images.remove({
                                    '_id': item._id
                                }, function(err, numRemoved) {
                                    if (err) throw err;
                                    count - ;
                                    console.dir('count: ' + count);
                                    if (count == 0) db.close();
                                });
                            } else {
                                count - ;
                                console.dir('count: ' + count);
                                if (count == 0) db.close();
                            }
                        });
                    }
                });
            });
        })
    });
});
```


<h3 dir="ltr">
  Ejemplo 2
</h3>

<p dir="ltr">
  Registro de usuario y validación usando node y mongo
</p>

```js
var bcrypt = require('bcrypt-nodejs');
/\* The UsersDAO must be constructed with a connected database object \*/

function UsersDAO(db) {
    "use strict";
    /* If this constructor is called without the "new" operator, "this" points

    \* to the global object. Log a warning and call it correctly. \*/
    if (false === (this instanceof UsersDAO)) {
        console.log('Warning: UsersDAO constructor called without "new" operator');
        return new UsersDAO(db);
    }
    var users = db.collection("users");
    this.addUser = function(username, password, email, callback) {
        "use strict";
        // Generate password hash  
        var salt = bcrypt.genSaltSync();
        var password_hash = bcrypt.hashSync(password, salt);
        // Create user document  
        var user = {
            '_id': username,
            'password': password_hash
        };
        // Add email if set  
        if (email != "") {
            user['email'] = email;
        }
        users.insert(user, function(err, result) {
            "use strict";
            if (!err) {
                console.log("Inserted new user");
                return callback(null, result[0]);
            }
            return callback(err, null);
        });
    }
    this.validateLogin = function(username, password, callback) {
        "use strict";
        // Callback to pass to MongoDB that validates a user document  
        function validateUserDoc(err, user) {
            "use strict";
            if (err) return callback(err, null);
            if (user) {
                if (bcrypt.compareSync(password, user.password)) {
                    callback(null, user);
                } else {
                    var invalid_password_error = new Error("Invalid password");
                    // Set an extra field so we can distinguish this from a db error  
                    invalid_password_error.invalid_password = true;
                    callback(invalid_password_error, null);
                }
            } else {
                var no_such_user_error = new Error("User: " + user + " does not exist");
                // Set an extra field so we can distinguish this from a db error  
                no_such_user_error.no_such_user = true;
                callback(no_such_user_error, null);
            }
        }
        users.findOne({
            '_id': username
        }, validateUserDoc);
    }
}
module.exports.UsersDAO = UsersDAO;
```


<h3 dir="ltr">
  Ejemplo 3
</h3>

<p dir="ltr">
  Operaciones de un blog
</p>

```js
/* The PostsDAO must be constructed with a connected database object */

function PostsDAO(db) {
    "use strict";
    /* If this constructor is called without the "new" operator, "this" points

    \* to the global object. Log a warning and call it correctly. \*/
    if (false === (this instanceof PostsDAO)) {
        console.log('Warning: PostsDAO constructor called without "new" operator');
        return new PostsDAO(db);
    }
    var posts = db.collection("posts");
    this.insertEntry = function(title, body, tags, author, callback) {
        "use strict";
        console.log("inserting blog entry" + title + body);
        // fix up the permalink to not include whitespace  
        var permalink = title.replace(/\s/g, '_');
        permalink = permalink.replace(/\W/g, ”);
        // Build a new post  
        var post = {
            "title": title,
            "author": author,
            "body": body,
            "permalink": permalink,
            "tags": tags,
            "comments": [],
            "date": new Date()
        }
        // now insert the post  
        posts.insert(post, function(err, result) {
            "use strict";
            if (err) return callback(err, null);
            console.log("Inserted new post");
            callback(err, permalink);
        });
    };
    this.getPosts = function(num, callback) {
        "use strict";
        posts.find()
            .sort('date', -1)
            .limit(num)
            .toArray(function(err, items) {
                "use strict";
                if (err) return callback(err, null);
                console.log("Found " + items.length + " posts");
                callback(err, items);
            });
    };
    this.getPostsByTag = function(tag, num, callback) {
        "use strict";
        posts.find({
                tags: tag
            })
            .sort('date', -1)
            .limit(num)
            .toArray(function(err, items) {
                "use strict";
                if (err) return callback(err, null);
                console.log("Found " + items.length + " posts");
                callback(err, items);
            });
    };
    this.getPostByPermalink = function(permalink, callback) {
        "use strict";
        posts.findOne({
            'permalink': permalink
        }, function(err, post) {
            "use strict";
            if (err) return callback(err, null);
            // XXX: Look here for final exam to see where we store "num_likes"  
            // fix up likes values. set to zero if data is not present  
            if (typeof post.comments === 'undefined') {
                post.comments = [];
            }
            // Each comment document in a post should have a "num_likes" entry, so we have to  
            // iterate all the comments in the post to make sure that is the case  
            for (var i = 0; i < post.comments.length; i++) {
                if (typeof post.comments[i].num_likes === 'undefined') {
                    post.comments[i].num_likes = 0;
                }
                post.comments[i].comment_ordinal = i;
            }
            callback(err, post);
        });
    };
    this.addComment = function(permalink, name, email, body, callback) {
        "use strict";
        var comment = {
            'author': name,
            'body': body
        }
        if (email != "") {
            comment['email'] = email
        }
        posts.update({
            'permalink': permalink
        }, {
            '$push': {
                'comments': comment
            }
        }, function(err, numModified) {
            "use strict";
            if (err) return callback(err, null);
            callback(err, numModified);
        });
    };
    this.incrementLikes = function(permalink, comment_ordinal, callback) {
        "use strict";
        posts.findOne({
            'permalink': permalink
        }, function(err, post) {
            "use strict";
            console.dir(post.comments[comment_ordinal]["num_likes"]);
            if (err) return callback(err, null);
            if (!post.comments[comment_ordinal]["num_likes"]) {
                post.comments[comment_ordinal]["num_likes"] = 0;
            }
            post.comments[comment_ordinal]["num_likes"] += 1;
            posts.update({
                'permalink': permalink
            }, post, function(err, numModified) {
                console.log("numModified= " + numModified);
                if (err) return callback(err, null);
                callback(err, numModified);
            });
        });
    };
}
module.exports.PostsDAO = PostsDAO;
```


<h2 dir="ltr">
  Enlaces a la guía completa
</h2>

  * Guía 1: [Introducción a MongoDB, características clave](http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-1/ "Características básicas e introducción a Mongodb")
  * Guía 2: [Arrancar MongoDB e importar datos](http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-2/ "Iniciar el motor de MongoDB e insertar documentos")  ‎
  * Guía 3: Comandos y operaciones esenciales
  * Guía 4: [Comandos y operaciones avanzadas](http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-4 "Comandos y operaciones avanzadas")
  * Guía 5: [Esquema de datos dirigido por la aplicación](http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-5 "Application Driven Schema")
  * Guía 6: [Crear, manejar y entender los índices](http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-6 "índices en mongodb")
  * Guía 7: [Análisis de datos con el framework de agregación](http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-7 "Aggregation Framework")
  * Guía 8: [Replicación y Sharding](http://blog.findemor.es/2015/06/aprender-a-usar-mongodb-guia-8 "Replication and sharding")