---
id: 310
title: Cómo incluir un Fragment en un ListView de Android
date: 2014-07-28T16:52:46+00:00
author: findemor
layout: post
guid: http://blog.findemor.es/?p=310
permalink: /2014/07/como-incluir-un-fragment-en-un-listview-de-android/
image: /wp-content/uploads/2014/07/fansapp_expocosplay_demo.jpg
categories:
  - Android
  - Desarrollo
  - How to
  - Java
tags:
  - Android
  - Java
---
No son pocas las veces en las que en Android tenemos **una lista en la que queremos que el primer elemento sea más grande**, más vistoso **y con un comportamiento especial**. Lo primero que se nos viene a la cabeza es poner la lista a continuación de un LinearLayout cualquiera&#8230; pero entones quedará muy poco espacio para la lista. Y alguien dirá: usamos un ScrollView&#8230; ya pero ¿**Cómo meter un ListView dentro de un ScrollView en Android**? Pues hay una alternativa mejor: **meter un Fragment como primer elemento de la lista**.

<!--more-->

Como os comentaba, existen **muchas opciones** para encarar este problema. Pero hay 3 que he visto con mas frecuencia:

  * **Método 1**: Se coloca la información destacada arriba y a continuación un ListView. **El gran problema** consiste en que estamos dejando **muy poco espacio** en la pantalla para el scroll de la lista. De hecho, en algunas proporciones de pantalla ni siquiera se verá, por no hablar de lo mal enfocado que está desde la experiencia de usuario (espacio totalmente desaprovechado una vez visualizada la parte superior).
  * **Método 2**: Un ListView (o linearLayouts dinámicos) metidos dentro de un ScrollView. **El problema es grave también**, ya que manejar con los dedos una **jerarquía anidada de elementos &#8220;_scrolables_&#8220;** es infernal desde la perspectiva de la UX.
  * **Método 3**: Un ListView donde el primer elemento es distinto a los demás. **Esta solución es la óptima,** y en principio bastaría con manejar dos tipos de Layout y cargarlos correctamente en el getView de nuestro ArrayAdapter de la lista. Pero hoy vamos a darle una vueltecita más, y **vamos a usar un Fragmento como primer elemento**. De este modo ganamos muchísimo en potencia y flexibilidad de la solución. Vamos a ello.

# Un fragmento en tu lista. El código de ejemplo.

Para ilustrar el caso sin manchar con demasiado código el post, he decidido subir un **ejemplo completo a Github**, lo podéis descargar desde el siguiente enlace:

> <a title="Ejemplo en github" href="https://github.com/findemor/FragmentListView" target="_blank">https://github.com/findemor/FragmentListView</a>

En él **encontrareis dos proyectos de Android** funcionando:

  * **Lib**: es un proyecto android de tipo librería que contiene únicamente el array Adapter que maneja fragmentos internamente. Lo he separado en un proyecto aparte para que se vea que es una solución totalmente horizontal y fácil de integrar en otras aplicaciones.
  * **Demo**: es el proyecto de demostración de uso del proyecto Lib (hay que importar el proyecto Lib como librería). Lo único que hace es generar una pantalla con una lista, dentro de un fragmento. La lista esta implementando la solución que se propone en este post.

# El proyecto Lib. La solución en sí misma.

Como se ha dicho anteriormente, este proyecto es muy sencillo y no haría falta realmente que fuese un proyecto a parte, pero de este modo se facilita su lectura. **Si solo estas interesado en utilizarlo, y no necesitas saber como funciona, te recomiendo saltarte esto y avanzar hasta la sección &#8220;El proyecto Demo. El ejemplo de uso&#8221;**. Solo hay dos elementos relevantes:

  * El layout _**frame\_fragment\_features.xml**_ : contiene un frame para uso interno en el ArrayAdapter.
  * La clase **_BaseAdapter.java_** : contiene la implementación del ArrayAdapter donde se hace la magia.

## BaseAdapter.java. La magia.

Como verás, el código no es nada complicado, así que solo voy a describir la solución brevemente y de forma un tanto abstracta.

La clase BaseAdapter implementa un ArrayAdapter de objetos Object. De este modo, puedes manejar listas de cualquier tipo de objetos (imágenes, comentarios de chat, etc).

Su implementación es bastante estándar, salvo por el hecho de que está distinguiendo en algunos de sus métodos (getItemViewType, getItemCount, getView&#8230;) entre el primero de los elementos de la lista, y los demás. Eso es porque realmente va a mostrar la lista desde la posición 1 en adelante, dejando la posición 0 para el elemento destacado.

En el método getView es donde se lleva a cabo esta inclusión del elemento 0, invocando al método getCustomViewFeatured para devolver el fragmento en tal caso. Como un fragmento es bastante pesado de instanciar, se almacena una caché de la instancia para cargarlo de memoria en caso de que exista.

[java]  
/**  
* Devuelve la vista apropiada para cada tipo de elemento, almacenando una cache con el destacado  
*/  
@Override  
public View getView(int position, View convertView, ViewGroup parent) {

if (position == 0)  
{  
if (mView0Cached == null) mView0Cached = getCustomViewFeatured(convertView);  
return mView0Cached;  
}else  
{  
return getCustomViewItem(position &#8211; 1, convertView);  
}  
}  
[/java]

# El proyecto Demo. El ejemplo de uso

A continuación vamos a ver como se utiliza la librería.  Para ello, **utiliza el proyecto de Demo o crea un proyecto propio, y a continuación importa y referencia en él el proyecto Lib que podéis encontrar en <a title="codigo en github" href="https://github.com/findemor/FragmentListView" target="_blank">github</a>**. Una vez referenciado, ya podemos continuar.

## Paso 1 &#8211; Preparar el layout

Como en cualquier lista que vayáis a implementar en Android, necesitareis preparar algunos layouts: **el principal, que contiene el ListView**, podéis verlo en el ejemplo bajo el nombre _**activity_main.xml**_; y por supuesto también hay al menos **un elemento para las filas de la lista**, que en este caso se llama _**item_left.xml**_.

En nuestro caso, tendremos que preparar además **un layout para el elemento destacado**. Lo podéis encontrar con el nombre **_item_featured.xml_** y no contiene ninguna característica reseñable.

Si sabéis hacer una lista en android, este paso es trivial, pero si es la primera vez, aquí podéis encontrar un artículo con una guía para entenderlas mejor y aprender a implementarlas: <a title="Aprender a programar listas" href="http://blog.findemor.es/2011/02/como-programar-en-android-guia-3/" target="_blank">http://blog.findemor.es/2011/02/como-programar-en-android-guia-3/ </a>

## Paso 2 &#8211; preparar el Adapter de la lista

La clase a la que nos referimos aquí se llama <a href="https://github.com/findemor/FragmentListView/blob/master/Demo/src/com/devergence/example/fraglistview/adapters/FeaturedAdapter.java" target="_blank">FeaturedAdapter</a>.java en el ejemplo.

Se trata de un Adapter normal para una lista cualquiera, con la particularidad de que **extiende la clase BaseAdapter del proyecto Lib**. Al heredarla, se requiere la implementación de algunos métodos abstractos. Vamos a ir uno por uno:

**El constructor: FeaturedAdapter**

El constructor recibe y entrega al BaseAdapter la lista de elementos que van a mostrarse en la lista, así como el parámetro featuredItem, que contiene la información del elemento que se mostrará destacado en la lista.

Además, como podeis ver en el comentario del código, en el constructor se indica el número de tipos de filas que contendrá la lista (en el ejemplo es 1). Esto es así, porque si por ejemplo estais desarrollando un chat, quizás queráis usar distintos layouts para los mensajes propios y ajenos de la lista, en ese caso tendríais que incrementar el valor de este parámetro, así como crear otras constantes en la clase (en nuestro caso solo tenemos TYPE\_LEFT\_ITEM = 1).

**Preparar la vista: getViewItem**

El método getViewItem será invocado automáticamente cada vez que un nuevo elemento vaya a aparecer en pantalla, así que aquí es donde rellenamos el layout correspondiente con los datos del objeto de la lista. Lo único a tener en cuenta es que si tenéis más de un tipo de Item (como se ha dicho en el párrafo anterior) tendríais que distinguirlos en este punto. Recuerda que aquí nunca tienes que distinguir el elemento destacado, eso se hace en otra parte.

**Determinar el tipo de fila: getItemType**

Si teneis mas de un tipo de fila, aquí tendreis que decir de que tipo será dada su posición en la lista, devolviendo la constante oportuna que hayáis definido.

**Instanciar el elemento destacado: getFeaturedFragment**

En éste método construis e instanciais el Fragment que controla el elemento destacado.

**Intefaz de comunicación: getFragmentParentFragmentActivity**

Finalmente, aquí se devuelve la referencia al FragmentActivity que tengáis en vuestra aplicación, ya que se supone que es en ella donde se implementa la interfaz que maneja los eventos que ocurren en el interior del fragmento (así no hay dependencias entre los fragmentos, sino que hay una pieza maestra que los controla).

## Paso 3 &#8211; preparar el fragment del elemento destacado

Esto, evidentemente, será propio de cada aplicación en función de lo que queráis mostrar, pero no presenta ninguna diferencia respecto a otros fragmentos. En el ejemplo, lo podéis encontrar como <a title="Fragment Featured" href="https://github.com/findemor/FragmentListView/blob/master/Demo/src/com/devergence/example/fraglistview/fragments/FragmentFeatured.java" target="_blank">FragmentFeatured</a>.java.

## Paso 4 &#8211; declarar las interfaces de comunicaciones

Si vuestro fragmento va a desatar eventos (clicks que llevan a otras pantallas, etc) éstas no deberian controlarse directamente en el fragmento para no generar dependencias entre ellos y el resto de la aplicación. Para evitarlo, es buena idea declarar una interfaz e implementarla en la Actividad que lo maneje. Podeis ver un ejemplo en la clase <a title="Interfaz" href="https://github.com/findemor/FragmentListView/blob/master/Demo/src/com/devergence/example/fraglistview/interfaces/FeaturedItemInterface.java" target="_blank">FeaturedItemIterface</a>.java.

## Paso 5 &#8211; final

Ya está todo montado&#8230; solo falta instanciar nuestro adaptador en la Activity que necesite mostrar la lista con nuestros datos. En el ejemplo esto está directamente en la Actividad principal, en la clase <a title="Main Activity" href="https://github.com/findemor/FragmentListView/blob/master/Demo/src/com/devergence/example/fraglistview/MainActivity.java" target="_blank">MainActivity</a>.java.

[java]  
//Preparamos el adaptador  
FeaturedAdapter commentsAdapter = new FeaturedAdapter(this, R.layout.item_left, featuredItem, comments);

//Aplicamos el adaptador  
ListView lista = (ListView)findViewById(R.id.listView);  
lista.setAdapter(commentsAdapter);  
[/java]

Además, si os fijáis bien, veréis que esta clase implementa la interfaz de comunicaciones de la que se ha hablado y que maneja los eventos del fragmento.

[java]  
public class MainActivity extends FragmentActivity  
implements FeaturedItemInterface {

&#8230;

@Override  
public void onTestButtonClick(FeaturedItem o) {  
Toast.makeText(this, "Este mensaje se genera en el fragmento pero lo maneja la actividad", Toast.LENGTH_SHORT).show();  
}  
[/java]

## Y se acabó

Esta técnica es la que se ha utilizado para crear la pantalla de detalle de una publicación en la aplicación fansapp ExpoCosplay para Android, por ejemplo. Podéis ver el resultado en esta imagen.

[<img class="aligncenter size-medium wp-image-327" alt="fansapp_expocosplay_demo" src="http://blog.findemor.es/wp-content/uploads/2014/07/fansapp_expocosplay_demo-204x300.jpg" width="204" height="300" srcset="http://blog.findemor.es/wp-content/uploads/2014/07/fansapp_expocosplay_demo-204x300.jpg 204w, http://blog.findemor.es/wp-content/uploads/2014/07/fansapp_expocosplay_demo-696x1024.jpg 696w, http://blog.findemor.es/wp-content/uploads/2014/07/fansapp_expocosplay_demo.jpg 1440w" sizes="(max-width: 204px) 100vw, 204px" />](http://blog.findemor.es/wp-content/uploads/2014/07/fansapp_expocosplay_demo.jpg)

Y a vosotros ¿Os ha resultado útil? ¿Qué técnica usáis para hacer esto?