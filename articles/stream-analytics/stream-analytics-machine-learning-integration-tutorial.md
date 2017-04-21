<properties
    pageTitle="Análisis de opinión mediante el análisis de secuencia de Azure y aprendizaje del equipo de Azure | Microsoft Azure"
    description="Cómo usar una función definida por el usuario y el aprendizaje en una tarea de análisis de secuencia"
    keywords=""
    documentationCenter=""
    services="stream-analytics"
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"
/>


<tags 
    ms.service="stream-analytics" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="10/04/2016" 
    ms.author="jeffstok"
/>

# <a name="sentiment-analysis-by-using-azure-stream-analytics-and-azure-machine-learning"></a>Análisis de opinión mediante el análisis de secuencia de Azure y aprendizaje del equipo de Azure #

En este artículo está diseñado para ayudarle a configurar rápidamente una tarea de análisis de secuencia de Azure simple, con la integración de aprendizaje del equipo de Azure. Se use un modelo de aprendizaje de análisis de opinión de la Galería de inteligencia Cortana para analizar los datos de texto streaming y determinar la calificación de opinión en tiempo real. La información en este artículo puede ayudarle a entender escenarios como análisis de opinión en tiempo real en la transmisión de datos de Twitter, analizar los registros de chats de cliente con el personal de soporte técnico y evaluar comentarios en los foros, blogs y vídeos, además de muchos otros en tiempo real, predictivas puntuación escenarios.

Este artículo ofrece un archivo CSV de muestra con texto como entrada en el almacenamiento de blobs de Windows Azure, que se muestra en la siguiente imagen. El trabajo aplica el modelo de análisis de opinión como una función definida por el usuario (UDF) en los datos de texto de ejemplo desde el almacenamiento de blobs de Windows. El resultado final se coloca en el mismo almacén de blobs de otro archivo CSV. 

![Aprendizaje de máquina de análisis de secuencia](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)  

La imagen siguiente muestra esta configuración. Para un escenario más realista, puede reemplazar el almacenamiento de blobs con transmisión de datos de Twitter de una entrada de Azure evento Hubs. Además, puede crear una visualización en tiempo real de [Microsoft Power BI](https://powerbi.microsoft.com/) de la opinión agregada.    

![Aprendizaje de máquina de análisis de secuencia](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)  

## <a name="prerequisites"></a>Requisitos previos

Los requisitos previos para completar las tareas que se muestran en este artículo son los siguientes:

-   Una suscripción activa de Azure.
-   Un archivo CSV con algunos datos en él. Puede descargar el archivo que se muestra en la figura 1 de [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample Data/sampleinput.csv), o puede crear su propio archivo. En este artículo, se supone que usa uno suministrado para su descarga en GitHub.

En un nivel alto, para completar las tareas que se muestra en este artículo, tendrá que hacer lo siguiente:

1.  Cargue el archivo CSV de entrada al almacenamiento de blobs de Windows Azure.
2.  Agregar un modelo de análisis de opinión de la Galería de inteligencia de Cortana para el área de trabajo de aprendizaje del equipo de Azure.
3.  Implementar este modelo como un servicio web en el área de trabajo de aprendizaje del equipo.
4.  Crear una tarea de análisis de secuencia que llama a este servicio web como una función para determinar la opinión de la entrada de texto.
5.  Iniciar el trabajo de análisis de secuencia y observe el resultado.

## <a name="upload-the-csv-input-file-to-blob-storage"></a>Cargar el archivo CSV de entrada al almacenamiento de blobs

En este paso, puede usar cualquier archivo CSV, como el que se ha especificado como disponible para su descarga en GitHub. Puede usar [El Explorador de almacenamiento de Azure](http://storageexplorer.com/) o Visual Studio para cargar el archivo o puede utilizar código personalizado. Usamos ejemplos basados en Visual Studio.

1.  En Visual Studio, haga clic en **Azure** > **almacenamiento** > **Adjuntar almacenamiento externo**. Escriba un **nombre de cuenta** y una **clave de cuenta**.  

    ![Análisis de secuencia de máquina aprendizaje, Explorador de servidores](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-server-explorer.png)  

2.  Expandir el almacenamiento conectado en el paso 1, haga clic en **Crear contenedor de blobs de Windows**y, a continuación, escriba un nombre lógico. Después de crear el contenedor, abrirlo para ver su contenido. (Está vacía en este momento).  

    ![Aprendizaje del equipo de análisis de flujo, crear blob](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-create-blob.png)  

3.  Para cargar el archivo CSV, haga clic en **Cargar Blob**y, a continuación, haga clic en **archivo desde el disco local**.  

## <a name="add-the-sentiment-analytics-model-from-the-cortana-intelligence-gallery"></a>Agregar el modelo de análisis de opinión de la Galería de inteligencia de Cortana

1.  Descargar el [modelo de análisis de opinión predictiva](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) de la Galería de inteligencia de Cortana.  
2.  Studio de aprendizaje del equipo, haga clic en **Abrir en Studio**.  

    ![Transmitir el análisis de aprendizaje, abra Studio de aprendizaje de equipo](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  

3.  Inicie sesión en el área de trabajo. Seleccione la ubicación que mejor se adapte a su propia ubicación.
4.  Haga clic en **Ejecutar** en la parte inferior de la página.  
5.  Después de que el proceso se ejecuta correctamente, haga clic en **Servicio Web de implementar**.
6.  El modelo de análisis de opinión está listo para usar. Para validar, haga clic en el botón de **prueba** y proporcionar la entrada de texto, como por ejemplo, "Quiero Microsoft". La prueba debe devolver un resultado similar al siguiente:

`'Predictive Mini Twitter sentiment analysis Experiment' test returned ["4","0.715057671070099"]...`  

![Aprendizaje de máquina secuencia análisis, datos de análisis](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-analysis-data.png)  

En la columna de **aplicaciones** , haga clic en el vínculo para **Excel 2010 o libro anterior** obtener la clave de API y la dirección URL que necesitará más adelante para configurar el trabajo de análisis de la secuencia. (Este paso es obligatorio solo a usar un modelo de aprendizaje del equipo desde el área de trabajo de otra cuenta de Azure. En este artículo se supone que este es el caso, para resolver ese escenario.)  

Tenga en cuenta la dirección URL y el acceso a la clave del servicio de web desde el archivo descargado de Excel, como se muestra a continuación:  

![Aprendizaje de equipo de análisis de secuencia, vistazo rápido](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  

## <a name="create-a-stream-analytics-job-that-uses-the-machine-learning-model"></a>Crear una tarea de análisis de secuencia que utiliza el modelo de aprendizaje de equipo

1.  Vaya al [portal de Azure](https://manage.windowsazure.com).  
2.  Haga clic en **nuevo** > **Data Services** > **análisis de secuencia** > **crear rápido**. Escriba un nombre para el trabajo en **Nombre del trabajo**, escriba la región apropiada para el trabajo en la **región**y, a continuación, seleccione la cuenta en la **Cuenta de almacenamiento de supervisión Regional**.    
3.  Una vez creado el trabajo, en la pestaña **entradas** , haga clic en **Agregar una entrada**.  

    ![Aprendizaje del equipo de análisis de secuencia, agregar entrada de aprendizaje de equipo](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-input-screen.png)  

4.  En la primera página del Asistente para **Agregar la entrada** , haga clic en el **flujo de datos**y, a continuación, haga clic en **siguiente**. En la siguiente página, haga clic en **Almacenamiento de blobs** como la entrada y, a continuación, haga clic en **siguiente**.  
5.  En la página de **Configuración de almacenamiento de blobs** del asistente, proporcione el nombre de contenedor de almacenamiento cuenta blob que haya definido anteriormente al cargar los datos. Haga clic en **siguiente**. **Formato de serialización del evento**, haga clic en **CSV**. Acepte los valores predeterminados para el resto de la página de **configuración de serialización** . Haga clic en **Aceptar**.  
6.  En la ficha **resultados** , haga clic en **Agregar un resultado**.  

    ![Aprendizaje del equipo de análisis de flujo, Agregar resultado](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-output-screen.png)  

7.  Haga clic en **Almacenamiento de blobs**y, a continuación, escriba los mismos parámetros, excepto el contenedor. El valor de **entrada** se configuró para leer el contenedor denominado "test" donde se cargó el archivo **CSV** . Para la **salida**, escriba "testoutput". Nombres de contenedor deben ser diferentes. Compruebe que exista este contenedor.     
8.  Haga clic en **siguiente** para configurar **configuración de serialización de la salida**. Como ocurre con la **entrada**, haga clic en **CSV**y, a continuación, haga clic en el botón **Aceptar** .
9.  En la pestaña de **funciones** , haga clic en **Agregar una función de aprendizaje del equipo**.  

    ![Aprendizaje del equipo de análisis de secuencia, agregar función de aprendizaje de equipo](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-ml-function.png)  

10. En la página **Configuración del servicio de Web de aprendizaje de equipo** , busque el área de trabajo de aprendizaje, servicio web y extremo predeterminado. En este artículo, aplicar la configuración manual para familiarizarse con la configuración de un servicio web para cualquier área de trabajo, siempre y cuando conoce la dirección URL y tiene la clave API. Escriba la **dirección URL** del extremo y la **clave API**. Haga clic en **Aceptar**.    

    ![Aprendizaje de máquina de análisis de secuencia, el aprendizaje de servicio web](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-ml-web-service.png)    

11. En la pestaña **consulta** , modifique la consulta como sigue:    

 ```
    WITH subquery AS (  
        SELECT text, sentiment(text) as result from input  
    )  
 
    Select text, result.[Scored Labels]  
    Into output  
    From subquery  
 ```    
12. Haga clic en **Guardar** para guardar la consulta.

## <a name="start-the-stream-analytics-job-and-observe-the-output"></a>Iniciar el trabajo de análisis de secuencia y observe el resultado

1.  Haga clic en **Iniciar** en la parte inferior de la página de trabajo.
2.  En el **Cuadro de diálogo de consulta de inicio**, haga clic en **Hora personalizado**y, a continuación, seleccione una hora antes al cargar el CSV al almacenamiento de blobs. Haga clic en **Aceptar**.  

    ![Aprendizaje de máquina de análisis de secuencia, tiempo personalizado](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-custom-time.png)  

3.  Vaya al almacenamiento de blobs de Windows mediante la herramienta que usaste para cargar el archivo CSV, por ejemplo, Visual Studio.
4.  Unos minutos después de inicia el trabajo, se crea el contenedor de salida y se carga un archivo CSV a él.  
5.  Abra el archivo en el editor de CSV predeterminado. Debe mostrarse algo similar al siguiente:  

    ![Ver el análisis de secuencia aprendizaje, CSV](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  

## <a name="conclusion"></a>Conclusión

En este artículo muestra cómo crear un trabajo de análisis de secuencia que lee la transmisión de datos de texto y se aplica el análisis de opinión a los datos en tiempo real. Puede realizar todo esto sin tener que preocuparse de los pormenores de la creación de un modelo de análisis de opinión. Esta es una de las ventajas de la serie de inteligencia Cortana.

También puede ver métricas relacionadas con la función de aprendizaje del equipo de Azure. Para ello, haga clic en la pestaña **Monitor** . Se muestran tres métricas relacionadas con la función.  

- **Las solicitudes de la función** indica el número de solicitudes enviadas a un servicio web de aprendizaje del equipo.  
- **Eventos de la función** indica el número de eventos de la solicitud. De forma predeterminada, cada solicitud a un servicio web de aprendizaje de equipo contiene hasta 1.000 eventos.  
- **Las solicitudes de función de error** indica el número de solicitudes de errores para el servicio web de aprendizaje del equipo.  

    ![Aprendizaje de máquina de análisis de secuencia, el aprendizaje monitor vista](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-ml-monitor-view.png)  
