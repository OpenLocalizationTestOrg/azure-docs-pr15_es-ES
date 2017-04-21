<properties
   pageTitle="Integración de almacén de datos de lago con otros servicios de Azure | Microsoft Azure"
   description="Comprender cómo se integra el almacén de datos de lago con otros servicios de Azure"
   documentationCenter=""
   services="data-lake-store"
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/28/2016"
   ms.author="nitinme"/>

# <a name="integrating-data-lake-store-with-other-azure-services"></a>Integración de almacén de datos de lago con otros servicios de Azure

Almacén de lago de datos de Azure puede usarse junto con otros servicios de Azure para habilitar una amplia gama de escenarios. El siguiente artículo enumera los servicios que se pueden integrar datos lago tienda con.

## <a name="use-data-lake-store-with-azure-hdinsight"></a>Utilizar el almacén de lago de datos con HDInsight de Azure

Puede proporcionar un clúster de [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) que usa el almacén de datos de lago como el almacenamiento HDFS compatible. En esta versión, para clústeres Hadoop y tormenta en Windows y Linux, puede usar almacén de datos de lago solo como un almacenamiento adicional. Estos clústeres seguirá pudiendo usar el almacenamiento de Azure (WASB) como el almacenamiento predeterminado. Sin embargo, para clústeres HBase en Windows y Linux, puede usar almacén de datos de lago como el almacenamiento de forma predeterminada, almacenamiento adicional o ambos.

Para obtener instrucciones sobre cómo aprovisionar un clúster de HDInsight con el almacén de datos de lago, consulte:

* [Aprovisionar a un clúster de HDInsight con almacén de lago de datos con el Portal de Azure](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Aprovisionar a un clúster de HDInsight con el almacén de datos lago con PowerShell de Azure](data-lake-store-hdinsight-hadoop-use-powershell.md)

**¿Prefiere vídeos?** Siga los vínculos siguientes para ver los vídeos con instrucciones sobre cómo usar el almacén de datos de lago con HDInsight clústeres.

* [Crear un clúster de HDInsight con acceso al almacén de datos de lago](https://mix.office.com/watch/l93xri2yhtp2)
* Una vez que el clúster está configurado, [datos de Access en el almacén de datos lago mediante secuencias de comandos de la sección y cerdo](https://mix.office.com/watch/1n9g5w0fiqv1q)


## <a name="use-data-lake-store-with-azure-data-lake-analytics"></a>Utilizar el almacén de lago de datos con el análisis de datos de Azure lago

[Análisis de lago de datos de Azure](../data-lake-analytics/data-lake-analytics-overview.md) le permite trabajar con datos grande a escala de la nube. Dinámicamente aprovisiona recursos y le permite hacer el análisis en terabytes o incluso exabytes de datos que se pueden almacenar en un número de orígenes de datos compatibles, uno de ellos está almacén de datos de lago. Análisis de datos lago especialmente está optimizado para trabajar con el almacén de lago de datos de Azure - proporcionar el máximo nivel de rendimiento, el rendimiento y la puesta en paralelo para las cargas de trabajo de datos grande.

Para obtener instrucciones sobre cómo usar el análisis de datos lago con lago almacén de datos, vea [Introducción a análisis de lago de datos con el almacén de datos de lago](../data-lake-analytics/data-lake-analytics-get-started-portal.md).

**¿Prefiere vídeos?** Siga los vínculos siguientes para ver los vídeos con instrucciones sobre cómo usar el almacén de datos de lago con HDInsight clústeres.

* [Conectar el análisis de datos de Azure lago al almacén de lago de datos de Azure](https://mix.office.com/watch/qwji0dc9rx9k)
* [Almacén de lago de datos de Access Azure a través de análisis de datos lago](https://mix.office.com/watch/1n0s45up381a8)


## <a name="use-data-lake-store-with-azure-data-factory"></a>Utilizar el almacén de lago de datos con el generador de datos de Azure

Puede usar el [Generador de datos de Azure](https://azure.microsoft.com/services/data-factory/) para recopilar datos de tablas de Azure, base de datos de SQL Azure, almacenamiento de datos de SQL Azure, Azure almacenamiento de Blobs y bases de datos local. Ser un ciudadano de primera clase en el ecosistema de Azure, generador de datos de Azure puede usarse para organizar la recopilación de datos de origen de estos al almacén de lago de datos de Azure.

Para obtener instrucciones sobre cómo usar el generador de datos de Azure con lago almacén de datos, vea [mover datos hacia y desde el almacén de lago de datos mediante el generador de datos](../data-factory/data-factory-azure-datalake-connector.md).

**¡Vídeos de nuevo!** Ver [Orquestación de datos con el generador de datos de Azure para lago almacén de datos de Azure](https://mix.office.com/watch/1oa7le7t2u4ka). 

## <a name="copy-data-from-azure-storage-blobs-into-data-lake-store"></a>Copie datos de Azure almacenamiento de Blobs en almacén de datos de lago

Almacén de lago de datos de Azure proporciona una herramienta de línea de comandos, AdlCopy, que permite copiar los datos desde el almacenamiento de blobs de Windows Azure en una cuenta de almacén de datos lago. Para obtener más información, vea [Copiar datos de Azure almacenamiento de Blobs al almacén de datos lago](data-lake-store-copy-data-azure-storage-blob.md).

## <a name="copy-data-between-azure-sql-database-and-data-lake-store"></a>Copiar datos entre la base de datos de SQL Azure y el almacenamiento de lago de datos

Puede usar Apache Sqoop para importar y exportar datos entre la base de datos de SQL Azure y el almacenamiento de lago de datos. Para obtener más información, consulte [Copiar datos entre la tienda de lago datos y la base de datos de SQL Azure con Sqoop](data-lake-store-data-transfer-sql-sqoop.md).

**Vea este vídeo** en [Utilizando Apache Sqoop para mover datos entre orígenes relacionales y el almacenamiento de lago de datos de Azure](https://mix.office.com/watch/1butcdjxmu114).

## <a name="use-data-lake-store-with-stream-analytics"></a>Utilizar el almacén de lago de datos con el análisis de secuencia

Puede usar el almacén de datos de lago como uno de los resultados para almacenar los datos que se transmite mediante el análisis de secuencia de Azure. Para obtener más información, vea [datos de secuencia de blobs de Windows Azure almacenamiento en almacén de lago de datos mediante el análisis de secuencia de Azure](data-lake-store-stream-analytics.md).

## <a name="use-data-lake-store-with-power-bi"></a>Utilizar el almacén de lago de datos con Power BI

Puede usar Power BI para importar datos desde una cuenta de almacén de datos lago para analizar y visualizar los datos. Para obtener más información, vea [analizar datos almacén de datos lago con Power BI](data-lake-store-power-bi.md).

## <a name="use-data-lake-store-with-data-catalog"></a>Utilizar el almacén de lago de datos con el catálogo de datos

Puede registrar datos del almacén de datos lago en el catálogo de datos de Azure para que los datos reconocible en toda la organización. Para obtener más información, vea [registrar datos del almacén de datos lago en catálogo de datos de Azure](data-lake-store-with-data-catalog.md).


## <a name="see-also"></a>Vea también

- [Introducción a Azure lago de almacén de datos](data-lake-store-overview.md)
- [Empezar a trabajar con el almacén de datos lago con Portal](data-lake-store-get-started-portal.md)
- [Empezar a trabajar con el almacén de datos lago con PowerShell](data-lake-store-get-started-powershell.md)  
