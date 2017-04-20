<properties
    pageTitle="Implementar un servicio web de aprendizaje del equipo | Microsoft Azure"
    description="Cómo convertir un experimento de entrenamiento en un experimento predictivo, prepararla para su implementación, luego implementarlo como un servicio web de aprendizaje del equipo de Azure."
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

# <a name="deploy-an-azure-machine-learning-web-service"></a>Implementar un servicio web de aprendizaje del equipo de Azure

Aprendizaje de máquinas Azure permite generar, probar e implementar soluciones analíticas predictivas.

Desde un punto-de-vista de alto nivel, esto se hace en tres pasos:

- **[Crear un experimento de capacitación]** - Azure máquina Learning Studio es un entorno de desarrollo visual en colaboración que usas para entrenar y probar un modelo de análisis predictivo utilizando los datos de entrenamiento que se proporciona.
- **[Convertirlo en un experimento predictivo]** - una vez que el modelo se entrenó con los datos existentes y esté listo para utilizarlo para anotar los nuevos datos, preparar y optimizar el experimento para hacer predicciones.
- **Implementarlo como un servicio web** - se puede implementar el experimento predictivo como servicio web Azure [nuevo] o [clásico] . Los usuarios pueden enviar datos a su modelo y recibir las predicciones de su modelo.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="create-a-training-experiment"></a>Crear un experimento de formación

Para formar un modelo de análisis predictivo, utilice Azure máquina de aprendizaje Studio para crear un experimento de formación donde incluye varios módulos para cargar los datos de entrenamiento, preparar los datos según sea necesario, aplicar algoritmos de aprendizaje de la máquina y evaluar los resultados. Puede iterar en un experimento y probar algoritmos de aprendizaje de máquinas diferentes para comparar y evaluar los resultados.

Se explica el proceso de creación y administración de los experimentos de formación más exhaustivamente en otro lugar. Para obtener más información, vea estos artículos:

- [Crear un experimento simple en Azure máquina Learning Studio](machine-learning-create-experiment.md)
- [Desarrollar una solución predictiva con el aprendizaje de la máquina de Azure](machine-learning-walkthrough-develop-predictive-solution.md)
- [Importar los datos de entrenamiento en Azure máquina Learning Studio](machine-learning-data-science-import-data.md)
- [Administrar iteraciones de experimento en Azure máquina Learning Studio](machine-learning-manage-experiment-iterations.md)

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Convertir el experimento de formación en un experimento de predicción

Una vez que ha entrenado su modelo, está preparado para convertir el experimento de formación en un experimento predictivo para anotar los nuevos datos.

Convirtiendo en un experimento predictivo, se está preparando el modelo capacitado para implementarse como un servicio web de puntuación. Los usuarios del servicio web pueden enviar datos de entrada a su modelo y su modelo le enviará los resultados de la predicción. Cuando se convierte en un experimento predictivo, tenga en cuenta cómo piensa su modelo para ser utilizado por otros usuarios.

Para convertir el experimento de formación en un experimento predictivo, haga clic en **Ejecutar** en la parte inferior del lienzo experimento, haga clic en **Configurar servicio Web**, seleccione **Servicio Web predictivo**.

![Convertir en el experimento de puntuación](./media/machine-learning-publish-a-machine-learning-web-service/figure-1.png)

Para obtener más información acerca de cómo realizar esta conversión, vea [convertir un experimento de formación aprendizaje automático a un experimento predictivo](machine-learning-convert-training-experiment-to-scoring-experiment.md).

Los pasos siguientes describen la implementación de un experimento de predicción como un nuevo servicio web. También puede implementar el experimento como servicio de web estándar.

## <a name="deploy-the-predictive-experiment-as-a-new-web-service"></a>Implementar el experimento predictivo como un nuevo servicio web

Ahora que se ha preparado el experimento predictivo, se puede implementar como un servicio web de Azure. Mediante el servicio web, los usuarios pueden enviar los datos a su modelo y el modelo devolverá sus predicciones.

Para implementar el experimento predictivo, haga clic en **Ejecutar** en la parte inferior del lienzo experimento. Una vez que el experimento ha terminado de ejecutarse, haga clic en **Servicio Web de implementación** y seleccione **implementar servicio Web [nuevo]**.  Abre la página de implementación del portal del servicio Web de aprendizaje de la máquina.

### <a name="machine-learning-web-service-portal-deploy-experiment-page"></a>Portal de servicio Web de aprendizaje de máquina página experimento de implementar

En la página implementar el experimento, escriba un nombre para el servicio web.
Seleccione un plan de precios. Si tienes un plan de precios existente, que se puede seleccionar, en caso contrario debe crear un nuevo plan de precios para el servicio.

1.  En el **Plan de precios** de lista desplegable, seleccione un plan existente o seleccione la opción de **Seleccionar el nuevo plan** .
2.  En **Nombre del Plan**, escriba un nombre que identifique el plan en su factura.
3.  Seleccione uno de los **Niveles de Plan mensual**. El valor predeterminado de niveles de plan para los planes de su país o región predeterminados y el servicio web se implementa en esa región.

Haga clic en **Deploy** y la página de **Inicio rápido** para abrir el servicio de web.

La página de inicio rápido de servicio web proporciona acceso y orientación sobre las tareas más comunes que se debe realizar después de crear un servicio web. Desde aquí, puede tener acceso fácilmente tanto la página de prueba y Consume.

<!-- ![Deploy the web service](./media/machine-learning-publish-a-machine-learning-web-service/figure-2.png)-->

### <a name="test-your-web-service"></a>Probar el servicio web

Para probar el nuevo servicio web, haga clic en **probar el servicio web** en tareas comunes. En la página de prueba, puede probar el servicio web como un servicio de petición y respuesta (RR) o un servicio de ejecución por lotes (BES).

La página de prueba de RR muestra las entradas, salidas y cualquier parámetro global que ha definido para el experimento. Para probar el servicio web, puede introducir valores adecuados para las entradas manualmente o proporcionar un archivo con formato de separados por comas (CSV) de valor que contiene los valores de prueba.

Para probar con RR, desde el modo de vista de lista, escriba los valores apropiados para las entradas y haga clic en **Prueba de solicitud-respuesta**. Los resultados de la predicción se muestran en la columna de salida a la izquierda.

![Implementar el servicio web](./media/machine-learning-publish-a-machine-learning-web-service/figure-5-test-request-response.png)

Para probar a su BES, haga clic en **lote**. En la página de prueba por lotes, haga clic en Examinar en su entrada y seleccione un archivo CSV que contiene los valores de ejemplo adecuada. Si no dispone de un archivo CSV y creado el experimento predictivo utilizando máquina de aprendizaje Studio, puede descargar el conjunto de datos para el experimento predictivo y usarlo.

Para descargar el conjunto de datos, abra la máquina de aprendizaje Studio. Abra el experimento predictivo y haga clic con el botón secundario del mouse en la entrada para el experimento. En el menú contextual, seleccione **conjunto de datos** y, a continuación, seleccione **Descargar**.

![Implementar el servicio web](./media/machine-learning-publish-a-machine-learning-web-service/figure-7-mls-download.png)

Haga clic en **prueba**. El estado del trabajo de ejecución por lotes de muestra a la derecha debajo de **Trabajos por lotes de prueba**.

![Implementar el servicio web](./media/machine-learning-publish-a-machine-learning-web-service/figure-6-test-batch-execution.png)

<!--![Test the web service](./media/machine-learning-publish-a-machine-learning-web-service/figure-3.png)-->

En la página de **configuración** , puede cambiar la descripción, título, actualizar la clave de la cuenta de almacenamiento y habilitar los datos de ejemplo para el servicio web.

![Configurar el servicio web](./media/machine-learning-publish-a-machine-learning-web-service/figure-8-arm-configure.png)

Una vez que haya implementado el servicio web, puede:

- **Acceso** a través de la API del servicio web.
- **Administrar** a través del portal de servicios web de aprendizaje del equipo de Azure o el portal clásico de Azure.
- **Actualización** si cambia el modelo.

### <a name="access-the-web-service"></a>Acceso al servicio web

Una vez que implementa el servicio web de estudio de aprendizaje de la máquina, puede enviar datos al servicio y recibir respuestas mediante programación.

La página **Consume** proporciona toda la información que necesita tener acceso al servicio web. Por ejemplo, la clave API se proporciona para permitir el acceso autorizado al servicio.

Para obtener más información acerca del acceso a un servicio web de aprendizaje de la máquina, consulte [cómo consumir un servicio web implementado de aprendizaje de la máquina de Azure](machine-learning-consume-web-services.md).

### <a name="manage-your-new-web-service"></a>Administrar su nuevo servicio web

Puede administrar su portal de servicios Web de aprendizaje de la máquina de servicios web clásica. Desde la [página principal del portal](https://services.azureml-test.net/), haga clic en **Servicios Web**. Desde la página de servicios web, puede eliminar o copiar un servicio. Para supervisar un servicio específico, haga clic en el servicio y, a continuación, haga clic en **escritorio**. Para supervisar los trabajos por lotes asociados al servicio web, haga clic en **El registro de solicitudes de lotes**.

## <a name="deploy-the-predictive-experiment-as-a-classic-web-service"></a>Implementar el experimento predictivo como servicio web clásico

Ahora que se ha preparado suficientemente el experimento predictivo, se puede implementar como un servicio web de Azure. Mediante el servicio web, los usuarios pueden enviar los datos a su modelo y el modelo devolverá sus predicciones.

Para implementar el experimento predictivo, haga clic en **Ejecutar** en la parte inferior del lienzo experimento y, a continuación, haga clic en **Servicio Web de implementación**. El servicio web está configurado y se colocan en el tablero de mandos del servicio web.

![Implementar el servicio web](./media/machine-learning-publish-a-machine-learning-web-service/figure-2.png)

Puede probar el servicio web en el portal de servicios Web de aprendizaje de la máquina o la máquina de aprendizaje Studio.

Para probar el servicio web de solicitud de respuesta, haga clic en el botón de **prueba** en el escritorio de servicio web. Aparece un cuadro de diálogo para solicitar los datos de entrada para el servicio. Éstas son las columnas esperadas por el experimento de puntuación. Escriba un conjunto de datos y, a continuación, haga clic en **Aceptar**. Los resultados generados por el servicio web se muestran en la parte inferior del panel.

Puede hacer clic en el vínculo de vista previa **de prueba** para probar el servicio en el portal Web de Azure máquina Learning Services como se mostró anteriormente en la sección de servicio web nuevo.

Para probar el servicio de ejecución por lotes, haga clic en **probar** vínculo de vista previa. En la página de prueba por lotes, haga clic en Examinar en su entrada y seleccione un archivo CSV que contiene los valores de ejemplo adecuada. Si no dispone de un archivo CSV y creado el experimento predictivo utilizando máquina de aprendizaje Studio, puede descargar el conjunto de datos para el experimento predictivo y usarlo.

![Probar el servicio web](./media/machine-learning-publish-a-machine-learning-web-service/figure-3.png)

En la página de **configuración** , puede cambiar el nombre para mostrar del servicio y proporcionar una descripción. El nombre y la descripción se muestra en el [portal clásico Azure](http://manage.windowsazure.com/) donde gestionar sus servicios web.

Puede proporcionar una descripción para los datos de entrada, salida de datos y parámetros del servicio web escribiendo una cadena para cada columna en el **Esquema de entrada**, **Esquemas de salida**y **Parámetro del servicio Web**. Estas descripciones se utilizan en la documentación de código de ejemplo proporcionada para el servicio web.

Puede habilitar el registro diagnosticar los errores que está viendo cuando se tiene acceso el servicio web. Para obtener más información, vea [Habilitar el registro para los servicios web de aprendizaje de la máquina](machine-learning-web-services-logging.md).

![Configurar el servicio web](./media/machine-learning-publish-a-machine-learning-web-service/figure-4.png)

También puede configurar los extremos del servicio web en el portal de aprendizaje Web Servicios de Azure equipo similar al procedimiento mostrado anteriormente en la sección de servicio web nuevo. Las opciones son diferentes, puede agregar o cambiar la descripción del servicio, habilitar el registro y activar los datos de ejemplo para probar.

### <a name="access-the-web-service"></a>Acceso al servicio web

Una vez que implementa el servicio web de estudio de aprendizaje de la máquina, puede enviar datos al servicio y recibir respuestas mediante programación.

El tablero de mandos proporciona toda la información que necesita tener acceso al servicio web. Por ejemplo, la clave API se proporciona para permitir el acceso autorizado al servicio y páginas de Ayuda de API sirven para ayudarle a empezar a escribir el código.

Para obtener más información acerca del acceso a un servicio web de aprendizaje de la máquina, consulte [cómo consumir un servicio web implementado de aprendizaje de la máquina de Azure](machine-learning-consume-web-services.md).

### <a name="manage-the-web-service"></a>Administrar el servicio web

Hay varias de las acciones que puede realizar para supervisar un servicio web. Puede actualizarla y eliminarlo. También puede agregar extremos adicionales a un servicio web de clásico además del extremo predeterminado que se crea cuando se implemente.

Para obtener más información, vea [administrar un área de trabajo de aprendizaje de la máquina de Azure](machine-learning-manage-workspace.md) y [administrar un servicio web mediante el portal de servicios Web de Azure máquina de aprendizaje](machine-learning-manage-new-webservice.md).

<!-- When this article gets published, fix the link and uncomment
For more information on how to manage Azure Machine Learning web service endpoints using the REST API, see **Azure machine learning web service endpoints**.
-->

## <a name="update-the-web-service"></a>Actualizar el servicio web

Puede realizar cambios en el servicio web, como actualizar el modelo con datos de entrenamiento adicional e implementar nuevo, sobrescribiendo el servicio web original.

Para actualizar el servicio web, abra el experimento predictivo original que se utilizó para implementar el servicio web y hacer una copia editable, haga clic en **Guardar como**. Realice los cambios y, a continuación, haga clic en **Servicio Web de implementación**.

Debido a que ha implementado este experimento antes, le pregunta si desea sobrescribir (servicio Web clásico) o actualizar (nuevo servicio web) el servicio existente. Haga clic en **Sí** o **actualización** detiene el servicio web existente e implementa el nuevo experimento predictivo se implementa en su lugar.

> [AZURE.NOTE] Si ha realizado cambios de configuración en el servicio web original, por ejemplo, escribir un nuevo nombre para mostrar o descripción, necesitará especificar esos valores de nuevo.

Una opción para actualizar el servicio web es el modelo de reciclaje mediante programación. Para obtener más información, consulte [aprendizaje de máquinas de reciclaje modela mediante programación](machine-learning-retrain-models-programmatically.md).


<!-- internal links -->
[Crear un experimento de formación]: #create-a-training-experiment
[Convertirlo en un experimento de predicción]: #convert-the-training-experiment-to-a-predictive-experiment
[Nuevo]: #deploy-the-predictive-experiment-as-a-new-Web-service
[clásico]: #deploy-the-predictive-experiment-as-a-new-Web-service
[Access]: #access-the-Web-service
[Manage]: #manage-the-Web-service-in-the-azure-management-portal
[Update]: #update-the-Web-service
