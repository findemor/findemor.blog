---
id: 84
title: Aplicar estilos CSS al código fuente en WordPress
date: 2010-12-02T20:27:26+00:00
author: findemor
layout: post
guid: http://blog.findemor.es/?p=84
permalink: /2010/12/aplicar-estilos-css-al-codigo-fuente-en-wordpress/
categories:
  - Frontend
---
A menudo tenemos la necesidad de postear en nuestro blog WordPress un ejemplo de implementación que contiene un trozo de código fuente. Y también sabemos por experiencia que cuando buscamos una solución a un problema frustrante, tendemos a huir a toda velocidad de las páginas donde el código fuente se mezcla con el resto del mensaje, cual **código spaghetti aberrado**, resultando ininteligible.

Los chicos de **WordPress** han pensado en esto y nos proporcionan un plugin perfecto para que nuestros ejemplos de código fuente resulten atractivos a los lectores: <a rel="nofollow" href="http://wordpress.org/extend/plugins/syntaxhighlighter/installation/"><strong>SyntaxHighlighter Evolved</strong></a>, sigue leyendo para conocerlo.

<!--more-->

Después de instalar el plugin anterior como cualquier otro plugin (no obstante las instrucciones se encuentran en el enlace), incluir un bloque de código fuente resulta tan sencillo como añadir las etiquetas.

\[source language=”;css”;\]\[/source\]  
código escrito en css  
[/source code]

<span style="color: #ff6600;"><strong>ATENCIÓN</strong></span>: aunque en el ejemplo pone “**source code**”; separado, debéis poner **sourcecode**. Lo he puesto separado para evitar que se ejecute el plugin.

En el ejemplo pone language=”;css”; porque el bloque contiene sintaxis Css, pero si nuestro ejemplo está escrito en otro lenguaje, basta con cambiar css por el parametro apropiado, de esta lista:

  * actionscript3
  * bash
  * coldfusion
  * cpp
  * csharp
  * css
  * delphi
  * erlang
  * fsharp
  * diff
  * groovy
  * javascript
  * java
  * javafx
  * matlab (keywords only)
  * objc
  * perl
  * php
  * text
  * powershell
  * python
  * r
  * ruby
  * scala
  * sql
  * vb
  * xml

Tambien existen parametros de configuración que personalizan la salida generada por el plugin si deseamos darle un toque distinto, aunque no son necesarios, de hecho, el parametro “language”; tampoco es necesario, y en caso de no escribirse el contenido se mostrara como texto monoespaciado (útil por ejemplo para hacer dibujos ascii jaja). En negrita marco el valor por defecto de entre los posibles valores que puede tomar el atributo.

  * `autolinks` (**true**/false) — Todas las urls del código funcionan como enlaces.
  * `collapse` (true/**false**) — Contrae el bloque de código, el usuario debe pulsar sobre el para expandirlo y leerlo.
  * `firstline` (number) — Cambia el valor inicial del contador de lineas. Por defecto empieza en 1.
  * `gutter` (**true**/false) — Muestra una columna a la izquierda con el contador de lineas.
  * `highlight` (lista de numeros separados por comas) — Resalta las lineas deseadas.
  * `htmlscript` (true/**false**) — Todas las lineas con código HTML o XML aparecerán resaltadas. Es útil cuando se mezcla alguno de estos lenguajes de marcado con otros como PHP.
  * `light` (true/**false**) — Oculta la columna del contador de lineas y la barra de herramientas para simplificar su aspecto en bloques pequeños.
  * `padlinenumbers` (**true**/false/integer) — Permite modificar el padding de la columna del contador de lineas. True sera un padding automatico, False elimina el padding, y un numero determina el padding.
  * `toolbar` (**true**/false) — Muestra una barra de herramientas cuando pasas el raton sobre el código.
  * `wraplines` (true/false) — Un valor True evita que las lineas se partan cuando son demasiado largas, mostrando una barra de desplazamiento si es necesario.

Aqui hay un ejemplo de como quedaria usando el parametro highlight:

```
  
public void HelloWorld(string saludo)  
{  
//La linea siguiente es importante  
Console.WriteLine(saludo);  
}  
```


Espero que os resulte útil.

Para más informacón leed el artículo oficial (en inglés), [AQUI](http://en.support.wordpress.com/code/).