<properties
    pageTitle="Guía técnica de la plantilla de solución de inteligencia de Cortana para mantenimiento predictivo en aeroespaciales y de otras empresas | Microsoft Azure"
    description="Una guía técnica para la plantilla de solución con Microsoft Cortana inteligencia predictiva mantenimiento de aeroespacial, utilidades y transporte."
    services="cortana-analytics"
    documentationCenter=""
    authors="fboylu"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="cortana-analytics"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="fboylu" />

# <a name="technical-guide-to-the-cortana-intelligence-solution-template-for-predictive-maintenance-in-aerospace-and-other-businesses"></a>Guía técnica de la plantilla de solución de inteligencia de Cortana para mantenimiento predictivo en aeroespaciales y de otras empresas

## <a name="acknowledgements"></a>**Confirmaciones**
En este artículo se editada por científicos datos Yan Zhang, Gauher Shaheen, Fidan Boylu Uz e ingeniería de software Dan Grecoe en Microsoft.

## <a name="overview"></a>**Información general**

Plantillas de solución están diseñadas para acelerar el proceso de creación de una demostración E2E sobre el conjunto de aplicaciones de inteligencia de Cortana. Una plantilla de implementada se aprovisionar la suscripción con los componentes de inteligencia Cortana necesarios y crear las relaciones entre ellas. También se inicia la canalización de datos con datos de ejemplo generados a partir de una aplicación de generador de datos que se descarga e instala en el equipo local después de implementar la plantilla de solución. Los datos generados por el generador de se hacer uso de la canalización de datos y empezar a generar predicciones de aprendizaje del equipo que pueden visualizarse en el panel de Power BI. El proceso de implementación le guiará por los pasos necesarios para configurar las credenciales de la solución. Asegúrese de que estas credenciales como nombre de la solución, nombre de usuario y contraseña que desea proporcionar durante la implementación de registro.  

El objetivo de este documento es explicar la arquitectura de referencia y los distintos componentes que se aprovisione en su suscripción como parte de esta plantilla de solución. El documento también trata sobre cómo reemplazar los datos de ejemplo con datos reales de su propio podrán ver perspectivas y predicciones de sus propios datos. Además, el documento describe las partes de la plantilla de solución que tendría que modificarse si desea personalizar la solución con sus propios datos. Se proporcionan instrucciones sobre cómo crear paneles de Power BI para esta plantilla de solución al final.

>[AZURE.TIP] Puede descargar e imprimir una [versión PDF de este documento](http://download.microsoft.com/download/F/4/D/F4D7D208-D080-42ED-8813-6030D23329E9/cortana-analytics-technical-guide-predictive-maintenance.pdf).

## <a name="big-picture"></a>**Panorama general**

![](media/cortana-analytics-technical-guide-predictive-maintenance\predictive-maintenance-architecture.png)

Cuando se implementa la solución, se activan los diversos servicios de Azure dentro del conjunto de aplicaciones de análisis de Cortana (*es decir,* concentrador de evento, análisis de secuencia, HDInsight, el generador de datos, el aprendizaje, *etc.*). Arquitectura muestra el diagrama anterior, en un nivel alto, cómo se construye el mantenimiento predictiva para aeroespacial plantilla de solución de llevar a cabo. Podrá investigar estos servicios en el portal de azure haciendo clic en ellos en el diagrama de la plantilla de solución creado con la implementación de la solución con la excepción de HDInsight como este servicio se aprovisiona a petición cuando las actividades relacionadas canalización son necesarias para ejecutar y eliminado después.
Puede descargar una [versión de tamaño completo del diagrama](http://download.microsoft.com/download/1/9/B/19B815F0-D1B0-4F67-AED3-A40544225FD1/ca-topologies-maintenance-prediction.png).

Las secciones siguientes describen cada parte.

## <a name="data-source-and-ingestion"></a>**Recopilación y origen de datos**

### <a name="synthetic-data-source"></a>Origen de datos síntesis

Esta plantilla utiliza el origen de datos se genera desde una aplicación de escritorio que va a descargar y ejecutar localmente después de implementación correcta. Encontrará las instrucciones para descargar e instalar esta aplicación en la barra de propiedades cuando se selecciona el primer nodo denominado predictiva generador de datos de mantenimiento en el diagrama de la plantilla de solución. El servicio de [Azure evento concentrador](#azure-event-hub) con puntos de datos o eventos, que se utilizarán en el resto del flujo de solución de las fuentes de esta aplicación. Este origen de datos es consta de o derivado de datos esté disponibles públicamente del [repositorio de datos NASA](http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/) mediante el [conjunto de datos de simulación de Turbofan motor degradación](http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/#turbofan).

La aplicación de generación de evento rellenará el concentrador de eventos de Azure sólo mientras se ejecuta en el equipo.

### <a name="azure-event-hub"></a>Hub de evento de Azure

El servicio de [Azure evento concentrador](https://azure.microsoft.com/services/event-hubs/) es el destinatario de la información proporcionada por el origen de datos síntesis descrito anteriormente.

## <a name="data-preparation-and-analysis"></a>**Análisis y preparación de datos**


### <a name="azure-stream-analytics"></a>Análisis de secuencia de Azure

El servicio de [Análisis de secuencia de Azure](https://azure.microsoft.com/services/stream-analytics/) se usa para proporcionar casi el análisis en tiempo real en la secuencia de entrada del servicio de [Concentrador de evento de Azure](#azure-event-hub) y publicar los resultados en un panel de [Power BI](https://powerbi.microsoft.com) como archivado todos los eventos entrantes sin formato para el servicio de [Almacenamiento de Azure](https://azure.microsoft.com/services/storage/) para el procesamiento de más adelante en el servicio de [Generador de datos de Azure](https://azure.microsoft.com/documentation/services/data-factory/) .

### <a name="hd-insights-custom-aggregation"></a>Agregación de información de alta definición personalizada

El servicio de Azure HD información se usa para ejecutar secuencias de comandos de [subárbol](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) (organizados por generador de datos de Azure) para proporcionar agregaciones en los eventos sin procesar que se almacenaron mediante el servicio de análisis de secuencia de Azure.

### <a name="azure-machine-learning"></a>Aprendizaje del equipo de Azure

Se usa el servicio de [Aprendizaje del equipo de Azure](https://azure.microsoft.com/services/machine-learning/) (organizado por generador de datos de Azure) para realizar predicciones en la vida útil restante (RUL) de un motor de avión particular dado las entradas que se ha recibido.

## <a name="data-publishing"></a>**Publicación de datos**


### <a name="azure-sql-database-service"></a>Servicio de base de datos SQL Azure

Se usa el servicio de [Base de datos de SQL Azure](https://azure.microsoft.com/services/sql-database/) para almacenar (administrados por el generador de datos de Azure) las predicciones recibidas por el servicio de Azure el aprendizaje que se utilizarán en el panel de [Power BI](https://powerbi.microsoft.com) .

## <a name="data-consumption"></a>**Consumo de datos**

### <a name="power-bi"></a>Power BI

El servicio [Power BI](https://powerbi.microsoft.com) se usa para mostrar un panel que contiene agregaciones y alertas proporcionadas por el servicio de [Análisis de secuencia de Azure](https://azure.microsoft.com/services/stream-analytics/) , así como predicciones RUL almacenados en la [Base de datos de SQL Azure](https://azure.microsoft.com/services/sql-database/) que se crearon usando el servicio de [Aprendizaje del equipo de Azure](https://azure.microsoft.com/services/machine-learning/) . Para obtener instrucciones sobre cómo crear paneles de Power BI para esta plantilla de solución, consulte la sección siguiente.

## <a name="how-to-bring-in-your-own-data"></a>**Cómo incorporar sus propios datos**

Esta sección describe cómo hacer que sus propios datos en Azure y qué áreas requeriría cambios para los datos que se lleve a esta arquitectura.

No es probable que cualquier conjunto de datos que debería coincidir con el conjunto de datos que se utiliza el [conjunto de datos de simulación de Turbofan motor degradación](http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/#turbofan) utilizado para esta plantilla de solución. Descripción de los datos y los requisitos será crucial en cómo modificar esta plantilla para trabajar con sus propios datos. Si esta es la primera exposición para el servicio de aprendizaje del equipo de Azure, puede obtener una introducción a ella usando el ejemplo de [cómo crear su primer ensayo](machine-learning-create-experiment.md).

Las siguientes secciones describen las secciones de la plantilla que requieren modificaciones cuando se introduce un nuevo conjunto de datos.

### <a name="azure-event-hub"></a>Hub de evento de Azure

El servicio de Azure evento concentrador es muy genérico, que se pueden registrar datos en el hub en formato CSV o JSON. Se produce ningún procesamiento especial en el Hub de evento de Azure, pero es importante que conozca los datos en la que están introduciendo.

Este documento no describe cómo recopilar los datos, pero puede enviar fácilmente eventos o los datos a un concentrador de evento de Azure con la API de concentrador de evento.

### <a name="azure-stream-analytics"></a>Análisis de secuencia de Azure

El servicio de análisis de secuencia de Azure se usa para proporcionar cerca de análisis en tiempo real mediante la lectura de secuencias de datos y salida de datos a cualquier número de orígenes.

Para el mantenimiento predictiva para aeroespacial plantilla de la solución, la consulta de análisis de secuencia de Azure consta de cuatro subconsultas, cada consumir eventos desde el servicio de concentrador de evento de Azure y tener resultados que cuatro ubicaciones distintas. Estas salidas constan de tres conjuntos de datos de Power BI y una ubicación de almacenamiento de Azure.

Puede encontrar la consulta de análisis de secuencia de Azure por:

-   Iniciar sesión en el portal de Azure

-   Localizar los trabajos de análisis de secuencia ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-stream-analytics.png) que se genera cuando se implementa la solución (*por ejemplo*, **maintenancesa02asapbi** y **maintenancesa02asablob** para la solución de mantenimiento predictiva)

-   Seleccionar

    -   ***Entradas*** para ver la entrada de la consulta

    -   ***Consulta*** para ver la consulta en Sí

    -   ***Resultados*** para ver los resultados diferentes

Encontrará información acerca de la construcción de consulta de análisis de secuencia de Azure en la [Referencia de consulta de análisis de secuencia](https://msdn.microsoft.com/library/azure/dn834998.aspx) en MSDN.

En esta solución, las consultas de habían salida tres conjuntos de datos con cerca de la información de análisis en tiempo real sobre la secuencia de datos entrantes a un panel de Power BI que forma parte de esta plantilla de solución. Como ya hay conocimiento implícito información sobre el formato de datos entrantes, estas consultas necesarias para modificarse según el formato de datos.

La consulta en la segunda de trabajo de análisis de secuencia **maintenancesa02asablob** simplemente envía todos los eventos de [Concentrador de evento](https://azure.microsoft.com/services/event-hubs/) para [El almacenamiento de Azure](https://azure.microsoft.com/services/storage/) y, por tanto, no requiere alteración independientemente del formato de los datos como la información del evento completa se transmite al almacenamiento.

### <a name="azure-data-factory"></a>Generador de datos de Azure

El servicio de [Generador de datos de Azure](https://azure.microsoft.com/documentation/services/data-factory/) organiza el movimiento y tratamiento de datos. En el mantenimiento predictiva para aeroespacial plantilla solución el generador de datos se compone de tres [canalizaciones](../data-factory/data-factory-create-pipelines.md) que se mueven y los datos usando diversas tecnologías de proceso.  Puede acceder a su generador de datos abriendo el el nodo del generador de datos en la parte inferior del diagrama de plantilla solución creada con la implementación de la solución. Esto le llevará a la fábrica de datos en el portal de Azure. Si ve errores en los conjuntos de datos, puede omitir aquellos que aparecen debido a generador de datos que se implementaron antes de que se inició el generador de datos. Estos errores impedir que el generador de datos funcione.

![](media/cortana-analytics-technical-guide-predictive-maintenance/data-factory-dataset-error.png)

Esta sección describen las [actividades](../data-factory/data-factory-create-pipelines.md) contenidas en el [Generador de datos de Azure](https://azure.microsoft.com/documentation/services/data-factory/)y es necesario [canalizaciones](../data-factory/data-factory-create-pipelines.md) . A continuación, es la vista de diagrama de la solución.

![](media/cortana-analytics-technical-guide-predictive-maintenance/azure-data-factory.png)

Dos de las canalizaciones de este generador contienen [subárbol](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) las secuencias de comandos que se usan para dividir y agregar los datos. Cuando se ha indicado, las secuencias de comandos se encuentran en la cuenta de [Almacenamiento de Azure](https://azure.microsoft.com/services/storage/) creada durante la instalación. Será su ubicación: maintenancesascript\\\\secuencia de comandos\\\\subárbol\\ \\ (o https://[Your solución name].blob.core.windows.net/maintenancesascript).

Similar a las consultas de [Análisis de secuencia de Azure](#azure-stream-analytics-1) , las secuencias de comandos [subárbol](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) tienen conocimiento implícito información sobre el formato de datos entrantes, estas consultas necesarias para modificarse en función de los requisitos de [ingeniería de la característica](machine-learning-feature-selection-and-engineering.md) y el formato de datos.

#### <a name="aggregateflightinfopipeline"></a>*AggregateFlightInfoPipeline*

Esta [canalización](../data-factory/data-factory-create-pipelines.md) contiene una sola actividad - una actividad de [HDInsightHive](../data-factory/data-factory-hive-activity.md) con [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) que se ejecuta una secuencia de comandos de [sección](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) para dividir los datos que se coloca en [El almacenamiento de Azure](https://azure.microsoft.com/services/storage/) durante el trabajo de [Análisis de secuencia de Azure](https://azure.microsoft.com/services/stream-analytics/) .

La secuencia de comandos de [subárbol](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) para esta tarea partición es ***AggregateFlightInfo.hql***

#### <a name="mlscoringpipeline"></a>*MLScoringPipeline*

Esta [canalización](../data-factory/data-factory-create-pipelines.md) contiene varias actividades y cuyo resultado final es que experimentar las predicciones puntuadas desde el [Aprendizaje del equipo de Azure](https://azure.microsoft.com/services/machine-learning/) asociados con esta plantilla de solución.

Las actividades contenidas en esta son:

-   Actividad de [HDInsightHive](../data-factory/data-factory-hive-activity.md) mediante un [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) que se ejecuta una secuencia de comandos de [subárbol](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) para realizar agregaciones e ingeniería característica necesaria para el ensayo de [Aprendizaje del equipo de Azure](https://azure.microsoft.com/services/machine-learning/) .
    La secuencia de comandos de [subárbol](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) para esta tarea partición es ***PrepareMLInput.hql***.

-   [Copiar](https://msdn.microsoft.com/library/azure/dn835035.aspx) la actividad que mueve los resultados de la actividad de [HDInsightHive](../data-factory/data-factory-hive-activity.md) a un único blobs de [Azure almacenamiento](https://azure.microsoft.com/services/storage/) pueden acceder la actividad [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) .

-   Actividad de [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) que llama el [Aprendizaje del equipo de Azure](https://azure.microsoft.com/services/machine-learning/) experimentar que da los resultados que se ponga en un único blob de [Almacenamiento de Azure](https://azure.microsoft.com/services/storage/) .

#### <a name="copyscoredresultpipeline"></a>*CopyScoredResultPipeline*

Esta [canalización](../data-factory/data-factory-create-pipelines.md) contiene una sola actividad - una actividad de [copia](https://msdn.microsoft.com/library/azure/dn835035.aspx) que mueve que los resultados de la [Aprendizaje del equipo de Azure](#azure-machine-learning) experimentar la ***MLScoringPipeline*** a la [Base de datos de SQL Azure](https://azure.microsoft.com/services/sql-database/) aprovisionada como parte de la instalación de la plantilla de solución.

### <a name="azure-machine-learning"></a>Aprendizaje del equipo de Azure

El ensayo de [Aprendizaje del equipo de Azure](https://azure.microsoft.com/services/machine-learning/) utilizado para esta plantilla de solución proporciona la vida útil restante (RUL) de un motor de avión. El ensayo es específica del conjunto de datos consumido y, por tanto, requieren modificación o sustitución específico a los datos que se ha insertado.

Para obtener información acerca de cómo se haya creado el ensayo de aprendizaje del equipo de Azure, consulte [Mantenimiento predictiva: paso 1 de 3, preparación de datos y de ingeniería de la característica](http://gallery.cortanaanalytics.com/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2).

## <a name="monitor-progress"></a>**Supervisar el progreso**
 Una vez que se inicia el generador de datos, la canalización comienza a obtener hidratado y empiece a los distintos componentes de la solución comienza en la siguiente acción los comandos emitidos por el generador de datos. Hay dos formas que puede supervisar la canalización.

1. Uno de la tarea de análisis de secuencia escribe los datos entrantes con el almacenamiento de blobs de Windows. Si hace clic en el componente de almacenamiento de blobs de su solución de la pantalla correctamente implementa la solución y, a continuación, haga clic en Abrir en el panel derecho, se tardará en el [portal de administración](https://portal.azure.com/). Una vez allí, haga clic en BLOB. En el siguiente panel, verá una lista de contenedores. Haga clic en **maintenancesadata**. En el siguiente panel, verá la carpeta **rawdata** . En la carpeta rawdata, verá carpetas con nombres como horas = 17, hora = 18 etcetera. Si ve estas carpetas, indica que los datos sin formato están correctamente que se genera en el equipo y almacenado en este servicio. Debería ver los archivos csv que deberían tener tamaños finitos en MB en dichas carpetas.

2. El último paso de la canalización es escribir datos (por ejemplo, predicciones de aprendizaje de equipo) a la base de datos de SQL. Es posible que tenga que esperar un máximo de tres horas para los datos que aparecen en la base de datos de SQL. Una forma para supervisar la cantidad de datos está disponible en la base de datos de SQL es a través del [portal de azure](https://manage.windowsazure.com/). En el panel izquierdo, busque las bases de datos de SQL ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-SQL-databases.png) y haga clic en él. A continuación, busque la base de datos **pmaintenancedb** y haga clic en ella. En la siguiente página en la parte inferior, haga clic en administrar

    ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-manage.png).

    Aquí, puede hacer clic en nueva consulta y consulta para el número de filas (por ejemplo, seleccione count(*) de PMResult). A medida que crece la base de datos, debe aumentar el número de filas de la tabla.


## <a name="power-bi-dashboard"></a>**Panel de Power BI**

### <a name="overview"></a>Información general

Esta sección describe cómo configurar el panel de Power BI para visualizar los datos en tiempo real desde el análisis de secuencia de Azure (ruta de acceso rápido), así como los resultados de la predicción de lote de Azure máquina (ruta fría) de aprendizaje.

### <a name="setup-cold-path-dashboard"></a>Panel de fría ruta de instalación

En la canalización de datos y de la ruta de acceso, el objetivo esencial es obtener la RUL predictiva (restante vida útil) de cada motor de avión una vez que termina un vuelo (ciclo). El resultado de predicción se actualiza cada 3 horas para predecir los motores de avión que han terminado de un vuelo durante las últimas tres horas.

Power BI se conecta a una base de datos de SQL Azure como origen de datos, donde se almacenan los resultados de predicción. Nota: 1) al implementar la solución, la predicción de real se muestra en la base de datos dentro de 3 horas.
El archivo pbix que venía con la descarga de generador contiene algunos datos de valor de inicialización para que se pueden crear paneles de Power BI de inmediato. (2) en este paso, el requisito previo es que descargar e instalar el software gratuito [Power BI desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/).

Los siguientes pasos le guiará en cómo conectar el archivo pbix a la base de datos de SQL que se ha terminado en el momento de la implementación de la solución que contiene datos (*por ejemplo*. resultados de predicción) para visualización.

1.  Obtener las credenciales de la base de datos.

    Tendrá **el nombre del servidor de base de datos, el nombre de la base de datos, el nombre de usuario y contraseña** antes de pasar a los pasos siguientes. Estos son los pasos que le guiarán cómo encontrarlos.

    -   Una vez **' base de datos de SQL Azure'** en el diagrama de la plantilla de solución vuelve de color verde, haga clic en él y, a continuación, haga clic en **"Abrir"**.

    -   Verá una nueva pestaña o ventana del explorador que muestra la página del portal de Azure. En el panel izquierdo, haga clic en **'Grupos de recursos'** .

    -   Seleccione la suscripción que se usa para implementar la solución y, a continuación, seleccione **' YourSolutionName\_ResourceGroup'**.

    -   En el nuevo elemento emergente panel, haga clic en el ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-sql.png) icono para acceder a la base de datos. El nombre de la base de datos está situada junto a este icono (*por ejemplo*, **'pmaintenancedb'**) y el **nombre del servidor de base de datos** aparece en la propiedad del nombre de servidor y debe tener un aspecto similar a **YourSoutionName.database.windows.net**.

    -   El **nombre de usuario** de base de datos y la **contraseña** son los mismos que el nombre de usuario y contraseña previamente grabadas durante la implementación de la solución.

2.  Actualizar el origen de datos del archivo de informe de ruta fría con Power BI Desktop.

    -   En la carpeta en el equipo donde ha descargado y descomprimir el archivo de generador, haga doble clic en el **PowerBI\\PredictiveMaintenanceAerospace.pbix** archivo. Si ve los mensajes de advertencia al abrir el archivo, omitirlos. En la parte superior del archivo, haga clic en **Consultas de editar**.

        ![](media\cortana-analytics-technical-guide-predictive-maintenance\edit-queries.png)

    -   Verá dos tablas, **RemainingUsefulLife** y **PMResult**. Seleccione la primera tabla y haga clic en ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-query-settings.png) junto a **'Origen'** en **' pasos aplicados '** en el panel derecho de **' configuración de consulta '** . Pasar por alto los mensajes de advertencia que aparecen.

    -   En el elemento de la ventana emergente, reemplazar **'Server'** y **'Database'** con sus propios nombres de base de datos y servidor y, a continuación, haga clic en **'Aceptar'**. Nombre del servidor, asegúrese de que especifique el puerto 1433 (**YourSoutionName.database.windows.net, 1433**). Deje el campo de la base de datos como **pmaintenancedb**. Pasar por alto los mensajes de advertencia que aparecen en la pantalla.

    -   En el siguiente elemento emergente ventana, verá dos opciones en el panel izquierdo (**Windows** y **base de datos**). Haga clic en **'Database'**, rellene **''** y **'Contraseña'** (que es el nombre de usuario y contraseña que ha escrito la primera vez que se implementa la solución y crear una base de datos de SQL Azure). En ***Seleccione qué nivel de aplicar esta configuración a***, active la opción de nivel de base de datos. A continuación, haga clic en **'Conectar'**.

    -   Haga clic en la segunda tabla **PMResult** ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-navigation.png) junto a **'Origen'** en **' pasos aplicados '** en el panel derecho de **' configuración de consulta '** y actualizar los nombres de servidor y base de datos como en los pasos anteriores y haga clic en Aceptar.

    -   Una vez que está guiado volver a la página anterior, cierre la ventana. Aparecerá un mensaje de salida, haga clic en **Aplicar**. Por último, haga clic en el botón **Guardar** para guardar los cambios. El archivo de Power BI ahora ha establecido conexión al servidor. Si su visualizaciones están vacías, asegúrese de que desactivar las selecciones en las visualizaciones para visualizar todos los datos haciendo clic en el icono de borrador en la esquina superior derecha de la leyenda. Utilice el botón Actualizar para reflejar los nuevos datos en las visualizaciones. Inicialmente, solo verá los datos de valor de inicialización en sus visualizaciones como el generador de datos esté programado para actualizar cada tres horas. Después de 3 horas, verá nuevas predicciones reflejados en sus visualizaciones al actualizar los datos.

3.  (Opcional) Publicar el panel de la ruta de acceso y [Power](http://www.powerbi.com/)BI en línea. Tenga en cuenta que este paso necesita una cuenta de Power BI (o la cuenta de Office 365).

    -   Haga clic en **'Publicar'** y algunos segundos más tarde una muestra "Publicación para Power BI éxito!" con una marca de verificación verde. Haga clic en el vínculo "Abrir PredictiveMaintenanceAerospace.pbix en Power BI". Para obtener instrucciones detalladas, consulte [publicar de Power BI Desktop](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop).

    -   Para crear un nuevo panel: haga clic en el **+** situado junto a la sección **paneles** en el panel izquierdo. Escriba el nombre "Demostración de mantenimiento predictiva" para este nuevo panel.

    -   Una vez que abra el informe, haga clic en ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-pin.png) para anclar todas las visualizaciones al escritorio. Para obtener instrucciones detalladas, consulte [Anclar un mosaico a un panel de Power BI de un informe](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report).
    Vaya a la página de panel y ajustar el tamaño y la ubicación de su visualizaciones y editar sus títulos. Para obtener instrucciones detalladas sobre cómo modificar los mosaicos, vea [Editar un mosaico: cambio de tamaño, mover, cambiar el nombre, pin, eliminar, Agregar hipervínculo](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename). Esto es un panel de ejemplo con algunas visualizaciones de ruta fría anclados a él.  ¿Cuánto tiempo se ejecuta el generador de datos, dependiendo de los números en las visualizaciones pueden ser diferentes.
    <br/>
    ![](media\cortana-analytics-technical-guide-predictive-maintenance\final-view.png)
<br/>
    -   Para programar la actualización de los datos, pasar el mouse sobre el conjunto de datos de **PredictiveMaintenanceAerospace** , haga clic en ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-elipsis.png) y, a continuación, elija **Actualizar la programación**.
<br/>
        **Nota:** Si ve un mensaje de advertencia, haga clic en **Editar credenciales** y asegúrese de que sus credenciales de la base de datos son los mismos que se describe en el paso 1.
<br/>
    ![](media\cortana-analytics-technical-guide-predictive-maintenance\schedule-refresh.png)
<br/>
    -   Expanda la sección **Programar actualización** . Activar "mantener actualizados los datos".
    <br/>
    -   Programar la actualización según sus necesidades. Para obtener más información, consulte [actualización los datos de Power BI](https://support.powerbi.com/knowledgebase/articles/474669-data-refresh-in-power-bi).

### <a name="setup-hot-path-dashboard"></a>Panel de acceso rápido de la ruta de acceso de instalación

Los siguientes pasos le guiará cómo quiere visualizar los resultados de datos en tiempo real de los trabajos de análisis de secuencia generados en el momento de la implementación de soluciones. Se requiere una cuenta de [Power BI en línea](http://www.powerbi.com/) para realizar los siguientes pasos. Si no tiene una cuenta, puede [crear uno](https://powerbi.microsoft.com/pricing).

1.  Agregar a resultado de Power BI en el análisis de secuencia de Azure (ASA).

    -  Deberá seguir las instrucciones de [análisis de secuencia de Azure y Power BI: un panel de análisis en tiempo real para la visibilidad en tiempo real de transmisión de datos](stream-analytics-power-bi-dashboard.md) para configurar la salida de la tarea de análisis de secuencia de Azure como el panel de Power BI.
    - La consulta ASA tiene tres salidas que son **aircraftmonitor**, **aircraftalert**y **flightsbyhour**. Puede ver la consulta haciendo clic en la ficha consulta. Correspondientes a cada una de estas tablas, deberá agregar un resultado ASA. Cuando agrega el primer resultado (*por ejemplo,* **aircraftmonitor**) asegúrese de que el **Alias de salida**, el **Nombre de conjunto de datos** y el **Nombre de la tabla** son el mismo (**aircraftmonitor**). Repita los pasos para agregar los resultados para **aircraftalert**y **flightsbyhour**. Una vez que haya agregado los tres tablas de salida e inició el trabajo ASA, recibirá un mensaje de confirmación (*por ejemplo*, "Iniciar secuencia análisis trabajo maintenancesa02asapbi que se realizó correctamente").

2. Inicie sesión en [Power BI en línea](http://www.powerbi.com)

    -   En el panel izquierdo sección de conjuntos de datos de Mi área de trabajo, deberían aparecer el ***conjunto de datos*** de nombres **aircraftmonitor** **aircraftalert**y **flightsbyhour** . Se trata de los datos de secuencias que inserta de análisis de secuencia de Azure en el paso anterior. El conjunto de datos **flightsbyhour** posible que no aparezcan en el mismo momento que los otros dos conjuntos de datos debido a la naturaleza de la consulta SQL detrás. Sin embargo, deben mostrar después de una hora.
    -   Asegúrese de que el panel de ***visualizaciones*** está abierto y se muestra en el lado derecho de la pantalla.

3. Una vez que tenga los datos que fluye en Power BI, puede empezar a visualizar los datos de secuencias. A continuación es un panel de ejemplo con algunas visualizaciones de ruta de acceso activa anclado a él. Puede crear otros mosaicos de paneles basadas en conjuntos de datos correspondiente. ¿Cuánto tiempo se ejecuta el generador de datos, dependiendo de los números en las visualizaciones pueden ser diferentes.


    ![](media\cortana-analytics-technical-guide-predictive-maintenance\dashboard-view.png)

4. Estos son algunos pasos para crear uno de los mosaicos anteriores: mosaico de la "flota vista de Sensor 11 frente umbral 48,26":

    -   Haga clic en **aircraftmonitor** del conjunto de datos en el panel izquierdo de la sección de conjuntos de datos.

    -   Haga clic en el icono de **Gráfico de líneas** .

    -   Haga clic en **procesada** en el panel **campos** para que se muestre en "Ejes" en el panel de **visualizaciones** .

    -   Haga clic en "s11" y "s11\_alerta" para que ambos aparezcan en "Valores". Haga clic en la flecha pequeña situada junto a **s11** y **s11\_alerta**, cambiar "Suma" a "Promedio".

    -   Haga clic en **Guardar** en la parte superior y el nombre del informe "aircraftmonitor". El informe denominado "aircraftmonitor" se mostrará en la sección de **informes** en el panel de **navegación** a la izquierda.

    -   Haga clic en el icono de **Alfiler visuales** en la esquina superior derecha de este gráfico de líneas. Puede mostrarse una ventana de "Anclar a paneles" para que elegir un panel. Seleccione "Demostración de mantenimiento predictiva" y luego haga clic en "Anclar".

    -   Mantenga el mouse sobre este mosaico en el panel, haga clic en el icono de "Editar" en la esquina superior derecha para cambiar su título a "Flota vista de Sensor 11 frente umbral 48,26" y el subtítulo a "Promedio de flota a lo largo del tiempo".

## <a name="how-to-delete-your-solution"></a>**Cómo eliminar la solución**
Asegúrese de que se detenga el generador de datos cuando se usa no activa la solución mientras se ejecuta el generador de datos producirá costos más altos. Elimine la solución si no está utilizando. Si elimina la solución, se eliminarán todos los componentes que se aprovisione en la suscripción cuando se implementa la solución. Para eliminar la solución, haga clic en el nombre de la solución en el panel izquierdo de la plantilla de la solución y haga clic en eliminar.

## <a name="cost-estimation-tools"></a>**Herramientas de estimación de costos**

Las dos herramientas siguientes están disponibles para ayudarle a entender mejor los costos totales implicados en la ejecución del mantenimiento predictiva para aeroespacial plantilla solución en su suscripción:

-   [Herramienta de Estimador (en línea) de Microsoft Azure costo](https://azure.microsoft.com/pricing/calculator/)

-   [Herramienta de Estimador de costos de Microsoft Azure (escritorio)](http://www.microsoft.com/download/details.aspx?id=43376)
