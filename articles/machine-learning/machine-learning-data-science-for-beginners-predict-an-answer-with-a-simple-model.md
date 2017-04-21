<properties
   pageTitle="Predecir una respuesta con un modelo simple - modelo de regresión | Microsoft Azure"
   description="Cómo crear un modelo de regresión simple para predecir un precio en ciencia de datos para principiantes M4. Incluye una regresión lineal con datos de destino."                                  
   keywords="crear un modelo, modelo simple, la predicción de precio, modelo de regresión simple"
   services="machine-learning"
   documentationCenter="na"
   authors="cjgronlund"
   manager="jhubbard"
   editor="cjgronlund"/>

<tags
   ms.service="machine-learning"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/20/2016"
   ms.author="cgronlun;garye"/>

# <a name="predict-an-answer-with-a-simple-model"></a>Predecir una respuesta con un modelo simple

## <a name="video-4-data-science-for-beginners-series"></a>Vídeo 4: Ciencias de datos de serie principiantes

Obtenga información sobre cómo crear un modelo de regresión simple para predecir el precio de un diamante en ciencia de datos para principiantes M4. Se va a dibujar un modelo de regresión con datos de destino.

Para obtener el máximo provecho de la serie, vea todos ellos. [Vaya a la lista de vídeos](#other-videos-in-this-series)

> [AZURE.VIDEO data-science-for-beginners-series-predict-an-answer-with-a-simple-model]

## <a name="other-videos-in-this-series"></a>Otros vídeos de esta serie

*Ciencia de datos para principiantes* es una introducción rápida a ciencia datos en cinco vídeos breves.

  * Vídeo 1: [Las respuestas de ciencias datos 5 preguntas](machine-learning-data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 14 min.)*
  * Vídeo 2: [está listo para Ciencias datos sus datos?](machine-learning-data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 56 min.)*
  * Vídeo 3: [Formular una pregunta puede responder con datos](machine-learning-data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 17 min.)*
  * Vídeo 4: Predecir una respuesta con un modelo simple
  * Vídeo 5: [Copiar el trabajo de otras personas para realizar la ciencia de datos](machine-learning-data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 18 min.)*

## <a name="transcript-predict-an-answer-with-a-simple-model"></a>Transcripción: Predecir una respuesta con un modelo simple

Éste es el cuarto vídeo en la "datos ciencias para principiantes" serie. En este uno, se deberá crear un modelo simple y realice una predicción.

Un *modelo* es un artículo simplificado sobre nuestros datos. Mostraremos lo significan.

## <a name="collect-relevant-accurate-connected-enough-data"></a>Recopilar relevante, precisa y conectada, suficientes datos

Supongamos que desea comprar para un rombo. Tengo un anillo que pertenecen para mi mis hijos con una configuración para un diamante ingresar 1,35 y quiero para hacerse una idea de cuánto costará. Seguir un lápiz y Bloc de notas en la tienda de joyería y anote el precio de todos los rombos en el caso y cuánto valorar en número de quilates. Comenzando con el primer rombo - su 1.01 número de quilates y 7,366 $.

Ahora recorra y hacer esto para todos los rombos en la tienda.

![Columnas de datos de rombo](./media/machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model/diamond-data.png)

Observe que nuestra lista tiene dos columnas. Cada columna tiene un atributo diferente: peso en número de quilates y precio - y cada fila es un punto de datos único que representa un diamante único.

Realmente hemos creado un pequeño conjunto de datos aquí - una tabla. Observe que cumpla nuestros criterios de calidad:

* Los datos están **relevante** - peso definitivamente relacionado al precio
* Es **preciso** - hemos comprobado los precios que escribimos hacia abajo
* Es **conectado** : no hay espacios en blanco en cualquiera de estas columnas
* Y, como veremos, es **lo suficientemente** datos para responder a la pregunta

## <a name="ask-a-sharp-question"></a>Formular una pregunta nitidez

Ahora se le plantean nuestra pregunta de manera nitidez: "¿cuánto costará para comprar un diamante ingresar 1,35?"

Nuestra lista no tiene un diamante 1,35 ingresar en él, por lo que tenemos utilice el resto de nuestros datos para obtener una respuesta a la pregunta.

## <a name="plot-the-existing-data"></a>Trazar los datos existentes

Lo primero que haremos es dibujar una línea horizontal de número, denominada un eje, para representar los pesos. El rango de los pesos es 0 a 2, por lo que deberá dibujar una línea que cubre que rango y coloque marcas de paso para cada mitad ingresar.

A continuación se podrá dibujar un eje vertical para grabar el precio y conectar el eje horizontal grosor. Se trata en unidades de dólares. Ahora que tenemos un conjunto de ejes de coordenadas.

![Ejes de peso y precio](./media/machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model/weight-and-price-axes.png)

Vamos a tomar estos datos ahora y convertirla en un *trazado de dispersión*. Esta es una excelente forma de visualizar los conjuntos de datos numéricos.

Para el primer punto de datos, hemos ver completa una línea vertical en el número de quilates 1.01. A continuación, nos ver completa una línea horizontal en 7,366 $. Cuando se cumplen, se dibuja un punto. Esto representa nuestro rombo.

Ahora que vaya a través de cada rombo en esta lista y hacer lo mismo. Cuando estamos a través, esto es lo que obtenemos: un conjunto de puntos, uno para cada rombo.

![Gráfico de dispersión](./media/machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model/scatter-plot.png)

## <a name="draw-the-model-through-the-data-points"></a>Dibujar el modelo mediante los puntos de datos

Ahora si mira los puntos y squint, la colección es similar a una línea fat aproximada. Podemos llevar nuestro marcador y dibujar una línea recta a través de él.

Dibujar una línea, se crea un *modelo*. Considere esto como tomar del mundo real y hacer una versión de dibujos animados simple del mismo. Ahora los dibujos animados es incorrecta, la línea no vaya a través de todos los puntos de datos. Sin embargo, es una útil simplificar.

![Línea de regresión lineal.](./media/machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model/linear-regression-line.png)

El hecho de que todos los puntos no vaya exactamente a través de la línea es correcto. Científicos datos explican esto diciendo que hay el modelo - que es la línea - y, a continuación, cada punto tiene algunos *ruido* o *variación* asociada con él. Hay la relación perfecta subyacente y, a continuación, hay del mundo real, prácticos que agrega ruido e incertidumbre.

Dado que estamos intentando responder a la pregunta *cuánto?* se denomina una *regresión*. Y dado que estamos utilizando una línea recta, es una *regresión lineal*.

## <a name="use-the-model-to-find-the-answer"></a>Utilizar el modelo para encontrar la respuesta

Ahora que tenemos un modelo y le pedimos nuestra pregunta: ¿cuánto costará rombo ingresar 1,35?

Para responder a la pregunta, estamos ver completa número de quilates 1,35 y dibujar una línea vertical. Cuando este cruza la línea de modelo, nos ver completa una línea horizontal en el eje de moneda. Llegue a la derecha en 10.000. ¡Auge! Es la respuesta: rombo 1,35 ingresar costos aproximadamente 10.000 $.

![Encuentra la respuesta en el modelo](./media/machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model/find-the-answer.png)

## <a name="create-a-confidence-interval"></a>Crear un intervalo de confianza

Es natural preguntado cómo precisos este predicción. Es muy útil saber si el rombo ingresar 1,35 estará muy cerca de 10.000 $ o superior o inferior. Para determinarlo, vamos a dibujar un envolvente alrededor de la línea de regresión que incluye la mayoría de los puntos. Este sobre se denomina nuestro *intervalo de confianza*: estamos bastante seguros de que los precios entran en este sobre, porque en la mayoría de ellas pasada tiene. Podemos dibujar dos líneas horizontales más de que la línea de ingresar 1,35 cruza la parte superior y la parte inferior de dicho sobre.

![Intervalo de confianza](./media/machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model/confidence-interval.png)

Ahora podemos decir algo sobre nuestro intervalo de confianza: podemos decir con confianza que es el precio de un diamante ingresar 1,35 aproximadamente $10.000 - pero que podría ser tan bajo como $8.000 y puede ser tan alto como 12.000 $.

## <a name="were-done-with-no-math-or-computers"></a>Hemos terminado, sin matemáticas o equipos

Hemos hecho qué científicos datos pagará hacer y se hizo solo dibujando:

* Pedimos que nos podemos responder con datos de una pregunta
* Creamos un *modelo de* uso de *regresión lineal.*
* Hemos realizado una *predicción*, junto con un *intervalo de confianza*

Y no usamos matemáticas o equipos que para hacerlo.

Ahora si nos hemos tenía más información, como...

* el recorte del rombo
* variaciones de color (cómo cerrar el rombo es está en blanco)
* el número de inclusiones en el rombo

.. continuación habría tenemos más columnas. En ese caso, matemáticas pasa a ser útil. Si tiene más de dos columnas, que es difícil dibujar puntos en papel. La expresión matemática le permite ajustar dicha línea o ese plano a los datos perfectamente.

Además, si en lugar de un conjunto de rombos, tenemos dos mil o dos millones y luego puede hacer que funcionan con mayor rapidez con un equipo.

En la actualidad, hemos analizado cómo hacer regresión lineal y, a continuación, hemos realizado la predicción de uso de datos.

Asegúrese de desproteger los otros vídeos de "Datos de ciencias para principiantes" de aprendizaje de Microsoft Azure máquina.



## <a name="next-steps"></a>Pasos siguientes

  * [Pruebe una primera experimentar de ciencias de datos con Machine aprendizaje Studio](machine-learning-create-experiment.md)
  * [Obtener una introducción a aprendizaje en Microsoft Azure](machine-learning-what-is-machine-learning.md)
