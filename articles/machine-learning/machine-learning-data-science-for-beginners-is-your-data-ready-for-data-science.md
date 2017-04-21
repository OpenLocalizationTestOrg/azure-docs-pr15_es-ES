<properties
   pageTitle="¿Está preparado para Ciencias datos sus datos? Evaluación de datos | Microsoft Azure"
   description="Obtenga información sobre los criterios de 4 de datos esté listo para ciencias de datos. Ciencia de datos para principiantes M2 tiene ejemplos concretos ayudarle con la evaluación de datos básico."
   keywords="los datos pertinentes, evaluar datos, preparar datos, criterios de datos, datos listos"
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


# <a name="is-your-data-ready-for-data-science"></a>¿Está preparado para Ciencias datos sus datos?

## <a name="video-2-data-science-for-beginners-series"></a>Vídeo 2: Ciencias de datos de serie principiantes

Aprenda a evaluar los datos para asegurarse de que cumple los criterios básicos para estar preparado para ciencias de datos.

Para obtener el máximo provecho de la serie, vea todos ellos. [Vaya a la lista de vídeos](#other-videos-in-this-series)

> [AZURE.VIDEO data-science-for-beginners-series-is-your-data-ready-for-data-science]

## <a name="other-videos-in-this-series"></a>Otros vídeos de esta serie

*Ciencia de datos para principiantes* es una introducción rápida a ciencia datos en cinco vídeos breves.

  * Vídeo 1: [Las respuestas de ciencias datos 5 preguntas](machine-learning-data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 14 min.)*
  * Vídeo 2: Está los datos preparado para ciencias de datos?
  * Vídeo 3: [Formular una pregunta puede responder con datos](machine-learning-data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 17 min.)*
  * Vídeo 4: [Predecir una respuesta con un modelo simple](machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 42 min.)*
  * Vídeo 5: [Copiar el trabajo de otras personas para realizar la ciencia de datos](machine-learning-data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 18 min.)*

## <a name="transcript-is-your-data-ready-for-data-science"></a>Transcripción: Está los datos preparado para ciencias de datos?

Bienvenido a "¿está preparado para Ciencias datos sus datos?" el segundo vídeo de la serie de *Datos de ciencias para principiantes*.  

Antes de ciencias de datos pueden proporcionarle las respuestas que desee, debe darle algunos prima de alta calidad para trabajar con. Al igual que realizar una pizza, mejor los componentes de que inicio con mejor el producto final.

## <a name="criteria-for-data"></a>Criterios para los datos

Por lo tanto, en el caso de ciencias de datos, hay algunos elementos que debemos reunir.

Se necesitan los datos:

  * Relevantes
  * Conectado
  * Precisa
  * Suficiente para trabajar con

## <a name="is-your-data-relevant"></a>¿Es relevante sus datos?

Para que el primer ingrediente - necesitamos datos relevantes.

![Datos pertinentes frente a los datos no pertinentes - evaluar datos](./media/machine-learning-data-science-for-beginners-is-your-data-ready-for-data-science/machine-learning-data-science-relevant-and-irrelevant-data.png)

Busque la tabla a la izquierda. Se cumplen siete personas fuera de barras de San Cristóbal, mide su nivel de alcohol sangre, el promedio de bateo Sox rojo en su última partida y el precio de leche en el almacén de comodidad más cercano.

Se trata de todos los datos perfectamente legítimos. Es solo es la que no es relevante. No hay ninguna relación evidente entre estos números. Si le ha proporcionado el precio actual de leche y el promedio de bateo Sox rojo, no hay ninguna manera puede adivinar mi contenido de alcohol sangre.

Mirar ahora la tabla a la derecha. Esta vez hemos medido cada persona masa del cuerpo y el número de bebidas que han tenido en cuenta.  Los números de cada fila ahora son relevantes entre sí. Si le proporcionó mi cuerpo masivo y el número de Margaritas he tenido, puede crear una estimación en mi sangre alcohol contenido.

## <a name="do-you-have-connected-data"></a>¿Se ha conectado a datos?

El siguiente ingrediente es datos conectados.

![Datos frente a datos desconectado - criterios de datos, datos listos conectados](./media/machine-learning-data-science-for-beginners-is-your-data-ready-for-data-science/machine-learning-data-science-connected-vs-disconnected-data.png)

Aquí tiene algunos datos relevantes de la calidad de hamburguesas: rejilla temperatura, peso patty y la clasificación de la comida local revista. Pero tenga en cuenta los espacios de la tabla a la izquierda.

La mayoría de los conjuntos de datos faltan algunos valores. Es frecuente tener taladros así y hay formas de trabajar con ellos. Pero si hay demasiado falta, los datos se empiezan a parecerse queso suizo.

Si mira la tabla de la izquierda, hay para que la cantidad de datos que faltan, que es difícil dar con cualquier tipo de relación entre el grosor de temperatura y patty de gas. Este es un ejemplo de datos sin conexión.

Sin embargo, la tabla a la derecha, está lleno y completa - un ejemplo de datos conectados.

## <a name="is-your-data-accurate"></a>¿Es preciso sus datos?

El siguiente ingrediente que debemos es precisión. Hay cuatro destinos que le gustaría visitas con flechas.

![Datos precisos frente a datos incorrectos - criterios de datos](./media/machine-learning-data-science-for-beginners-is-your-data-ready-for-data-science/machine-learning-data-science-inaccurate-vs-accurate-data.png)

Mire el destino en la esquina superior derecha. Tenemos una agrupación estrecha derecha alrededor de la bullseye. Que, por supuesto, es preciso. Desigual, en el idioma de la ciencia de datos, el rendimiento de nuestro a la derecha de destino que hay debajo también se considera preciso.

Si desea asignar el centro de estas flechas, verá que es muy cerca de la bullseye. Las flechas se extienden todos alrededor de destino, por lo que están consideran imprecisas, pero están centrados en la bullseye, por lo que están consideran precisos.

Ahora, mire el destino de la esquina superior izquierda. Aquí nuestras flechas visitas muy cerca una agrupación estrecha. Son precisos, pero son incorrectos porque el centro de la forma de desactivar la bullseye. Y, por supuesto, las flechas situadas en la parte inferior izquierda de destino son incorrectos e imprecisa. Este archer necesita más práctica.

## <a name="do-you-have-enough-data-to-work-with"></a>¿Tiene suficiente datos para trabajar con?

Por último, ingrediente #4: es necesario tener suficiente datos.

![¿Tiene suficientes datos para su análisis? Evaluación de datos](./media/machine-learning-data-science-for-beginners-is-your-data-ready-for-data-science/machine-learning-data-science-barely-enough-data.png)

Piense en cada punto de datos en la tabla como un trazo de pincel en un dibujo. Si tiene sólo algunos de ellos, el dibujo puede ser bastante aproximado: es difícil determinar qué es.

Si agrega algunas más trazos de pincel, se inicia la pintura obtener un poco más nítido.

Cuando haya apenas suficiente trazos, puede ver lo suficiente decisiones generales. ¿Es en otro que podría ser conveniente visite? Tiene un aspecto nítido, que es similar a agua limpia: Sí, que es donde voy de vacaciones.

A medida que agrega más datos, la imagen se hace más precisa y puede tomar decisiones más detalladas. Ahora pueda mirar los tres hoteles en el banco izquierdo. Sabe, realmente como las características de arquitectura de en primer plano. Deberá permanece mi, en el tercer piso.

Con los datos pertinentes, conectada, precisa y suficientemente, se dispone de todos los componentes que es necesario hacer algunos ciencias de datos de alta calidad.

Asegúrese de desproteger los cuatro vídeos en *Ciencia de datos para principiantes* de aprendizaje de Microsoft Azure máquina.




## <a name="next-steps"></a>Pasos siguientes

  * [Pruebe una primera experimentar de ciencias de datos con Machine aprendizaje Studio](machine-learning-create-experiment.md)
  * [Obtener una introducción a aprendizaje en Microsoft Azure](machine-learning-what-is-machine-learning.md)
