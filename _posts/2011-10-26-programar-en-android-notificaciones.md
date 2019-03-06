---
id: 190
title: 'Programar en Android: Notificaciones'
date: 2011-10-26T21:10:58+00:00
author: findemor
layout: post
guid: http://blog.findemor.es/?p=190
permalink: /2011/10/programar-en-android-notificaciones/
image: /wp-content/uploads/2011/10/notificaciones_android_blog_findemor-64x64.jpg
categories:
  - Android
  - Desarrollo
  - Java
---
En esta Guía vamos a aprender **a preguntar a un servidor en segundo plano**, **y a mostrar un aviso en la barra de notificaciones** en caso de que el servidor nos entregue cierta respuesta, por ejemplo cuando la información consultada haya cambiado, incluso cuando la aplicación no está mostrandose. A diferencia de las notificaciones Push de iOS, en Android **la aplicación debe realizar las consultas de forma activa** en segundo plano. Además veremos como hacer que **brille el led** y **activaremos la vibración**. De forma colateral, **aprenderemos a hacer hilos que ejecuten tareas en segundo plano** para no bloquear el dispositivo.

En esta guía se dan por supuestos algunos conceptos básicos de programación, y en concreto Java y el SDK de Android, aún así los bloques de código están muy explicados y si tenéis alguna duda no dudéis en comentarlo.

#### <!--more-->¿Más o menos como funciona una notificación en Android?

Como comentábamos anteriormente, en Android (a diferencia de iOS y sus notificaciones Push certificadas) **es la aplicación la que debe interrogar al servidor** cuando suceda determinado evento (que generalmente será un **temporizador**), para comprobar si la información consultada ha cambiado (por ejemplo) y mostrar una notificación al usuario de forma que éste entienda que hay cambios en la aplicación, y que debe abrirla cuando el usuario pulse en ella.

Esta notificación puede ir acompañada de un completo despliegue de luz y color en forma de leds parpadeando con distintos colores o vibración, pero el dispositivo que ejecute la aplicación no siempre dispondrá del hardware necesario para ello.

#### ¿Qué vamos a hacer?

**Vamos a explicar el código imprescindible** para hacer una aplicación que haga lo siguiente:

<p style="padding-left: 30px;">
  Abre una <strong>ventana donde podemos activar un servicio</strong> que:
</p>

<p style="padding-left: 30px;">
  <strong>Cada 30 segundos comprobará si un numero elegido al azar es par </strong>o impar.
</p>

<p style="padding-left: 60px;">
  <strong>Si el número es par mostrará una notificación</strong>.
</p>

<p style="padding-left: 60px;">
  Si el número es impar no hará nada.
</p>

Realizará estas tareas **aunque la aplicación no esté mostrándose** en la pantalla. De hecho, aunque no está explicado en detalle en esta guía, es posible hacer que ni siquiera haya que ejecutar la aplicación para que comiencen a lanzarse notificaciones. Hablaremos un poco de ello al final.

Es una aplicación ciertamente inútil, pero no añade una lógica más compleja que nos haga perder el foco de nuestro objetivo: aprender a mostrar notificaciones. Dar el salto de esto a mostrar notificaciones cuando un recurso online ha cambiado es muy sencillo, pero si veo que hay muchas dudas lo ampliaré en una futura guía.

#### Paso 1 &#8211;  Implementar la tarea que va a lanzar la notificación

Lo primero que vamos a hacer es **crear el servicio que, ejecutado cada cierto tiempo, determinará si debe o no lanzarse una notificación**.

Para ello, creamos una clase a la que llamaremos **alarmChecker** y que **extiende la clase Service e implementa Runnable,** esto lo haremos así para tener una interfaz en la que podamos **ejecutar un proceso en segundo plano**.

En el método **run()** generaremos un **numero aleatorio,** y **comprobaremos si es par** o no, en cualquier caso **lanzaremos un mensaje al manejador** indicando que numero hemos generado.

En el método **handler (el manejador) escuchamos** los mensajes del método run(), y si hemos recibido un evento de tipo **MSG\_KEY\_ES_PAR**, llamamos al método Notificar().

El método **Notificar()** crea un objeto **Intent** que será el que se encargue de **abrir la Actividad que nosotros deseemos** cuando el usuario **haga click en la notificación**. Después creamos la notificación, dandole un texto para que se muestre en la barra y un icono desde la carpeta de recursos de Android.

A continuación configuramos la notificación, añadiendole **sonido**, **vibración** (**OJO** porque para utilizar la vibración hay que declarar el permiso correspondiente en el manifest), **luz**, color de la luz, tipo de parpadeo, etc.  
**Finalmente lanza la notificación** asignando un ID.

Este **servicio debe declararse en el manifest** para que pueda ser ejecutado.

El resto de métodos de la clase son necesarios por la definición de la interfaz implementada, aunque no es necesario explicarlos ahora.

Podeis aprender mucho más sobre notificaciones en la <a title="Notification Manager" href="http://developer.android.com/reference/android/app/NotificationManager.html" target="_blank">web de desarrolladores de Android</a>.

Así queda la clase

[sourcecode language=&#8221;java&#8221; collapse=&#8221;true&#8221;]

public class alarmChecker extends Service implements Runnable {

public static final int APP\_ID\_NOTIFICATION = 0;  
private NotificationManager mManager;

private final int MSG\_KEY\_ES_IMPAR = 1;  
private final int MSG\_KEY\_ES_PAR = 2;

/**  
* Método del hilo asíncrono, que obtiene un numero aleatorio y comprueba su paridad  
*/  
public void run() {

final Random myRandom = new Random();

int numeroAleatorio = myRandom.nextInt();

Log.e("alarmChecker", "se genero un " + numeroAleatorio);  
if (numeroAleatorio % 2 > 0)  
{  
//Respondemos que es impar  
handler.sendEmptyMessage(MSG\_KEY\_ES_IMPAR);  
}else  
{  
//Respondemos que es par  
handler.sendEmptyMessage(MSG\_KEY\_ES_PAR);  
}  
}

/**  
* Procesa eventos desde el hilo run  
*/  
private Handler handler = new Handler() {  
@Override  
public void handleMessage(Message msg) {  
switch (msg.what){  
case MSG\_KEY\_ES_PAR: //Hemos obtenido un numero par  
Notificar();  
break;  
}  
}  
};

/**  
* prepara y lanza la notificacion  
*/  
private void Notificar() {

//Prepara la actividad que se abrira cuando el usuario pulse la notificacion  
Intent intentNot = new Intent(this, retornoActivity.class);

//Prepara la notificacion  
Notification notification = new Notification(R.drawable.icon, "Hay cambios", System.currentTimeMillis());  
notification.setLatestEventInfo(this, getString(R.string.app_name), getString(R.string.notified),  
PendingIntent.getActivity(this.getBaseContext(), 0, intentNot, PendingIntent.FLAG\_CANCEL\_CURRENT));

//Le añade sonido  
notification.defaults |= Notification.DEFAULT_SOUND;  
//Le añade vibración  
notification.defaults |= Notification.DEFAULT_VIBRATE;

//Le añade luz mediante LED  
notification.defaults |= Notification.DEFAULT_LIGHTS;

//La notificación se detendrá cuando el usuario pulse en ella  
notification.flags = Notification.FLAG\_AUTO\_CANCEL;

//Intenta establecer el color y el parpadeo de la bombilla lED  
try  
{  
notification.ledARGB = 0xff00ff00;  
notification.ledOnMS = 300;  
notification.ledOffMS = 1000;  
notification.flags |= Notification.FLAG\_SHOW\_LIGHTS;  
}catch(Exception ex)  
{  
//Nothing  
}

//Lanza la notificación  
mManager.notify(APP\_ID\_NOTIFICATION, notification);

}

//interface events

}  
[/sourcecode]

Así queda nuestro manifest

[sourcecode language=&#8221;xml&#8221;]  
<?xml version="1.0" encoding="utf-8"?>  
<manifest xmlns:android="http://schemas.android.com/apk/res/android"  
package="com.example.findemor.push"  
android:versionCode="1"  
android:versionName="1.0">  
<uses-permission android:name="android.permission.VIBRATE" />  
<application android:icon="@drawable/icon" android:label="@string/app_name">  
<activity android:name=".mainActivity"  
android:label="@string/app_name">  
<intent-filter>  
<action android:name="android.intent.action.MAIN" />  
<category android:name="android.intent.category.LAUNCHER" />  
</intent-filter>  
</activity>  
<activity android:name=".retornoActivity" />  
<service android:name=".alarmChecker" />  
</application>  
<uses-sdk android:minSdkVersion="7" />

</manifest>  
[/sourcecode]

#### Paso 2 &#8211; Ejecutar la comprobación de notificaciones

**Ya hemos implementado en el paso anterior el servicio que lanza notificaciones** cuando lo considera oportuno, **pero no lo estamos ejecutando** en ningún sitio.

Para ello, en nuestra **clase Main**, a la que he llamado **mainActivity** y que **es la primera que se ejecuta al arrancar la aplicación**, tendrá un **botón** que mostrará el mensaje &#8220;Alarma desactivada&#8221; cuando no esté activa, y &#8220;Alarma activada&#8221; cuando sí lo esté. **Al pulsar este botón, se iniciará el servicio que implementamos en el Paso 1**, y cuando la aplicación no esté en pantalla ese servicio se seguirá ejecutando cada cierto tiempo.

La forma de **hacer que un servicio se ejecute** cada cierto tiempo es **pidiendo al sistema operativo** (Android) q**ue nos avise cuando ese tiempo haya pasado**. En Android esto se hace **suscribiendose al sistema de alarmas &#8220;AlarmManager&#8221;**. Vamos a ver como hacer esto.

Así queda el código de la clase **mainActivity**.

[sourcecode language=&#8221;java&#8221;]  
public class mainActivity extends Activity {

private static PendingIntent pendingIntent;

Button m_btnAlarma = null;

/*\* Called when the activity is first created. \*/  
@Override  
public void onCreate(Bundle savedInstanceState) {  
super.onCreate(savedInstanceState);  
setContentView(R.layout.main);

m_btnAlarma = ((Button)findViewById(R.id.btnAlarm));  
m_btnAlarma.setOnClickListener(  
new View.OnClickListener() {  
public void onClick(View view) {  
CambiarEstadoAlarma();  
}});  
}

/**  
* Desactiva o activa la alarma, estableciendola al estado contrario del actual  
*/  
private void CambiarEstadoAlarma()  
{  
if (pendingIntent == null)  
{  
//La alarma está desactivada, la activamos  
ActivarAlarma();  
}else  
{  
//La alarma está activada, la desactivamos  
DesactivarAlarma();  
}  
}

/**  
* Desactiva la alarma  
*/  
private void DesactivarAlarma()  
{  
AlarmManager alarmManager = (AlarmManager)getSystemService(ALARM_SERVICE);  
alarmManager.cancel(pendingIntent);

m_btnAlarma.setText(getString(R.string.btnAlarmOFF));  
pendingIntent = null;

Toast.makeText(mainActivity.this, "Alarma detenida", Toast.LENGTH_LONG).show();

}

/**  
* Activa la alarma  
*/  
private void ActivarAlarma()  
{

int comprobacionIntervaloSegundos = 30;

Intent myIntent = new Intent(mainActivity.this, alarmChecker.class);  
pendingIntent = PendingIntent.getService(mainActivity.this, 0, myIntent, 0);

AlarmManager alarmManager = (AlarmManager)getSystemService(ALARM_SERVICE);

Calendar calendar = Calendar.getInstance();  
calendar.setTimeInMillis(System.currentTimeMillis());  
calendar.add(Calendar.SECOND, 10);  
alarmManager.setRepeating(AlarmManager.RTC_WAKEUP, calendar.getTimeInMillis(), comprobacionIntervaloSegundos * 1000, pendingIntent);

m_btnAlarma.setText(getString(R.string.btnAlarmON));

Toast.makeText(mainActivity.this, "Alarma iniciada", Toast.LENGTH_LONG).show();

}

}

[/sourcecode]

Así queda el Layout **&#8220;main.xml&#8221;**

[sourcecode language=&#8221;xml&#8221;]  
<?xml version="1.0" encoding="utf-8"?>  
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"  
android:orientation="vertical"  
android:layout\_width="fill\_parent"  
android:layout\_height="fill\_parent"  
>  
<Button android:text="@string/btnAlarmOFF"  
android:id="@+id/btnAlarm"  
android:layout\_width="fill\_parent"  
android:layout\_height="wrap\_content"></Button>  
</LinearLayout>  
<button></button>

[/sourcecode]

En el método **onCreate(…)** obtenemos el **botón** del Layout y le indicamos que cuando se produzca el evento click, **cambie el estado de la alarma**.

En el método **CambiarEstadoAlarma(), comprobamos si la alarma esta activa** o no. Cuando la alarma se activa, un objeto que hemos declarado como **privare static PendingIntent,** tendrá valor, si no lo tiene, es que la alarma no está activa. Por lo tanto, comprobamos su valor y activamos o desactivamos la alarma en consecuencia.

En el método **DesactivarAlarma()**, **obtenemos la instancia del AlarmManager** del sistema, que gestiona las alarmas, y le **solicitamos que cancele la nuestra** (pasando como parámetro el **pendingIntent** que contiene nuestra alarma) le asignamos valor nulo a pendingIntent y cambiamos el texto del botón.

En el método **ActivarAlarma()**, creamos el **Intent** que será nuestra alarma, pasándole la clase que se encarga de comprobar si hay notificaciones. Obtenemos después una **instancia al AlarmManager del sistema** y establecemos una alarma que **se repetirá cada cierto tiempo** (con el método **setRepeating**) pasando el tiempo del intervalo (en milisegundos), en el ejemplo se comprobará cada 30 segundos. Finalmente cambiamos el texto del botón para que ahora ponga &#8220;Alarma activada&#8221;).

Como veis, es bastante fácil.

#### Paso 3 &#8211; ¿Donde vamos cuando el usuario pulsa la notificación?

Tal y como hemos definido en el Paso 1, **cuando el usuario pulsa la notificación**, ésta **llama a la actividad retornoActivity**. Esta actividad también debe estar definida en el manifest.  
Esta actividad no hace nada en el ejemplo, **simplemente muestra un mensaje**.

#### Paso 4 &#8211; ¡A probar!

Es **hora de ejecutar la aplicación**, darle al botón de activar alarma, y pulsar el botón inicio y seguir usando el teléfono con normalidad.

Comprobaremos como se muestran notificaciones cuando el sistema tiene a bien generar un numero aleatorio par (cada 30 segundos generará uno, y si hay suerte… será par y veremos la notificación).

<div id="attachment_202" style="width: 310px" class="wp-caption aligncenter">
  <a href="http://blog.findemor.es/wp-content/uploads/2011/10/notificaciones_android_blog_findemor.jpg"><img class="size-medium wp-image-202" title="notificaciones_android_blog_findemor" src="http://blog.findemor.es/wp-content/uploads/2011/10/notificaciones_android_blog_findemor-300x248.jpg" alt="notificacion en android" width="300" height="248" srcset="http://blog.findemor.es/wp-content/uploads/2011/10/notificaciones_android_blog_findemor-300x248.jpg 300w, http://blog.findemor.es/wp-content/uploads/2011/10/notificaciones_android_blog_findemor-150x124.jpg 150w" sizes="(max-width: 300px) 100vw, 300px" /></a>
  
  <p class="wp-caption-text">
    Ejemplo de notificación
  </p>
</div>

#### Paso 5 &#8211; El código

[Aquí](http://blog.findemor.es/resources/EvenNotification.zip) tenéis el código fuente completo del ejemplo, por si algo no funciona.

<p style="padding-left: 30px;">
  <a href="http://blog.findemor.es/resources/EvenNotification.zip">http://blog.findemor.es/resources/EvenNotification.zip</a>
</p>

#### Paso 6 Tip &#8211; ¿Y si queremos acceder a un recurso online…?

**Cuando queremos realizar una comprobación** en un servidor al que para acceder necesitamos **acceso a internet**, basta con sustituir el código correspondiente en la clase alarmChecker, de forma que en lugar de generar un número aleatorio, **se conecte a ese recurso online** y tome las decisiones oportunas con los datos recibidos. Por supuesto, habrá que añadir el permiso correspondiente al manifest para acceder a internet.

Sin embargo **nos daremos cuenta de un problema**… cuando el móvil pasa a estado de **reposo**, la conexión a **internet se desactiva** para reducir el consumo de batería, y cuando nuestro servicio alarmChecker intente acceder al recurso online, **se producirá un error** porque no podrá hacerlo.

**Un truco** para solucionar este problema, es realizar varios intentos, es decir:

**Intentamos acceder a internet para conseguir el recurso y realizar la comprobación**… si lo hemos conseguido, ¡estupendo!, pero si no… si se trata de un problema de conectividad, **esperamos unos segundos (ejecutando un sleep) y después volvemos a intentarlo**. Lo normal es que después de la primera comprobación, Android arranque la WIFI o el chip 3G, y aunque sea demasiado tarde para nuestro primer intento, el segundo conseguirá acceder a internet.

Por supuesto **hay que tener mucho cuidado** al limitar el numero de intentos, no vaya a ser que generemos un bucle infinito que queme literalmente nuestra batería.  
Pero bueno, esto es **solo un tip**, hay muchas cosas que mejorar de este resumen, y si alguien tiene **propuestas mejores** para solucionar este problema de conectividad, son totalmente **bienvenidas**.<img src="http://blog.findemor.es/wp-includes/images/smilies/simple-smile.png" alt=":)" class="wp-smiley" style="height: 1em; max-height: 1em;" /> 

#### Paso 7 Tip &#8211; ¿Y si apago el dispositivo?

Evidentemente en nuestro ejemplo, si el dispositivo se reinicia, o si forzamos el cierre del servicio, dejaran de generarse notificaciones.

En Android es posible arrancar un servicio cuando el dispositivo se ha arrancado (justo despues de arrancar el sistema pero parece ser que antes de montar las unidades de almacenamiento externo, ojo con esto si la aplicación se esta ejecutando desde ahí o si debe acceder).

Esto se hace mediante los **BroadcastReceiver** y la acción **BOOT_COMPLETED**.

En este blog <a rel="nofollow" href="http://androidgps.blogspot.com/2008/09/starting-android-service-at-boot-time.html" target="_blank">http://androidgps.blogspot.com</a> teneis un buen ejemplo<img src="http://blog.findemor.es/wp-includes/images/smilies/simple-smile.png" alt=":)" class="wp-smiley" style="height: 1em; max-height: 1em;" />