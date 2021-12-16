# Gentil introducción a Redes Neuronales de Grafos (*Graph Neural Networks* o *GNN*)



## **Zona de juego GNN**

Hasta este punto hemos conocido los componentes para construir la arquitectura de una GNN; el siguiente paso es ir a la práctica.

Para jugar un poco, utilizaremos el modelo interactivo en la sección **GNN Playground** del [paper](https://distill.pub/2021/gnn-intro/). El modelo interactivo utiliza el conjunto de datos de olores de Leffingwell, que está compuesto por moléculas con percepciones de olor asociadas, con la intención de clasificar si una molécula tiene un olor intenso o no (etiqueta binaria).

La predicción de la relación de una estructura molecular (grafo) con su olor es un problema bastante interesante. Por ejemplo, el ajo y la mostaza, que pueden contener la molécula de alcohol alílico, tienen esta cualidad. La molécula piperitona, que se utiliza a menudo para los caramelos con sabor a menta, también se describe como de olor penetrante.


Para la representación de cada molécula como un grafo, los átomos son nodos que contienen una codificación *one-hot* para su identidad atómica (carbono, nitrógeno, oxígeno, flúor) y los enlaces son aristas que contienen una codificación *one-hot* para su tipo de enlace (simple, doble, triple o aromático).

El modelo interactivo se construye utilizando capas secuenciales de GNN, seguidas de un modelo lineal con una activación sigmoidepara la clasificación. La función de actualización para los atributos del grafo es un MLP de 1 capa (*1-layer of Multilayer Perceptron*) con una función de activación relu y una capa para la normalización de las activaciones.**

Las características que podemos personalizar en este modelo de GNN son:

1. El número de capas GNN, también llamado profundidad (*depth*).

2. La dimensionalidad de cada atributo cuando se actualiza. 

3. La función de agregación utilizada en *pooling*: máximo, media o suma.

4. Los atributos del gráfico que se actualizan, o  los estilos de *message passing*: nodos, aristas y representación global. Estos se controlan mediante interruptores booleanos (activación o desactivación). 

![Dashboard interactivo](/images/dashboard_model.png)

Para entender cómo una GNN aprende una representación de un grafo optimizado para una tarea, es conveniente observar la penúltima capa de la GNN. Estos "grafos embebidos" (*graph embeddings*) son las salidas del modelo GNN justo antes de la predicción. 

![graph out](/images/graph_out.png)

Para la representación de está salida, y dado que estamos utilizando un modelo lineal generalizado para la predicción, un mapeo lineal es suficiente para permitirnos ver cómo el modelo está aprendiendo representaciones alrededor del límite de decisión. 

Los grafos, al estar construidos por vectores de alta dimensión, los reducimos a 2D mediante el análisis de componentes principales (*principal component analysis* o *PCA*). Un modelo perfecto haría visibles los datos etiquetados por separado, pero como estamos reduciendo la dimensionalidad y también tenemos modelos imperfectos, este límite puede ser más difícil de ver.

Ahora que conocemos la zona de juego, podremos interactuar con el modelo. :)

<iframe width="640" height="480" src="https://www.youtube.com/embed/icW-n6riXIQ" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## **Algunas lecciones empíricas de diseño GNN**

Al explorar las opciones de arquitectura anteriores, es posible que haya encontrado que algunos modelos tienen un mejor rendimiento que otros. ¿Hay algunas opciones claras de diseño de GNN que nos darán un mejor rendimiento? Por ejemplo, ¿los modelos GNN más profundos funcionan mejor que los menos profundos? ¿O hay una clara elección entre funciones de agregación? Las respuestas van a depender de los datos, [1] [2], e incluso diferentes formas de caracterizar y construir gráficos pueden dar diferentes respuestas.

Con la siguiente figura, exploramos el espacio de las arquitecturas GNN y el desempeño de esta tarea a través de algunas opciones de diseño importantes: estilo de transmisión de mensajes, dimensionalidad de las incrustaciones, número de capas y tipo de operación de agregación.

Cada punto del diagrama de dispersión representa un modelo: el eje x es el número de variables entrenables y el eje y es el rendimiento.

![Figura 1][img1]

Diagrama de dispersión del rendimiento de cada modelo frente a su número de variables entrenables.

Lo primero que hay que notar es que, sorprendentemente, un mayor número de parámetros se correlaciona con un mayor rendimiento. Los GNN son un tipo de modelo muy eficiente en cuanto a parámetros: incluso para una pequeña cantidad de parámetros (3k) ya podemos encontrar modelos con alto rendimiento.

A continuación, podemos observar las distribuciones de rendimiento agregadas en función de la dimensionalidad de las representaciones aprendidas para diferentes atributos de gráficos.

![Figura 2][img2]

Rendimiento agregado de modelos en diferentes dimensiones de nodo, borde y globales.

Podemos notar que los modelos con mayor dimensionalidad tienden a tener un mejor rendimiento medio y de límite inferior, pero no se encuentra la misma tendencia para el máximo. Algunos de los modelos de mejor rendimiento se pueden encontrar para dimensiones más pequeñas. Dado que una mayor dimensionalidad también va a involucrar un mayor número de parámetros, estas observaciones van de la mano con la figura anterior.

A continuación, podemos ver el desglose del rendimiento en función del número de capas GNN.

![Figura 3][img3]

Gráfico de número de capas frente al rendimiento del modelo y diagrama de dispersión del rendimiento del modelo frente al número de parámetros. Cada punto está coloreado por el número de capas.

El diagrama de caja muestra una tendencia similar, mientras que el rendimiento medio tiende a aumentar con el número de capas, los modelos de mejor rendimiento no tienen tres o cuatro capas, sino dos. Además, el límite inferior de rendimiento disminuye con cuatro capas. Este efecto se ha observado antes, los GNN con un mayor número de capas transmitirán información a una distancia mayor y pueden correr el riesgo de que sus representaciones de nodos se 'diluyan' a partir de muchas iteraciones sucesivas. [3]

En general, parece que la suma tiene una mejora muy leve en el rendimiento medio, pero max o mean pueden dar modelos igualmente buenos. Esto es útil para contextualizar cuando se observan [las capacidades discriminatorias/expresivas](https://distill.pub/2021/gnn-intro/#comparing-aggregation-operations) de las operaciones de agregación.

Las exploraciones anteriores han dado mensajes contradictorios. Podemos encontrar tendencias medias en las que una mayor complejidad proporciona un mejor rendimiento, pero podemos encontrar contraejemplos claros en los que los modelos con menos parámetros, número de capas o dimensionalidad funcionan mejor. Una tendencia mucho más clara se refiere a la cantidad de atributos que se transmiten información entre sí.

Aquí desglosamos el rendimiento según el estilo de transmisión de mensajes. En ambos extremos, consideramos modelos que no se comunican entre entidades gráficas ("ninguna") y modelos que tienen mensajes pasados ​​entre nodos, bordes y globales.

![Figura 4][img4]

Gráfico de transmisión de mensajes frente al rendimiento del modelo y diagrama de dispersión del rendimiento del modelo frente al número de parámetros. Cada punto está coloreado por el paso del mensaje. 

En general, vemos que cuantos más atributos del gráfico se comuniquen, mejor será el rendimiento del modelo promedio. Nuestra tarea se centra en las representaciones globales, por lo que aprender explícitamente este atributo también tiende a mejorar el rendimiento. Nuestras representaciones de nodos también parecen ser más útiles que las representaciones de bordes, lo que tiene sentido ya que se carga más información en estos atributos.

Hay muchas direcciones en las que puede ir desde aquí para obtener un mejor rendimiento. Deseamos destacar dos direcciones generales, una relacionada con algoritmos gráficos más sofisticados y otra hacia el gráfico en sí.

Una de las fronteras de la investigación GNN no es la creación de nuevos modelos y arquitecturas, sino “cómo construir gráficos”, para ser más precisos, dotar a los gráficos de estructuras o relaciones adicionales que se puedan aprovechar. Como vimos en términos generales, cuanto más se comunican los atributos del gráfico, más tendemos a tener mejores modelos. En este caso particular, podríamos considerar hacer que los gráficos moleculares sean más ricos en características, agregando relaciones espaciales adicionales entre nodos, agregando bordes que no sean enlaces o relaciones explícitas que se puedan aprender entre subgráficos.

[img1]: visualization.png "Figura 1"

[img2]: visualization2.png "Figura 2"

[img3]: visualization3.png "Figura 3"

[img4]: visualization4.png "Figura 4"

# **Referencias**
1. Gráfico de evaluación comparativa Redes neuronales
V.P. Dwivedi, CK Joshi, T. Laurent, Y. Bengio, X. Bresson.
2020
2. Espacio de diseño para redes neuronales gráficas
J. You, R. Ying, J. Leskovec. 2020.
3. Agregación de vecindario principal para redes gráficas 
G. Corso, L. Cavalleri, D. Beaini, P. Lio, P. Velickovic. 2020.
