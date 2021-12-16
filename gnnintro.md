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
