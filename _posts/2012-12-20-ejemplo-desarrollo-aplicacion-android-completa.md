---
id: 247
title: Ejemplo de desarrollo de una aplicación Android completa
date: 2012-12-20T18:04:27+00:00
author: findemor
layout: post
guid: http://blog.findemor.es/?p=247
permalink: /2012/12/ejemplo-desarrollo-aplicacion-android-completa/
image: /wp-content/uploads/2012/12/shareyourdreams.png
categories:
  - Android
  - Desarrollo
  - How to
  - Java
  - Tecnología
tags:
  - Android
  - Java
  - tutorial
---
Después de publicar algunas <a title="Cómo programar en Android: Guía 6" href="http://blog.findemor.es/2012/02/como-programar-en-android-guia-6/" target="_blank">guías de desarrollo</a>, quizás algunos de vosotros estéis interesados en ver **un ejemplo completo de una aplicación real**. Pues de esto va este post: os he preparado y **destripado la aplicación &#8220;Share your dreams&#8221;**, o &#8220;Comparte tus sueños&#8221; según aparece nombrada en el Google Play de España.

<!--more-->

#### ¿Qué hace la aplicación?

**Share your dreams es una aplicación sencilla pero muy resultona**. Su propósito es servir de &#8220;**diario de sueños**&#8220;, es decir, cuando tenemos un sueño o una pesadilla es una pena que se nos olvide y se eche a perder ese sinsentido tan curioso que nos ha mantenido la mente ocupada toda la noche&#8230; pues bien, en **Share your dreams podemos dejarlo registrado para recordarlo un tiempo después, y de paso, podemos compartirla de manera anónima con los demás**.

Es más&#8230; aquellos que a veces nos ponemos místicos podremos llegar a preguntarnos si esa noche que tuve una pesadilla, y mi amigo tambien&#8230; ¿**habrán tenido pesadillas otras personas cerca de mi**? ¿**guarda algún tipo de relación la cercania entre nosotros con lo que soñamos**? ¿**La gente en el mundo tiene más sueños o más pesadillas**? ¿a qué le da más importancia? ¡Pues **esto también podremos verlo gracias a un mapa y al registro de la ubicación** aproximada de la persona que registra el sueño!

Y para terminar un juego: igual que el romantico acto de arrancar pétalos a un margarita no pasa de moda&#8230; ¿porque no incluir una bola mágica a la que preguntar si **nuestros sueños van a cumplirse?**

En este post voy a intentar **explicar las características principales de cada una de estas funciones** para arrojar un poco de luz sobre el **desarrollo en Android.** 

Por favor, tened en cuenta que es solo un ejemplo, y que puede que existan formas mejores de hacer las cosas que todos estaremos encantados de leer si los compartis en los comentarios, pero que **el objetivo de la aplicación es meramente didáctico** y como tal se ha diseñado.

#### 1 &#8211; Características generales importantes de la implementación

**Lo primero que llama la atención de la aplicación es que existe la posibilidad de compartir los elementos de interés,** es decir, pesadillas y sueños, con otros usuarios. De hecho, estos se comparten **de manera geolocalizada** (es decir, sabemos donde están ubicados en el mundo gracias a que incluyen sus coordenadas). Debido a esto ya vemos que tendríamos que pelearnos con la tecnología **GPS** y no solo eso, sino **desarrollar un servicio web y alojarlo en internet**, **implementando un API segura** para poder utilizar estas operaciones de **registro**, y las **consultas** desde los dispositivos.

**Por suerte esto no va a ser necesario**, ya que **emplearemos el API de una plataforma llamada topoos que sirve para facilitar este tipo de tareas**, y que gracias a **su SDK es perfecto para desarrollar estas aplicaciones** sin tener que preocuparnos de desarrollar nada en la web. Si no sabeis lo que es os recomiendo echarle un ojo a <a title="web de topoos" href="http://www.topoos.com" target="_blank">la página web</a> ya que **facilita mucho el desarrollo de aplicaciones móviles dependientes de contexto**.

Además, también hemos visto que **vamos a mostrar esta información en un mapa**, para eso utilizaremos también otro API, en este caso el de **Google Maps**.

Para monetizar la aplicación y que nuestro esfuerzo se vea _tristemente_ recompensado, **vamos a incluir anuncios publicitarios**, **con el SDK de AdMob**.

**Debido a estas tres APIs el proyecto no funcionará directamente cuando descarguéis el código**, ya que usar un API requiere incluir una serie de códigos secretos (API_keys o tokens) que no es buena idea publicar en un repositorio. Pero no os preocupeis porque precisamente para hacerlo funcionar está este post.<img src="http://blog.findemor.es/wp-includes/images/smilies/simple-smile.png" alt=":)" class="wp-smiley" style="height: 1em; max-height: 1em;" /> 

Puedes descargar la aplicación para Android desde Google Play aquí

> <a title="share your dreams Play Google" href="http://bit.ly/shareyourdreams" target="_blank">http://bit.ly/shareyourdreams</a>

#### 2 &#8211; Descargar el código fuente

Como acabamos de ver, **el código no funcionará bien hasta que no hayamos obtenido e incluido nuestras propias claves de las APIs utilizadas**, pero es necesario descargarlo y tenerlo cerca para entender las explicaciones siguientes.

Podeis descargar el código de este proyecto desde el repositorio GIT:

> <a title="GIT ShareYourDreams" href="http://github.com/findemor/shareyourdreams" target="_blank">http://github.com/findemor/shareyourdreams</a>

#### 3 &#8211; Obtener acceso a la plataforma topoos

Ya que **topoos nos va a proporcionar todo lo necesario para trabajar con geolocalización** y especialmente, en este caso, nos permite disponer de un **completo API de servicios en la nube** en los que poder almacenar y compartir información entre instancias de nuestra aplicación, **se va a convertir en el eje centrar de esta aplicación**, **permitiéndonos preocuparnos únicamente de lo que nos interesa: registrar sueños y pesadillas.**

Para usar topoos, lo primero que tenemos que hacer es **ir al panel de desarrolladores, registrarnos y crear una nueva aplicación.** Cuando hayamos creamos la aplicación **nos dará una serie de tokens** (son secretos asi que los que publico aquí no son válidos y solo están a modo de ejemplo).

<div id="attachment_268" style="width: 310px" class="wp-caption aligncenter">
  <a href="http://blog.findemor.es/wp-content/uploads/2012/12/SYD_panel_tos.jpg"><img class="size-medium wp-image-268 " title="Aplicación registrada en topoos" alt="Aplicación registrada en topoos" src="http://blog.findemor.es/wp-content/uploads/2012/12/SYD_panel_tos-300x234.jpg" width="300" height="234" srcset="http://blog.findemor.es/wp-content/uploads/2012/12/SYD_panel_tos-300x234.jpg 300w, http://blog.findemor.es/wp-content/uploads/2012/12/SYD_panel_tos-150x117.jpg 150w, http://blog.findemor.es/wp-content/uploads/2012/12/SYD_panel_tos.jpg 982w" sizes="(max-width: 300px) 100vw, 300px" /></a>
  
  <p class="wp-caption-text">
    Aplicación registrada en topoos
  </p>
</div>

Estos tokens nos permiten crear usuarios y obtener nuevos tokens de acceso. **Como nuestra aplicación no necesita distinguir e identificar usuarios individuales, y para facilitar las explicaciones, vamos a utilizar únicamente el que se llama APPTOKEN_ADMIN**, es decir, una firma que nunca caduca y que proporciona permiso de alto nivel a nuestra aplicación en topoos.

También **necesitaremos <a title="SDK topoos Android" href="http://docs.topoos.com/tools/sdks/android/" target="_blank">descargar el SDK de topoos</a> para Android y arrastrarlo a la carpeta libs de nuestro proyecto.** (Habrá que crearla si no existe).

#### 4 &#8211; Configurar la aplicación con el token de topoos

Podréis observar que el código de la aplicación está organizado en 4 paquetes:

  * **.shareyourdreams:** Contiene las Actividades (pantallas) de la aplicación)
  * **.shareyourdreams.internal**: Contiene clases de uso interno de la aplicación, como entidades y el acceso y creación de la base de datos.
  * **.shareyourdreams.map**: Contiene las clases que nos facilitan tareas comunes para trabajar con el mapa de Google Maps.
  * **.shareyourdreams.topoos**: Contiene las clases que definen como se va a comunicar nuestra app con topoos.

Pues bien, abrimos el fichero **AccessInterface.java de este último paquete** y en el atributo **TOPOOS\_ADMIN\_APP_TOKEN escribimos el que hemos obtenido en el Paso anterior** en el panel de desarrollador de topoos al crear nuestra app.

[sourcecode language=&#8221;java&#8221; highlight=&#8221;4&#8243;]  
public class AccessInterface {

////Must get your tokens from topoos developer panel https://developers.topoos.com  
private static final String TOPOOS\_ADMIN\_APP_TOKEN = "70a05a65-097f-429b-8e43-6b9b6078xxa7";  
[/sourcecode]

Si vemos <a title="POI topoos" href="http://docs.topoos.com/reference/poi-point-of-interest/" target="_blank">la documentación de topoos</a>, podremos ver que **un mecanismo perfecto para guardar los sueños y pesadillas es el que topoos denomina <a title="POI en topoos" href="http://docs.topoos.com/reference/poi-point-of-interest/" target="_blank">Puntos de Interés</a>**, pero que estos **Puntos de Interés necesitan que existan una serie de categorías con los que organizarlos y consultarlos**. Por lo que a**ntes de continuar hay que generar UNA ÚNICA vez estas categorias**. Vamos a ver como:

  1. En MainActivity.java **descomenta** las siguientes líneas del método onCreate:  
    [sourcecode language=&#8221;java&#8221; highlight=&#8221;9,10,11,12,13,14,15&#8243;]  
    public class MainActivity extends CustomActivity {</p> 
    @Override  
    public void onCreate(Bundle savedInstanceState) {  
    super.onCreate(savedInstanceState);  
    setContentView(R.layout.activity_main);
    
    //The first time we must manually create the necessary POIs categories, and get the IDs for use in the whole app  
    /*  
    try {  
    com.devergence.shareyourdreams.topoos.AccessInterface.PreregisterApplicationNeccessaryItems(this);  
    } catch (Exception e) {  
    e.printStackTrace();  
    }  
    */  
    //Now the application cloud context is ready
    
    }  
    [/sourcecode]</li> 
    
      * **Ejecuta** la aplicación en modo **DEBUG**.
      * **Apunta los valores que aparecen así en el LogCat de Eclipse.** 
        <div id="attachment_267" style="width: 310px" class="wp-caption alignnone">
          <a href="http://blog.findemor.es/wp-content/uploads/2012/12/SYD_LOG.jpg"><img class="size-medium wp-image-267" title="Resultado del Logcat" alt="Resultado del Logcat" src="http://blog.findemor.es/wp-content/uploads/2012/12/SYD_LOG-300x35.jpg" width="300" height="35" srcset="http://blog.findemor.es/wp-content/uploads/2012/12/SYD_LOG-300x35.jpg 300w, http://blog.findemor.es/wp-content/uploads/2012/12/SYD_LOG.jpg 667w" sizes="(max-width: 300px) 100vw, 300px" /></a>
          
          <p class="wp-caption-text">
            Resultado del Logcat
          </p>
        </div></li> 
        
          * **Vuelve a comentar** las líneas del punto 1 en el onCreate de MainActivity.</ol> 
        
        **Lo que estamos haciendo es registrar las Categorias que necesitamos**, y un Punto de Interés especial que más adelante explicaremos para que es (un pequeño truco de regalo<img src="http://blog.findemor.es/wp-includes/images/smilies/simple-smile.png" alt=":)" class="wp-smiley" style="height: 1em; max-height: 1em;" /> ). **Como esto solo hay que hacerlo una vez en la vida y ya no vale para nada más**,  lo podríamos haber hecho directamente en topoos a través de llamadas al API Rest, pero así todo queda escrito con código Java.
        
        Ahora **abrimos el fichero POICategories.java y damos los valores apropiados según lo que hemos visto en el LogCat del punto 3**.
        
        [sourcecode language=&#8221;java&#8221;]  
        public class POICategories {
        
        //The INTEGER identifier must be created when we register our POI Categories manually the first time
        
        public static int DREAM = 30; //Represents a good Dream  
        public static int NIGHTMARE = 31; //Represents a Nightmare  
        public static int USER = 33; //Category for user registered POIs  
        public static int SYSTEM = 32; //Category for app internal POIs  
        }  
        [/sourcecode]
        
        **Después, tambien a partir del dato que aparece en el LogCat, damos valor al atributo PREREGISTERES\_POI\_FOR_COUNTERS del fichero AccessInterface.java.**
        
        [sourcecode language=&#8221;java&#8221;]  
        ////We must use the integer id that we got when we registered this POI the first time (manually)  
        private static final int PREREGISTERED\_POI\_FOR_COUNTERS = 6614;  
        [/sourcecode]
        
        #### 5 &#8211; La aplicación ya funciona, pero el mapa se ve gris.
        
        Al igual que antes, el **API de Google Maps que estamos utilizando para visualizar el mapa necesita un Token de Acceso**, y si no se configura el mapa se mostrará como un mar gris muy desagradable. Como es bastante largo de explicar y ya hay guías muy completas para obtener un token de google maps, seguro que os sirve con **la referencia oficial para configurar este parámetro**.
        
        > <a href="https://developers.google.com/maps/documentation/android/start#obtain_a_google_maps_api_key" target="_blank">https://developers.google.com/maps/documentation/android/start#obtain_a_google_maps_api_key</a>
        
        Si preferis un tutorial, hay uno que os recomiendo para obtener el API Key del Google maps v2 en Android:
        
        > <a href="http://www.sgoliver.net/blog/?p=3244" target="_blank">http://www.sgoliver.net/blog/?p=3244</a>
        
        <span style="line-height: 1.666666667;">En cualquier caso, una vez que tengais un API key valido, </span><strong style="line-height: 1.666666667;">el fichero que teneis que tocar es únicamente el AndroidManifest.xml.</strong>
        
        [sourcecode language=&#8221;xml&#8221; highlight=&#8221;8&#8243;]  
        <meta-data android:name="com.google.android.maps.v2.API_KEY"  
        android:value="XXXXXXXXXXXXXXXXXXXX\_PLACE\_HERE\_YOUR\_GOOGLEMAPS_APIKEY"/>  
        [/sourcecode]
        
        #### 6 &#8211; El mapa se ve, pero no aparece la publicidad.
        
        **Ocurre exactamente lo mismo que en los puntos anteriores**, **necesitamos el Token de un API** para acceder a este servicio de publicidad. **Para esto debemos ir a <a title="Panel de Admob" href="http://www.google.com/ads/admob/" target="_blank">AdMob</a>**, crear una cuenta y configurarla. Damos de alta un perfil y nos darán una pequeña clave que **debemos incluir en el layout ads_item.xml** de la aplicación, en el atributo **ads:adUnitId.**
        
        **Si este paso no se completa no pasa nada, es opcional**, pero no habrá publicidad en la aplicación.
        
        #### 7 &#8211; ¡La aplicación funciona!
        
        Hagamos entonces un repaso para entenderla mejor.
        
        ##### 7.1 &#8211; La pantalla principal.
        
        La actividad MainActivity es la pantalla principal. Carece de funcionalidad salvo como **menú principal.**
        
        <div id="attachment_276" style="width: 178px" class="wp-caption aligncenter">
          <a href="http://blog.findemor.es/wp-content/uploads/2012/12/menu.png"><img class="size-medium wp-image-276 " title="Menu principal" alt="Menu principal" src="http://blog.findemor.es/wp-content/uploads/2012/12/menu-168x300.png" width="168" height="300" srcset="http://blog.findemor.es/wp-content/uploads/2012/12/menu-168x300.png 168w, http://blog.findemor.es/wp-content/uploads/2012/12/menu-84x150.png 84w" sizes="(max-width: 168px) 100vw, 168px" /></a>
          
          <p class="wp-caption-text">
            Menu principal
          </p>
        </div>
        
        Me gustaría agradecer **el estupendo trabajo que ha hecho <a title="Neld Ayanami en twitter" href="https://twitter.com/neld_ayanami" target="_blank">@neld_ayanami</a> para dejarla así de bonita** con su magnífico trabajo de diseño.
        
        ##### 7.2 &#8211; La pantalla de registro de Sueño/Pesadilla
        
        **La actividad UploadActivity contiene ambas funcionalidades.** Recibe un **parámetro &#8220;type&#8221;** a través de los Extras, y así puede saber si fue invocada para registrar una pesadilla o un sueño. **En función de eso mostrará un aspecto u otro y realizara una u otra acción al pulsar el botón de enviar.**
        
        <div id="attachment_277" style="width: 310px" class="wp-caption aligncenter">
          <a href="http://blog.findemor.es/wp-content/uploads/2012/12/sueno_pes.png"><img class="size-medium wp-image-277" title="Publicar" alt="Publicar" src="http://blog.findemor.es/wp-content/uploads/2012/12/sueno_pes-300x253.png" width="300" height="253" /></a>
          
          <p class="wp-caption-text">
            Publicar
          </p>
        </div>
        
        Como vamos a **geolocalizar los sueños, necesitamos saber la ubicación GPS del dispositivo**. Para eso nos **suscribimos al LocationManager** del sistema y no permitiremos que el usuario pulse enviar (**el botón estará desactivado) hasta que haya escrito algun texto y hasta que el chip GPS se haya calentado** y haya obtenido la ubicación aproximada en la que se encuentra.
        
        Cuando tenemos ambos datos, **lo unico que haremos será hacer la llamada al SDK de topoos para registrar el Punto de Interés** con estos datos. **La categoría que le pongamos al Punto de Interés sera la que nos permite distinguir sueños de pesadillas**. Ademas siempre lo metemos en una subcategoria (USER) para saber que este elemento lo ha registrado el usuario y no el sistema, por si en el futuro quisiesemos distinguir los manuales de los automaticamente registrados.
        
        Como el tiempo de respuesta de una llamada a un API depende de la conexión que tenga el usuario en ese momento, **lo hacemos dentro de un hilo secundario para evitar bloquear la aplicación** si la conexión es mala, y mientras tanto mostramos un Progress Dialog.
        
        **Detalle**: Fijaos que en este punto, además de registrar el POI en topoos, estamos registrandolo en la base de datos local, y tambien estamos dando de alta un elemento Warning en el punto de interés que creamos al configurar la aplicación (que es totalmente opcional pero que más adelante explicaremos el truco).
        
        ##### 7.3 &#8211; La Base de datos local
        
        **La actividad DatabaseActivity contiene una lista de elementos que han sido registrados desde este dispositivo**, es decir: los sueños y pesadillas que se han creado desde ahí **en el punto 7.2**.
        
        <div id="attachment_278" style="width: 178px" class="wp-caption aligncenter">
          <a href="http://blog.findemor.es/wp-content/uploads/2012/12/basedatos.png"><img class="size-medium wp-image-278" title="Base de datos" alt="Base de datos" src="http://blog.findemor.es/wp-content/uploads/2012/12/basedatos-168x300.png" width="168" height="300" srcset="http://blog.findemor.es/wp-content/uploads/2012/12/basedatos-168x300.png 168w, http://blog.findemor.es/wp-content/uploads/2012/12/basedatos-84x150.png 84w" sizes="(max-width: 168px) 100vw, 168px" /></a>
          
          <p class="wp-caption-text">
            Base de datos
          </p>
        </div>
        
        Esta actividad es muy sencilla, **consulta la base de datos**, que contiene toda la información relevante para el usuario de un punto de interés, **y los muestra en una lista adornada con icono** en función de si es sueño o pesadilla.
        
        Como ya hemos visto <a title="Como programar en Android: Guia 4" href="http://blog.findemor.es/2011/02/como-programar-en-android-guia-4/" target="_blank">esto en las guias </a>no lo detallo mucho, pero es un buen ejemplo real de uso de Bases de datos y listas personalizadas.
        
        ##### 7.4 &#8211; El Mapa
        
        **La actividad MapActivity implementa el mapa de Google Maps.** 
        
        <div id="attachment_279" style="width: 178px" class="wp-caption aligncenter">
          <a href="http://blog.findemor.es/wp-content/uploads/2012/12/mapa.png"><img class="size-medium wp-image-279" title="Sueños cercanos" alt="Sueños cercanos" src="http://blog.findemor.es/wp-content/uploads/2012/12/mapa-168x300.png" width="168" height="300" srcset="http://blog.findemor.es/wp-content/uploads/2012/12/mapa-168x300.png 168w, http://blog.findemor.es/wp-content/uploads/2012/12/mapa-84x150.png 84w" sizes="(max-width: 168px) 100vw, 168px" /></a>
          
          <p class="wp-caption-text">
            Sueños cercanos
          </p>
        </div>
        
        En él se hacen varias cosas:
        
          * **Se obtiene la posición actual del usuario,** igual que hicimos en el paso 7.2, para saber donde centrar el mapa y pedir los sueños y pesadillas cercanos.
          * **En un hilo secundario, obtenemos los sueños y pesadillas cercanos**: fijaos lo sencilla que es esta tarea gracias al SDK de topoos, por eso lo hemos elegido.
          * **En el hilo secundario, consultamos los contadores mundiales de sueños y pesadillas registrados.** Este es el tip/truco al que me he referido varias veces a lo largo del post, y que explico al final en detalle.<img src="http://blog.findemor.es/wp-includes/images/smilies/simple-smile.png" alt=":)" class="wp-smiley" style="height: 1em; max-height: 1em;" /> 
          * **Mostramos un circulo con transparencia que representa el radio de búsqueda.**
        
        ##### 7.5 &#8211; La pantalla de adivinación
        
        **La actividad SpellActivity implementa la función más divertida y simple de todas.** Su objetivo es que el usuario se divierta intentando averiguar si su sueño se cumplirá o no.  
        
        
        <div id="attachment_280" style="width: 310px" class="wp-caption aligncenter">
          <a href="http://blog.findemor.es/wp-content/uploads/2012/12/spell2.jpg"><img class="size-medium wp-image-280" title="Generando el hechizo" alt="Generando el hechizo" src="http://blog.findemor.es/wp-content/uploads/2012/12/spell2-300x253.jpg" width="300" height="253" /></a>
          
          <p class="wp-caption-text">
            Generando el hechizo
          </p>
        </div>
        
  
        **El código es muy simple y no tiene elementos especiales, pero sí algunas transiciones y animaciones** (como el uso de un gif animado en android) que puede ser curioso (si bien la alternativa escogida, usando un webview, es la más simple y por ello veréis que desafortunadamente no funciona en todos los dispositivos).
        
        ##### 7.6 &#8211; Otras pantallas
        
        Vereis que hay otras actividades que carecen de interés en este post:
        
          * **AboutActivity**: es la tipica pantalla donde hay información de desarrollador.
          * **SplashscreenActivity**: se muestra durante un par de segundos cuando la aplicación se abre, a modo de presentación.
        
        ##### 7.7 &#8211; Importante! La herencia de Actividades
        
        **En el código del proyecto podeis ver que hay clases abstractas para los distintos tipos de actividades**: CustomActivity, CustomListActivity y CustomMapActivity.
        
        Estas clases son las que **heredaremos en las demás actividades del proyecto**, en lugar de las clásicas &#8220;Activity&#8221; o &#8220;ListActivity&#8221;.
        
        **El objetivo de estas clases es autenticar el usuario de topoos de la manera más sencilla, y permitir meter facilmente analíticas de aplicación si lo deseamos.**
        
        **Veamos por ejemplo la clase CustomActivity.java.**
        
        **Si os fijais, lo que hace es definir comportamiento general para todas las actividades de nuestra aplicación, de este modo el código queda más limpio al poder reutilizar esto y no escribirlo en cada una.**
        
          * En el método **onCreate**, llama al método que crea y almacena el token de topoos en las preferencias, para que no haga falta incluir el token de manera explícita en ninguna otra llamada. Lo podriamos hacer de ese modo, incluyendo el parametro AccessToken, pero así no tenemos que escribirlo.
          * En los métodos **onStart** y **onStop**, invocamos la operación del SDK de las analíticas si queremos registrarlas.
          * Añadimos los métodos que crean y manejan los **menús** de la aplicación, con la opción para ver la pantalla About.
        
        ##### 7.8 &#8211; El truco para contar sueños y pesadillas
        
        Lo he mencionado varias veces en los puntos anteriores, pero lo he dejado para el final ya que es un mero aunque interesante tip.
        
        Cuando registramos un sueño o una pesadilla, estaría bien **dar al usuario un contador de cuantos se han registrado de cada tipo en el mundo.**
        
        Para ilustrar un ejemplo de contador personalizado, ya que en topoos (ha nacido hace poco) aún no hay una operación para almacenar contadores, **vamos a utilizar un Punto de Interés _secreto_**. Este es el que registramos en el **Paso 4** de configuración.
        
        **Lo que haremos será <a title="Warnings en topoos" href="http://docs.topoos.com/reference/poi-warnings/" target="_blank">registrar warnings</a> en este Punto de Interés.** Cuando registremos una pesadilla, registraremos un warning de **Duplicado**, cuando registremos un sueño, registraremos un Warning de **Cerrado**.
        
        Este punto de interés no vale para nada más, pero cuando querramos obtener este contador global, bastará con pedir la información del Punto de interés a topoos y **leer estos dos contadores** de warnings.
        
        Digo que es un truco porque es evidente que los warnings fueron ideados con otro propósito, pero si no los vamos a utilizar del modo &#8220;oficial&#8221; es un truco muy resultón, ¿no creeis?.
        
        #### 8 &#8211; Resultado
        
        Aqui tenéis el enlace al Google Play para descargar la aplicación o ver algunas capturas:
        
        > <a title="Google Play Share Your Dreams " href="https://play.google.com/store/apps/details?id=com.devergence.shareyourdreams" target="_blank">https://play.google.com/store/apps/details?id=com.devergence.shareyourdreams</a>
        
        #### 9 &#8211; Conclusión
        
        ¡Ya está!
        
        ¿Que os ha parecido, **fácil o difícil**?
        
        ¿**Se os ocurren otras aplicaciones interesantes basadas en geolocalización**?
        
        ¿Creeis que hay algún punto que debería ser mejor explicado?
        
        En cualquier caso, **un saludo a todos**,
        
        y ya que estamos en estas fechas&#8230; ¡que tengáis un **próspero desarrollo en Android** en el 2013!