---
id: 334
title: 'Guía rápida: generar un API mock con osprey-cli y RAML'
date: 2014-11-29T15:00:16+00:00
author: findemor
layout: post
guid: http://blog.findemor.es/?p=334
permalink: /2014/11/guia-rapida-generar-un-api-fake-con-osprey-cli-y-raml/
image: /wp-content/uploads/2014/11/Captura-de-pantalla-2014-11-29-a-las-15.53.47.png
categories:
  - API
  - Desarrollo
  - How to
tags:
  - api
  - node
  - raml
  - rest
---
Muy a menudo, cuando implementamos una aplicación, una parte importante de su arquitectura será un API REST. Para poder **paralelizar** el desarrollo de la aplicación cliente (consumidora) y el API REST (servicio remoto), lo ideal será contar lo antes posible con un prototipo del API, interactivo y pseudo-funcional, que además nos sirva para **validar la especificación** de nuestro API. **A este tipo de API, que devuelve datos _dummy_ para testeo, se la conoce como API Mock o fake**.

<!--more-->

Actualmente existen numerosas formas de lograr esto, empleando distintas tecnologías y lenguajes.

### ¿Qué vamos a utilizar?

  * **Node.js**: plataforma y entorno de desarrollo, que utiliza el lenguaje Javascript para desarrollar aplicaciones, y especialmente indicado para la construcción de APIs RESTful.
  * **RAML**: Lenguaje de especificación de APIs. Si defines tu API con RAML (aunque existen otros, como Swagger y API Blueprint), podrás usar este fichero para generar otras cosas rápidamente, como el API fake para testing, generar documentación, una consola interactiva, pruebas con SoapUI&#8230; y todo con un único fichero.

### ¿Qué necesitamos?

  1. Pues antes de nada, hay que <a title="Instalar node y npm" href="http://nodejs.org/" target="_blank">instalar Node.js y npm</a> (su gestor de paquetes).
  2. Además, necesitamos redactar la especificación de nuestro API en un documento RAML. Si no lo conoces, <a title="RAML" href="http://raml.org/" target="_blank">aquí puedes aprender</a> rápidamente.

_Nota: Si solo quieres probar esta técnica, puedes utilizar un fichero RAML de ejemplo de los numerosos que existen en Github o en la documentación de RAML. Por ejemplo <a href="https://raw.githubusercontent.com/kevinrenskers/raml2html/master/examples/example.raml" target="_blank">éste</a> extraído de la documentación de raml2html._**  
** 

### Guía rápida

Una vez que tenemos las cosas instaladas y hemos generado nuestro fichero RAML, podemos empezar.

  1. Instalamos osprey-cli, el paquete de node que genera el API fake 
      1. Abrimos el terminal o la consola de comandos
      2. Escribimos
      3. > npm install -g osprey-cli
    
      4. Si estas en linux o mac, y te da error, prueba anteponiendo el comando **_sudo_**
  2. <span style="line-height: 1.666666667;">Llamamos a nuestro fichero RAML &#8220;<em>api.raml</em>&#8220;</span>
  3. Ejecutamos el siguiente comando 
      1. > osprey new api.raml &#8211;name test &#8211;target test

Esto nos generará una carpeta **_test_**_ _con la estructura del proyecto. Para instalar las dependencias, vamos (usando el terminal) a la carpeta en la que se ha creado el fichero package.json y ejecutamos lo siguiente:

> npm install

Igual que antes, si esto da error, pude ser que no tengamos permisos. Probad con &#8220;sudo npm install&#8221;.

### Ejecutar y probar

Una vez se han instalado las dependencias, podemos ejecutar y probar nuestra API.

En el terminal, vamos a test/src y ejecutamos

> node app.js

En el terminal aparecerá un mensaje que nos indica que el servicio se ha arrancado correctamente y que estará escuchando en el puerto 3000.

Para probarlo, vamos al navegador y escribimos:

> http://localhost:3000/api/console

O si lo prefieres, puedes atacar los endpoints que hayas definido en tu API directamente desde POSTman en la url _http://localhost:3000/api/{tu_endpoint}_

### Cómo realizar cambios

Si te das cuenta de que necesitas hacer un cambio en tu API, puedes hacerlo fácilmente modificando el fichero RAML.

Para ello, ve a la carpeta _test/src/assets/raml/api.raml_ , edítalo como necesites y guarda los cambios.

Detén el servidor de node pulsando  CTRL+C para liberar el puerto 3000, y ejecútalo de nuevo con el comando:

> node app.js

Así de fácil. Ahora ya no hay excusa para no validar tu API sobre el terreno antes de ponerte a implementar.