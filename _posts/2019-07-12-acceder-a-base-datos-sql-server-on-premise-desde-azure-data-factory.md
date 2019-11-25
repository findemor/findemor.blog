---
title: Acceder a base de datos SQL Server on-premise desde Azure Data Factory
date: 2019-07-12T16:52:46+00:00
author: findemor
layout: post
permalink: /2019/07/acceder-base-datos-sql-server-on-premise-desde-azure-data-factory/
image: /wp-content/uploads/2019/07/12/success-diagnosis.jpg
categories:
  - Desarrollo
  - How to
tags:
  - Cloud
  - Azure
---
# Acceder a base de datos SQL Server on-premise desde Azure Data Factory

En ocasiones cuando tenemos entre manos un proyecto en el que pretendemos hacer algun flujo de datos o integración de datos, por ejemplo para hacer Business Intelligence, Business Analytics, Big Data, etc. necesitamos una herramienta con la que generar un workflow y los correspondientes pipelines de datos. 

Para ello existen numerosos ETL, con distintos propositos, complejidades, ventajas y desventajas. Algunos de ellos son Logstash (del stack de Elastic) y Apache NiFi, que son los dos que he estado utilizando hasta ahora, pero hoy quiero explicar una herramienta de Microsoft que en algunos casos facilita mucho estas tareas: __Azure Data Factory__.

En este artículo veremos la receta para crear un enlace con el que acceder a una base de datos MS-SQL on Premise (aunque para otras como Oracle, MySQL, etc. el proceso es el mismo). Es decir, la base de datos permanece en nuestras máquinas locales, detras de un firewall o una intranet, y podremos consultar su información desde el cloud para crear posteriormente los pipelines en Azure Data Factory (que ya veremos en otro post).

Intentaré mantener la guía sencilla pero dando por sentado algunos conceptos básicos de Azure.
Allá vamos.

## Crear Grupo de recursos y Recurso Data factory

En primer lugar, entraremos en el [Portal de Azure](Portal de Azure) (en el que debemos tener usuario y permisos para crear recursos).

Creamos en primer lugar un Grupo de Recursos en el que iremos añadiendo todos los recursos que vayamos necesitando para contruir nuestro pipeline.

![create resource group](/wp-content/uploads/2019/07/12/resource-group-creation.jpg)

Una vez que se ha creado el grupo de recursos, creamos un recurso de tipo "Data Factory" en él. Pulsamos añadir y rellenamos los campos, seleccionando el Resource Group existente que acabamos de crear.

![create adf](/wp-content/uploads/2019/07/12/adf-creation.jpg)

Y esperamos a que finalice la creación.

## Registrar el enlace de datos

Accedemos al Data factory que acabamos de crear en Azure y entramos en su panel de control pulsando en el botón __"Author & Monitor"__ de la pestaña Overview. Esto abrirá un nuevo panel donde podemos empezar a editar nuestro ETL.

Para lograrlo, pulsamos __"Editar > Connections > Integration Runtimes > New > Perform data movement and dispatch... > Self-Hosted"__.

![integration runtime creation](/wp-content/uploads/2019/07/12/ir-creation.jpg)

Le damos un nombre a nuestro nueva conexión Self-Hosted, y una descripción de que dentro de unos años nos haga sentir orgullosos de nuestro "yo del pasado".

Una vez tenemos esto, copiamos el __Token de Autenticación__ que nos hará falta en el paso siguiente. ¡No lo compartas ni lo pongas en lugares inseguros!

![create adf](/wp-content/uploads/2019/07/12/ir-token.jpg)

## Instalar Azure Data Factory Integration Runtime

Desde [este enlace](https://www.microsoft.com/en-us/download/details.aspx?id=39717) podemos descargar ADF Integration Runtime, que debemos instalar en el equipo al que queremos acceder desde Azure Data Factory (desde el cloud).

En este caso el equipo en el que lo  instalaremos es en el que tenemos acceso a la base de datos que queremos consultar desde Azure Data Factory, que a dia de hoy debe ser una máquina Windows.

El proceso de instalación es muy sencillo, simplemente ejecutar el fichero y seguir el wizard.

Cuando el proceso de instalación del servicio autohospedado haya finalizado, nos pedirá credenciales para registrarlo, ponemos el __Token de Autenticación__ que obtuvimos en el paso anterior. Al registrarlo veremos lo siguiente:

![success-instalation](/wp-content/uploads/2019/07/12/success-instalation.jpg)

Vamos a la petaña Diagnóstico a continuación e introducimos las credenciales de nuestra base de datos.

![success-diagnosis](/wp-content/uploads/2019/07/12/success-diagnosis.jpg)

## Comprobar que se ha creado el enlace

Si volvemos a nuestro panel de control de Data Factory (donde obtuvimos el token) podremos ver que aparece una nueva conexión a nuestra máquina. Si el Status no es Running, prueba a recargar la página web.

![registered integration runtime](/wp-content/uploads/2019/07/12/registered-ir.jpg)

Nota: si aún así no aparece como Running, seguramente se trate de un problema de conexión de la red en la que tienes la máquina.

## Crear la conexión a la base de datos local

Ahora lo que haremos será crear un servicio vinculado que permitirá acceder a los datos que hay en nuestra base de dato On Premise, empleando nuestro nuevo y reluciente Integration Runtime Service como medio para lograrlo.

Vamos a __"Editar > Connections > Linked Services > New > SQL Server"__.

![sql connection creation](/wp-content/uploads/2019/07/12/sql-connection-creation.jpg)

Damos un nombre y una descripción bien semántica a la nueva base de datos, introducimos las credenciales del mismo modo en que lo hicimos antes durante el diagnóstico del Integration Runtime Service en la máquina de windows.

Ponemos especial atención a __seleccionar el Integration Runtime__ que hemos instalado en el combo "Connect via integration runtime".

__Nota: __ Ojo con tu Server name si este tiene el caracter backslash (\), ya que en la herramienta de diagnostico habrá que escaparlo poniendo un doble backslash, mientras que en el panel de la web hay que usar solo uno.

Verifica que la conextión está correctamente vinculada haciendo click en el botón Test Connection, que te dará información en caso de que haya algo mal configurado.

Cuando diga "Connection successful", pulsa Finish para guardar el enlace a datos.

![linked sql config](/wp-content/uploads/2019/07/12/linked-sql-config.jpg)
