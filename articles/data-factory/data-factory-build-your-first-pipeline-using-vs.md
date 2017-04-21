<properties
    pageTitle="Crear su primer generador de datos (Visual Studio) | Microsoft Azure"
    description="En este tutorial, creará una canalización de generador de datos de Azure de ejemplo con Visual Studio."
    services="data-factory"
    documentationCenter=""
    authors="spelluru"
    manager="jhubbard"
    editor="monicar"/>

<tags
    ms.service="data-factory"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article" 
    ms.date="10/17/2016"
    ms.author="spelluru"/>

# <a name="tutorial-build-your-azure-first-data-factory-using-microsoft-visual-studio"></a>Tutorial: Generador de datos crear su primer Azure con Microsoft Visual Studio
> [AZURE.SELECTOR]
- [Información general y los requisitos previos](data-factory-build-your-first-pipeline.md)
- [Portal de Azure](data-factory-build-your-first-pipeline-using-editor.md)
- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Plantilla de administrador de recursos](data-factory-build-your-first-pipeline-using-arm.md)
- [API DE REST](data-factory-build-your-first-pipeline-using-rest-api.md)

En este artículo, se utiliza Microsoft Visual Studio para crear su primer generador de datos de Azure.

## <a name="prerequisites"></a>Requisitos previos
1. Lea el artículo de [Visión general de Tutorial](data-factory-build-your-first-pipeline.md) y complete los pasos de **requisito previo** .
2. Debe ser un **Administrador de la suscripción de Azure** para poder publicar entidades de generador de datos de Visual Studio a generador de datos de Azure.
3. Debe tener instalado en su equipo: 
    - 2013 de Visual Studio o Visual Studio de 2015
    - Descargar Azure SDK para 2013 de Visual Studio o Visual Studio de 2015. Vaya a la [Página de descarga de Azure](https://azure.microsoft.com/downloads/) y haga clic en **VS 2013** o **VS 2015** en la sección **.NET** .
    - Descargar el complemento del generador de datos de Azure más reciente para Visual Studio: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) o [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). También puede actualizar el complemento haciendo lo siguiente: en el menú, haga clic en **Herramientas** -> **extensiones y actualizaciones** -> **Online** -> **Galería de Visual Studio** -> **Generador de datos de Microsoft Azure Tools para Visual Studio** -> **Actualizar**. 
 
Ahora, vamos a usar Visual Studio para crear un generador de datos de Azure. 


## <a name="create-visual-studio-project"></a>Crear el proyecto de Visual Studio 
1. Iniciar **2013 de Visual Studio** o **Visual Studio de 2015**. Haga clic en **archivo**, elija **nuevo**y haga clic en **proyecto**. Verá el cuadro de diálogo **Nuevo proyecto** .  
2. En el cuadro de diálogo **Nuevo proyecto** , seleccione la plantilla de **DataFactory** y haga clic en **Proyecto de fábrica de datos vacía**.   

    ![Cuadro de diálogo nuevo proyecto](./media/data-factory-build-your-first-pipeline-using-vs/new-project-dialog.png)

3. Escriba un **nombre** para el proyecto, **ubicación**y un nombre para la **solución**y haga clic en **Aceptar**.

    ![Explorador de soluciones](./media/data-factory-build-your-first-pipeline-using-vs/solution-explorer.png)

## <a name="create-linked-services"></a>Crear servicios vinculados
Un generador de datos puede tener una o varias tuberías. Una canalización puede tener una o varias actividades. Por ejemplo, una actividad de copiar para copiar los datos desde un origen en un almacén de datos de destino y una actividad de subárbol HDInsight para ejecutar la secuencia de comandos de sección para transformar datos de entrada. Consulte [almacena datos compatibles](data-factory-data-movement-activities.md##supported-data-stores-and-formats) para todos los orígenes y receptores compatibles con la actividad de copia. Vea [calcular servicios vinculados](data-factory-compute-linked-services.md) de la lista de servicios de cálculo compatibles con el generador de datos. 

En este paso, vincular su cuenta de almacenamiento de Azure y un clúster de Azure HDInsight a petición en el generador de datos. La cuenta de almacenamiento de Azure contiene los datos de entrada y salidos para la canalización de este ejemplo. El servicio HDInsight vinculado se usa para ejecutar la secuencia de comandos de sección especificada en la actividad de la canalización de este ejemplo. Identificar qué datos almacén/cálculo servicios se usan en su situación y vincular estos servicios el generador de datos mediante la creación de servicios vinculados.  

Especificar el nombre y la configuración de la fábrica de datos más adelante al publicar la solución del generador de datos.

#### <a name="create-azure-storage-linked-service"></a>Crear el servicio de almacenamiento de Azure vinculado
En este paso, puede vincular su cuenta de Azure almacenamiento a su generador de datos. Para este tutorial, utiliza la misma cuenta de almacenamiento de Azure para almacenar los datos de entrada y salida y el archivo de comandos HQL. 

4. Haga clic con el botón **Servicios vinculados** en el Explorador de soluciones, elija **Agregar**y haga clic en **Nuevo elemento**.      
5. En el cuadro de diálogo **Agregar nuevo elemento** , seleccione **Servicios vinculados de Azure almacenamiento** de la lista y haga clic en **Agregar**. 
3. Reemplazar el **nombre de la cuenta** y **accountkey** con el nombre de su cuenta de almacenamiento de Azure y su clave. Para obtener información sobre cómo obtener la clave de acceso de almacenamiento, consulte [Ver, copiar y las teclas de acceso de almacenamiento regenerar](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)

    ![Almacenamiento de Azure vinculado servicio](./media/data-factory-build-your-first-pipeline-using-vs/azure-storage-linked-service.png)

4. Guarde el archivo **AzureStorageLinkedService1.json** .

#### <a name="create-azure-hdinsight-linked-service"></a>Crear servicio HDInsight de Azure vinculado
En este paso, puede vincular un clúster de HDInsight a petición a su generador de datos. El clúster HDInsight es creado en tiempo de ejecución automáticamente y se eliminan después de terminar procesamiento e inactivo durante el período de tiempo especificado. Puede usar su propio clúster de HDInsight en lugar de usar un clúster de HDInsight a petición. Para obtener más información, vea [Calcular servicios vinculados](data-factory-compute-linked-services.md) . 

1. En el **Explorador de soluciones**, haga **Servicios vinculados**, elija **Agregar**y haga clic en **Nuevo elemento**.
2. Seleccione **HDInsight en petición servicio vinculada**y haga clic en **Agregar**. 
3. Reemplazar el **JSON** con lo siguiente:

        {
          "name": "HDInsightOnDemandLinkedService",
          "properties": {
            "type": "HDInsightOnDemand",
            "typeProperties": {
              "version": "3.2",
              "clusterSize": 1,
              "timeToLive": "00:30:00",
              "linkedServiceName": "AzureStorageLinkedService1"
            }
          }
        }
    
    La tabla siguiente proporciona descripciones de las propiedades JSON utilizadas en el fragmento de código:
    
    (Propiedad) | Descripción
    -------- | -----------
    Versión | Especifica que la versión de la HDInsight creado para que sea 3.2. 
    ClusterSize | Especifica el tamaño del clúster HDInsight. 
    Período de vida | Especifica el tiempo de inactividad para el clúster HDInsight, antes de que se elimine.
    linkedServiceName | Especifica la cuenta de almacenamiento que se utiliza para almacenar los registros generados por HDInsight

    Tenga en cuenta lo siguiente: 
    
    - El generador de datos crea un clúster de HDInsight **basado en Windows** con la anterior JSON. También puede hacer que cree un clúster de HDInsight **basado en Linux** . Para obtener más información, vea [Servicios de vinculados a petición HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) . 
    - Puede usar **su propio clúster de HDInsight** en lugar de usar un clúster de HDInsight a petición. Para obtener más información, vea [Servicios vinculados de HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) .
    - El clúster HDInsight crea un **contenedor predeterminado** en el almacenamiento de blobs especificada en el JSON (**linkedServiceName**). HDInsight no eliminar este contenedor cuando se elimina el clúster. Este comportamiento es por diseño. Con el servicio de HDInsight vinculado a petición, se crea un clúster de HDInsight cada vez que se procesa un sector a menos que haya un clúster directo existente (**período de Vida**). El clúster se elimina automáticamente cuando se realiza el procesamiento.
    
        Mientras se procesan más sectores, verá muchos contenedores en el almacenamiento de blobs de Windows Azure. Si no es necesario para solucionar problemas de los trabajos, desea eliminarlos para reducir el costo de almacenamiento. Los nombres de los contenedores siguen un patrón: "adf**yourdatafactoryname**-**linkedservicename**- datetimestamp". Use herramientas como el [Explorador de almacenamiento de Microsoft](http://storageexplorer.com/) para eliminar contenedores en el almacenamiento de blobs de Windows Azure.

    Para obtener más información, vea [Servicios de vinculados a petición HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) . 
4. Guarde el archivo **HDInsightOnDemandLinkedService1.json** .

## <a name="create-datasets"></a>Crear conjuntos de datos
En este paso, creará los conjuntos de datos para representar la entrada y salida de datos para el procesamiento de la sección. Estos conjuntos de datos, consulte la **AzureStorageLinkedService1** que ha creado anteriormente en este tutorial. Los puntos de servicio vinculado a una cuenta de almacenamiento de Azure y conjuntos de datos especifican contenedor, carpeta, nombre de archivo en el almacenamiento de información que contiene la entrada y salida de datos.   

#### <a name="create-input-dataset"></a>Crear conjunto de datos de entrada

1. En el **Explorador de soluciones**, haga clic en **tablas**, elija **Agregar**y haga clic en **Nuevo elemento**. 
2. Seleccione **Blobs de Windows Azure** en la lista, cambie el nombre del archivo a **InputDataSet.json**y haga clic en **Agregar**.
3. Reemplazar el **JSON** en el editor con lo siguiente: 

    En el fragmento JSON, se crea un conjunto de datos denominado **AzureBlobInput** que representa datos de una actividad de la canalización de entrada. Además, especificar de que los datos de entrada se encuentran en el contenedor de blob llamado **adfgetstarted** y la carpeta llamada **inputdata**
        
        {
            "name": "AzureBlobInput",
            "properties": {
                "type": "AzureBlob",
                "linkedServiceName": "AzureStorageLinkedService1",
                "typeProperties": {
                    "fileName": "input.log",
                    "folderPath": "adfgetstarted/inputdata",
                    "format": {
                        "type": "TextFormat",
                        "columnDelimiter": ","
                    }
                },
                "availability": {
                    "frequency": "Month",
                    "interval": 1
                },
                "external": true,
                "policy": {}
            }
        } 

    La tabla siguiente proporciona descripciones de las propiedades JSON utilizadas en el fragmento de código:

  	| (Propiedad) | Descripción |
  	| :------- | :---------- |
  	| tipo | La propiedad tipo se establece en AzureBlob porque los datos residen en el almacenamiento de blobs de Windows Azure. |  
  	| linkedServiceName | se refiere a la AzureStorageLinkedService1 que creó anteriormente. |
  	| nombre de archivo | Esta propiedad es opcional. Si se omite esta propiedad, se seleccionan todos los archivos desde el folderPath. En este caso, se procesa solo la input.log. |
  	| tipo | Los archivos de registro están en formato de texto, por lo que usamos TextFormat. | 
  	| columnDelimiter | columnas en los archivos de registro están delimitadas por el carácter de coma () |
  	| frecuencia/intervalo | frecuencia en mes y el intervalo es 1, lo que significa que están disponibles los sectores entrados mensualmente. | 
  	| externos | Esta propiedad se establece en true si el servicio de generador de datos no genera los datos de entrada. | 
      
    
3. Guarde el archivo **InputDataset.json** . 

 
#### <a name="create-output-dataset"></a>Crear conjunto de datos de salida
Ahora, cree el conjunto de datos de salida para representar los datos de salida que se almacenan en el almacenamiento de blobs de Windows Azure. 

1. En el **Explorador de soluciones**, haga clic en **tablas**, elija **Agregar**y haga clic en **Nuevo elemento**. 
2. Seleccione **Blobs de Windows Azure** en la lista, cambie el nombre del archivo a **OutputDataset.json**y haga clic en **Agregar**. 
3. Reemplazar el **JSON** en el editor con lo siguiente: 

    En el fragmento JSON, se crea un conjunto de datos denominado **AzureBlobOutput**y que especifica la estructura de los datos que se produce la secuencia de comandos de la sección. Además, especificar que los resultados se almacenan en el contenedor de blob llamado **adfgetstarted** y la carpeta denominada **partitioneddata**. La sección **disponibilidad** especifica que el conjunto de datos de salida se genera mensualmente.
    
        {
          "name": "AzureBlobOutput",
          "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService1",
            "typeProperties": {
              "folderPath": "adfgetstarted/partitioneddata",
              "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
              }
            },
            "availability": {
              "frequency": "Month",
              "interval": 1
            }
          }
        }

    Vea la sección **crear el conjunto de datos de entrada** para obtener una descripción de estas propiedades. No establezca la propiedad externa en un conjunto de datos de salida como el conjunto de datos generado por el servicio de generador de datos.

4. Guarde el archivo **OutputDataset.json** .


### <a name="create-pipeline"></a>Crear canalización
En este paso, creará el primer canalización con una actividad **HDInsightHive** . La segmentación de datos de entrada está disponible mensual (frecuencia: mes, intervalo: 1), sector de salida se produce mensual y también se establece la propiedad del programador para la actividad a mensual. Debe coincidir con la configuración para el conjunto de datos de salida y el programador de actividad. Actualmente, conjunto de datos de salida es qué unidades de la programación, así que debe crear un conjunto de datos de salida, incluso si la actividad produce ningún resultado. Si la actividad no realiza cualquier entrada, puede omitir la creación del conjunto de datos de entrada. Se explican las propiedades que se utilizan en el siguiente JSON al final de esta sección.

1. En el **Explorador de soluciones**, haga clic con el botón **canalizaciones**, elija **Agregar**y haga clic en **nuevo elemento.** 
2. Seleccione **Subárbol canalización de transformación** de la lista y haga clic en **Agregar**. 
3. Reemplazar el **JSON** con el fragmento de código siguiente.

    > [AZURE.IMPORTANT] Reemplace **storageaccountname** con el nombre de su cuenta de almacenamiento.

        {
            "name": "MyFirstPipeline",
            "properties": {
                "description": "My first Azure Data Factory pipeline",
                "activities": [
                    {
                        "type": "HDInsightHive",
                        "typeProperties": {
                            "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                            "scriptLinkedService": "AzureStorageLinkedService1",
                            "defines": {
                                "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                                "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
                            }
                        },
                        "inputs": [
                            {
                                "name": "AzureBlobInput"
                            }
                        ],
                        "outputs": [
                            {
                                "name": "AzureBlobOutput"
                            }
                        ],
                        "policy": {
                            "concurrency": 1,
                            "retry": 3
                        },
                        "scheduler": {
                            "frequency": "Month",
                            "interval": 1
                        },
                        "name": "RunSampleHiveActivity",
                        "linkedServiceName": "HDInsightOnDemandLinkedService"
                    }
                ],
                "start": "2016-04-01T00:00:00Z",
                "end": "2016-04-02T00:00:00Z",
                "isPaused": false
            }
        }

    En el fragmento JSON, se crea una canalización que consta de una sola actividad que usa subárbol para procesar los datos en un clúster de HDInsight.
    
    En el fragmento JSON, se crea una canalización que consta de una sola actividad que usa subárbol para procesar los datos en un clúster de HDInsight.
    
    El archivo de comandos de la sección, **partitionweblogs.hql**, se almacena en la cuenta de almacenamiento de Azure (especificado por el scriptLinkedService, denominado **AzureStorageLinkedService1**) y en la carpeta de **script** en el contenedor **adfgetstarted**.

    La sección **define** se utiliza para especificar la configuración de tiempo de ejecución que se pasa a la secuencia de comandos de la sección como valores de configuración de la sección (por ejemplo ${hiveconf: inputtable}, ${hiveconf:partitionedtable}).

    Las propiedades de **Inicio** y **final** de la canalización especifica el período activo de la canalización.

    En la actividad JSON, puede especificar que se ejecuta la secuencia de comandos de la sección en el cálculo especificado por el **linkedServiceName** : **HDInsightOnDemandLinkedService**.

    > [AZURE.NOTE] Para obtener más información sobre las propiedades JSON que se usan en el ejemplo, vea [Anatomía de una canalización](data-factory-create-pipelines.md#anatomy-of-a-pipeline) . 
3. Guarde el archivo **HiveActivity1.json** .

### <a name="add-partitionweblogshql-and-inputlog-as-a-dependency"></a>Agregar partitionweblogs.hql y input.log como una dependencia 

1. Haga clic con el botón **dependencias** en la ventana **Explorador de soluciones** , elija **Agregar**y haga clic en **Elemento existente**.  
2. Vaya a la **C:\ADFGettingStarted** y seleccione **partitionweblogs.hql**, **input.log** archivos, haga clic en **Agregar**. Estos dos archivos como parte de los requisitos previos que haya creado en el [Tutorial de información general](data-factory-build-your-first-pipeline.md).

Cuando se publica la solución en el siguiente paso, el archivo **partitionweblogs.hql** se cargará a la carpeta de secuencias de comandos en el contenedor de blob **adfgetstarted** .   

### <a name="publishdeploy-data-factory-entities"></a>Publicar o implementar entidades del generador de datos

18. Haga clic en proyecto en el Explorador de soluciones y haga clic en **Publicar**. 
19. Si ve el cuadro de diálogo **iniciar sesión en su cuenta de Microsoft** , escriba sus credenciales para la cuenta que tiene la suscripción de Azure y haga clic en **iniciar sesión**.
20. Verá el cuadro de diálogo siguiente:

    ![Cuadro de diálogo Publicar](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)

21. En la página de fábrica configurar datos, haga lo siguiente: 
    1. Seleccione la opción **Crear nuevo generador de datos** .
    2. Escriba un único **nombre** para el generador de datos. Por ejemplo: **FirstDataFactoryUsingVS09152016**. El nombre debe ser único global.  
    
    
        > [AZURE.IMPORTANT] Si recibe el error **nombre del generador de datos "FirstDataFactoryUsingVS" no está disponible** cuando se publica, cambie el nombre (por ejemplo, yournameFirstDataFactoryUsingVS). Vea el tema de la [Fábrica de datos - las reglas de nombres](data-factory-naming-rules.md) para las reglas de nomenclatura para artefactos del generador de datos.
3. Seleccione la suscripción para el campo de la **suscripción** .
     
     
        > [AZURE.IMPORTANT] Si no ve ninguna suscripción, asegúrese de que ha iniciado sesión con una cuenta que sea un administrador o co-administración de la suscripción.  
        
    4. Seleccione el **grupo de recursos** para el generador de datos que se cree. 
    5. Seleccione la **región** de la fábrica de datos. 
    6. Haga clic en **siguiente** para pasar a la página **Publicar elementos** . (Presione **PESTAÑA** para sacar el campo nombre a si el botón **siguiente** está deshabilitado). 
23. En la página **Publicar elementos** , asegúrese de que las fábricas datos entidades están seleccionadas y haga clic en **siguiente** para pasar a la página de **Resumen** .     
24. Revise el resumen y haga clic en **siguiente** para iniciar el proceso de implementación y ver el **Estado de implementación**.
25. En la página de **Estado de la implementación** , debe ver el estado del proceso de implementación. Cuando haya terminado la implementación, haga clic en Finalizar. 

 
Aspectos importantes que tenga en cuenta: 

- Si recibe el error: "**esta suscripción no está registrada para usar el espacio de nombres Microsoft.DataFactory**", siga uno de los siguientes e intente publicar de nuevo: 

    - En Azure PowerShell, ejecute el comando siguiente para registrar el proveedor de datos fábrica. 
        
            Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
    
        Puede ejecutar el comando siguiente para confirmar el generador de datos proveedor está registrado. 
    
            Get-AzureRmResourceProvider
    - Inicie sesión con la suscripción de Azure en el [portal de Azure](https://portal.azure.com) y navegue a un módulo de generador de datos (o) crear un generador de datos en el portal de Azure. Esta acción registra automáticamente el proveedor por usted.
-   El nombre de la fábrica de datos puede estar registrado como un nombre DNS en el futuro y por tanto se hacen visible públicamente.
-   Para crear instancias del generador de datos, debe ser un administrador o co-administración de la suscripción de Azure

 
## <a name="monitor-pipeline"></a>Canalización de Monitor

### <a name="monitor-pipeline-using-diagram-view"></a>Mediante la vista Diagrama de canalización de Monitor
6. Inicie sesión en el [portal de Azure](https://portal.azure.com/), haga lo siguiente:
    1. Haga clic en **más servicios** y haga clic en **generadores de datos**.
        ![Examinar generadores de datos](./media/data-factory-build-your-first-pipeline-using-vs/browse-datafactories.png) 
    2. Seleccione el nombre de su generador de datos (por ejemplo: **FirstDataFactoryUsingVS09152016**) en la lista de generadores de datos. 
        ![Seleccione el generador de datos](./media/data-factory-build-your-first-pipeline-using-vs/select-first-data-factory.png)
7. En la página principal para el generador de datos, haga clic en **diagrama**.
  
    ![Mosaico de diagrama](./media/data-factory-build-your-first-pipeline-using-vs/diagram-tile.png)
7. En la vista de diagrama, consulte información general sobre los conjuntos de datos usados en este tutorial y canalizaciones.
    
    ![Vista de diagrama](./media/data-factory-build-your-first-pipeline-using-vs/diagram-view-2.png) 
8. Para ver todas las actividades de la canalización, secundario canalización en el diagrama y haga clic en abrir canalización. 

    ![Menú abrir canalización](./media/data-factory-build-your-first-pipeline-using-vs/open-pipeline-menu.png)
9. Confirme que ve la actividad HDInsightHive en la canalización. 
  
    ![Vista de canalización abierto](./media/data-factory-build-your-first-pipeline-using-vs/open-pipeline-view.png)

    Para volver a la vista anterior, haga clic en **Generador de datos** en el menú de navegación en la parte superior. 
10. En la **Vista de diagrama**, haga doble clic en el conjunto de datos **AzureBlobInput**. Confirme que la segmentación de datos se encuentra en estado **Listo** . Puede tardar unos minutos para el sector en mostrarse en estado listo. Si no ocurre después de que espera en algún momento, compruebe si tiene el archivo de entrada (input.log) que se coloca en el contenedor de la derecha (adfgetstarted) y la carpeta (inputdata).

    ![Entrada de división en estado listo](./media/data-factory-build-your-first-pipeline-using-vs/input-slice-ready.png)
11. Haga clic en **X** para cerrar el módulo **AzureBlobInput** . 
12. En la **Vista de diagrama**, haga doble clic en el conjunto de datos **AzureBlobOutput**. Verá que la segmentación de datos que se está procesando.

    ![Conjunto de datos](./media/data-factory-build-your-first-pipeline-using-vs/dataset-blade.png)
9. Cuando haya finalizado el proceso, vea la segmentación de datos en estado **Listo** .

    > [AZURE.IMPORTANT] Creación de un clúster de HDInsight a petición suele algún tiempo (aproximadamente 20 minutos). Por lo tanto, espere la canalización de pasar **unos 30 minutos** para procesar la segmentación de datos.  

    ![Conjunto de datos](./media/data-factory-build-your-first-pipeline-using-vs/dataset-slice-ready.png) 
    
10. Cuando la segmentación de datos está **preparada** , compruebe la carpeta de **partitioneddata** en el contenedor de **adfgetstarted** en el almacenamiento de blobs para los datos de salida.  
 
    ![datos de salida](./media/data-factory-build-your-first-pipeline-using-vs/three-ouptut-files.png)
11. Haga clic en la segmentación de datos para ver los detalles acerca de él en un módulo de **sectores de datos** .

    ![Detalles del sector de datos](./media/data-factory-build-your-first-pipeline-using-vs/data-slice-details.png)  
12. Haga clic en una actividad de ejecutar en la **lista de ejecuciones de actividad** para ver los detalles sobre una actividad ejecutar (subárbol actividad en nuestra situación) en una ventana de la **actividad de los detalles de ejecución** .   
    ![Detalles de ejecución de actividad](./media/data-factory-build-your-first-pipeline-using-vs/activity-window-blade.png)  
    
    En los archivos de registro, puede ver la información de estado y consulta de sección que se ha ejecutado. Estos registros son útiles para solucionar problemas.  
 

Para obtener instrucciones sobre cómo usar el portal de Azure para supervisar la canalización y conjuntos de datos que haya creado en este tutorial, vea [canalización y conjuntos de datos de Monitor](data-factory-monitor-manage-pipelines.md) .

### <a name="monitor-pipeline-using-monitor--manage-app"></a>Supervisar la canalización utilizando Monitor y administrar aplicaciones
También puede utilizar el Monitor y administrar la aplicación para supervisar su canalizaciones. Para obtener información detallada sobre el uso de esta aplicación, vea [Monitor y administrar canalizaciones generador de datos de Azure con supervisión y aplicación de administración de](data-factory-monitor-manage-app.md).

1. Haga clic en el Monitor y administrar el mosaico.

    ![Supervisar y administrar mosaico](./media/data-factory-build-your-first-pipeline-using-vs/monitor-and-manage-tile.png) 
2. Debería ver a Monitor y administrar aplicaciones. Cambiar la **hora de inicio** y **hora de finalización para que coincidan con inicio** (04-01-2016 12:00 A.M.) y fin (04-02-2016 12:00 A.M.) de la canalización y haga clic en **Aplicar**.

    ![Supervisar y administrar la aplicación](./media/data-factory-build-your-first-pipeline-using-vs/monitor-and-manage-app.png) 
3. Seleccione una ventana de actividad en la lista de ventanas de actividad para ver los detalles acerca de él. 
    ![Detalles de la ventana de actividad](./media/data-factory-build-your-first-pipeline-using-vs/activity-window-details.png)


> [AZURE.IMPORTANT] Cuando la segmentación de datos se procesa correctamente, se elimina el archivo de entrada. Por lo tanto, si desea volver a ejecutar la segmentación de datos o realizar de nuevo el tutorial, cargue el archivo de entrada (input.log) a la carpeta inputdata del contenedor adfgetstarted.
 

## <a name="use-server-explorer-to-view-data-factories"></a>Usar el Explorador de servidores para ver generadores de datos

1. En **Visual Studio**, haga clic en **Ver** en el menú y haga clic en **Explorador de servidores**.
2. En la ventana Explorador de servidores, expanda **Azure** y **Generador de datos**. Si ve que **inicie sesión en Visual Studio**, escriba la **cuenta** asociada a su suscripción de Azure y haga clic en **continuar**. Escriba la **contraseña**y haga clic en **iniciar sesión**. Visual Studio intenta obtener información acerca de todas las fábricas de datos de Azure en la suscripción. Ver el estado de esta operación en la ventana de la **Lista de tareas del generador de datos** .

    ![Explorador de servidores](./media/data-factory-build-your-first-pipeline-using-vs/server-explorer.png)
3. Puede haga un generador de datos y seleccione **Exportar el generador de datos a un proyecto nuevo** para crear un proyecto de Visual Studio basado en un generador de datos existente.

    ![Exportar datos factory](./media/data-factory-build-your-first-pipeline-using-vs/export-data-factory-menu.png)

## <a name="update-data-factory-tools-for-visual-studio"></a>Actualizar herramientas de generador de datos para Visual Studio

Para actualizar las herramientas de generador de datos de Azure para Visual Studio, haga lo siguiente:

1. Haga clic en **Herramientas** en el menú y seleccione **extensiones y actualizaciones**.
2. Seleccione **las actualizaciones** en el panel izquierdo y, a continuación, seleccione la **Galería de Visual Studio**.
3. Seleccione el **Generador de datos de Azure tools para Visual Studio** y haga clic en **Actualizar**. Si no ve esta entrada, ya tiene la versión más reciente de las herramientas. 

## <a name="use-configuration-files"></a>Usar archivos de configuración
Puede usar archivos de configuración en Visual Studio para configurar las propiedades de los servicios o tablas o canalizaciones vinculadas diferente para cada entorno. 

Considere la siguiente definición de JSON para un servicio de almacenamiento de Azure vinculado. Para especificar la **cadena de conexión** con distintos valores para el nombre de la cuenta y accountkey basado en el entorno (desarrollo/prueba/producción) a la que va a implementar entidades del generador de datos. Este comportamiento se puede conseguir usando el archivo de configuración independiente para cada entorno. 

    {
        "name": "StorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "description": "",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        }
    } 

### <a name="add-a-configuration-file"></a>Agregar un archivo de configuración
Agregar un archivo de configuración para cada entorno realizando los siguientes pasos:   

1. Haga clic en el proyecto del generador de datos en su solución de Visual Studio, elija **Agregar**y haga clic en **nuevo elemento**.
2. Seleccione **configuración** de la lista de las plantillas instaladas en la parte izquierda, seleccione el **Archivo de configuración**, escriba un **nombre** para el archivo de configuración y haga clic en **Agregar**.

    ![Agregar archivo de configuración](./media/data-factory-build-your-first-pipeline-using-vs/add-config-file.png)
3. Agregar parámetros de configuración y sus valores en el siguiente formato.

        {
            "$schema": "http://datafactories.schema.management.azure.com/vsschemas/V1/Microsoft.DataFactory.Config.json",
            "AzureStorageLinkedService1": [
                {
                    "name": "$.properties.typeProperties.connectionString",
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
                }
            ],
            "AzureSqlLinkedService1": [
                {
                    "name": "$.properties.typeProperties.connectionString",
                    "value":  "Server=tcp:spsqlserver.database.windows.net,1433;Database=spsqldb;User ID=spelluru;Password=Sowmya123;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
                }
            ]
        }

    Este ejemplo configura la propiedad de cadena de conexión de un servicio de almacenamiento de Azure vinculado y un servicio de SQL Azure vinculado. Observe que la sintaxis para especificar el nombre es [JsonPath](http://goessner.net/articles/JsonPath/).   

    Si JSON tiene una propiedad que tiene una matriz de valores como se muestra en el siguiente código:  

        "structure": [
            {
                "name": "FirstName",
                "type": "String"
            },
            {
                "name": "LastName",
                "type": "String"
            }
        ],
    
    Configurar las propiedades tal como se muestra en el siguiente archivo de configuración (usar indización basada en cero): 
        
        {
            "name": "$.properties.structure[0].name",
            "value": "FirstName"
        }
        {
            "name": "$.properties.structure[0].type",
            "value": "String"
        }
        {
            "name": "$.properties.structure[1].name",
            "value": "LastName"
        }
        {
            "name": "$.properties.structure[1].type",
            "value": "String"
        }

### <a name="property-names-with-spaces"></a>Nombres de propiedad con espacios
Si un nombre de propiedad tiene espacios, utilice corchetes como se muestra en el ejemplo siguiente (nombre de servidor de base de datos): 

     {
         "name": "$.properties.activities[1].typeProperties.webServiceParameters.['Database server name']",
         "value": "MyAsqlServer.database.windows.net"
     }


### <a name="deploy-solution-using-a-configuration"></a>Implementar la solución utilizando una configuración
Cuando se publican entidades del generador de datos de Azure en VS, puede especificar la configuración que desea utilizar para esa operación de publicación. 

Para publicar entidades en un proyecto de generador de datos de Azure con el archivo de configuración:   

1. Haga clic en proyecto de fábrica de datos y haga clic en **Publicar** para ver el cuadro de diálogo **Publicar elementos** . 
2. Seleccione un generador de datos existente o especifique valores para crear un generador de datos en la página **Configurar generador de datos** y haga clic en **siguiente**.   
3. En la página **Publicar elementos** : ver una lista desplegable con las configuraciones disponibles para el campo **Seleccione configuración de implementación** .

    ![Seleccione el archivo de configuración](./media/data-factory-build-your-first-pipeline-using-vs/select-config-file.png)

4. Seleccione el **archivo de configuración** que desea usar y haga clic en **siguiente**. 
5. Confirme que ve el nombre del archivo JSON en la página de **Resumen** y haga clic en **siguiente**. 
6. Una vez finalizada la operación de implementación, haga clic en **Finalizar** . 

Cuando se implementa, los valores del archivo de configuración se utilizan para establecer los valores de propiedades en los archivos JSON para entidades de fábrica de datos antes de que las entidades se implementan en el servicio generador de datos de Azure.   

## <a name="summary"></a>Resumen 
En este tutorial, ha creado un generador de datos de Azure para procesar datos ejecutando el script de sección en un clúster de hadoop HDInsight. Utiliza el Editor del generador de datos en el portal de Azure para realizar los siguientes pasos:  

1.  Crea un **Generador de datos**de Azure.
2.  Crear dos **servicios vinculados**:
    1.  Servicio de **Almacenamiento de azure** vinculado al que vincular el almacenamiento de blobs de Windows Azure que contiene los archivos de entrada y salida para el generador de datos.
    2.  **HDInsight de Azure** a petición vinculado servicio vincular un clúster de HDInsight Hadoop a petición en el generador de datos. Generador de datos de Azure crea una HDInsight Hadoop clúster just-in-time para procesar datos de entrada y producir datos de salida. 
3.  Crea dos **conjuntos de datos**, que describen los datos de entrada y salidos de actividad de sección HDInsight de la canalización. 
4.  Crea una **canalización** con una actividad de **Subárbol HDInsight** .  


## <a name="next-steps"></a>Pasos siguientes
En este artículo, ha creado una canalización con una actividad de transformación (HDInsight actividad) que se ejecuta una secuencia de comandos de la sección en un clúster de HDInsight a petición. Para ver cómo utilizar una actividad de copiar para copiar datos de un blobs de Windows Azure en SQL Azure, vea [Tutorial: copiar datos de un Azure blob a SQL Azure](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
  
## <a name="see-also"></a>Vea también
| Tema | Descripción |
| :---- | :---- |
| [Actividades de transformación de datos](data-factory-data-transformation-activities.md) | Este artículo proporciona una lista de las actividades de transformación de datos (como HDInsight sección transformación utilizados en este tutorial) compatibles con el generador de datos de Azure. | 
| [Programación y ejecución](data-factory-scheduling-and-execution.md) | En este artículo se explica los aspectos de programación y ejecución del generador de datos de Azure del modelo de aplicación. |
| [Canalizaciones](data-factory-create-pipelines.md) | Este artículo le ayudará a comprender canalizaciones y actividades en el generador de datos de Azure y cómo usarlas para llevar a cabo controlados por datos flujos de trabajo para su situación o empresa. |
| [Conjuntos de datos](data-factory-create-datasets.md) | Este artículo le ayudará a comprender los conjuntos de datos en el generador de datos de Azure.
| [Supervisar y administrar tuberías mediante la aplicación de supervisión](data-factory-monitor-manage-app.md) | En este artículo se describe cómo supervisar, administrar y depurar tuberías mediante la supervisión y la aplicación de administración. 
