<properties 
    pageTitle="Generador de datos de Azure - preguntas más frecuentes" 
    description="Preguntas más frecuentes sobre el generador de datos de Azure." 
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
    ms.date="09/12/2016" 
    ms.author="shlo"/>

# <a name="azure-data-factory---frequently-asked-questions"></a>Generador de datos de Azure - preguntas más frecuentes

## <a name="general-questions"></a>Preguntas generales

### <a name="what-is-azure-data-factory"></a>¿Qué es el generador de datos de Azure?

Generador de datos es basada en nube que **automatiza el movimiento y la transformación de datos**de servicios de integración de datos. Igual que un generador que se ejecute del equipo para tomar prima y transformarlos en mercancías terminadas, el generador de datos organiza servicios existentes que recopilan datos sin formato y transforman en información de listos para usar. 
 
Generador de datos le permite crear flujos de trabajo controlados por datos para mover datos entre tanto local y almacena datos de nube como proceso o transformar datos mediante servicios de cálculo como HDInsight de Azure y Azure datos lago análisis. Después de crear una canalización que realiza la acción que necesita, puede programar que se ejecute periódicamente (cada hora, diariamente, semanalmente etcetera).   

Para obtener más detalles, vea [Introducción y conceptos clave](data-factory-introduction.md) . 

### <a name="where-can-i-find-pricing-details-for-azure-data-factory"></a>¿Dónde puedo encontrar detalles de los precios de fábrica de datos de Azure?

Consulte la [página de detalles de precios de fábrica de datos] [ adf-pricing-details] de los detalles de precios para el generador de datos de Azure.  

### <a name="how-do-i-get-started-with-azure-data-factory"></a>¿Cómo puedo empezar con el generador de datos de Azure?

- Para obtener información general del generador de datos de Azure, vea [Introducción al generador de datos de Azure](data-factory-introduction.md).
- Para obtener un tutorial acerca de cómo **Copiar o mover datos** mediante copia actividad, vea [copiar los datos desde el almacenamiento de blobs de Windows Azure a la base de datos de SQL Azure](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
- Para obtener un tutorial acerca de cómo **Transformar datos** con HDInsight subárbol actividad. Ver [datos de proceso ejecutando el script de subárbol en clúster Hadoop](data-factory-build-your-first-pipeline.md) 
  
### <a name="what-is-the-data-factorys-region-availability"></a>¿Qué es la disponibilidad de la región del generador de datos?
Generador de datos está disponible en **Europa del Norte**y **Oeste de Estados Unidos** . Los servicios de proceso y almacenamiento usados generadores de datos pueden estar en otras regiones. Vea [las regiones admitidos](data-factory-introduction.md#supported-regions). 
 
### <a name="what-are-the-limits-on-number-of-data-factoriespipelinesactivitiesdatasets"></a>¿Cuáles son los límites de la cantidad de datos fábricas/canalizaciones/actividades o conjuntos de datos?
 
Vea la sección de **Límites de generador de datos de Azure** del artículo de la [suscripción de Azure y los límites del servicio, cuotas y las restricciones](../azure-subscription-service-limits.md#data-factory-limits) .

### <a name="what-is-the-authoringdeveloper-experience-with-azure-data-factory-service"></a>¿Qué es la experiencia de creación o programador con el servicio de generador de datos de Azure?

Puede autor o crear generadores de datos mediante uno de estos procedimientos:

- **Portal de Azure**  
   aspas de la fábrica de datos en el portal de Azure proporcionan interfaz de usuario enriquecido para crear servicios de datos fábricas ad vinculado. El **Editor del generador de datos**, que forma parte del portal, le permite crear fácilmente servicios vinculados, tablas, conjuntos de datos y canalizaciones especificando las definiciones de JSON para estos artefactos. Para obtener un ejemplo de usar el editor de portal para crear e implementar un generador de datos, vea [crear su primer canalización de datos con Azure portal](data-factory-build-your-first-pipeline-using-editor.md) .

- **Visual Studio**  
   puede usar Visual Studio para crear un generador de datos de Azure. Para obtener información detallada, vea [crear su primer canalización de datos mediante Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) . 

- **PowerShell Azure**  
   consulte [crear y monitor generador de datos de Azure con PowerShell Azure](data-factory-build-your-first-pipeline-using-powershell.md) para obtener un tutorial/tutorial para crear un generador de datos con PowerShell. Vea [Referencia de datos fábrica Cmdlet] [ adf-powershell-reference] contenido en la biblioteca de MSDN para obtener una documentación completa de cmdlets de generador de datos.
   
- **Biblioteca de clases .NET**  
   mediante programación, puede crear generadores de datos usando el generador de datos .NET SDK. Para obtener un tutorial de creación de un generador de datos con .NET SDK, vea [crear, supervisar y administrar fábricas de datos usando .NET SDK](data-factory-create-data-factories-programmatically.md) . Vea la [Referencia de biblioteca de clases de fábrica datos] [ msdn-class-library-reference] para obtener una documentación completa de datos fábrica .NET SDK.

- **API de REST**  
   también puede usar la API de REST expuestas por el servicio de generador de datos de Azure para crear e implementar generadores de datos. Vea la [Referencia de la API de REST de datos fábrica] [ msdn-rest-api-reference] para una documentación exhaustiva de la API de REST generador de datos.
 
- **Azure plantilla de administrador de recursos** 
   ver [Tutorial: crear su primer generador de datos de Azure con el Administrador de recursos de Azure plantilla](data-factory-build-your-first-pipeline-using-arm.md) detalles fo. 

### <a name="can-i-rename-a-data-factory"></a>¿Puedo cambiar el nombre de un generador de datos?
No. Al igual que otros recursos de Azure, no se puede cambiar el nombre de un generador de datos de Azure. 

### <a name="can-i-move-a-data-factory-from-one-azure-subscription-to-another"></a>¿Puedo mover un generador de datos de una suscripción de Azure a otro? 
Sí. Use el botón **Ir** en el módulo de generador de datos como se muestra en el diagrama siguiente. 

![Mover el generador de datos](media/data-factory-faq/move-data-factory.png)

### <a name="what-are-the-compute-environments-supported-by-data-factory"></a>¿Qué son los entornos de cálculo compatibles con el generador de datos?
La tabla siguiente proporciona una lista de los entornos de cálculo compatible con el generador de datos y las actividades que se pueden ejecutar en ellos. 

| Calcular el entorno | actividades |
| ------------------- | -------- | 
| [HDInsight a petición clúster](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) o [su propio HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) | [DotNet](data-factory-use-custom-activities.md), [subárbol](data-factory-hive-activity.md), [cerdo](data-factory-pig-activity.md), [MapReduce](data-factory-map-reduce.md), [transmisión Hadoop](data-factory-hadoop-streaming-activity.md) | 
| [Lote de Azure](data-factory-compute-linked-services.md#azure-batch-linked-service) | [DotNet](data-factory-use-custom-activities.md) |  
| [Aprendizaje del equipo de Azure](data-factory-compute-linked-services.md#azure-machine-learning-linked-service) | [Máquina actividades de aprendizaje: ejecución por lotes y recursos de actualización](data-factory-azure-ml-batch-execution-activity.md) |
| [Análisis de datos de Azure lago](data-factory-compute-linked-services.md#azure-data-lake-analytics-linked-service) | [Datos de análisis de lago U SQL](data-factory-usql-activity.md)
| [SQL azure](data-factory-compute-linked-services.md#azure-sql-linked-service), [almacenamiento de datos SQL Azure](data-factory-compute-linked-services.md#azure-sql-data-warehouse-linked-service), [SQL Server](data-factory-compute-linked-services.md#sql-server-linked-service) | [Procedimiento almacenado](data-factory-stored-proc-activity.md)

## <a name="activities---faq"></a>Actividades - preguntas más frecuentes
### <a name="what-are-the-different-types-of-activities-you-can-use-in-a-data-factory-pipeline"></a>¿Cuáles son los diferentes tipos de actividades que puede usar en una canalización de fábrica de datos? 

- [Actividades de movimiento de datos](data-factory-data-movement-activities.md) para mover los datos.
- [Las actividades de transformación de datos](data-factory-data-transformation-activities.md) de proceso o transformar datos. 

### <a name="when-does-an-activity-run"></a>¿Cuándo se ejecuta una actividad?
La configuración de **disponibilidad** en la tabla de datos de salida determina cuando se ejecuta la actividad. Si se especifican los conjuntos de datos de entrada, la actividad comprueba si se cumplen todas las dependencias de entrada de datos (es decir, estado **Listo** ) antes de que empiece la ejecución. 

## <a name="copy-activity---faq"></a>Copiar actividad - preguntas más frecuentes
### <a name="is-it-better-to-have-a-pipeline-with-multiple-activities-or-a-separate-pipeline-for-each-activity"></a>¿Es mejor tener una canalización con varias actividades o un proceso independiente para cada actividad? 
Se supone que canalizaciones para agrupar actividades relacionadas. Si los conjuntos de datos que se conectan a ellas no se consuman en cualquier otra actividad fuera de la canalización, puede mantener las actividades de una canalización. De esta forma, no necesitará períodos activa de canalización de cadena para que se alineen con otros. Además, la integridad de datos en las tablas internas de la canalización de se conserva mejor cuando se actualiza la canalización. Actualización de canalización esencialmente deja de todas las actividades dentro de la canalización, los elimina y crea de nuevo. Desde la perspectiva de edición, también puede ser más fácil ver el flujo de datos dentro de las actividades relacionadas en un archivo JSON para la canalización.

### <a name="what-are-the-supported-data-stores"></a>¿Cuáles son lo almacena datos compatibles?
[AZURE.INCLUDE [data-factory-supported-data-stores](../../includes/data-factory-supported-data-stores.md)]

### <a name="what-are-the-supported-file-formats"></a>¿Qué son los formatos de archivo compatibles? 
[AZURE.INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]

### <a name="where-is-the-copy-operation-performed"></a>¿Dónde se realiza la operación de copia? 
Para obtener más información, vea la sección [movimientos de datos disponibles globalmente](data-factory-data-movement-activities.md#global) . En resumen, cuando se trata de un almacén de datos local, se realiza la operación de copiar la puerta de enlace de administración de datos en su entorno local. Y cuando el movimiento de datos entre dos almacena de nube, se realiza la operación de copia de la región más cercana a la ubicación de receptor en la misma geografía. 


## <a name="hdinsight-activity---faq"></a>Actividad de HDInsight - preguntas más frecuentes

### <a name="what-regions-are-supported-by-hdinsight"></a>¿Qué regiones son compatibles con HDInsight?

Consulte la sección disponibilidad geográfica en el siguiente artículo: o los [Detalles de precios de HDInsight][hdinsight-supported-regions].

### <a name="what-region-is-used-by-an-on-demand-hdinsight-cluster"></a>¿En qué región se utiliza un clúster de HDInsight a petición?

Crear el clúster de HDInsight a petición de la misma región donde existe el almacenamiento especificado para usarse con el clúster.    

### <a name="how-to-associate-additional-storage-accounts-to-your-hdinsight-cluster"></a>¿Cómo puedo asociar cuentas de almacenamiento adicional al clúster HDInsight?

Si está utilizando su propio HDInsight clúster (BYOC - Traer su propio clúster), vea los temas siguientes: 

- [Usar un clúster de HDInsight con Metastores y cuentas de almacenamiento alternativo][hdinsight-alternate-storage]
- [Usar cuentas de almacenamiento adicional con HDInsight subárbol][hdinsight-alternate-storage-2]

Si está utilizando un clúster a petición creadas por el servicio de generador de datos, especifique las cuentas de almacenamiento adicional para el HDInsight vinculan servicio para que el servicio de generador de datos puede registrar en su nombre. En la definición de JSON para el servicio vinculado a petición, utilice **additionalLinkedServiceNames** (propiedad) para especificar las cuentas de almacenamiento alternativo tal como se muestra en el siguiente fragmento JSON:
 
    {
        "name": "MyHDInsightOnDemandLinkedService",
        "properties":
        {
            "type": "HDInsightOnDemandLinkedService",
            "typeProperties": {
                "clusterSize": 1,
                "timeToLive": "00:01:00",
                "linkedServiceName": "LinkedService-SampleData",
                "additionalLinkedServiceNames": [ "otherLinkedServiceName1", "otherLinkedServiceName2" ] 
            }
        }
    } 

En el ejemplo anterior, otherLinkedServiceName1 y otherLinkedServiceName2 representan servicios vinculados cuyas definiciones contienen credenciales de clúster HDInsight necesita tener acceso a cuentas de almacenamiento alternativo.

## <a name="slices---faq"></a>Sectores: preguntas más frecuentes

### <a name="why-are-my-input-slices-not-in-ready-state"></a>¿Por qué mi entrados sectores no en estado listo?  
Un error común no es propiedad **externo** para establecer **true** en el conjunto de datos de entrada cuando los datos de entrada están externos a la fábrica de datos (no producida por el generador de datos). 

En el ejemplo siguiente, solo es necesario configurar **externos** true en **dataset1**.  

**DataFactory1** Canalización 1: dataset1 -> activity1 -> dataset2 -> activity2 -> dataset3 canalización 2: dataset3 -> activity3 -> dataset4

Si tiene otro generador de datos con una canalización que toma dataset4 (creados mediante una canalización 2 de fábrica datos 1), marque dataset4 como un conjunto de datos externo porque el conjunto de datos se produce un generador de datos distintos (DataFactory1, no DataFactory2).  

**DataFactory2**    
Canalización 1: dataset4 -> activity4 -> dataset5

Si la propiedad externa es establecer correctamente, compruebe si los datos de entrada existen en la ubicación especificada en la definición de conjunto de datos de entrada. 

### <a name="how-to-run-a-slice-at-another-time-than-midnight-when-the-slice-is-being-produced-daily"></a>¿Cómo puedo ejecutar un sector en otro momento que medianoche cuando la segmentación de datos se produce diariamente?
Utilice la propiedad de **desplazamiento** para especificar la hora a la que desea que el sector va a producir. Para obtener más información sobre esta propiedad, vea la sección [disponibilidad de conjunto de datos](data-factory-create-datasets.md#Availability) . Este es un ejemplo rápido:

    "availability":
    {
        "frequency": "Day",
        "interval": 1,
        "offset": "06:00:00"
    }

Inician y sectores diarios en **6 A.M.** en lugar de la medianoche de forma predeterminada.     

### <a name="how-can-i-rerun-a-slice"></a>¿Cómo puedo volver a ejecutar un sector?
Puede volver a ejecutar una división en una de las siguientes maneras: 

- Use Monitor y administrar aplicaciones para volver a ejecutar una ventana actividad o sector. Para obtener instrucciones, vea [volver a ejecutar seleccionado windows actividad](data-factory-monitor-manage-app.md#re-run-selected-activity-windows) .   
- Haga clic en **Ejecutar** en la barra de comandos en el módulo de **Sectores de datos** para el sector en el portal de Azure.
- Ejecutar el cmdlet **Set-AzureRmDataFactorySliceStatus** con estado establecido en **espera** para el sector.   
    
        Set-AzureRmDataFactorySliceStatus -Status Waiting -ResourceGroupName $ResourceGroup -DataFactoryName $df -TableName $table -StartDateTime "02/26/2015 19:00:00" -EndDateTime "02/26/2015 20:00:00" 

Vea [Configurar AzureRmDataFactorySliceStatus] [ set-azure-datafactory-slice-status] para obtener información detallada acerca del cmdlet. 

### <a name="how-long-did-it-take-to-process-a-slice"></a>¿Cuánto tiempo tardó en procesar un sector?
Utilice el Explorador de ventana de actividad en el Monitor y administrar aplicaciones para saber cuánto tiempo se tardó en procesar un segmento de datos. Para obtener información detallada, consulte [Actividad de ventana del explorador](data-factory-monitor-manage-app.md#activity-window-explorer) . 

También puede hacer lo siguiente en el portal de Azure:  

1. Haga clic en el mosaico de **conjuntos de datos** en el módulo de **Generador de datos** para el generador de datos.
2. Haga clic en el conjunto de datos específico en el módulo de **conjuntos de datos** .
3. Seleccione el sector que le interesa en la lista de **sectores recientes** en el módulo de la **tabla** .
4. Haga clic en la actividad ejecutar desde la lista de **Ejecuciones de actividad** en el módulo de **Sectores de datos** . 
5. Haga clic en el mosaico de **Propiedades** en el módulo de **Detalles de ejecución de la actividad** . 
6. Debe ver el campo **duración** con un valor. Este valor es el tiempo necesario para procesar la segmentación de datos.   

### <a name="how-to-stop-a-running-slice"></a>¿Cómo puedo dejar de un sector ejecución?
Si necesita detener la canalización de ejecución, puede usar el cmdlet de [Suspensión AzureRmDataFactoryPipeline](https://msdn.microsoft.com/library/mt603721.aspx) . Actualmente, suspender la canalización no impide que las ejecuciones del sector que están en curso. Una vez que termine de ejecuciones en curso, no se selecciona ningún segmento adicional.

Si realmente desea detener todas las ejecuciones inmediatamente, sería la única manera de eliminar la canalización y vuelva a crearlo. Si opta por eliminar la canalización, no necesita eliminar tablas y servicios vinculados usados por la canalización. 


[create-factory-using-dotnet-sdk]: data-factory-create-data-factories-programmatically.md
[msdn-class-library-reference]: https://msdn.microsoft.com/library/dn883654.aspx
[msdn-rest-api-reference]: https://msdn.microsoft.com/library/dn906738.aspx

[adf-powershell-reference]: https://msdn.microsoft.com/library/dn820234.aspx 
[azure-portal]: http://portal.azure.com
[set-azure-datafactory-slice-status]: https://msdn.microsoft.com/library/mt603522.aspx

[adf-pricing-details]: http://go.microsoft.com/fwlink/?LinkId=517777
[hdinsight-supported-regions]: http://azure.microsoft.com/pricing/details/hdinsight/
[hdinsight-alternate-storage]: http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx
[hdinsight-alternate-storage-2]: http://blogs.msdn.com/b/cindygross/archive/2014/05/05/use-additional-storage-accounts-with-hdinsight-hive.aspx
 
