#¿Que es una RNA? (Red Neuronal ARtificial)

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
