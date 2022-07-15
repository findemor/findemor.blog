# ¿Que es una RNA? (Red Neuronal ARtificial)

Son algoritmos de ML que de alguna manera se inspiran en las redes neuronales de los seres vivos.
Actualmente están muy de moda porque ahora sí existe una gran cantidad de datos disponibles, que este tipo de redes necesitan para obtener buenos resultados, junto con el hecho de que el poder computacional es suficiente incluso en ordenadores particulares.

## El concepto de neurona M-P

La neurona de McCulloch y Pits fue la primera que se diseñó de manera artificial. Esencialmente recibe uno o más valores binarios y devuelve un único valor binario, cuyo valor dependerá del número de valores de entrada que están activos (la salida estará activa cuando el número supere un determinado threshold establecido manualmente).

Las entradas pueden ser excitadores o inhibidoras, en caso de que una entrada inhibidora esté activa, el resultado siempre será inactivo.

![Neurona MP imagen de la Wikipedia](https://upload.wikimedia.org/wikipedia/commons/c/c8/Mccullochpitts.png)  
[Saber más: Neurona MP en Wikipedia](https://es.wikipedia.org/wiki/Neurona_de_McCulloch-Pitts)

Tiene algunas limitaciones importantes, por ejemplo el hecho de que los valores solo puedan ser binarios, que las entradas no puedan tener distintso pesos,  y solo permiten resolver problemas linealmente separables.

Por ejemplo puede comportarse como una puerta lógica AND porque es un problema linealmente separable:

![AND Perceptron en Wikipedia](https://upload.wikimedia.org/wikipedia/commons/thumb/8/82/Computer.Science.AI.Neuron.AND.svg/225px-Computer.Science.AI.Neuron.AND.svg.png)

Pero no puede comportarse como un XOR, ya que no lo es:

![XOR Perceptron en Wikipedia](https://upload.wikimedia.org/wikipedia/commons/thumb/9/93/Computer.Science.AI.Neuron.XOR.svg/225px-Computer.Science.AI.Neuron.XOR.svg.png)

Estas limitaciones forzaron a nuevas versiones de neurona artificial, donde la más destacable es el __Perceptrón__.

[5.28]

## Perceptrón

El percerptrón es un diseño de neurona artificial más avanzado, que añade el concepto de peso especifico (parámetro) a cada uno de los inputs, que además no son solo binarios, sino que acepta valores reales, al igual que la salida (por lo que ya no es una salida binaria). 

El perceptrón puede no estar limitado a una única neurona, sino que puede ser una combinación de varias neuronas, en cuyo caso cada una se denomina TLU (o Threshold Logic Unit).

Además de los N inputs y sus correspondientes pesos, las TLU reciben un input adicional 0 que representa el threshold, de manera que al incluir este peso como parte de la función de agregación (z) ya no es necesario definirlo manualmente.

> z(X) = 1*W0 + X1*W1 + ... XN*WN  

Y la función de activación (a) por lo tanto siempre sería en comparación con 0

> a(z(X)) = 1 si z(X) >= 0 y 0 si z(x) < 0

Este peso generalmente se denomina __bias__ y se introduce como un input más adicional.

![Perceptrón imagen de la Wikipedia](https://upload.wikimedia.org/wikipedia/commons/thumb/b/b0/Perceptr%C3%B3n_5_unidades.svg/1280px-Perceptr%C3%B3n_5_unidades.svg.png)  
[Saber más: Perceptrón en Wikipedia](https://es.wikipedia.org/wiki/Perceptr%C3%B3n)

### Funcionamiento del Perceptrón


En el caso del Perceptrón hay dos funciones de activación que son tan habituales como simples:

* __Heaviside step function__: el resultado es 0 si el resultado de la función de agregación es menor que 0, o es 1 si el resultado de la función de agregación es mayor o igual que 0.

![Heaviside step function en Wikipedia](https://upload.wikimedia.org/wikipedia/commons/thumb/4/4a/Funci%C3%B3n_Cu_H.svg/420px-Funci%C3%B3n_Cu_H.svg.png)

La otra función habitualmente utilizada en el perceptrón es:

* __Sign function__: en este caso la predicción no sería binaria. El resultado sería -1 si el resultado de agregación es menor que 0, 0 si el resultado es 0, y 1 si el resultado era mayor que 0.

![Sign function en Wikipedia](https://upload.wikimedia.org/wikipedia/commons/thumb/4/4f/Signum_function.svg/450px-Signum_function.svg.png)


### Entrenamiento del perceptrón

Para calcular los parametros del perceptron que conforman el modelo de aprendizaje (pesos w1, w2... wN y bias), en primer lugar se inicializan aleatoriamente.
Despues, cogemos alguna de las observaciones de entrenamiento y aplicamos la función de agregación y asignacion con los parametros que tengamos.
Comprobamos si el resultado de la predicción coincide con el correcto según la observación de entrenamiento.
Iterativamente, se utiliza la siguiente función para ir acercando los parametros que inicializamos aleatoriamente a valores que proporcionen cada vez resultados más acertados:

> $W_{i,j} = W_{i,j} + \eta(y_{j} - \hat{y_{j}})x_{i}$

El simbolo $\eta$ es el ratio de aprendizaje o learning rate, que es la velocidad a la que vamos a modificar los parametros para acercarnos al valor optimo. Si es demasiado alto podriamos pasarnos el valor optimo.

Vemos que, segun la formula, hay que modificar el parametro de peso $W_{i,j}$ sumandole al valor actual, el ratio de aprendizaje multiplicado por la diferencia entre el valor que hemos predicho, y el valor de la predicción correcto (el de la observación) y por el input que corresponde a la caracteristica observada.

Iterar sobre esta formula, modificando cada vez los parametros de peso, nos acercará a parametros que cada vez arrojen un numero menor de errores.

### Limitaciones

El perceptrón tiene algunos inconvenientes, por ejemplo el hecho de que no devuelve probabilidades, lo que si podría conseguirse con otras funciones de activación cuyo resultado sea un valor continuo.

Los límites de decisión son lineales, por lo que tampoco puede resolver problemas que son simples, como le ocurria a la Neurona M-P y la puerta lógica XOR.

Debido a estas limitaciones surgieron modelos de Perceptrón con más de una capa, los que se denominan Perceptron Multicapa o Multi-Layer Perceptron.

[6.36 - Perceptrón.ipynb]


## Perceptrón multicapa

El Perceptrón Multicapa, Multilayer Perceptron o Deep feedforward network es uno de los modelos más conocidos y empleados.

Es similar al perceptron simple, con la excepción de que en este caso tiene más de 2 capas, es decir, que además de su primera capa (__input layer__) y su capa de resultado (__output layer__), incluye una o más capas intermedias (__hidden layers__).

![Perceptron multicapa en Wikipedia](https://upload.wikimedia.org/wikipedia/commons/6/64/RedNeuronalArtificial.png)

En un perceptrón multicapa, todas las neuronas (TLUs) están completamente conectadas (__Fully Connected__), es decir las entradas están conectadas a todas las salidas de la capa anterior , y sus salidas a las entradas de todas las neuronas de la capa siguiente. Además, no existen otras conexiones (por ejemplo, las salidas de una neurona nunca actuan como entrada de una neurona de una capa anterior, no existiendo por lo tando feedback o recurrencia). Por esta razón, también reciben el nombre de __Deep Feedforward Network__.

Según la mayoría de la literatura, cuando existen 2 o más capas ocultas, podemos considerar que se trata de una red neuronal profunda, por lo que un perceptrón multicapa con esta configuración se consideraría una __Red Neuronal Profunda__ o __Deep Neural Network__.

### Partes de la red

En redes neuronales como el perceptrón, tendremos ciertas partes esenciales que la forman y que a su vez son las que permiten su entrenamiento y funcionamiento.

El diagrama a continuación muestra cuales son las principales:

![Partes de la Red Neuronal Artificial](../wp-content/uploads/pending/rna_parts.png)

A modo de resumen:

* __Dataset__: es el [conjunto de datos](https://blog.findemor.es/2022-06-09-Inteligencia-artificial-flujo-de-trabajo/#datos), en este caso estoy considerando los datos con los que se realiza el entrenamiento (formados por los input vectors, características o variables independientes, y por las etiquetsa o variables dependientes de salida).
* __Layers__: las capas de neuronas, un percepton simple tendrá una capa de entrada y una de salida, mientras que los multicapa tendrán una o más capas intermedias u ocultas. Las capas están formadas por neuronas, en función del número de variables de entrada y salida, y estarán conectadas de una manera específica.
* __Parámetros__: como ya hemos ido viendo, cada una de las entradas de las neuronas lleva asignado un parámetro de peso, que en último lugar, junto con las definiciones de las funciones de agregación y activación de cada neurona, determina cómo se comportará esta. Cuando hablamos de un modelo de red neuronal nos estamos refiriendo esencialmente a la topología de la red y a los parámetros seleccionados.
* __Predicción__: introduciendo un vector de valores o input variables en la primera capa de la red, juntos con los paramámetros (pesos), obtendremos un valor resultante de salida, o predicción. 
* __Medición del error__: Para saber si la red está "acertando" o no, [existen diferentes estrategias y métricas](https://blog.findemor.es/2022-06-30-Inteligencia-artificial-validar-el-modelo/). En cualquier caso de alguna manera la medición del error consiste en identificar el grado de diferencia que hay entre el valor de la predicción que hemos obtenido para los parametros actuales, y el resultado real que deberíamos haber obtenido (que en caso de un conjunto de datos supervisado podemos conocer porque debería haber sido revisado manualmente).
* __Función de optimización__: el valor de error obtenido permite reajustar los parámetros para que el error obtenido sea cada vez menor. Existen diferentes funciones que permiten obtener nuevos parámetros actualizados para el modelo, ya hemos hablado antes de ello en la sección de [Entrenamiento del perceptrón](!!!!!!!!!!!!!!!!!!!!!!!!)
* __Actualizamos los parámetros__: al actualizar los parametros en base a los resultados de la función de optimización, logramos que el error entre el valor de la predicción y el valor real sea cada vez menor, hasta que tras un cierto número de iteraciones finalmente obtenemos resultado óptimo o suficientemente adecuado para los parámetros. En ese momento tenemos el modelo listo para ser puesto a prueba con el conjunto de test, ¡y posteriormente desplegado en producción! con observaciones nuevas cuya salida desconocemos.

### Función sigmoide

Históricamente había muchos inconvenientes para poder emplear los perceptrones multicapa por la dificultad que suponía su entrenamiento. Afortunadamente gracias a un cambio en la función de activación, que hasta entonces [era la heaviside step function o la sign function](!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!), por una nueva función, la __sigmoide__, lo que abría las puertas a un nuevo método de entrenamiento basado en el algoritmo conocido como __backpropagation__.

> $sigmoid(z) = \frac{1}{1 + e^{-z}}$

![Función sigmoide en Wikipedia](https://upload.wikimedia.org/wikipedia/commons/thumb/6/66/Funci%C3%B3n_sigmoide_01.svg/450px-Funci%C3%B3n_sigmoide_01.svg.png)

Una de las diferencias más importantes con respecto a la función heaviside, es el hecho de que el resultado no es discreto (0 o 1) sino que es un valor continuo, que además podremos interpretar como la probabilidad de que el resultado sea positivo.

Existen otras funciones además de la __sigmoide__ que se emplean actualmente muy a menudo, como la __ReLU__ o la __tangente hiperbólica__.