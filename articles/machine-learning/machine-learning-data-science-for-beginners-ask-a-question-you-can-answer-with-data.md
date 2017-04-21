<properties
   pageTitle="Formular una pregunta puede responder con datos - formular preguntas | Microsoft Azure"
   description="Obtenga información sobre cómo formular una pregunta de ciencias de datos en la ciencia de datos para principiantes M3. Incluye una comparación de las preguntas de clasificación y regresión."
   keywords="preguntas de ciencias de datos, formular preguntas, preguntas de regresión, preguntas de clasificación, una pregunta"
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

# <a name="ask-a-question-you-can-answer-with-data"></a>Formular una pregunta que puede responder con datos

## <a name="video-3-data-science-for-beginners-series"></a>Vídeo 3: Ciencias de datos de serie principiantes

Obtenga información sobre cómo formular una pregunta de ciencias de datos en la ciencia de datos para principiantes M3. En este vídeo se incluye una comparación de preguntas de clasificación y regresión algoritmos.

Para obtener el máximo provecho de la serie, vea todos ellos. [Vaya a la lista de vídeos](#other-videos-in-this-series)

> [AZURE.VIDEO data-science-for-beginners-ask-a-question-you-can-answer-with-data]

## <a name="other-videos-in-this-series"></a>Otros vídeos de esta serie

*Ciencia de datos para principiantes* es una introducción rápida a ciencia datos en cinco vídeos breves.

  * Vídeo 1: [Las respuestas de ciencias datos 5 preguntas](machine-learning-data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 14 min.)*
  * Vídeo 2: [está listo para Ciencias datos sus datos?](machine-learning-data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 56 min.)*
  * Vídeo 3: Hacer una pregunta que puede responder con datos
  * Vídeo 4: [Predecir una respuesta con un modelo simple](machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 42 min.)*
  * Vídeo 5: [Copiar el trabajo de otras personas para realizar la ciencia de datos](machine-learning-data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 18 min.)*

## <a name="transcript-ask-a-question-you-can-answer-with-data"></a>Transcripción: Formular una pregunta que puede responder con datos

Bienvenido a la tercera vídeo de la serie "Ciencia de datos para principiantes".  

En este uno de ellos, obtendrá algunas sugerencias para formular una pregunta que puede responder con datos.

Puede que aparezca más fuera de este vídeo, si lo desea ver primero los dos vídeos anterior de esta serie: "puede responder a la ciencia de datos 5 preguntas" y "Es los datos están listos para ciencias de datos?"

## <a name="ask-a-sharp-question"></a>Formular una pregunta nitidez

Hemos analizado cómo ciencias de datos es el proceso de uso de nombres (también denominados categorías o etiquetas) y números de predecir una respuesta a una pregunta. Pero no puede ser cualquier pregunta; tiene que ser un *pregunta nitidez.*

No tiene una pregunta vaga responder con un nombre o un número. Debe ser una pregunta nitidez.

Suponga que encuentra una lámpara mágica con un genie que le dará una respuesta verídicamente cualquier pregunta. Pero es un genie perjudiciales e intentará realizar su respuesta como vagas y confusa como ausente obtiene con. Desea anclar le hacia abajo con una pregunta tan hermética que no puede ayudar a pero saber qué desea saber.

Si desea hacer una pregunta imprecisa, como "¿qué se va a ocurrir con Mis cotizaciones?", se puede responder la genie, "cambiará el precio". Es una respuesta fieles, pero no es muy útil.

Pero si hacer una pregunta nitidez, como "¿qué precio de venta de mi cotizaciones será la próxima semana?", el genie no puede ayudar a pero le da una específica responda y predecir un precio de venta.

## <a name="examples-of-your-answer-target-data"></a>Ejemplos de su respuesta: datos de destino

Una vez formular una pregunta, compruebe si tiene algunos ejemplos de la respuesta en los datos.

Si la pregunta es "¿qué precio de venta de mi cotizaciones será la próxima semana?" a continuación, hay que asegurarse de que nuestros datos incluyen el historial de cotizaciones.

Si la pregunta es "qué coche en mi flota va errores primero?" a continuación, hay que asegurarse de que nuestros datos incluyen información sobre errores anteriores.

![Datos de destino: ejemplos de su respuesta. Formular una pregunta de ciencias de datos.](./media/machine-learning-data-science-for-beginners-ask-a-question-you-can-answer-with-data/machine-learning-data-science-target-data.png)

Estos ejemplos de respuestas se denominan destino. Un destino es lo que estamos intentando predecir acerca de los puntos de datos en el futuro, ya sea una categoría o un número.

Si no tiene los datos de destino, debe obtener algunos. No podrá responder a su pregunta sin él.

## <a name="reformulate-your-question"></a>Reformulate su pregunta

A veces, puede cambiar el texto de la pregunta para obtener una respuesta más útil.

La pregunta "Es este punto de datos o B?" Muestra la categoría (o nombre o etiqueta) de un objeto. Para responder a él, usamos un *algoritmo de clasificación*.

La pregunta "¿cuánto?" o "¿cuántos?" muestra una cantidad. Para contestar usamos un *algoritmo de regresión*.

Para ver cómo podemos transformar estos, echemos un vistazo a la pregunta, "qué noticia es el más interesante en este lector?" Le pide una predicción de una única opción de muchas posibilidades - decir "Es este A o B o C o D?" - y utilizará un algoritmo de clasificación.

Pero esta pregunta será más fácil de responder si se cambiar el texto como "cómo interesante es cada artículo en esta lista para este lector?" Ahora puede dar a cada artículo una puntuación numérica y, a continuación, es fácil de identificar el artículo puntuación más alta. ¿Este es un Rehacer de la pregunta de clasificación a una pregunta de regresión o cuánto?

![Reformulate su pregunta. Pregunta de clasificación frente a la pregunta de regresión.](./media/machine-learning-data-science-for-beginners-ask-a-question-you-can-answer-with-data/machine-learning-data-science-classification-question-vs-regression-question.png)

Cómo se debe formular que una pregunta es una indicación al algoritmo de qué puede ofrecerle una respuesta.

Encontrará que ciertas familias de algoritmos - como las de nuestro ejemplo artículo de noticias - están estrechamente relacionados. Puede reformulate su pregunta para usar el algoritmo que proporciona la respuesta más útil.

Pero, más importantes, esa pregunta nitidez - la pregunta que puede responder con datos. Y compruebe que tiene los datos correctos para responderla.

Hemos analizado algunos principios básicos para hacer una pregunta, que puede responder con datos.

Asegúrese de desproteger los otros vídeos de "Datos de ciencias para principiantes" de aprendizaje de Microsoft Azure máquina.


## <a name="next-steps"></a>Pasos siguientes

  * [Pruebe una primera experimentar de ciencias de datos con Machine aprendizaje Studio](machine-learning-create-experiment.md)
  * [Obtener una introducción a aprendizaje en Microsoft Azure](machine-learning-what-is-machine-learning.md)
