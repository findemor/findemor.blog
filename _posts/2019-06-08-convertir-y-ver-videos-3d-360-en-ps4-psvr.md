---
id: 371
title: 'Ver videos 3D SBS 180 y 360 en PSVR de PS4'
date: 2019-06-08T12:40:48+00:00
author: findemor
layout: post
image: /wp-content/headers/psvr.jpg
categories:
  - Tutorial
tags:
  - VR
---

Ver videos con las PSVR de PS4 es, en ocasiones, un poco complicado, en parte debido a que la consola es un tanto excigente con el formato en el que deben estar los videos para poder reproducirlos.

En Playstation 4, ahora mismo contamoss con 2 reproductores interesantes para poder ver videos con nuestras VR, uno de ellos es el Reproductor multimedia que viene por defecto, y el otro es Littlstar, vamos a comprarlos brevemente.

## Reproductor Multimedia

Este reproductor es gratuito, ya que viene por defecto, y hace algunos meses lanzaron una actualización con la que es posible activar el modo VR cuando estas reproduciendo un video; sin embargo, hasta donde yo he podido probar, sólo permite reproducir videos 360 o 180, pero no videos 3D SBS (Side by Side) ni 3D OU.

Si no necesitas ver video 3D, puedes usar este reproductor, cuyos requisitos son:

> Video: 
> H.264/MPEG-4 AVC High Profile Level 4.2  
> 
> Audio: 
> AAC LC, AC-3 (Dolby Digital), LPCM  
> 
> File Format:  
> MP4 

## Reproductor Littlstar

Podemos encontrar este reproductor en la [PlayStation Store](https://store.playstation.com/es-es/product/EP2870-CUSA06742_00-LITT1ST4RTHEG0AT), siendo uno de los más potentes en este momento. Sin embargo no es completamente gratis, ya que algunas funciones, como cargar videos de nuestra libreria, necesitan adquirir una licencia si es que duran más de 2 minutos. Puedes consultar ssus distintass licencias en la pagina web de [Littlstar](https://littlstar.com/).

Si te animas a usar este reproductor, tendras que meter los videos en una memoria USB, dentro de una carpeta llamada "Littlstar" en la raiz del disco, en un formato compatible:

> Video:  
> H.264 High@5.1 or lower 
> Max pixel width: 2560, Max frame rate: 60fps, YUV420/NV12 color space,  20Mbps bit rate or lower  
> 
> Audio Requirements: 
> Stereo Audio only 
> AAC (Advanced Audio Coding) 
> 320Kbps bit rate or lower 
> 
> File format: 
> File extensions: MP4, MOV, No fragmented MP4, No DRM, 15 min max duration

## Requisitos de PSVR

Por otro lado, si bien estos son los formatos aceptados por los reproductores, hay que tener en cuenta que si lo vamos a visualizar en las PSVR en lugar de en un monitor, vamos a estar limitados por las capacidades de éstas, por lo que de nada va a servir tener una calidad superior.

Nuestras PSVR tienen una __una única pantalla de 1920x1080__ lo que nos ofrece una resolución individual __por ojo de 960x1080__. Así que realmente, no necesitamos que nuestros videos tengan más de esta resolución, y haremos que el reproductor funcione con mas eficiencia si evitamos que reescale la imagen desde resoluciones superiores.

# Guía para convertir videos 3D SBS, 3D OU, 180 o 360 para verlos en PSVR

Para convertir videos 3D Side by Side, 180 o 360 para verlos en cualquiera de los dos reproductores multimedia anteriores vamos a utilizar la herramienta [FFMPEG](https://ffmpeg.org/) que deberemos descargar y descomprimir, tras lo que pondremos el ejecutable (.exe) dentro de la carpeta donde tenemos los videos que queremos convertir, por ejemplo.

Despues, abrimos una consola o PowerShell en esa misma carpeta, y ejecutamos el siguiente comando, cambiando "MI_FICHERO.mp4" por el nombre del fichero que queremos convertir:

```shell
.\ffmpeg.exe -i MI_FICHERO.mp4 -c:v libx264 -crf 23 -vprofile high -level 4.2 -pix_fmt yuv420p -c:a aac -ac 2 -b:a 100k -r 24 -f segment -segment_time 900 -reset_timestamps 1  -vf scale=1920:1080 SALIDA_%03d.mp4
```

Los ficheros resultantes tendrán como nombre SALIDA_XX.mp4, siendo XX el indice de numeracion, ya que se va generar un video cada 900 segundos (15 minutos) para maximizar la compatibilidad con Littlstar, que recomiendan videos de hasta esta duración. Aunque si no quieres que se corten en segmentos puedes eliminar esta parte del comando "-segment_time 900".

¿Os ha servido esta guía?