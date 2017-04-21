<properties     
    pageTitle="Datos de Azure Factory - ejemplos" 
    description="Proporciona detalles acerca de los ejemplos que se incluyen con el servicio de generador de datos de Azure." 
    services="data-factory" 
    documentationCenter="" 
    authors="sharonlo101" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/18/2016" 
    ms.author="shlo"/>

# <a name="azure-data-factory---samples"></a>Datos de Azure Factory - ejemplos

## <a name="samples-on-github"></a>Ejemplos en GitHub
El [repositorio de Azure GitHub-DataFactory](https://github.com/azure/azure-datafactory) contiene varios ejemplos que le ayudarán a rápidamente impulso con servicio de generador de datos de Azure (o) modificar las secuencias de comandos y usarla en la aplicación propia. La carpeta Samples\JSON contiene fragmentos de JSON para escenarios comunes.

| Ejemplo | Descripción |
| :----- | :---------- | 
| [Tutorial ADF](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ADFWalkthrough) | Este ejemplo proporciona un tutorial to-end para el procesamiento de archivos de registro con el generador de datos de Azure cada datos de archivos de registro de información. <br/><br/>En este tutorial, la canalización de fábrica de datos recopila registros de ejemplo, procesos y enriquece los datos de los registros con datos de referencia y transforma los datos para evaluar la eficacia de una campaña de marketing que se ha lanzado recientemente. |
| [Ejemplos JSON](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON) | En este ejemplo se proporciona ejemplos JSON para escenarios comunes. | 
| [Ejemplo de aplicación de descarga de datos HTTP](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HttpDataDownloaderSample) | Este ejemplo proporcionan descargar datos de un extremo HTTP a almacenamiento de blobs de Azure con actividad .NET personalizada. |
| [Ejemplo de actividad de red punto del dominio de aplicación combinación cruzada](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/CrossAppDomainDotNetActivitySample) | En este ejemplo le permite crear una actividad de .NET personalizada que no se limita a las versiones de ensamblado utilizadas por el selector ADF (por ejemplo, WindowsAzure.Storage v4.3.0, Newtonsoft.Json v6.0.x, etcetera). |
| [Ejecutar secuencia de comandos de R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample) |  Este ejemplo incluye la actividad personalizada del generador de datos que se pueden usar para invocar RScript.exe. En este ejemplo sólo funciona con su propio clúster de HDInsight (no a petición) que ya tiene instalado R en él. |
| [Invocar trabajos de motor de clúster HDInsight Hadoop](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/Spark) | Este ejemplo muestra cómo usar MapReduce actividad invocar un programa de motor. Motor simplemente copia los datos de un contenedor de blobs de Windows Azure a otro. |
| [Análisis de Twitter con Azure máquina aprendizaje lote puntuación actividad](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TwitterAnalysisSample-AzureMLBatchScoringActivity) | Este ejemplo muestra cómo usar AzureMLBatchScoringActivity para invocar un modelo de aprendizaje del equipo de Azure que lleva a cabo análisis de opinión de twitter, puntuación, predicción etcetera. |
| [Análisis de Twitter con actividad personalizada](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TwitterAnalysisSample-CustomC%23Activity) |  Este ejemplo muestra cómo usar una actividad de .NET personalizada para invocar un modelo de aprendizaje del equipo de Azure que lleva a cabo análisis de opinión de twitter, puntuación, predicción etcetera. |
| [Canalizaciones con parámetros para el aprendizaje Azure](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ParameterizedPipelinesForAzureML/) | El ejemplo proporciona un código C#-to-end para implementar canalizaciones N de puntuación y formación cada una con un parámetro de región diferente en la lista de regiones procedente de un archivo parameters.txt, que se incluye con este ejemplo. | 
| [Actualización de datos de referencia para las tareas de análisis de secuencia de Azure](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ReferenceDataRefreshForASAJobs) |  En este ejemplo se muestra cómo usar el generador de datos de Azure y análisis de secuencia de Azure juntos para ejecutar las consultas con datos de referencia y configurar la actualización de datos de referencia en una programación. |
| [Canalización híbrido con local Hortonworks Hadoop](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HybridPipelineWithOnPremisesHortonworksHadoop) | El ejemplo utiliza un clúster de Hadoop local como un destino de cálculo para ejecutar trabajos en el generador de datos al igual que puede agregar otros destinos de cálculo como un HDInsight según clúster de Hadoop en la nube. |
| [Herramienta de conversión de JSON](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSONConversionTool) | Esta herramienta permite convertir JSONs de la versión anterior de 2015-07-01-obtener una vista previa más reciente o vista previa de 2015-07-01 (predeterminado). |  
| [Archivo de entrada de muestra de SQL U](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/U-SQL%20Sample%20Input%20File) |  Este archivo es un archivo de ejemplo que se utiliza una actividad U SQL. | 

## <a name="azure-resource-manager-templates"></a>Plantillas de administrador de recursos de Azure
Puede encontrar las siguientes plantillas de administrador de recursos de Azure para fábrica de datos en Github. 

| Plantilla | Descripción |
| -------- | ----------- | 
| [Copiar desde el almacenamiento de blobs de Windows Azure en la base de datos SQL Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy) | Implementar esta plantilla crea un generador de datos de Azure con una canalización que copia datos desde el almacenamiento de blobs de Windows Azure especificado en la base de datos de SQL Azure |    
| [Copie de Salesforce al almacenamiento de blobs de Windows Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy) | Implementar esta plantilla crea un generador de datos de Azure con una canalización que copie datos de la cuenta de Salesforce especificada para el almacenamiento de blobs de Windows Azure. |    
| [Transformar datos ejecutando el script de sección en un clúster de HDInsight de Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation) | Implementar esta plantilla crea un generador de datos de Azure con la canalización de transformación de datos ejecutando el script de la sección de ejemplo en un clúster de Azure HDInsight Hadoop. | 


## <a name="samples-in-azure-portal"></a>Ejemplos de portal de Azure
Puede usar el mosaico de **canalizaciones de ejemplo** en la página de inicio de la fábrica de datos para implementar canalizaciones de ejemplo y sus entidades asociadas (conjuntos de datos y servicios vinculados) en el generador de datos. 

1. Crear un generador de datos o abrir un generador de datos existente. Vea [Introducción al generador de datos de Azure](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md#CreateDataFactory) para conocer los pasos para crear un generador de datos.
2. En el módulo de **Generador de datos** para el generador de datos, haga clic en el mosaico de **canalizaciones de ejemplo** .

    ![Mosaico de canalizaciones de ejemplo](./media/data-factory-samples/SamplePipelinesTile.png)

2. En el módulo de **canalizaciones de muestra** , haga clic en el **ejemplo** que se va a implementar. 
    
    ![Módulo de ejemplo de canalizaciones](./media/data-factory-samples/SampleTile.png)

3. Especificar opciones de configuración de la muestra. Por ejemplo, la clave de nombre y la cuenta de la cuenta de almacenamiento de Azure, el nombre del servidor de SQL Azure, base de datos, identificador de usuario y contraseña, etcetera. 

    ![Módulo de ejemplo](./media/data-factory-samples/SampleBlade.png)

4. Cuando haya terminado con especificando las opciones de configuración, haga clic en **crear** para crear o implementar el ejemplo canalizaciones y usadas por la canalizaciones servicios o tablas vinculadas.
5. Ver el estado de implementación en el mosaico de ejemplo hizo clic en una versión anterior, en el módulo de **canalizaciones de ejemplo** .

    ![Estado de implementación](./media/data-factory-samples/DeploymentStatus.png)

6. Cuando ve el mensaje **implementación correcta** en el mosaico de la muestra, cierre el módulo de **canalizaciones de ejemplo** .  
5. En el módulo de **Generador de datos** , verá que se agregan los servicios vinculados, conjuntos de datos y canalizaciones a su generador de datos.  

    ![Módulo de generador de datos](./media/data-factory-samples/DataFactoryBladeAfter.png)
   
## <a name="samples-in-visual-studio"></a>Ejemplos de Visual Studio

### <a name="prerequisites"></a>Requisitos previos

Debe tener instalado en su equipo: 

- 2013 de Visual Studio o Visual Studio de 2015
- Descargar Azure SDK para 2013 de Visual Studio o Visual Studio de 2015. Vaya a la [Página de descarga de Azure](https://azure.microsoft.com/downloads/) y haga clic en **VS 2013** o **VS 2015** en la sección **.NET** .
- Descargar el complemento del generador de datos de Azure más reciente para Visual Studio: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) o [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Si está utilizando 2013 de Visual Studio, también puede actualizar el complemento siguiendo estos pasos: en el menú, haga clic en **Herramientas** -> **extensiones y actualizaciones** -> **Online** -> **Galería de Visual Studio** -> **Generador de datos de Microsoft Azure Tools para Visual Studio** -> **Actualizar**.

### <a name="use-data-factory-templates"></a>Usar plantillas de fábrica de datos

1. Haga clic en **archivo** , en el menú, seleccione **nuevo**y haga clic en **proyecto**. 
2. En el cuadro de diálogo **Nuevo proyecto** , siga los pasos siguientes: 
    1. Seleccione **DataFactory** en **plantillas**. 
    2. En el panel derecho, seleccione **Plantillas del generador de datos** . 
    3. Escriba un **nombre** para el proyecto. 
    4. Seleccione una **ubicación** para el proyecto. 
    5. Haga clic en **Aceptar**. 

    ![Cuadro de diálogo nuevo proyecto](./media/data-factory-samples/vs-new-project-adf-templates.png)
6. En el cuadro de diálogo **Plantillas de fábrica de datos** , seleccione la plantilla de ejemplo en la sección **Plantillas de caso de uso** y haga clic en **siguiente**. Los siguientes pasos mostrarle con la plantilla de **Generación de perfiles de cliente** . Pasos son similares para los demás ejemplos. 

    ![Cuadro de diálogo de plantillas de fábrica de datos](./media/data-factory-samples/vs-data-factory-templates-dialog.png) 
7. En el cuadro de diálogo **Configuración de fábrica de datos** , haga clic en **siguiente** en la página de **Conceptos básicos del generador de datos** .
8. En la página **Configurar generador de datos** , siga los pasos siguientes: 
    1. Seleccione **Crear nuevo generador de datos**. También puede seleccionar el **Generador de datos de uso existente**.
    2. Escriba un **nombre** para el generador de datos.
    3. Seleccione la **suscripción de Azure** en que desea que el generador de datos que se cree. 
    4. Seleccione el **grupo de recursos** para el generador de datos.
    5. Seleccione el **Oeste de Estados Unidos**, **Oriental de Estados Unidos**o **Europa del Norte** de la **región**.
    6. Haga clic en **siguiente**. 
9. En la página **almacena datos configurar** , especifique un existente **base de datos de SQL Azure** y **cuenta de Azure almacenamiento** (o) crear base de datos y almacenamiento de información y haga clic en siguiente. 
10. En la página **Configurar calcular** , seleccione valores predeterminados y haga clic en **siguiente**. 
11. En la página **Resumen** , revise toda la configuración y haga clic en **siguiente**. 
12. En la página de **Estado de la implementación** , espere a que termine la implementación y haga clic en **Finalizar**.
13. Haga clic en proyecto en el Explorador de soluciones y haga clic en **Publicar**. 
19. Si ve el cuadro de diálogo **iniciar sesión en su cuenta de Microsoft** , escriba sus credenciales para la cuenta que tiene la suscripción de Azure y haga clic en **iniciar sesión**.
20. Verá el cuadro de diálogo siguiente:

    ![Cuadro de diálogo Publicar](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)

21. En la página **Configurar generador de datos** , siga los pasos siguientes: 
    1. Confirmar esta opción **Generador de datos de uso existente** .
    2. Seleccione el **Generador de datos** que había seleccionar al utilizar la plantilla. 
    6. Haga clic en **siguiente** para pasar a la página **Publicar elementos** . (Presione **PESTAÑA** para sacar el campo nombre a si el botón **siguiente** está deshabilitado). 
23. En la página **Publicar elementos** , asegúrese de que las fábricas datos entidades están seleccionadas y haga clic en **siguiente** para pasar a la página de **Resumen** .     
24. Revise el resumen y haga clic en **siguiente** para iniciar el proceso de implementación y ver el **Estado de implementación**.
25. En la página de **Estado de la implementación** , debe ver el estado del proceso de implementación. Cuando haya terminado la implementación, haga clic en Finalizar. 

Para obtener más información sobre cómo usar Visual Studio para crear entidades de datos fábrica y publicarlos en Azure, vea [crear su primer generador de datos (Visual Studio)](data-factory-build-your-first-pipeline-using-vs.md) .          