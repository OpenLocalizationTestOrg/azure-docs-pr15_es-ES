<properties 
   pageTitle="Escenarios de datos relacionados con el almacén de datos de lago | Microsoft Azure" 
   description="Comprender los diferentes escenarios y herramientas mediante los datos que pueden ingestión, procesan, descargar y visualizarse en un almacén de datos de lago" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/06/2016"
   ms.author="nitinme"/>

# <a name="using-azure-data-lake-store-for-big-data-requirements"></a>Usar almacén de lago de datos de Azure para requisitos de datos grande

Hay cuatro fases claves en el procesamiento de datos grande:

* Recopila grandes cantidades de datos en un almacén de datos en tiempo real o por lotes
* Procesamiento de los datos
* Descargar los datos
* Visualización de los datos

En este artículo, veremos estas fases con respecto a lago almacén de datos de Azure para conocer las opciones y herramientas disponibles para satisfacer sus necesidades de datos grande.

## <a name="ingest-data-into-data-lake-store"></a>Recopilar datos en el almacén de datos de lago

En esta sección se resalta los diferentes orígenes de datos y las distintas maneras en que pueden ser ingeridos esos datos a una cuenta de almacén de datos lago.

![Datos de ingesta en el almacén de datos de lago] (./media/data-lake-store-data-scenarios/ingest-data.png "Datos de ingesta en el almacén de datos de lago")

### <a name="ad-hoc-data"></a>Datos ad hoc

Esto representa más pequeños conjuntos de datos que se utiliza para los prototipos una aplicación de datos grande. Existen diferentes maneras de recopilación datos ad hoc en función del origen de datos.

| Origen de datos        | Recopilar mediante                                                                        |
|--------------------|----------------------------------------------------------------------------------------|
| Equipo local     | <ul> <li>[Portal de Azure](/data-lake-store-get-started-portal.md)</li> <li>[Azure PowerShell](data-lake-store-get-started-powershell.md)</li> <li>[Azure CLI entre plataformas](data-lake-store-get-started-cli.md)</li> <li>[Usando datos lago Tools para Visual Studio](../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md#upload-source-data-files) </li></ul> |
| Almacenamiento de Azure Blob | <ul> <li>[Generador de datos de Azure](../data-factory/data-factory-azure-datalake-connector.md#sample-copy-data-from-azure-blob-to-azure-data-lake-store)</li> <li>[Herramienta AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[Ejecutar en el clúster de HDInsight de DistCp](data-lake-store-copy-data-wasb-distcp.md)</li> </ul> |

 
### <a name="streamed-data"></a>Datos transmitidos

Representa los datos que se pueden generar diversas fuentes como aplicaciones, dispositivos, sensores, etcetera. Pueden ser ingeridos estos datos en un almacén de datos lago con herramientas de variedad. Estas herramientas normalmente se capturar y procesar los datos en una base por evento en tiempo real y, a continuación, escribir los eventos en lotes en almacén de datos de lago para que pueden procesarse aún más. 

Siguientes son herramientas que puede usar:
 
* [Secuencia azure análisis] (.. / secuencia de análisis de datos lago salida) - eventos ingeridos en evento Hubs pueden escribirse lago de datos de Azure con un resultado de la tienda de lago de datos de Azure.
* [Azure HDInsight tormenta](../hdinsight/hdinsight-storm-write-data-lake-store.md) - puede escribir datos directamente al almacén de datos lago desde el clúster de tormenta.
* [EventProcessorHost](../event-hubs/event-hubs-csharp-ephcs-getstarted.md#receive-messages-with-eventprocessorhost) : puede recibir eventos de Hubs de evento y, a continuación, escribir al almacén de datos lago mediante el [Almacén de datos lago .NET SDK](data-lake-store-get-started-net-sdk.md).

### <a name="relational-data"></a>Datos relacionales

También puede origen bases de datos relacionales. Durante un período de tiempo, bases de datos relacionales recopilan grandes cantidades de datos que pueden proporcionar información clave si se procesan a través de una canalización de datos grande. Puede usar las siguientes herramientas para mover datos en el almacén de datos de lago.

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Generador de datos de Azure](../data-factory/data-factory-data-movement-activities.md)

### <a name="web-server-log-data-upload-using-custom-applications"></a>Datos de registro de servidor Web (cargar con aplicaciones personalizadas)

Este tipo de conjunto de datos se denomina específicamente porque el análisis de datos de registro de servidor web es un caso de uso común para las aplicaciones de datos grande y requiere grandes volúmenes de archivos de registro de cargarse en el almacén de datos de lago. Puede usar cualquiera de las siguientes herramientas para escribir sus propios scripts o aplicaciones a cargar dichos datos.

* [Azure CLI entre plataformas](data-lake-store-get-started-cli.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Almacén de lago de datos de Azure .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Generador de datos de Azure](../data-factory/data-factory-data-movement-activities.md)

Para cargar los datos de registro de servidor web y para cargar otros tipos de datos (por ejemplo, los datos de sentimientos social), es un buen método para escribir sus propias secuencias de comandos o aplicaciones personalizadas porque proporciona la flexibilidad para incluir los datos al cargar el componente como parte de la aplicación de datos grande más grande. En algunos casos este código puede adoptar la forma de una secuencia de comandos o la utilidad de la línea de comandos simple. En los demás casos, el código puede utilizarse para integrar un tamaño grande para procesamiento de datos en una solución o aplicación empresarial.


### <a name="data-associated-with-azure-hdinsight-clusters"></a>Datos asociados con los clústeres HDInsight de Azure

La mayoría de los tipos de clúster HDInsight (Hadoop, HBase, tormenta) admiten almacén de lago de datos como un repositorio de almacenamiento de datos. HDInsight clústeres acceso a datos de Azure almacenamiento de Blobs (WASB). Para mejorar el rendimiento, puede copiar los datos de WASB a una cuenta de almacén de datos lago asociada con el clúster. Puede usar las siguientes herramientas para copiar los datos.

* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)
* [Servicio de AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
* [Generador de datos de Azure](../data-factory/data-factory-azure-datalake-connector.md#sample-copy-data-from-azure-blob-to-azure-data-lake-store)

### <a name="data-stored-in-on-premise-or-iaas-hadoop-clusters"></a>Datos almacenan en local o IaaS Hadoop clústeres

Grandes cantidades de datos pueden almacenarse en clústeres Hadoop existentes, localmente en equipos con HDFS. Los clústeres Hadoop pueden estar en una implementación local o pueden estar dentro de un clúster de IaaS en Azure. Puede haber requisitos para copiar estos datos al almacén de lago de datos de Azure para un enfoque único o de forma periódica. Hay varias opciones que puede usar para ello. A continuación se ofrece una lista de alternativas y las ventajas y desventajas asociadas.

| Enfoque  | Detalles | Ventajas   | Consideraciones  |
|-----------|---------|--------------|-----------------|
| Usar generador de datos de Azure (ADF) para copiar datos directamente desde Hadoop clústeres al almacén de lago de datos de Azure | [ADF admite HDFS como origen de datos](../data-factory/data-factory-hdfs-connector.md) | ADF permite del cuadro HDFS, primera clase llevar a cabo administración y supervisión | Requiere Data Management Gateway para su implementación local o en el clúster de IaaS |
| Exportar datos de Hadoop como archivos. A continuación, copie los archivos al almacén de lago de datos de Azure mediante mecanismo adecuado.                                   | Puede copiar archivos a Azure datos lago almacén mediante: <ul><li>[Azure PowerShell para el sistema operativo Windows](data-lake-store-get-started-powershell.md)</li><li>[CLI de varias plataformas para OS Windows Azure](data-lake-store-get-started-cli.md)</li><li>Aplicación personalizada con cualquier SDK de almacén de datos lago</li></ul> | Rápido empezar. Puede hacer cargas personalizadas                                                   | Proceso de varios pasos que implica varias tecnologías. Administración y supervisión crecerá a ser un desafío con el tiempo debido a la naturaleza de las herramientas personalizada |
| Use Distcp para copiar los datos de Hadoop para el almacenamiento de Azure. Copiar los datos desde el almacenamiento de Azure al almacén de datos lago con mecanismo adecuado. | Puede copiar datos de Azure almacenamiento en almacén de datos lago mediante: <ul><li>[Generador de datos de Azure](../data-factory/data-factory-data-movement-activities.md)</li><li>[Herramienta AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[DistCp de Apache que se ejecutan en clústeres HDInsight](data-lake-store-copy-data-wasb-distcp.md)</li></ul>| Puede usar herramientas de código abierto. | Proceso de varios pasos que implica varias tecnologías |

### <a name="really-large-datasets"></a>Realmente grandes conjuntos de datos

Para cargar conjuntos de datos que van en varios terabytes, usando los métodos descritos anteriormente a veces puede ser lenta y costosas. En estos casos, puede usar las siguientes opciones.

* **Uso de Azure ExpressRoute**. Azure ExpressRoute le permite crear conexiones privadas entre los centros de datos de Azure y la infraestructura en sus dispositivos locales. Esto proporciona una opción confiable para transferir grandes cantidades de datos. Para obtener más información, consulte la [documentación de Azure ExpressRoute](../expressroute/expressroute-introduction.md).


* **"Sin conexión" carga de datos**. Si usa Azure ExpressRoute no es viable por cualquier motivo, puede usar [servicio de importación o exportación de Azure](../storage/storage-import-export-service.md) para enviar las unidades de disco duro con los datos de un centro de datos de Azure. Los datos se cargan primero a Azure almacenamiento de Blobs. A continuación, puede usar [Generador de datos de Azure](../data-factory/data-factory-azure-datalake-connector.md#sample-copy-data-from-azure-blob-to-azure-data-lake-store) o [AdlCopy herramienta](data-lake-store-copy-data-azure-storage-blob.md) para copiar los datos de Azure almacenamiento de Blobs al almacén de datos lago.

    >[AZURE.NOTE] Mientras usando el servicio de importación o exportación, los tamaños de archivo en los discos que se envían al centro de datos de Azure no debe ser mayor que 200 GB.


## <a name="process-data-stored-in-data-lake-store"></a>Procesar datos almacenados en el almacén de datos de lago

Una vez que los datos están disponibles en el almacén de datos de lago puede ejecutar el análisis de datos con las aplicaciones de datos de gran compatibles. Actualmente, puede usar para ejecutar los trabajos de análisis de datos en los datos almacenados en el almacén de datos de lago HDInsight de Azure y Azure datos lago análisis. 

![Analizar datos en el almacén de datos de lago] (./media/data-lake-store-data-scenarios/analyze-data.png "Analizar datos en el almacén de datos de lago")

Puede buscar en los ejemplos siguientes.

* [Crear un clúster HDInsight con el almacén de datos de lago como almacenamiento](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Usar el análisis de datos de Azure lago con el almacén de datos de lago](../data-lake-analytics/data-lake-analytics-get-started-portal.md)



## <a name="download-data-from-data-lake-store"></a>Descargar datos desde el almacén de datos de lago

También puede descargar o mover los datos de almacén de lago de datos de Azure para escenarios como:

* Mover los datos a otros repositorios para interactuar con las canalizaciones de procesamiento de datos existente. Por ejemplo, que desea mover los datos de almacén de lago de datos a la base de datos de SQL Azure o local de SQL Server.
* Descargar datos en el equipo local para el procesamiento en entornos IDE durante la creación de prototipos de aplicaciones.

![Datos de salida de almacén de datos de lago] (./media/data-lake-store-data-scenarios/egress-data.png "Datos de salida de almacén de datos de lago")

En estos casos, puede utilizar cualquiera de las siguientes opciones:

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Generador de datos de Azure](../data-factory/data-factory-data-movement-activities.md)
* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)

También puede usar los siguientes métodos para escribir su propio script o la aplicación para descargar datos desde el almacén de datos de lago.

* [Azure CLI entre plataformas](data-lake-store-get-started-cli.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Almacén de lago de datos de Azure .NET SDK](data-lake-store-get-started-net-sdk.md)

## <a name="visualize-data-in-data-lake-store"></a>Visualizar datos en el almacén de datos de lago

Puede usar una combinación de servicios para crear representaciones visuales de los datos almacenados en el almacén de datos de lago.

![Visualizar datos almacén de datos lago] (./media/data-lake-store-data-scenarios/visualize-data.png "Visualizar datos almacén de datos lago")

* Puede iniciar mediante el [Generador de datos de Azure para mover los datos del almacén de datos lago al almacén de datos de SQL Azure](../data-factory/data-factory-data-movement-activities.md#supported-data-stores)
* Después de eso, puede [integrar el almacenamiento de datos de SQL Azure de Power BI](../sql-data-warehouse/sql-data-warehouse-integrate-power-bi.md) para crear una representación visual de los datos.
