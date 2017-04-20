<properties
    pageTitle="Paso 5: Implementar el servicio Web de aprendizaje de equipo | Microsoft Azure"
    description="Paso 5 de la desarrollar un tutorial de solución predictivo: implementar un experimento predictivo en equipo Studio de aprendizaje como un servicio Web."
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
    ms.date="10/05/2016"
    ms.author="garye"/>


# <a name="walkthrough-step-5-deploy-the-azure-machine-learning-web-service"></a>Tutorial paso 5: Implementar el servicio Web de aprendizaje del equipo de Azure

Este es el quinto paso del tutorial, [desarrollar una solución de analítica predictiva en aprendizaje de máquinas de Azure](machine-learning-walkthrough-develop-predictive-solution.md)


1.  [Crear un área de aprendizaje automático](machine-learning-walkthrough-1-create-ml-workspace.md)
2.  [Cargar datos existentes](machine-learning-walkthrough-2-upload-data.md)
3.  [Crear un nuevo experimento](machine-learning-walkthrough-3-create-new-experiment.md)
4.  [Entrenar y evaluar los modelos](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.  **Implementar el servicio Web**
6.  [Acceso al servicio Web](machine-learning-walkthrough-6-access-web-service.md)

----------

Para permitir que otros usuarios puedan utilizar el modelo de predicción, hemos desarrollado en este tutorial, se implementa como un servicio Web en Azure.

Hasta este punto hemos estado experimentando con nuestro modelo de formación. Pero el servicio implementado ya no va a realizar la formación: genera predicciones al anotar la entrada del usuario basada en nuestro modelo. Por lo que vamos a hacer algunos preparación para convertir este experimento de un experimento de ***formación*** para una ***predicción*** experimentar. 

Se trata de un proceso de dos pasos:  

1. Convertir la *formación experimentar* que hemos creado en un *experimento de predicción*
2. Implementar el experimento predictivo como servicio Web

Pero en primer lugar, debemos reducir este experimento un poco. Actualmente disponemos de dos modelos distintos en el experimento, pero sólo queremos un modelo cuando implementamos esto como un servicio Web.  

Supongamos que hemos decidido que el árbol se incrementó fue el mejor modelo utilizar. Así que lo primero que hay que hacer es eliminar la [Máquina de vectores de soporte de dos clases] de[ two-class-support-vector-machine] módulo y los módulos que se utilizaron para la formación. Puede que desee realizar una copia del experimento primero haciendo clic en **Guardar como** en la parte inferior del lienzo experimento.

Es necesario eliminar los siguientes módulos:  

- [Máquina de soporte de dos clases de Vector][two-class-support-vector-machine]
- [Modelo del tren] [ train-model] y el [Modelo de puntuación] [ score-model] módulos que estaban conectados a él
- [Normalizar datos] [ normalize-data] (dos)
- [Evaluar el modelo][evaluate-model]

Seleccione el módulo y presione la tecla SUPR o haga clic en el módulo y seleccione **Eliminar**.

Ahora estamos listos para implementar este modelo mediante el [Árbol de decisión de aumentar de dos clases]de[two-class-boosted-decision-tree].

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Convertir el experimento de formación en un experimento de predicción

Convertir en un experimento predictivo implica tres pasos:

1. Guardar el modelo se ha entrenado y reemplace nuestros módulos de capacitación
2. Recortar el experimento para quitar módulos que sólo se necesitaron para la formación
3. Definir que el servicio Web aceptará la entrada y donde se genera la salida

Afortunadamente, los tres pasos pueden realizarse haciendo clic en **establecer seguridad de Web service** en la parte inferior del lienzo experimento (seleccione la opción **servicio Web predictivo** ).

Al hacer clic en **establecer seguridad de Web service**, suceden varias cosas:

- El modelo capacitado se guarda como un único módulo de **Modelo capacitados** en la paleta de módulo a la izquierda del lienzo experimento (puede encontrarlo en **Modelos capacitados**).
- Se quitaron los módulos que se utilizaron para la formación. En concreto:
  - [Árbol de decisión se incrementó de dos clases][two-class-boosted-decision-tree]
  - [Modelo de tren][train-model]
  - [Datos divididos][split]
  - el segundo [Ejecutar Script de R] [ execute-r-script] módulo que fue utilizado para los datos de prueba
- Se agrega el modelo guardado capacitado en el experimento.
- Se agregan módulos de **entrada de servicio Web** y el **resultado del servicio Web** .

> [AZURE.NOTE] Se ha guardado el experimento en dos partes en fichas que se han agregado en la parte superior del lienzo de experimento: el experimento de formación original está en la ficha **formación experimentar**y el experimento predictivo recién creado está bajo **predictivo experimentar**.

Debemos realizar un paso adicional con este experimento particular.
Hemos agregado dos [Ejecutar Script de R] [ execute-r-script] módulos para proporcionar una función de ponderación a los datos de formación y prueba. No es necesario hacerlo en el modelo final.

Estudio de aprendizaje de máquina quitado uno [Ejecutar Script de R] [ execute-r-script] módulo cuando quita la [división] [ split] módulo. Ahora podemos quitar el otro y [Editor de metadatos] de conectar ahora[ metadata-editor] directamente al [Modelo de puntuación][score-model].    

Nuestro experimento debe tener el siguiente aspecto:  

![Puntuación del modelo capacitado][4]  

> [AZURE.NOTE] Puede que se pregunte por qué salimos del conjunto de datos de datos de la tarjeta de crédito de UCI alemán en el experimento de predicción. El servicio se va a utilizar los datos del usuario, no el conjunto de datos original, así que ¿por qué dejar el conjunto de datos original en el modelo?
>
>Es cierto que el servicio no necesita los datos de la tarjeta de crédito original. Pero necesita el esquema de datos, que incluye información como el número de columnas hay y qué columnas son numéricas. Esta información del esquema es necesaria interpretar los datos del usuario. Dejaremos estos componentes conectados de modo que el módulo de resultados tiene el esquema del conjunto de datos cuando se ejecuta el servicio. Los datos no se utilizan, sólo el esquema.  

Ejecutar el experimento una última vez (haga clic en **Ejecutar**.) Si desea comprobar que el modelo sigue funcionando, haga clic en el resultado del [Modelo de puntuación] [ score-model] módulo y seleccione **Ver resultados**. Verá que se muestran los datos originales, junto con el valor de riesgo de crédito ("anotó Labels") y el valor de probabilidad de puntuación ("anotó probabilidades".) 

## <a name="deploy-the-web-service"></a>Implementar el servicio Web

Puede implementar el experimento como un servicio Web clásico o un nuevo servicio Web basado en el Administrador de recursos de Azure.

### <a name="deploy-as-a-classic-web-service"></a>Implementar como un servicio Web clásico ###

Para implementar un servicio Web clásico derivado de nuestro experimento, haga clic en **Implementar el servicio de Web** debajo del lienzo y seleccione **Implementar servicio Web [clásico]**. Máquina Learning Studio implementa el experimento como un servicio Web y le lleva al escritorio para ese servicio Web. Desde aquí, puede volver al experimento (**Ver instantáneas** o **más reciente**) y ejecutar una simple prueba del servicio Web (vea **prueba del servicio Web** a continuación). También es aquí información para crear aplicaciones que pueden tener acceso al servicio Web (más que en el paso siguiente de este tutorial).

![Panel de servicio Web][6]

Puede configurar el servicio haciendo clic en la ficha **configuración** . Aquí puede modificar el nombre del servicio (ha dado el nombre de experimento por defecto) y proporcionar una descripción. También puede dar más descriptivas etiquetas para las columnas de entrada y salidas.  

![Configurar el servicio Web][5]  

### <a name="deploy-as-a-new-web-service"></a>Implementar como un servicio Web nuevo

Para implementar un servicio Web nuevo derivado de nuestro experimento, haga clic en **Servicio Web de implementación** por debajo del lienzo e **Implementar Web Service [New]**. Estudio de aprendizaje de la máquina se transfiere a la página de experimento implementar servicios Web de aprendizaje del equipo de Azure.

Escriba un nombre para el servicio Web y seleccione un plan de precios. Si tienes un plan de precios existente, que se puede seleccionar, en caso contrario debe crear un nuevo plan de precios para el servicio. 

1.  En el menú desplegable de **Plan de precios** , seleccione un plan existente o seleccione la opción de **Seleccionar el nuevo plan** .
2.  En **Nombre del Plan**, escriba un nombre que identifica el plan en su factura.
3.  Seleccione uno de los **Niveles de Plan mensual**. El valor predeterminado de niveles de plan para los planes de su país o región predeterminados y el servicio Web se implementa en esa región.

Haga clic en **Deploy** y la página de **Inicio rápido** para abrir el servicio de Web.

Puede configurar el servicio haciendo clic en la opción de menú **Configurar** . Aquí puede modificar el nombre del servicio (ha dado el nombre de experimento por defecto) y proporcionar una descripción. 

Para probar la selección de servicio Web, haga clic en la opción de menú **de prueba** (consulte **probar el servicio Web** más abajo). Para obtener información sobre cómo crear aplicaciones que pueden tener acceso al servicio Web, haga clic en la opción de menú **Consume** (más en el paso siguiente de este tutorial).

> [AZURE.TIP] Puede actualizar el servicio Web después de que se ha implementado. Por ejemplo, si desea cambiar su modelo, editar el experimento de aprendizaje, ajustar los parámetros del modelo y haga clic en **Servicio Web de implementación**. A continuación, seleccione **Implementar servicio Web [clásico]** o **implementar Web [New]**. Cuando vuelva a implementar el experimento, reemplaza el servicio Web, ahora con su modelo actualizado.  

## <a name="test-the-web-service"></a>Probar el servicio Web

### <a name="test-a-classic-web-service"></a>Probar un servicio Web clásico

Puede probar el servicio en el estudio de aprendizaje de la máquina o en el portal Web de Azure máquina Learning Services. Pruebas en el portal Web de Azure máquina Learning Services tiene la ventaja de permitir que se habilite 

**Probar en máquina Learning Studio**

En la página del **escritorio** , haga clic en el botón **prueba** en **Extremo predeterminado**. Un cuadro de diálogo se emergen y solicitar los datos de entrada para el servicio. Estas son las mismas columnas que aparecían en el conjunto de datos de riesgo de crédito alemán original.  

Escriba un conjunto de datos y, a continuación, haga clic en **Aceptar**. 

**Probar en el portal de servicios de Web de aprendizaje de máquina de Azure**

En la página de **panel** , haga clic en el vínculo vista previa de **prueba** en el **Extremo predeterminado**. La página de prueba en el portal Web de Azure máquina Learning Services para el extremo del servicio Web se abre y le pide los datos de entrada para el servicio. Estas son las mismas columnas que aparecían en el conjunto de datos de riesgo de crédito alemán original.

Haga clic en **Prueba de solicitud-respuesta**. 

En el servicio Web, se ingresan los datos a través del módulo de **entrada del servicio Web** , a través del [Editor de metadatos] [ metadata-editor] módulo y al [Modelo de puntuación] [ score-model] módulo donde se anotó. A continuación, se envían los resultados del servicio Web a través de la **salida del servicio Web**.

**Probar un nuevo servicio Web**

En el portal de servicios Web de aprendizaje del equipo de Azure, haga clic en **prueba** en la parte superior de la página. Se abre la página de **prueba** y que se pueden introducir datos para el servicio. Los campos de entrada mostrados corresponden a las columnas que se presentaron en el conjunto de datos de riesgo de crédito alemán original. 

Escriba un conjunto de datos y, a continuación, haga clic en **Prueba de solicitud-respuesta**.

Mostrarán los resultados de la prueba en el lado derecho de la página en la columna de salida. 

Cuando se prueba en el portal de servicios de Web de aprendizaje de máquina de Azure, puede habilitar los datos de ejemplo que puede utilizar para probar el servicio de solicitud y respuesta. Si ha creado el servicio Web en equipo aprendizaje Studio, los datos de ejemplo se toman de los datos de los utilizados para entrenar el modelo.

> [AZURE.TIP] La forma que tenemos el experimento predictivo configurado, todo el resultado del [Modelo Score] [ score-model] módulo se devuelven. Esto incluye todos los datos de entrada más el valor de riesgo de crédito y la puntuación de probabilidad. Si desea devolver algo diferente - por ejemplo, sólo el crédito riesgo valor -, a continuación, puede insertar un [Proyecto columnas] [ project-columns] módulo entre [Modelo Score] [ score-model] y el **resultado del servicio Web** para eliminar las columnas que no desea que el servicio Web puede devolver. 

## <a name="manage-the-web-service"></a>Administrar el servicio Web

**Administrar un servicio Web clásico**

Una vez que haya implementado el servicio Web clásico, puede administrar desde el [portal de clásico de Azure](https://manage.windowsazure.com).

1. Iniciar sesión en el [portal de clásico de Azure](https://manage.windowsazure.com).
2. En el panel de servicios Microsoft Azure, haga clic en **Aprendizaje de máquinas**.
3. Haga clic en el área de trabajo.
4. Haga clic en la ficha de **servicios Web** .
5. Haga clic en el servicio Web que hemos creado.
6. Haga clic en el extremo "predeterminado".

Desde aquí, puede hacer cosas como supervisar cómo está haciendo el servicio Web y hacer ajustes de rendimiento cambiando concurrentes cuántos llama al servicio pueden controlar.
Incluso, puede publicar el servicio Web en el mercado de Azure.

Para obtener más detalles, consulte:

- [La creación de extremos](machine-learning-create-endpoint.md)
- [Escala de servicio Web](machine-learning-scaling-webservice.md)
- [Publicar el servicio Web de aprendizaje del equipo de Azure en el mercado de Azure](machine-learning-publish-web-service-to-azure-marketplace.md)

**Administrar un servicio Web en el portal de servicios de Web de aprendizaje de máquina de Azure**

Una vez que haya implementado el servicio Web, clásica o nuevo, puede administrar desde el [portal de servicios Web de aprendizaje del equipo de Azure](https://servics.azureml.net).

Para supervisar el rendimiento del servicio Web:

1. Iniciar sesión en el [portal de servicios Web de aprendizaje del equipo de Azure](https://servics.azureml.net).
2. Haga clic en **servicios Web**.
3. Haga clic en el servicio Web.
4. Haga clic en el **tablero de mandos**.

----------

**Siguiente: [acceso al servicio Web](machine-learning-walkthrough-6-access-web-service.md)**

[1]: ./media/machine-learning-walkthrough-5-publish-web-service/publish1.png
[2]: ./media/machine-learning-walkthrough-5-publish-web-service/publish2.png
[3]: ./media/machine-learning-walkthrough-5-publish-web-service/publish3.png
[4]: ./media/machine-learning-walkthrough-5-publish-web-service/publish4.png
[5]: ./media/machine-learning-walkthrough-5-publish-web-service/publish5.png
[6]: ./media/machine-learning-walkthrough-5-publish-web-service/publish6.png


<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[metadata-editor]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/
[project-columns]: https://msdn.microsoft.com/en-us/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/