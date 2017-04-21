<properties
    pageTitle="Convertir un ensayo de cursos de aprendizaje de equipo a un ensayo predictivo | Microsoft Azure"
    description="Cómo convertir un ensayo de formación de aprendizaje de máquina utilizado para el modelo de análisis predictiva a un ensayo predictivo que se puede implementar como un servicio web de aprendizaje."
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/19/2016"
    ms.author="garye"/>

# <a name="convert-a-machine-learning-training-experiment-to-a-predictive-experiment"></a>Convertir un ensayo de cursos de aprendizaje de equipo a un ensayo predictivo

El aprendizaje Azure le permite crear, probar e implementar soluciones de análisis predictiva.

Una vez que haya creado e itera en una *formación experimentar* para entrenar el modelo de análisis predictiva y esté listo para usarlo para puntuación nuevos datos, debe preparar y simplificar el ensayo de puntuación. A continuación, puede implementar este *ensayo predictiva* como un servicio web de Azure para que los usuarios puedan enviar datos a su modelo y recibir predicciones de su modelo.

Al convertir en un ensayo predictivo, recibe el modelo capacitado listo para su implementación como un servicio web. Los usuarios del servicio web enviará datos de entrada a su modelo y su modelo le enviará los resultados de predicción. Así como convertir a un ensayo predictivo que desee tener en cuenta cómo piensa del modelo para usarlo con otros usuarios.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

El proceso de convertir un ensayo formación a un ensayo predictivo consta de tres pasos:

1.  Guarde el modelo de aprendizaje de máquina que ha preparado y reemplace la máquina algoritmo de aprendizaje [Tren modelo] [ train-model] módulos con su modelo capacitado guardado.
2.  Recortar el ensayo sólo a los módulos que son necesarios para la puntuación. Un ensayo de formación incluye un número de módulos que son necesarios para el curso pero no son necesarios una vez que el modelo es capacitado y listos para usar para la puntuación.
3.  Definir donde en su prueba aceptará datos desde el usuario del servicio web y qué datos se devolverán.

## <a name="set-up-web-service-button"></a>Botón de configurar servicio Web

Después de ejecutar el ensayo (botón**Ejecutar** en la parte inferior del lienzo de ensayo), el botón de **Configurar servicio Web** (seleccione la opción de **Servicio de Web predictiva** ) realizará por usted los tres pasos de convertir el ensayo formación a un ensayo predictivo:

1.  Guarda el modelo capacitado como un módulo en la sección de **Modelos de formación** de la paleta de módulo (a la izquierda del lienzo de ensayo), a continuación, reemplaza el equipo de aprendizaje algoritmo y [Modelo tren] [ train-model] módulos con el modelo capacitado guardado.
2.  Quita módulos que claramente no son necesarios. En nuestro ejemplo, esto incluye los [Datos divididos][split], 2 º [Puntuación modelo][score-model]y [Evaluar modelo] [ evaluate-model] módulos.
3.  Crea la entrada de servicio Web y módulos de salida y se agregan en ubicaciones predeterminadas en su prueba.

Por ejemplo, el siguiente procedimiento entrena un modelo de árbol de decisión aumentada de clase de dos con datos del censo de muestra:

![Ensayo de formación][figure1]

Los módulos en este ensayo realizan básicamente cuatro funciones diferentes:

![Funciones de módulo][figure2]

Al convertir este ensayo formación en un ensayo predictivo, algunos de estos módulos ya no son necesarios o que tienen un propósito diferente:

- **Datos** : los datos de este conjunto de datos de ejemplo no se usa durante la puntuación: el usuario del servicio web proporciona los datos que se cuentan. Sin embargo, los metadatos de este conjunto de datos, como tipos de datos, se utilizan el modelo de capacitado. Por lo que debe tener el conjunto de datos en el ensayo predictiva para que puede proporcionar metadatos.

- **Preparar el** - dependiendo de los datos que se enviará de puntuación, estos módulos pueden o no pueden ser necesarios para procesar los datos entrantes.

    Por ejemplo, en este ejemplo, el conjunto de datos de ejemplo puede tener valores que faltan e incluye columnas que no son necesarios para entrenar el modelo. Por lo tanto una [Limpiar datos que faltan] [ clean-missing-data] módulo incluyó para tratar los valores que faltan y un [Seleccionar columnas de conjunto de datos] [ select-columns] módulo se incluyó para excluir las columnas adicionales en el flujo de datos. Si sabe que los datos que se enviará de puntuación a través del servicio web no tendrán valores que faltan, a continuación, puede quitar los [Datos que faltan limpiar] [ clean-missing-data] módulo. No obstante, como las [Seleccionar columnas de conjunto de datos] [ select-columns] módulo ayuda a definir el conjunto de características que se cuentan, ese módulo debe permanecer.

- **Tren** - una vez que el modelo ha sido formado correctamente, guardarlo como un módulo único modelo capacitado. A continuación, reemplace los módulos individuales con el modelo capacitado guardado.

- **Puntuación** : en este ejemplo, el módulo de división se utiliza para dividir el flujo de datos en un conjunto de datos de prueba y aprendizaje. En el ensayo predictiva esto no es necesario y se puede quitar. Asimismo, el 2 º [Puntuación modelo] [ score-model] módulo y el [Modelo de evaluar] [ evaluate-model] módulo se usan para comparar los resultados de los datos de prueba, por lo que estos módulos también no son necesarios en la prueba predictiva. El restante [Puntuación modelo] [ score-model] módulo, sin embargo, es necesaria para devolver un resultado de puntuación a través del servicio web.

A continuación aparece nuestro ejemplo tras hacer clic en **Configurar servicio Web**:

![Convertir predictiva ensayo][figure3]

Esto puede ser suficiente para preparar el ensayo a implementar como un servicio web. Sin embargo, desea realizar algunas operaciones adicionales específicas de su ensayo.

### <a name="adjust-input-and-output-modules"></a>Ajustar los módulos de entrada y salidos

En su prueba de formación, usa un conjunto de datos de entrenamiento y, a continuación, se produjo algún procesamiento para obtener los datos en un formulario que necesita el algoritmo de aprendizaje del equipo. Si los datos que espera recibir a través del servicio web no tendrá este proceso, puede mover el **módulo de entrada de servicio Web** a un nodo diferente en su prueba.

Por ejemplo, de forma predeterminada, **Configurar servicio Web** coloca el módulo de **entrada de servicio Web** en la parte superior de su flujo de datos, como se muestra en la figura anterior. Sin embargo, si los datos de entrada no tendrá este proceso, puede colocar manualmente la **entrada del servicio Web** más allá de los módulos de procesamiento de datos:

![Mover la entrada de servicio web][figure4]

Los datos de entrada proporcionados a través del servicio web pasará directamente en el módulo de puntuación modelo sin preprocesamiento que sea necesario.

Del mismo modo, de forma predeterminada, **Configurar servicio Web** coloca el módulo de salida del servicio Web en la parte inferior de su flujo de datos. En este ejemplo, el servicio web devolverá al usuario el resultado del [Modelo de puntuación] [ score-model] módulo que incluye el vector completa de datos de entrada, además de la puntuación de resultados.

Sin embargo, si prefiere devolver algo diferentes: por ejemplo, solo los resultados de la puntuación y no el vector completo de datos de entrada -, a continuación, pueden insertar un [Seleccionar columnas de conjunto de datos] [ select-columns] módulo excluir todas las columnas excepto los resultados de la puntuación. A continuación, mueva el módulo de **salida del servicio Web** a los resultados de las [Columnas Seleccione conjunto de datos] [ select-columns] módulo:

![Mover el resultado del servicio web][figure5]

### <a name="add-or-remove-additional-data-processing-modules"></a>Agregar o quitar módulos de procesamiento de datos adicionales

Si hay más módulos en la prueba que sabe que no son necesarios durante la puntuación, se pueden quitar. Por ejemplo, porque el módulo de **entrada del servicio Web** nos hemos movido a un punto después de los módulos de procesamiento de datos, podemos quitar los [Datos que faltan limpiar] [ clean-missing-data] módulo desde el ensayo predictiva.

Nuestra ensayo predictiva ahora es similar a esta:

![Quitar módulo adicional][figure6]

### <a name="add-optional-web-service-parameters"></a>Agregar parámetros opcionales de servicio Web

En algunos casos, desea permitir que el usuario del servicio web cambiar el comportamiento de módulos cuando se tiene acceso al servicio. *Parámetros del servicio Web* permiten hacer esto.

Un ejemplo común está configurando los [Datos de importación] [ import-data] módulo para que el usuario del servicio web implementado puede especificar otro origen de datos cuando se tiene acceso al servicio web. O la configuración de la [Exportación de datos] [ export-data] módulo para que se puede especificar un destino diferente.

Puede definir parámetros de servicio Web y asociarlos con uno o más parámetros del módulo, y puede especificar si son necesarios u opcionales. El usuario del servicio web puede proporcionar valores para estos parámetros cuando se tiene acceso al servicio y las acciones de módulo se modificará en consecuencia.

Para obtener más información acerca de los parámetros del servicio Web, consulte [Uso de Azure máquina aprendizaje parámetros del servicio Web ] [ webserviceparameters].

[webserviceparameters]: machine-learning-web-service-parameters.md


## <a name="deploy-the-predictive-experiment-as-a-web-service"></a>Implementar el ensayo predictiva como un servicio web

Ahora que ha preparado suficientemente el ensayo predictiva, puede implementar como un servicio web de Azure. Mediante el servicio web, los usuarios pueden enviar datos a su modelo y el modelo devolverá sus predicciones.

Para obtener más información sobre el proceso de implementación completa, vea [implementar un servicio web de aprendizaje del equipo de Azure][deploy]

[deploy]: machine-learning-publish-a-machine-learning-web-service.md


<!-- Images -->
[figure1]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure1.png
[figure2]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure2.png
[figure3]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure3.png
[figure4]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure4.png
[figure5]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure5.png
[figure6]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure6.png


<!-- Module References -->
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[export-data]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/
