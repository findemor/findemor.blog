https://www.coursera.org/learn/machine-learning-projects/lecture/yeHYT/why-ml-strategy

## ¿Por qué?

Cuando implementamos un modelo de Machine Learning o Aprendizaje Automático, puede que lleguemos a un punto en el que sea necesario empezar a realizar cambios para afinar la precisión del modelo. En [enlace al post ultimo de xx-deep-learning] vimos algunos tips sobre por donde empezar a trabajar en este ajuste del modelo para conseguir mejores resultados, pero lógicamente probar todas las posibilidades podría llevarnos un tiempo del que seguramente no disponemos.

Además, existen muchas razones por las que los proyectos de IA pueden fracasar, incluso aunque los resultados del modelo sean buenos:
* El proceso es demasiado artesano y dificil de industrializar
* El conocimiento queda en manos de unos pocos y no se traslada al resto de la orgnización
* El equipo o la estrategia de datos se configuran incorrectamente.
* El resultado, aunque técnicamente correcto, está lejos de las expectativas iniciales
* No tener claro cuales eran los objetivos que se pretenden alcanzar realmente con la inversión
* No tener clara la inversión necesaria, o como gestionarla
* Sesgos, calidad de los datos, volumetria...

En los siguientes artículos trataré de volcar mis notas acerca de lo que he aprendido al respecto de como gestionar un proyecto de ML/IA para intentar minimizar algunos de estos posibles problemas.

### Ortogonalización

La ortogonalización consiste en separar el tuning del modelo en distintos elementos que permitan ajustar únicamente una cosa cada vez.

Un ejemplo de ortogonalización es el que se emplea en los ajustes de un proyector, que dispone de distintos botones para enfocar, arreglar la perspectiva, rotar o escalar el tamaño de la imagen. Sin ortgonalización, tendríamos únicamente una rueda, que al girarse ajustaría en distinta cantidad todos esos comportamientos a la vez (por ejemplo giraria 0.3 veces hacia la derecha a la vez que escala la pantalla x 1.2) lo cual complicaría bastante el proceso.

En Machine Learning, hacemos una serie de asunciones:
1. En primer lugar tratamos de ajustar la función de coste al conjunto de entrenamiento
2. Despues, al de validación cruzada.
3. Despues al de pruebas
4. Finalmente comprobamos el rendimiento con casos reales.

En cada uno de esos pasos tenemos distintas "ruedas" que girar para ajustar cada uno de los comportamientos. Debemos identificar esas "ruedas" y aprender a elegir la adecuada.
Por ejemplo, en el punto 1 al trabajar con el conjunto de entrenamiento, podríamos escoger un modelo diferente de aprendizaje, o decidir aumentar el tamaño de la red neuronal, entre otros.

Sin embargo, si no se está comportando bien con el conjunto de validación cruzada, existen otras "ruedas" diferentes que ajustar, como la regularización, o aumentar el tamaño del conjunto de datos, por ejemplo.

Para solucionar problemas al predecir casos reales, podríamos querer ajustar la función de costes, o introducir algunos cambios en el conjunto de datos de entrenamiento.

Contemplando todos estos ejemplos podemos advertir que hay algunas técnicas que no encajan bien en esta __ortogonalización__. Por ejemplo, en el primer paso habríamos podido aplicar tecnicas como el __early stopping__ del que ya hablamos en esta otra publicación. Sin embargo no debería ser nunca nuestra primera opción, ya que estaríamos realizando cambios simultaneos sobre distintos elementos (afectando tanto al conjunto de entrenamiento, como al de validación, al mismo tiempo).

### Objetivos claros

Como mencioné anteriormente, uno de los problemas por los que podría fracasar un proyecto de IA es precisamente no tener unos objetivos claros. Si no se dispone de un método para saber que hemos alcanzado nuestro propósito y en que grado, o no se conoce el dicho propósito (o no todos los interesados esperan lo mismo), entonces sin duda vamos a tener problemas.

Dos métricas que se emplean habitualmente son la __precisión__ y el __recall__. 
* Precision: viene a ser algo así como que de los datos que se etiquetaron con la etiqueta A, cuantos son A realmente.
* Recall: vendría a significar lo opuesto, es decir, de los datos que son realmente A, cuantos se etiquetaron como A por nuestras predicciones.

Entonces, si en un momento dado tenemos un 95% de precisión y un 90% de recall, hacemos cambios en el modelo y obtenemos 90% de precisión y un 95% de recall: ¿Cuál de los dos modelos es mejor? ¿Cuál de ellos deberíamos escoger?

Este es un ejemplo sencillo de como no disponer de una única y sencilla metrica para evaluar el rendimiento de nuestro modelo puede dificultarnos la toma de decisión y hacernos dar bandazos en la búsqueda de una solución mejor.

La solución a este problema de ejemplo, podría consistir en emplear como única metrica el F1 Score. De hecho, F1 Score responde a una formula que emplea la precisión P y el recall R para determinar la eficacia de un modelo (de manera vulgar podríamos decir que es algo así como la media de ambos valores, aunque para ser exactos se trata de la [Media Armónica](https://en.wikipedia.org/wiki/Harmonic_mean)).

#### Optimizing vs Satisficing

Elegir una única metrica no siempre es tan sencillo. Puede que haya características del modelo que estén en unidades diferentes o que sean de tipos tan distintos que la "unificación" no sea posible. Un ejemplo de esto sería un modelo que cuya Precisión mejora a medida que también aumenta el tiempo que tarda en dar respuesta.

En estos casos, es recomendable escoger una única metrica como métrica de optimización (es decir, aquella que permitirá determinar qué modelo es mejor de entre varias opciones), y decidir que el resto de métricas existentes serán métricas de satisfacción. 

Con las __satisficing metrics__ no buscamos comparar los modelos entre si, sino que simplemente estableceremos umbrales (_thresholds_) que determinarán si un modelo es válido o no. En el ejemplo anterior, si tenemos lo siguiente:

|Modelo|Precision|Latencia|
|---|---|---|
|A  |90%|10ms   |
|B  |91%|25ms   |
|C  |99%|1300ms   |

Y definiendo Precisión como métrica de optimización y Latencia como una de las métricas de satisfacción (en este caso, estableciendo por ejemplo un theshold de 1000ms), escogeríamos el modelo B, ya que es el más óptimo que cumple los requisitos de satisfacción.

Esta forma de trabajo __nos permite elegir fácilmente entre modelos incluso son métricas difiles de comparar__, así como __respetar la norma de tener una única métrica de optimización sin dejar de lado otros criterios importantes__ que deban ser respetado en nuestro modelo.

#### Métricas erroneas

En alguna ocasión podría ocurrirnos que la métrica que hemos definido no sea realmente la que estamos buscando. Por ejemplo, imaginemos un modelo que tiene un error muy pequeño, pero cuando cada uno de esos errores es muy grave, frente a uno que tiene un error mayor, pero sus errores son menos graves.

En este caso, la metrica única que hemos definido indicará que el primer modelo es mejor, pero nosotros, los humanos, seguramente prefiramos el segundo.

Cuando nos encontramos frente a un caso como este, lo recomendable es realizar una modificación en la fórmula de la función de error de manera que incorpore este aspecto de los resultados que se estaba obviando. Por ejemplo, si la función que empleabamos era esta:

> $ferr( \hat{y},y ) = \frac{1}{m} \sum_{i=1}^m (y^{i} - \hat{y}^{i})^2$

En la que calculamos el error cuadratico medio (diferencia entre el valor real y el de la predicción). Podríamos incoporar un factor de peso en función de la gravedad subjetiva del error, por ejemplo con la función de pérdida _weighted MSE_.

> $ferr( \hat{y},y ) = \frac{1}{m} \frac{\sum_{i=1}^m w_i (y^{i} - \hat{y}^{i})^2}{\sum_{i=1}^m w_i}$

De manera que la métrica tenga en cuenta este aspecto subjetivo que denota la gravedad del error, con lo que probablemente el segundo modelo, cuyos errores eran menos graves, pase a tener una mejor métrica de optimización.

No obstante esto pone atención a la manera de evaluar y comparar los resultados de cada modelo, pero faltaría una segunda parte, que consistiría en, __una vez reconocida esta circunstancia y redefinida la fórmula, realizar las modificaciones oportunas para continuar optimizando su resultado conforme a la nueva métrica__.

Sería algo así como decir que en el primer paso estamos poniendo la diana en el lugar hacia el que queremos apuntar, y en el segundo, estaríamos ajustando el tiro para acertar cada vez con más precisión en su centro.


### Distribución del conjunto de datos

Otros factores que pueden alterar el correcto funcionamiento del equipo implicado en el desarrollo de un modelo de aprendizaje automático, son las decisiones alrededor del trabajo con el conjunto de datos que se emplea para el entrenamiento, validación cruzada y pruebas.

#### Validación cruzada y pruebas

Un ejemplo sencillo de un problema derivado de los datos, sería: imaginemos que tenemos datos de distintas regiones (la región A y la región B), y usamos los datos de la región A para la validación cruzada y los de la región B como conjunto de pruebas. 

En este caso, los datos de distintas regiones pueden ser tan diferentes entre si que despues de varias iteraciones para afinar el modelo, y someterlo al conjunto de pruebes, encontrariamos unos resultados muy malos.

Una solución a este problema consistiría en combinar los datos de ambas regiones en un unico dataset, y dividirlos en validación cruzada y pruebas escogiendolos de manera aleatoria.

En conclusión, es importante escoger conjuntos de datos para cada propósito que reflejen del mismo modo y recojan toda la informacion que queramos tener en consideración en el futuro.

### Tamaño de los conjuntos de datos

En la literatura del Machine Learning tradicional, __siempre se habla de dividir el conjunto de entrenamiento y pruebas usando una proporcion del 70/30%__. En caso de tener conjuntos de entrenamiento, validación cruzada y pruebas, es habitual oir hablar de cifras parecidas a un __60/20/20%__ para cada uno.

Estas cifras, aunque adecuadas, no siempre tienen porqué ser las recomendables.

El objetivo de elegir estas proporciones es disponer de un volumen de datos que sea representativo y con ello proporcione garantias de que en las evaluaciones de rendimiento que hagamos sobre el modelo reflejen un rendimiento sobre la variedad de casos que encontraremos al someterlo a observaciones del mundo real.

Esas proporciones son adecuadas cuando hablamos de un Machine Learning tradicional, en el que los conjuntos de datos son de 1000 o 10000 registros, por lo que se necesitaba un gran porcentaje de datos para conseguir esa representación en el conjunto de pruebas.

Sin embargo, __en la era del Big Data, estas proporciones pueden no ser adecuadas__, ya que cuando nuestro conjunto de datos original tiene más de un millón de registros, por ejemplo, estariamos creando unos conjuntos de validación cruzada o pruebas excesiva e innecesariamente grandes.

En los __casos en los que tengamos una cantidad tan grande de datos, es razonable utilizar proporciones distintas, por ejemplo 98/1/1% para los tres conjuntos__, aún estaría dando, para 1.000.000 de registros, un total de 10.000 registros para el test, lo cual debería ser suficiente en la mayoría de casos para examinar el rendimiento del modelo.
