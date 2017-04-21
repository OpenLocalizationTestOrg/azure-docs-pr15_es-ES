<properties 
    pageTitle="Transformación de datos: Datos de proceso y transformar | Microsoft Azure" 
    description="Obtenga información sobre cómo transformar datos o datos de proceso de fábrica de datos de Azure con Hadoop, el aprendizaje o análisis de lago de datos de Azure." 
    keywords="transformación de datos, los datos de proceso, transformar datos, la actividad de transformación"
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
    ms.date="09/23/2016" 
    ms.author="shlo"/>

# <a name="transform-data-in-azure-data-factory"></a>Transformar datos en el generador de datos de Azure
> [AZURE.SELECTOR]
[Sección](data-factory-hive-activity.md)  
[Cerdo](data-factory-pig-activity.md)  
[MapReduce](data-factory-map-reduce.md)  
[Transmisión de Hadoop](data-factory-hadoop-streaming-activity.md)
[El aprendizaje](data-factory-azure-ml-batch-execution-activity.md) 
[Procedimiento almacenado](data-factory-stored-proc-activity.md)
[Datos lago análisis U-SQL](data-factory-usql-activity.md)
[.NET personalizada](data-factory-use-custom-activities.md)
   

## <a name="overview"></a>Información general 
Este artículo explica las actividades de transformación de datos en el generador de datos de Azure que puede usar para transformar y procesa los datos sin formato en predicciones y perspectivas. Una actividad de transformación se ejecuta en un entorno informático como clúster de Azure HDInsight o un lote de Azure. Proporciona vínculos a artículos con información detallada sobre cada actividad de transformación.
 
Generador de datos admite las siguientes transformación actividades de datos que se pueden agregar a [canalizaciones](data-factory-create-pipelines.md) individualmente o encadenadas con otra actividad.

> [AZURE.NOTE] Para obtener un tutorial con instrucciones paso a paso, vea el artículo [crear una canalización de transformación de sección](data-factory-build-your-first-pipeline.md) .  

## <a name="hdinsight-hive-activity"></a>Actividad de la sección de HDInsight
La actividad de subárbol HDInsight de una canalización de datos fábrica ejecuta consultas subárbol sus propias o clúster de HDInsight basado en Windows y Linux a petición. Consulte el artículo de [Actividad de la sección](data-factory-hive-activity.md) para obtener información detallada sobre esta actividad. 

## <a name="hdinsight-pig-activity"></a>Actividad de cerdo HDInsight
La actividad de cerdo HDInsight de una canalización de datos fábrica ejecuta consultas cerdo sus propias o clúster de a petición HDInsight basado en Windows o Linux. Consulte el artículo [Cerdo actividad](data-factory-pig-activity.md) para obtener información detallada sobre esta actividad. 

## <a name="hdinsight-mapreduce-activity"></a>HDInsight MapReduce actividad
La actividad HDInsight MapReduce en una canalización de datos fábrica ejecuta programas MapReduce sus propias o clúster de a petición HDInsight basado en Windows o Linux. Consulte el artículo [MapReduce actividad](data-factory-map-reduce.md) para obtener información detallada sobre esta actividad.

Puede usar MapReduce actividad para ejecutar programas de motor en el clúster HDInsight Spark. Consulte [motor invocar programas de la fábrica de datos de Azure](data-factory-spark.md) para obtener información detallada.

## <a name="hdinsight-streaming-activity"></a>Actividad de transmisión de HDInsight
La actividad de transmisión de HDInsight de una canalización de datos fábrica ejecuta programas de transmisión de Hadoop sus propias o clúster de a petición HDInsight basado en Windows o Linux. Ver la [actividad de transmisión de HDInsight](data-factory-hadoop-streaming-activity.md) para obtener más información sobre esta actividad.

## <a name="machine-learning-activities"></a>Actividades de aprendizaje de equipo
Generador de datos de Azure le permite crear fácilmente canalizaciones que utilizan un servicio web de aprendizaje del equipo de Azure publicado para análisis predictivo. Uso de la [Actividad de ejecución de lotes](data-factory-azure-ml-batch-execution-activity.md#invoking-a-web-service-using-batch-execution-activity) en una canalización del generador de datos de Azure, puede invocar un servicio web de aprendizaje para realizar predicciones en los datos por lotes.

Con el tiempo, los modelos de predictivos en el aprendizaje de máquina puntuación experimentación necesitan aprender a utilizarla con nuevos conjuntos de datos de entrada. Cuando haya terminado con la formación, que desea actualizar el servicio web puntuación con el modelo de aprendizaje de máquina retrained. Puede usar la [Actividad de recursos actualizar](data-factory-azure-ml-batch-execution-activity.md#updating-models-using-update-resource-activity) para actualizar el servicio web con el modelo recién capacitado.  

Ver [las actividades de usar el aprendizaje](data-factory-azure-ml-batch-execution-activity.md) para obtener más información sobre estas actividades de aprendizaje del equipo. 

## <a name="stored-procedure-activity"></a>Actividad de procedimiento almacenado
Puede usar la actividad de procedimiento almacenado de SQL Server en una canalización del generador de datos para invocar un procedimiento almacenado en una del siguientes almacena datos: Azure base de datos SQL, almacenamiento de datos de SQL Azure, base de datos de SQL Server de la empresa o una máquina virtual de Azure. Consulte el artículo de [Actividad de procedimiento almacenado](data-factory-stored-proc-activity.md) para obtener información detallada.  

## <a name="data-lake-analytics-u-sql-activity"></a>Actividad de análisis de lago U-SQL de datos
Actividad de SQL U de análisis de datos lago se ejecuta una secuencia de comandos SQL U en un clúster de análisis de lago de datos de Azure. Consulte el artículo de [Actividad SQL U de análisis de datos](data-factory-usql-activity.md) para obtener información detallada. 

## <a name="net-custom-activity"></a>Actividad de .NET personalizada
Si necesita transformar datos de forma que no es compatible con el generador de datos, puede crear una actividad personalizada con su propia lógica de procesamiento de datos y usar la actividad de la canalización. Puede configurar la actividad de .NET personalizada para ejecutar con un servicio de Azure lote o en un clúster de HDInsight de Azure. Vea el artículo [usar las actividades personalizadas](data-factory-use-custom-activities.md) para obtener información detallada. 

Puede crear una actividad personalizada para ejecutar las secuencias de comandos de R en el clúster HDInsight con R instalado. Consulte [Ejecutar R Script con el generador de datos de Azure](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample). 

## <a name="compute-environments"></a>Calcular entornos
Crear un servicio en el entorno de cálculo vinculado y, a continuación, usar el servicio vinculado al definir una actividad de transformación. Existen dos tipos de entornos de cálculo compatibles con el generador de datos. 

1. **A petición**: en este caso, el entorno es completamente administrado por el generador de datos. Se crea automáticamente el servicio de generador de datos antes de que un trabajo se envía a los datos de proceso y quita una vez finalizado el trabajo. Puede configurar y controlar la configuración específica del entorno de cálculo a petición para la ejecución del trabajo, la administración de clúster y arranque acciones. 
2. **Traer su propia**: en este caso, puede registrar su propio entorno (por ejemplo HDInsight clúster) como un servicio en el generador de datos vinculado. El entorno se administra por usted y el servicio de generador de datos usa para ejecutar las actividades. 

Consulte el artículo [Calcular servicios vinculadas](data-factory-compute-linked-services.md) para obtener información sobre calcular servicios compatibles con el generador de datos. 


## <a name="summary"></a>Resumen
Generador de datos de Azure es compatible con las siguientes actividades de transformación de datos y los entornos de cálculo para las actividades. Las actividades de transformación pueden ser agregadas a canalizaciones individualmente o encadenadas con otra actividad.

Actividad de transformación de datos |  Calcular el entorno 
:----------------------- | :--------------------
[Sección](data-factory-hive-activity.md) | HDInsight [Hadoop] 
[Cerdo](data-factory-pig-activity.md) | HDInsight [Hadoop]  
[MapReduce](data-factory-map-reduce.md) | HDInsight [Hadoop]  
[Hadoop transmisión](data-factory-hadoop-streaming-activity.md) | HDInsight [Hadoop]
[Máquina actividades de aprendizaje: ejecución por lotes y recursos de actualización](data-factory-azure-ml-batch-execution-activity.md) | Azure VM 
[Procedimiento almacenado](data-factory-stored-proc-activity.md) | SQL Azure, almacenamiento de datos SQL Azure o SQL Server |
[Datos de análisis de lago U SQL](data-factory-usql-activity.md) | Análisis de datos de Azure lago 
[DotNet](data-factory-use-custom-activities.md) | HDInsight [Hadoop] o lote de Azure
   

