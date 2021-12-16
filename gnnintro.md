# **Gentil introducción a Redes Neuronales de Grafos** (*Graph Neural Networks* o *GNN*)


<div style="text-align: justify">Las redes neuronales han sido aprovecahdas para implementarse en los gráficos o grafos. A continuación, se explora y explica algunos componentes indispensable para poder constuir una red neuronal gráfica y las opciones de diseño detrás de ellos.

![Redes neuronales gráficas][img1]

Este articulo es una parte de dos publicaciones creadas por Distill sobre redes neuronales gráficas. Para entender cómo las convoluciones sobre imágenes se generalizan naturalmente a convoluciones gráficas, se puede visitar el siguiente enlace: [***Comprender las convoluciones en los gráficos***](https://distill.pub/2021/understanding-gnns/) [1].

Los gráficos siempre se encuentran a nuestro alrededor; objetos del mundo real a menudo se definen en términos de conexiones con otros objetos. Estas conexiones y objetos forman un conjunto que de manera natural pueden ser llamados como gráficos. Algunos investigadores se han dado a la tarea de desarrollar y crear redes neuronales de gráficos o GNN, es decir, estas redes operan en datos de gráficos [2]. El desarrollo a permitido que las aplicaciones en la práctica han aumentado sus capacidades y un poder expresivo, estas aplicaciones van desde la detección de noticias falsas, predicción de tráfico, hasta el descubrimiento de antibacterianos.

Este trabajo se divide en cuatro partes tratando de explicar las redes neuronales gráficas modernas. En primer lugar, se analiza qué tipo de datos se expresan de forma más natural en forma de gráfico y algunos ejemplos comunes. En segundo lugar, se explora qué hace que los gráficos sean diferentes de otros tipos de datos y algunas de las elecciones especializadas que se deben hacer al usar gráficos. En tercer lugar, se construye un GNN moderno, recorriendo cada una de las partes del modelo, comenzando con innovaciones históricas de modelado en el campo. Se procede a mostrar gradualmente una implementación básica a un modelo GNN de última generación. En cuarto y último lugar, se proporciona un campo de juegos GNN donde puede jugar con una tarea y un conjunto de datos de palabras reales para construir una intuición más sólida de cómo cada componente de un modelo GNN contribuye a las predicciones que hace.

## **Qué es una gráfica o grafo**

Comenzaremos con lo que es una gráfica. Un gráfico representa las relaciones (bordes) entre una colección de entidades (nodos).

![Componentes de un grafo][img2]

Algunos componentes de los gráficos son:
- Vértices (o nodos)
- Aristas (o bordes)
- Atributos globales (o nodo maestro)

Estos componentes pueden almacenar información en cada una de estas partes del gráfico.

Además, podemos especializar gráficos al asociar la direccionalidad a los bordes (dirigidos, no dirigidos).

![Direccionalidad de los bordes][img3]

Los bordes se pueden dirigir, donde un borde **e** tiene un nodo fuente, **v<sub>src</sub>** y un nodo de destino **v<sub>dst</sub>**. En este caso, la información fluye desde **v<sub>src</sub>** a **v<sub>dst</sub>**. También pueden ser no dirigidos, donde no existe una noción de nodos de origen o destino, y la información fluye en ambas direcciones. Tenga en cuenta que tener un solo borde no dirigido es equivalente a tener un borde dirigido desde **v<sub>src</sub>** a **v<sub>dst</sub>**, y otro borde dirigido desde **v<sub>dst</sub>** a **v<sub>src</sub>**.

Esto puede parecer muy abstracto, ya que los gráficos son estructuras de datos muy flexibles. Pero se muestran algunos ejemplos en la siguiente sección.

# **Gráficos y dónde encontrarlos**

Probablemente se tenga noción o conocimiento sobre algunos tipos de datos gráficos, las redes sociales es un ejemplo de ellos. Sin embargo, los gráficos son una representación de datos extremadamente poderosa y general, se mostrarán dos tipos de datos que quizás no crea que puedan modelarse como gráficos: imágenes y texto. Esto puede resultar contradictorio o poco probable, pero se puede aprender más sobre simetría y estructura de las imágenes y el texto al verlos como gráficos.

## **Imágenes como gráficos**

Las imágenes normalmente son pensadas como cuadriculas rectangulares que almacenan pixeles y son representadas como matrices. Otra forma de ver las imagenes es como gráficos con estructutra regular donde cada pixel nos representa un nodo y se conecta a través de un borde a los pixeles adyacentes. Cada píxel sin borde tiene exactamente 8 vecinos, y la información almacenada en cada nodo es un vector tridimensional que representa el valor RGB del píxel.

Una forma de visualizar la conectividad de un gráfico es a través de su matriz de adyacencia. Ordenamos los nodos, en este caso cada uno de 25 píxeles en una imagen simple de 5x5 de una carita sonriente, y llenamos una matriz de **n<sub>nodos</sub> x n<sub>nodos</sub>** con una entrada si dos nodos comparten un borde.

![Imagenes como gráficos][img4]

## **Texto como gráficos**

Podemos digitalizar texto asociando índices a cada carácter, palabra o ficha, y representando el texto como una secuencia de estos índices. Esto crea un gráfico dirigido simple, donde cada carácter o índice es un nodo y está conectado a través de un borde al nodo que lo sigue.

![Textos como gráficos][img5]

En la práctica no se suele codificar así tanto texto como imágenes: estas representaciones gráficas son redundantes ya que todas las imágenes y todo el texto tendrán estructuras muy regulares. Las imágenes tienen una estructura de bandas en su matriz de adyacencia porque todos los nodos (píxeles) están conectados en una cuadrícula. La matriz de adyacencia para el texto es solo una línea diagonal, porque cada palabra solo se conecta con la palabra anterior y con la siguiente.

## **Datos con valores gráficos en la naturaleza**

Pasemos a los datos que están estructurados de forma más heterogénea. En estos ejemplos, el número de vecinos de cada nodo es variable (a diferencia del tamaño de vecindario fijo de imágenes y texto). Estos datos son difíciles de expresar de otra manera que no sea un gráfico.

### **Moléculas como gráficos.** Todas las partículas están interactuando, pero cuando un par de átomos están atrapados a una distancia estable entre sí, decimos que comparten un enlace covalente. Diferentes pares de átomos y enlaces tienen diferentes distancias (por ejemplo, enlaces simples, enlaces dobles). Es una abstracción muy conveniente y común describir este objeto 3D como un gráfico, donde los nodos son átomos y los bordes son enlaces covalentes. [3]

![Moléculas como gráficos][img6]

### **Redes sociales como gráficos.** Las redes sociales son herramientas para estudiar patrones de comportamiento colectivo de personas, instituciones y organizaciones. Podemos construir un gráfico que represente grupos de personas modelando individuos como nodos y sus relaciones como bordes.

![Redes sociales como gráficos 1][img7]

A diferencia de los datos de imagen y texto, las redes sociales no tienen matrices de adyacencia idénticas.

![Redes sociales como gráficos 2][img8]

### **Redes de citas como gráficos.** Podemos visualizar estas redes de citas como un gráfico, donde cada artículo es un nodo y cada borde dirigido es una cita entre un artículo y otro. Además, podemos agregar información sobre cada artículo en cada nodo, como una palabra incrustada del resumen. [4] [5] [6]

La estructura de los gráficos del mundo real puede variar mucho entre diferentes tipos de datos; algunos gráficos tienen muchos nodos con pocas conexiones entre ellos, o viceversa. Los conjuntos de datos de gráficos pueden variar ampliamente (tanto dentro de un conjunto de datos dado como entre conjuntos de datos) en términos de la cantidad de nodos, bordes y conectividad de los nodos.

| Conjunto de datos | Dominio | Gráficas | Nodos | Bordes | Min | Significar | Max |
| -- | -- | -- | -- | -- | -- | -- | -- |
| club de karate | Red social | 1 | 34 | 78 |  | 4.5 | 17 |
| qm9 | Pequeñas moleculas | 134k | <=9 | <=26> | 1 | 2 | 5 |
| Cora | Red de citas | 1 | 23.166 | 91.5 | 1 | 7.8 | 379 |
| Enlaces de Wikipedia | Gráfico de conocimiento | 1 | 12 M | 378 M |  | 62,24 | 1 M |

Resumen de estadísticas sobre gráficos que se encuentran en el mundo real. Los números dependen de las decisiones de caracterización. Se pueden encontrar estadísticas y gráficos más útiles en KONECT [7]

# **¿Qué tipo de problemas tienen los datos estructurados en gráficos?**

Hay tres tipos generales de tareas de predicción en gráficos: nivel de gráfico, nivel de nodo y nivel de borde.

En una tarea a nivel de gráfico, predecimos una sola propiedad para un gráfico completo. Para una tarea a nivel de nodo, predecimos alguna propiedad para cada nodo en un gráfico. Para una tarea a nivel de borde, queremos predecir la propiedad o presencia de bordes en un gráfico.

Para los tres niveles de problemas de predicción descritos anteriormente (nivel de gráfico, nivel de nodo y nivel de borde), se muestra que todos los siguientes problemas se pueden resolver con una sola clase de modelo, el GNN.

## **Clases de problemas de predicción de gráficos**

### **Tarea a nivel de gráfico**

En una tarea a nivel de gráfico, nuestro objetivo es predecir la propiedad de un gráfico completo. Por ejemplo, para una molécula representada como un gráfico, podríamos querer predecir a qué huele la molécula o si se unirá a un receptor implicado en una enfermedad.

![Tarea a nivel de gráfico][img9]

Esto es análogo a los problemas de clasificación de imágenes con MNIST y CIFAR, donde queremos asociar una etiqueta a una imagen completa. Con el texto, un problema similar es el análisis de sentimientos, en el que queremos identificar el estado de ánimo o la emoción de una oración completa a la vez.

### **Tarea a nivel de nodo**

Las tareas a nivel de nodo están relacionadas con la predicción de la identidad o función de cada nodo dentro de un gráfico.

![Tarea a nivel de nodo][img10]

Siguiendo la analogía de la imagen, los problemas de predicción a nivel de nodo son análogos a la segmentación de la imagen , donde intentamos etiquetar el papel de cada píxel en una imagen. Con el texto, una tarea similar sería predecir las partes del discurso de cada palabra en una oración (por ejemplo, sustantivo, verbo, adverbio, etc.).

### **Tarea a nivel de borde**

Un ejemplo de inferencia a nivel de borde es la comprensión de la escena de la imagen. Más allá de identificar objetos en una imagen, los modelos de aprendizaje profundo se pueden utilizar para predecir la relación entre ellos. Podemos expresar esto como una clasificación de nivel de borde: dados nodos que representan los objetos en la imagen, deseamos predecir cuál de estos nodos comparte un borde o cuál es el valor de ese borde. Si deseamos descubrir conexiones entre entidades, podríamos considerar el gráfico completamente conectado y basándonos en su valor predicho podar los bordes para llegar a un gráfico disperso.

En (b), arriba, la imagen original (a) se ha segmentado en cinco entidades: cada uno de los luchadores, el árbitro, la audiencia y la colchoneta. (C) muestra las relaciones entre estas entidades.

![Tarea a nivel de borde 1][img11]

A la izquierda tenemos un gráfico inicial construido a partir de la escena visual anterior. A la derecha se muestra un posible etiquetado de borde de este gráfico cuando se podaron algunas conexiones en función de la salida del modelo.

![Tarea a nivel de borde 2][img12]

[img1]: https://artfromcode.files.wordpress.com/2017/04/index_img_41.jpg "Redes neuronales gráficas"

[img2]: https://sites.google.com/site/unidad6teoriadegrafos/_/rsrc/1464617456012/6-1-elementos-y-caracteristicas-de-los-grafos/6-1-1-componentes-de-un-grafo-aristas-vertices-lazos-y-valencia/grafo%28nodo%20y%20arista.png "Componentes de un grafico"

[img3]: https://blogs.ua.es/jabibics/files/2011/01/grafos1.png "Clasificación de gráficos"

[img4]: https://upload.wikimedia.org/wikipedia/commons/f/f9/Matriz_de_adyacencia.jpg "Ejemplo de imágenes como gráficos"

[img5]: https://cdn.kastatic.org/ka-perseus-images/faa1c44e1ab848623096b85b9aa32626b8d8d040.png "Ejemplo de textos como gráficos"

[img6]: https://culturacientifica.com/app/uploads/2014/08/Cayley-1.png "Moléculas como gráficos"

[img7]: https://miro.medium.com/max/724/0*kWGR5wsMTeL_PYZV "Redes sociales como gráficos 1"

[img8]: https://www.madrimasd.org/blogs/matematicas/files/2020/03/six2.jpg "Redes sociales como gráficos 2"

[img9]: https://upload.wikimedia.org/wikipedia/commons/thumb/0/00/SMILES.png/330px-SMILES.png "Tarea a nivel de gráfico"

[img10]: https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcQNkLcMzq12ny7pw5HuZu4N8xSNBZGq-r8ZaQ&usqp=CAU "Tarea a nivel de nodo"

[img11]: https://distill.pub/2021/gnn-intro/merged.0084f617.png "Tarea a nivel de borde"

[img12]: https://distill.pub/2021/gnn-intro/edges_level_diagram.c40677db.png "Tarea a nivel de borde"



## **Zona de juego GNN**

Hasta este punto hemos conocido los componentes para construir la arquitectura de una GNN; el siguiente paso es ir a la práctica.

Para jugar un poco, utilizaremos el modelo interactivo en la sección **GNN Playground** del [paper](https://distill.pub/2021/gnn-intro/). El modelo interactivo utiliza el conjunto de datos de olores de Leffingwell, que está compuesto por moléculas con percepciones de olor asociadas, con la intención de clasificar si una molécula tiene un olor intenso o no (etiqueta binaria).

La predicción de la relación de una estructura molecular (grafo) con su olor es un problema bastante interesante. Por ejemplo, el ajo y la mostaza, que pueden contener la molécula de alcohol alílico, tienen esta cualidad. La molécula piperitona, que se utiliza a menudo para los caramelos con sabor a menta, también se describe como de olor penetrante.

Para la representación de cada molécula como un grafo, los átomos son nodos que contienen una codificación *one-hot* para su identidad atómica (carbono, nitrógeno, oxígeno, flúor) y los enlaces son aristas que contienen una codificación *one-hot* para su tipo de enlace (simple, doble, triple o aromático).

El modelo interactivo se construye utilizando capas secuenciales de GNN, seguidas de un modelo lineal con una activación sigmoide para la clasificación. La función de actualización para los atributos del grafo es un MLP de 1 capa (*1-layer of Multilayer Perceptron*) con una función de activación relu y una capa para la normalización de las activaciones.**

Las características que podemos personalizar en este modelo de GNN son:

1. El número de capas GNN, también llamado profundidad (*depth*).

2. La dimensionalidad de cada atributo cuando se actualiza. 

3. La función de agregación utilizada en *pooling*: máximo, media o suma.

4. Los atributos del gráfico que se actualizan, o  los estilos de *message passing*: nodos, aristas y representación global. Estos se controlan mediante interruptores booleanos (activación o desactivación). 

![Dashboard interactivo](/images/dashboard_model.png)

Para entender cómo una GNN aprende una representación de un grafo optimizado para una tarea, es conveniente observar la penúltima capa de la GNN. Estos "grafos embebidos" (graph embeddings) son las salidas del modelo GNN justo antes de la predicción. 

![graph out](/images/graph_out.png)

Para la representación de está salida, y dado que estamos utilizando un modelo lineal generalizado para la predicción, un mapeo lineal es suficiente para permitirnos ver cómo el modelo está aprendiendo representaciones alrededor del límite de decisión. 

Los grafos, al estar construidos por vectores de alta dimensión, los reducimos a 2D mediante el análisis de componentes principales (***principal component analysis* o *PCA*)**. Un modelo perfecto haría visibles los datos etiquetados por separado, pero como estamos reduciendo la dimensionalidad y también tenemos modelos imperfectos, este límite puede ser más difícil de ver.

Ahora que conocemos la zona de juego, podremos interactuar con el modelo. :)

<iframe width="640" height="480" src="https://www.youtube.com/embed/icW-n6riXIQ" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
