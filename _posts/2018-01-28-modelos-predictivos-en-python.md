---
id: 512
title: Modelos predictivos en Python
date: 2018-01-28T13:40:52+00:00
author: findemor
layout: post
guid: http://blog.findemor.es/?p=512
permalink: /2018/01/modelos-predictivos-en-python/
image: /wp-content/uploads/2018/01/descarga.png
categories:
  - Desarrollo
  - machine-learning
  - python
tags:
  - AI
  - estadística
  - machine-learning
  - python
---
Continúo este repaso, cuaderno de notas, apuntes, (o como lo queramos llamar) a la ciencia de datos usando Python. En esta ocasión me voy a centrar en los modelos predictivos, como la regresión lineal y polinómica (los modelos más habituales, pero no por ello poco interesantes), así como la regresión multivariante y los modelos multinivel.

Hace algun tiempo hice un curso pero que muy recomendable (pondré el enlace al final del post), del profesor Andrew Ng, que explicaba estos y otros conceptos mucho más avanzados, desde un punto de vista matemático y teórico&#8230; en este post no lo veremos así, sino sus aplicaciones prácticas y nos centraremos en su implementación en Python.

<!--more-->

Todo lo que vamos a ver en este post se centra fundamentalmente en intentar definir una función tal que se ajuste a los valores que hayamos podido obtener de casos reales (las observaciones). Esa función nos permitiría &#8220;predecir&#8221; nuevos valores que aún no hayamos visto.

## Regresión Lineal

La regresión lineal (o Linear Regression en inglés), intentará calcular una linea recta que se ajuste todo lo posible a nuestro conjunto de observaciones.

¿Como se calcula? Internamente, la regresión lineal intenta establecer los parámetros que definen una función lineal ( y = **m**x + **b** ) tal que se minimice la suma de los errores cuadráticos de las observaciones a la linea definida por dicha función (se minimice la distancia de cada observación a la linea). En realidad es algo sencillo, pero aún así no tenemos que preocuparnos demasiado por ello ya que Python lo hará por nosotros.

Veamos un ejemplo, generando algunos datos (completamente inventados y al azar, por supuesto) de cuanto pesa una persona en función de su altura:

[python]  
%matplotlib inline  
import numpy as np  
from pylab import *

\# Generamos datos "aleatorios"  
altura = np.random.normal(170, 20, 1000)  
peso = altura / 2.5 + np.random.normal(0, 1, 1000)

\# scatter(altura,peso) ## Si quisieramos mostrar los datos sin la función de ajuste

\# Con esta función tan sencilla calculamos la regresión lineal  
from scipy import stats  
slope, intercept, r\_value, p\_value, std_err = stats.linregress(altura, peso)

\# Ahora lo representamos pintando también la regresión lineal  
\# como una linea roja sobre las observaciones (en azul)  
import matplotlib.pyplot as plt

def predict(x):  
return slope * x + intercept

fitLine = predict(altura)

plt.scatter(altura, peso)  
plt.plot(altura, fitLine, c=&#8217;r&#8217;)  
plt.show()  
[/python]

<a href="http://blog.findemor.es/wp-content/uploads/2017/12/regresion-lineal.png" rel="attachment wp-att-513"><img class="aligncenter size-medium wp-image-513" src="http://blog.findemor.es/wp-content/uploads/2017/12/regresion-lineal-300x200.png" alt="regresion-lineal" width="300" height="200" srcset="http://blog.findemor.es/wp-content/uploads/2017/12/regresion-lineal-300x200.png 300w, http://blog.findemor.es/wp-content/uploads/2017/12/regresion-lineal.png 378w" sizes="(max-width: 300px) 100vw, 300px" /></a>

&nbsp;

Hay más formas de calcular este ajuste: por ejemplo, si en lugar de trabajar con dos dimensiones, lo hiciésemos con datos tridimensionales, se aplica a menudo el método del Gradiente Descendiente, que itera para encontrar la linea que mejor se ajusta el contorno definido por los datos en una superficie, con un impacto computacional mayor. **Este método se usa a menudo en datos con muchas dimensiones**.

### Medir el error

Algo que nunca debemos olvidar es que los modelos con los que vamos a trabajar cuando nos enfrentamos a problemas de esta naturaleza (data science, machine learning&#8230;) rara vez serán exactos, y lo que suelen ofrecer son resultados probables, estimaciones, etc. razón por la cual siempre debemos disponer de algún mecanismo que nos permita medir el error que producen nuestros cálculos, tanto para saber como de buenos son nuestros resultados, como para compararlos de una forma objetiva y cuantificable con la aplicación de otros métodos alternativos que puedan funcionar de forma distinta dependiendo de las propiedades de nuestro conjunto de datos.

#### Coeficiente de determinación (R-Cuadrado o R^2)

Esta forma de medición es: la fracción de la variación total de Y que se genera en nuestro modelo. Esta medición es aplicable en problemas de regresión lineal sencilla.

> Error = 1 &#8211; ( suma de errores al cuadrado) / (suma de la variación al cuadrado de la media)

El error podrá ser un valor de 0 a 1, 0 significa que hay un error máximo, y 1 es que se capturó totalmente la varianza de los datos en el ajuste lineal, el resultado perfecto.

En el ejemplo anterior, su cálculo es tan sencillo como leer el valor del parámetro devuelvo por la función durante el calculo de la regresión lineal, de nombre **r_value**.

[python]  
\# Lo elevamos al cuadrado para obtener R^2  
r_value ** 2  
\# OUT: 0.98554872784874969  
[/python]

Como podemos ver, el valor es muy cercano a uno, lo que significa que nuestra función se ajusta casi perfectamente a las observaciones. Es una buena aproximación.

## Regresión polinómica

En muchas ocasiones nuestros datos no se ajustan a una linea recta, sino que describen curvas más o menos complejas. En estos casos en los que las relaciones no son lineales podemos realizar un ajuste a una función polinómica del orden que consideremos que se ajustará mejor a la complejidad de las curvas descritas por nuestros datos.

  * Polinomio de segundo orden: y = **a**x^2+**b**x+**c**
  * Polinomio de tercer oden: y = **a**x^3 + **b**x^2 + **c**x + **d**

Polinomios de mayor nivel producen curvas más complejas&#8230; aunque hay que intentar no sobrepasarse, ya que se puede producir un overfitting, es decir, nos ajustamos tanto a los datos de nuestras muestras que aparentemente se produce un ajuste perfecto, pero si nuestras muestras tienen muchas varianzas, probablemente no sirva como modelo predictivo, y tenga grandes errores cuando aparezcan nuevas muestras.

Lo ideal es intentar visualizar o al menos comprender los datos con los que contamos primero, para imaginar como de compleja será realmente la curva que subyace aparentemente bajo estos datos. Si conseguimos visualizar esta curva, podemos comprender si se está ajustando a los outliers, o si está realizando una correcta generalización de los datos de muestra. Tener un error bajo en el ajuste polinómico significa únicamente que nos estamos ajustando a los datos de muestra&#8230; no que vayamos a realizar predicciones correctas.

[python]  
%matplotlib inline  
from pylab import *  
import numpy as np

\# creamos algunas muestras aleatorias, en las que el numero de ventas de productos  
\# se reduzca a medida que aumenta su precio  
np.random.seed(2)  
itemPrices = np.random.normal(3.0, 1.0, 1000)  
purchaseAmount = np.random.normal(50.0, 10.0, 1000) / itemPrices

\# calculamos la curva polinomica de 4 grado que se ajusta a los datos  
\# usando la funcion polyfit  
x = np.array(itemPrices)  
y = np.array(purchaseAmount)

p4 = np.poly1d(np.polyfit(x, y, 4))

\# pintamos la muestra y la funcion polinomica en rojo para ver como se ajusta  
import matplotlib.pyplot as plt

xp = np.linspace(0, 7, 100)  
plt.scatter(x, y)  
plt.plot(xp, p4(xp), c=&#8217;r&#8217;)  
plt.show()  
[/python]

El resultado, como vemos a continuación, es una función que parece ajustarse bastante correctamente a nuestros datos de muestra. No parece haber un overfitting, ya que en los espacios entre las muestras y los outliers (el primer y el ultimo punto, por ejemplo) la curva no describe &#8220;subidas y bajadas&#8221; sin sentido.

### <a href="http://blog.findemor.es/wp-content/uploads/2018/01/descarga.png" rel="attachment wp-att-516"><img class="aligncenter size-full wp-image-516" src="http://blog.findemor.es/wp-content/uploads/2018/01/descarga.png" alt="Ajuste polinomico" width="378" height="252" srcset="http://blog.findemor.es/wp-content/uploads/2018/01/descarga-300x200.png 300w, http://blog.findemor.es/wp-content/uploads/2018/01/descarga.png 378w" sizes="(max-width: 378px) 100vw, 378px" /></a>  
Medir el error

Con lo que sabemos hasta ahora, podemos medir el error utilizando el R^2 SCORE.

[python]  
from sklearn.metrics import r2_score

r2 = r2_score(y, p4(x))  
print(r2)

\# OUT: 0.82937663963  
[/python]

Como sabemos, si se acerca a 0 el ajuste es pésimo, y cuanto más cerca de 1 esté, mejor se ajusta a nuestros datos de meustra. En este caso el valor indica que es un ajuste bastante correcto.

## Regresión multivariante

En los ejemplos que hemos visto hasta ahora, hemos realizado ajustes a datos que variaban en función de una única variable. Sin embargo en muchos casos lo que intentamos predecir varia en función de muchas variables que tienen influencia en ello. Por ejemplo, en el caso de la venta de viviendas, el precio puede variar en función de los años que lleva construida, los metros cuadrados, el numero de baños, número de chimeneas, de plantas&#8230; en este caso tendremos que usar un tipo de regresión que se ajusta en función de un gran numero de variables, y que se llama _**regresión multivariante**_ o_ **regresión múltiple**_. Por ejemplo:

> precio = a + b1\* años + b2 \* metros + b3 * baños

Los coeficientes b1, b2 y b3 nos permiten determinar como de importante es cada factor, sin embargo, para que la formula tenga sentido, cada uno de los 3 factores años, metros y baños deben estar normalizados, es decir, estar en escalas similares. Si esto se cumple y están normalizados, podremos seguir utilizando el R^2 como medida de ajuste a los datos de muestra.

Es importante tener en cuenta que este tipo de regresión no puede emplearse con variables o características que sean dependientes de otras que formen parte de la fórmula. Por ejemplo, en este caso, parece lógico que el número de baños dependa de la superficie en metros de la casa, en tal caso la formula no tendrá en cuenta esta dependencia por lo que podría arrojar predicciones erróneas&#8230; es importante detectar estas dependencias y eliminar todas las características dependientes que no sean realmente relevantes.

### Ejemplo

Para ilustrar esto, voy a utilizar un ejemplo del curso que recomiendo realizar si os interesa profundizar en este tema, os dejo el enlace aquí: <a href="https://www.udemy.com/data-science-and-machine-learning-with-python-hands-on/" target="_blank">https://www.udemy.com/data-science-and-machine-learning-with-python-hands-on/</a>

Partimos de un documento Excel que contiene un catálogo de vehículos con algunas de sus características:

<a href="http://blog.findemor.es/wp-content/uploads/2018/01/Captura.jpg" rel="attachment wp-att-518"><img class="aligncenter size-full wp-image-518" src="http://blog.findemor.es/wp-content/uploads/2018/01/Captura.jpg" alt="Captura" width="835" height="356" srcset="http://blog.findemor.es/wp-content/uploads/2018/01/Captura-300x128.jpg 300w, http://blog.findemor.es/wp-content/uploads/2018/01/Captura-768x327.jpg 768w, http://blog.findemor.es/wp-content/uploads/2018/01/Captura.jpg 835w" sizes="(max-width: 835px) 100vw, 835px" /></a>

Lo que vamos a hacer es utilizar algunas de sus características ordinales (numero de cilindros, puertas, etc) para obtener un modelo predictivo de su precio. Vamos a omitir las características categóricas, como el modelo, ya que no hay una forma rápida de convertir esto en factores que emplear en nuestras fórmulas (podria hacerse, asignando valores que tengan algun orden y significado a cada una de las categorías).

[python]  
import statsmodels.api as sm  
from sklearn.preprocessing import StandardScaler  
scale = StandardScaler()

\# determinamos que queremos calcular (variable y)  
\# y a partir de que caracteristicas (variable X)  
X = df[[&#8216;Mileage&#8217;, &#8216;Cylinder&#8217;, &#8216;Doors&#8217;]]  
y = df[&#8216;Price&#8217;]

\# Aplicamos la funcion que nos normaliza automaticamente todos los valores  
\# para que sean comparables y los coeficientes b1, b2 y b3 tengan sentido  
X[[&#8216;Mileage&#8217;, &#8216;Cylinder&#8217;, &#8216;Doors&#8217;]] = scale.fit\_transform(X[[&#8216;Mileage&#8217;, &#8216;Cylinder&#8217;, &#8216;Doors&#8217;]].as\_matrix())

print (X)

\# OUT: dataset de entrenamiento del modelo OLS  
Mileage Cylinder Doors  
0 -1.417485 0.527410 0.556279  
1 -1.305902 0.527410 0.556279  
2 -0.810128 0.527410 0.556279  
3 -0.426058 0.527410 0.556279  
4 0.000008 0.527410 0.556279  
5 0.293493 0.527410 0.556279  
6 0.335001 0.527410 0.556279

\# Usamos el modelo Ordinary Least Square del paquete statsmodel y creamos el modelo  
\# a partir de las caracteristicas de X y los valores del vector y  
est = sm.OLS(y, X).fit()

est.summary()

[/python]

Los resultados que arroja el modelo OLS serán los siguientes

<div class="cell code_cell rendered unselected" tabindex="2">
  <div class="output_wrapper">
    <div class="output output_scroll">
      <div class="output_area">
        <div class="output_subarea output_html rendered_html output_result">
          <table class="simpletable">
            <caption>OLS Regression Results</caption> <tr>
              <th>
                Dep. Variable:
              </th>
              
              <td>
                Price
              </td>
              
              <th>
                R-squared:
              </th>
              
              <td>
                0.064
              </td>
            </tr>
            
            <tr>
              <th>
                Model:
              </th>
              
              <td>
                OLS
              </td>
              
              <th>
                Adj. R-squared:
              </th>
              
              <td>
                0.060
              </td>
            </tr>
            
            <tr>
              <th>
                Method:
              </th>
              
              <td>
                Least Squares
              </td>
              
              <th>
                F-statistic:
              </th>
              
              <td>
                18.11
              </td>
            </tr>
            
            <tr>
              <th>
                Date:
              </th>
              
              <td>
                Sun, 28 Jan 2018
              </td>
              
              <th>
                Prob (F-statistic):
              </th>
              
              <td>
                2.23e-11
              </td>
            </tr>
            
            <tr>
              <th>
                Time:
              </th>
              
              <td>
                14:18:39
              </td>
              
              <th>
                Log-Likelihood:
              </th>
              
              <td>
                -9207.1
              </td>
            </tr>
            
            <tr>
              <th>
                No. Observations:
              </th>
              
              <td>
                804
              </td>
              
              <th>
                AIC:
              </th>
              
              <td>
                1.842e+04
              </td>
            </tr>
            
            <tr>
              <th>
                Df Residuals:
              </th>
              
              <td>
                801
              </td>
              
              <th>
                BIC:
              </th>
              
              <td>
                1.843e+04
              </td>
            </tr>
            
            <tr>
              <th>
                Df Model:
              </th>
              
              <td>
                3
              </td>
              
              <th>
              </th>
              
              <td>
              </td>
            </tr>
            
            <tr>
              <th>
                Covariance Type:
              </th>
              
              <td>
                nonrobust
              </td>
              
              <th>
              </th>
              
              <td>
              </td>
            </tr>
          </table>
          
          <table class="simpletable">
            <tr>
              <td>
              </td>
              
              <th>
                coef
              </th>
              
              <th>
                std err
              </th>
              
              <th>
                t
              </th>
              
              <th>
                P>|t|
              </th>
              
              <th>
                [0.025
              </th>
              
              <th>
                0.975]
              </th>
            </tr>
            
            <tr>
              <th>
                Mileage
              </th>
              
              <td>
                -1272.3412
              </td>
              
              <td>
                804.623
              </td>
              
              <td>
                -1.581
              </td>
              
              <td>
                0.114
              </td>
              
              <td>
                -2851.759
              </td>
              
              <td>
                307.077
              </td>
            </tr>
            
            <tr>
              <th>
                Cylinder
              </th>
              
              <td>
                5587.4472
              </td>
              
              <td>
                804.509
              </td>
              
              <td>
                6.945
              </td>
              
              <td>
                0.000
              </td>
              
              <td>
                4008.252
              </td>
              
              <td>
                7166.642
              </td>
            </tr>
            
            <tr>
              <th>
                Doors
              </th>
              
              <td>
                -1404.5513
              </td>
              
              <td>
                804.275
              </td>
              
              <td>
                -1.746
              </td>
              
              <td>
                0.081
              </td>
              
              <td>
                -2983.288
              </td>
              
              <td>
                174.185
              </td>
            </tr>
          </table>
          
          <table class="simpletable">
            <tr>
              <th>
                Omnibus:
              </th>
              
              <td>
                157.913
              </td>
              
              <th>
                Durbin-Watson:
              </th>
              
              <td>
                0.008
              </td>
            </tr>
            
            <tr>
              <th>
                Prob(Omnibus):
              </th>
              
              <td>
                0.000
              </td>
              
              <th>
                Jarque-Bera (JB):
              </th>
              
              <td>
                257.529
              </td>
            </tr>
            
            <tr>
              <th>
                Skew:
              </th>
              
              <td>
                1.278
              </td>
              
              <th>
                Prob(JB):
              </th>
              
              <td>
                1.20e-56
              </td>
            </tr>
            
            <tr>
              <th>
                Kurtosis:
              </th>
              
              <td>
                4.074
              </td>
              
              <th>
                Cond. No.
              </th>
              
              <td>
                1.03
              </td>
            </tr>
          </table>
        </div>
      </div>
    </div>
  </div>
</div>

<div class="cell text_cell selected unrendered" tabindex="2">
</div>

<div class="cell text_cell selected unrendered" tabindex="2">
  Como podemos ver, el coeficiente (en valor absoluto) más grande es con diferencia el número de cilindros (cylinder = 5587) lo que significa que esta es la característica más determinante a la hora de predecir el precio de un vehículo, por encima del número de puertas (doors) o el kilometraje (mileage).
</div>

<div class="cell text_cell selected unrendered" tabindex="2">
</div>

<div class="cell text_cell selected unrendered" tabindex="2">
  A veces es fácil darse cuenta de estas dependencias, cuales son más importantes y cuales menos, sin necesidad de recurrir a cosas más complejas que la media. Por ejemplo, si agrupamos los vehiculos del dataset por el numero de puertas y calculamos la media:
</div>

[python]  
y.groupby(df.Doors).mean()

\# OUT: resultado  
Doors  
2 23807.135520  
4 20580.670749  
[/python]

<div class="cell text_cell selected unrendered" tabindex="2">
  Observamos que no hay una gran diferencia en función del numero de puertas. Además, sorprendentemente a menor numero de puertas, mayor valor del vehículo: algo que puede deberse a que el conjunto de datos sea demasiado pequeño o a que los coches deportivos, más caros, tengan menor numero de puertas, incrementando la media.
</div>

&nbsp;

## Modelos multinivel

He mencionado un par de veces que hay que evitar tener en cuenta, en este tipo de regresiones, las dependencias entre las caracteristicas que se consideran. Sin embargo en muchas ocasiones la variabilidad de los datos nace de estas interdependencias, que pueden ser muy complejas.

Imagina por ejemplo de qué depende tu salud: del estado de cada una de tus células, de tus organos, de tu actividad fisica, de tu trabajo, de tu familia (de su propia salud y de los niveles de estrés que te producen). Pero a su vez, cada uno de estos elementos depende de otras cuestiones, como la calidad del aire del lugar donde realizas la actividad física, y esto depende a su vez de cuestiones estatales como las politicas de polucion, energias renovables, etc.

Es solo un ejemplo, pero es evidente que existe una interdependencia jerárquica a varios niveles, para cada una de las características que determinan tu salud. Este tipo de análisis se realiza mediante la aplicación de modelos multinivel, sin embargo es un tema difícil y peliagudo, que merece su propio curso y que nunca he visto aplicado, así que no puedo escribir acerca de él.

## Referencias y créditos

He redactado este artículo a partir de mi aprendizaje en las siguientes fuentes:

  * Curso Data Science: <a href="https://www.udemy.com/data-science-and-machine-learning-with-python-hands-on" target="_blank">https://www.udemy.com/data-science-and-machine-learning-with-python-hands-on </a>