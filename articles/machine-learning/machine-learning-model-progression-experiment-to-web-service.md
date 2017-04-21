<properties
    pageTitle="Cómo avanza de un modelo de aprendizaje de apropiados a un servicio Web operationalized | Microsoft Azure"
    description="Información general sobre los aspectos prácticos de cómo experimentar que avanza el modelo de Azure el aprendizaje de desarrollo a un servicio Web operationalized."
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
    ms.date="10/04/2016"
    ms.author="garye"/>


# <a name="how-a-machine-learning-model-progresses-from-an-experiment-to-an-operationalized-web-service"></a>Cómo avanza de un modelo de aprendizaje de apropiados a un servicio Web operationalized

Azure Studio de aprendizaje de máquina proporciona un lienzo interactivo que permite desarrollar, ejecutar, probar y repetir una ***experimentar*** que representa un modelo de análisis de predicción. Hay una amplia variedad de módulos que puede:

* Datos de entrada en el ensayo
* Manipular los datos
* Entrenar a un modelo con algoritmos de aprendizaje de equipo
* Puntuación del modelo
* Evaluar los resultados
* Valores de salida finales

Cuando esté satisfecho con el ensayo, puede implementar como un ***servicio Web de aprendizaje de máquina Azure clásico*** o un ***servicio Web de aprendizaje de nuevo Azure máquina*** para que los usuarios puedan enviar nuevos datos y recibir resultados.

En este artículo, le ofrecemos información general sobre los aspectos prácticos de cómo experimentar que avanza el modelo de aprendizaje del equipo de desarrollo a un servicio Web operationalized.

>[AZURE.NOTE] Hay otras maneras para desarrollar e implementar modelos de aprendizaje de equipo, pero en este artículo se centra en cómo usar Studio de aprendizaje del equipo. Para obtener información sobre cómo crear un servicio Web predictivo clásico con R, consulte la entrada de blog [generación e implementar predictiva Web aplicaciones usando RStudio y m de Azure](http://blogs.technet.com/b/machinelearning/archive/2015/09/25/build-and-deploy-a-predictive-web-app-using-rstudio-and-azure-ml.aspx).

Aunque Azure máquina aprendizaje Studio está diseñado para ayudarle a desarrollar e implementar un *modelo de análisis predictiva*, es posible usar Studio para desarrollar un ensayo que no incluye un modelo de análisis de predicción. Por ejemplo, un ensayo podría solo los datos de entrada, manipularlos y, a continuación, los resultados. Al igual que un ensayo análisis predictiva, puede implementar este ensayo no predictiva como un servicio Web, pero es un proceso más sencillo porque el ensayo no formación o un modelo de aprendizaje de puntuación. Aunque no es típico utilizar Studio de este modo, se deberá incluir en la discusión para que le podemos dar una explicación completa de cómo funciona Studio.

## <a name="developing-and-deploying-a-predictive-web-service"></a>Desarrollar e implementar un servicio Web predictivo

Estas son las fases que sigue a una solución típica como desarrollar e implementarlo con Machine Studio de aprendizaje:

![Flujo de implementación](media\machine-learning-model-progression-experiment-to-web-service\model-stages-from-experiment-to-web-service.png)

*Figura 1 - etapas de un modelo de análisis predictiva típica*

### <a name="the-training-experiment"></a>Ensayo de formación

La ***formación experimentar*** es la fase inicial de desarrollar el servicio Web en Studio de aprendizaje del equipo. El propósito de ensayo de formación es proporcionarle un lugar para desarrollar, probar, trabajemos y finalmente entrenar a un modelo de aprendizaje de equipo. Puede incluso entrenar varios modelos simultáneamente como busque la mejor solución, pero cuando haya terminado de experimentar seleccione un único capacitado eliminar el resto de la ensayo y del modelo. Para obtener un ejemplo de desarrollar un análisis predictivo experimentar, vea [desarrollar una solución de análisis predictiva de evaluación de riesgos de crédito en Azure el aprendizaje](machine-learning-walkthrough-develop-predictive-solution.md).

### <a name="the-predictive-experiment"></a>El ensayo predictiva

Una vez que tenga un modelo de formación en su prueba de formación, haga clic en **Configurar servicio Web** y seleccione **Predictiva servicio Web** en el equipo Studio de aprendizaje para iniciar el proceso de convertir el ensayo formación a un ***ensayo predictiva***. El propósito de la ensayo predictiva es usar el modelo cualificado para puntuación nuevos datos, con el objetivo de convertirse en operationalized finalmente como un servicio Web de Windows Azure.

Esta conversión se realiza para usted a través de los siguientes pasos:

-   Convertir el conjunto de módulos utilizados para formación en un único módulo y guardarlo como un modelo de formación

-   Eliminar todos los módulos extraños no relacionados con la puntuación

-   Agregar puertos de entrada y salidos que va a utilizar el servicio Web final

Puede haber más cambios que desea hacer preparar su ensayo predictiva implementar como un servicio Web. Por ejemplo, si desea que el servicio Web para mostrar solo un subconjunto de resultados, puede agregar un módulo de filtrado antes el puerto de salida.

En este proceso de conversión, no se descarta el ensayo de aprendizaje. Una vez finalizado el proceso, tiene dos pestañas en Studio: uno para el ensayo de formación y otro para el ensayo predictiva. De esta forma que puede realizar cambios en el ensayo formación antes de implementar el servicio Web y volver a crear el ensayo predictiva. O bien, puede guardar una copia de ensayo de formación para iniciar otra línea de experimentación.

>[AZURE.NOTE] Al hacer clic en **Servicio de Web predictiva** iniciar un proceso automático para convertir el ensayo formación en un ensayo predictivo y esto funciona bien en la mayoría de los casos. Si el ensayo formación es complejo (por ejemplo, si tienen varias rutas de aprendizaje que une), es preferible hacer esta conversión manualmente. Para obtener más información, vea [convertir un ensayo de cursos de aprendizaje a un ensayo predictivo](machine-learning-convert-training-experiment-to-scoring-experiment.md).

### <a name="the-web-service"></a>El servicio Web

Una vez satisfecho que el ensayo predictiva esté listo, puede implementar el servicio como un servicio Web clásico o un servicio Web nueva basada en el Administrador de recursos de Azure. Para que controle el modelo mediante la implementación como un *servicio Web de aprendizaje de máquina clásica*, haga clic en **Servicio Web de implementar** y seleccione **Implementar servicios Web [clásica]**. Para implementar como *servicio Web de aprendizaje de equipo nuevo*, haga clic en **Servicio Web de implementar** y seleccione **Implementar [New] servicio Web**. Los usuarios ahora pueden enviar datos a su modelo mediante el servicio Web REST API y volver recibir los resultados. Para obtener más información, vea [cómo utilizar un servicio Web de aprendizaje de Azure automática que se ha implementado desde un equipo de ensayo de aprendizaje](machine-learning-consume-web-services.md).

## <a name="the-non-typical-case-creating-a-non-predictive-web-service"></a>El caso no típico: crear un servicio Web no predictiva

Si el ensayo no formar a un modelo de análisis predictiva, entonces no es necesario crear un ensayo de aprendizaje y un ensayo puntuación: hay una sola ensayo y puede implementar como un servicio Web. Máquina aprendizaje Studio detecta si el ensayo contiene un modelo de predicción analizando los módulos que ha usado.

Después de que haya itera en su ensayo y está satisfecho con ella:

1.  Haga clic en **Configurar servicio Web** y seleccione el **Servicio Web de formación** : se agregan automáticamente los nodos de entrada y salidos

2.  Haga clic en **Ejecutar**

3. Haga clic en **Servicio Web de implementar** y seleccione **Implementar servicio Web [clásica]** o **implementar Web [New]** según el entorno de implementar.

Ahora se implementa el servicio Web, y puede tener acceso y administrar como un servicio Web predictivo.

## <a name="updating-your-web-service"></a>Actualizar el servicio Web

Ahora que ha implementado el ensayo como un servicio Web, ¿qué sucede si necesita actualizar?

Que depende de lo que necesita actualizar:

**Para cambiar la entrada o salida o desea modificar la forma en que el servicio Web administra los datos**

Si no va a cambiar el modelo, pero solo se cambia el modo en que el servicio Web controla los datos, puede editar el ensayo predictiva y haga clic en **Servicio Web de implementar** y vuelva a seleccionar **Implementar servicio Web [clásica]** o **implementar Web [New]** . Detener el servicio Web, se implementa el ensayo predictiva actualizada y reiniciar el servicio Web.

Aquí tenemos un ejemplo: supongamos que su ensayo predictiva devuelve toda la fila de datos de entrada con el resultado previsto. Puede decidir que desea que el servicio Web para devolver simplemente el resultado. Así que puede agregar un módulo de **Columnas de proyecto** en la misma predictiva, justo antes del puerto de salida, excluir las columnas que no sea el resultado. Cuando haga clic en **Servicio Web de implementar** y vuelva a seleccionar **Implementar servicio Web [clásica]** o **implementar Web [New]** , se actualiza el servicio Web.

**Desea que el modelo de datos nuevos de reciclaje**

Si desea conservar el modelo de aprendizaje de equipo, pero que le gustaría reciclaje con los nuevos datos, tiene dos opciones:

1.  **El modelo mientras se ejecuta el servicio Web de reciclaje** - si desea que el modelo mientras la predicción de reciclaje se está ejecutando el servicio Web, puede hacerlo realizando un par modificaciones en el ensayo de aprendizaje para que sea un ***ensayo reconversión***y luego puede implementar como un **servicio *web reconversión* **. Para obtener instrucciones sobre cómo hacerlo, consulte [reciclaje aprendizaje modelos mediante programación](machine-learning-retrain-models-programmatically.md).

2.  **Vuelva a la original ensayo de formación y usar los datos de diferentes de formación para desarrollar el modelo** - el ensayo predictiva está vinculado al servicio Web, pero el ensayo formación no está vinculado directamente de esta manera. Si modifica el ensayo formación original y haga clic en **Configurar servicio Web**, se creará un *nuevo* predictiva experimentar que, cuando se implementa, se creará un *nuevo* servicio Web. Solo no actualiza el servicio Web original.

    Si necesita modificar el ensayo de formación, ábralo y haga clic en **Guardar como** para hacer una copia. Esto deja intacta la misma formación original, ensayo predictiva y servicio Web. Ahora puede crear un nuevo servicio Web con los cambios. Una vez que se ha implementado el nuevo servicio Web, a continuación, puede decidir si detener el servicio Web anterior o mantenerlo ejecuta junto con uno nuevo.

**Desea entrenar un modelo diferente**

Si desea realizar cambios en su ensayo predictiva original, como la selección de un algoritmo de aprendizaje de otro equipo, intentando un método de aprendizaje diferente, por ejemplo, que es necesario que siga el segundo procedimiento descrito anteriormente para formación del modelo: abra el ensayo de formación, haga clic en **Guardar como** para hacer una copia y, a continuación, inicie la ruta de acceso nueva de desarrollo de un modelo de , crear el ensayo predictiva e implementar el servicio web. Se creará un nuevo sitio Web servicio no está relacionado con el elemento original -, puede decidir qué uno o ambos, mantener en ejecución.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más detalles sobre el proceso de desarrollo y ensayo, consulte los siguientes artículos:

-   convertir el ensayo: [convertir un ensayo de cursos de aprendizaje a un ensayo predictivo](machine-learning-convert-training-experiment-to-scoring-experiment.md)

-   implementar el servicio Web: [implementar un servicio web de aprendizaje del equipo de Azure](machine-learning-publish-a-machine-learning-web-service.md)

-   formación del modelo - [aprendizaje de reciclaje modelos mediante programación](machine-learning-retrain-models-programmatically.md)

Para obtener ejemplos de todo el proceso, consulte:

-   [Tutorial de aprendizaje de máquina: crear su primer ensayo en Azure máquina aprendizaje Studio](machine-learning-create-experiment.md)

-   [Tutorial: Desarrollar una solución de análisis predictiva para evaluación de riesgos de crédito en aprendizaje de máquina de Azure](machine-learning-walkthrough-develop-predictive-solution.md)