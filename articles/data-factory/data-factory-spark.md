<properties 
    pageTitle="Invocar programas de motor de la fábrica de datos de Azure" 
    description="Obtenga información sobre cómo invocar un generador de datos de Azure mediante la actividad MapReduce programas motor." 
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
    ms.topic="article" 
    ms.date="08/25/2016" 
    ms.author="spelluru"/>

# <a name="invoke-spark-programs-from-data-factory"></a>Invocar programas de motor de la fábrica de datos
## <a name="introduction"></a>Introducción
Puede usar la actividad MapReduce en una canalización del generador de datos para ejecutar programas de motor en el clúster HDInsight Spark. Consulte el artículo [MapReduce actividad](data-factory-map-reduce.md) para obtener información detallada sobre el uso de la actividad antes de leer este artículo. 

## <a name="spark-sample-on-github"></a>Ejemplo de motor en GitHub
El [motor - generador de datos de ejemplo en GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/Spark) muestra cómo usar MapReduce actividad invocar un programa de motor. Motor simplemente copia los datos de un contenedor de blobs de Windows Azure a otro. 

## <a name="data-factory-entities"></a>Entidades de generador de datos
La carpeta de **Motor-ADF/src/ADFJsons** contiene archivos de entidades de datos fábrica (servicios vinculados, los conjuntos de datos, canalización).  

En este ejemplo hay dos **servicios vinculados** : el almacenamiento de Azure y HDInsight de Azure. Especifique su nombre de almacenamiento de Azure y los valores de clave en **StorageLinkedService.json** y clusterUri, nombre de usuario y contraseña en **HDInsightLinkedService.json**.

Hay dos **conjuntos de datos** en este ejemplo: **input.json** y **output.json**. Estos archivos se encuentran en la carpeta de **conjuntos de datos** .  Estos archivos representan los conjuntos de datos de entrada y salida de la actividad de MapReduce

Buscar canalizaciones de ejemplo en la carpeta **ADFJsons/canalización** . Revise una canalización para comprender cómo invocar un programa de motor mediante el uso de la actividad de MapReduce. 

La actividad MapReduce está configurada para llamar a **com.adf.sparklauncher.jar** en el contenedor de **adflibs** en el almacenamiento de Azure (especificado en el StorageLinkedService.json). El código fuente de este programa es en motor-ADF/src/main/java/com/adf/llamadas de carpeta y motor de envío y ejecutar motor trabajos. 

> [AZURE.IMPORTANT] 
> Lea [Léame. TXT](https://github.com/Azure/Azure-DataFactory/blob/master/Samples/Spark/README.txt) para obtener la información más reciente y adicional antes de utilizar los datos de ejemplo. 
>  
> Utilice su propio clúster HDInsight Spark con este enfoque para invocar programas motor mediante la actividad MapReduce. Usar un clúster de HDInsight a petición no es compatible.   


## <a name="see-also"></a>Vea también
- [Actividad de sección](data-factory-hive-activity.md)
- [Actividad de cerdo](data-factory-pig-activity.md)
- [MapReduce actividad](data-factory-map-reduce.md)
- [Actividad de transmisión de Hadoop](data-factory-hadoop-streaming-activity.md)
- [Invocar secuencias de comandos de R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)
