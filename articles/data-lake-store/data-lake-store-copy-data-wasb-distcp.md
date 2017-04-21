<properties
   pageTitle="Copiar datos a y desde WASB en almacén de lago de datos mediante Distcp | Microsoft Azure"
   description="Use la herramienta Distcp para copiar datos a y desde Azure almacenamiento de Blobs al almacén de datos lago"
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
   ms.date="10/28/2016"
   ms.author="nitinme"/>

# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-data-lake-store"></a>Usar Distcp para copiar datos entre Azure almacenamiento de Blobs y almacén de datos de lago

> [AZURE.SELECTOR]
- [Usar DistCp](data-lake-store-copy-data-wasb-distcp.md)
- [Usar AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)


Una vez que haya creado un clúster de HDInsight que tiene acceso a una cuenta de la tienda de lago de datos, puede usar herramientas de ecosistema de Hadoop como Distcp para copiar datos **a y desde** un almacenamiento de clúster HDInsight (WASB) a una cuenta de almacén de datos lago. Este artículo ofrece instrucciones sobre cómo conseguirlo.

##<a name="prerequisites"></a>Requisitos previos

Antes de comenzar este artículo, debe tener el siguiente:

- **Suscripción de un Azure**. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/pricing/free-trial/).
- **Habilitar la suscripción Azure** de vista previa de datos lago almacén público. Vea [las instrucciones](data-lake-store-get-started-portal.md#signup).
- **Clúster de azure HDInsight** con acceso a una cuenta de almacén de datos lago. Consulte [crear un clúster de HDInsight con el almacén de datos de lago](data-lake-store-hdinsight-hadoop-use-portal.md). Asegúrese de que la opción Habilitar Escritorio remoto para el clúster.

## <a name="do-you-learn-fast-with-videos"></a>¿Puede obtener información rápidamente con vídeos?

[Vea este vídeo](https://mix.office.com/watch/1liuojvdx6sie) sobre cómo copiar datos entre Azure almacenamiento de Blobs y el almacenamiento de lago de datos usando DistCp.

## <a name="use-distcp-from-remote-desktop-windows-cluster-or-ssh-linux-cluster"></a>Usar Distcp de escritorio remoto (clúster de Windows) o SSH (Linux clúster)

Un clúster de HDInsight incluye la utilidad Distcp, que se puede utilizar para copiar datos de orígenes diferentes en un clúster de HDInsight. Si ha configurado el clúster HDInsight para usar el almacén de datos de lago como un almacenamiento adicional, la utilidad de Distcp puede ser usados-de-predefinidas para copiar datos a y desde una cuenta de almacén de datos lago también. En esta sección, veremos cómo usar la utilidad Distcp.

1. Si tiene un clúster de Windows, remoto en un clúster de HDInsight, que tiene acceso a una cuenta de almacén de datos lago. Para obtener instrucciones, vea [conectarse a clústeres mediante RDP](../hdinsight/hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp). En el clúster de escritorio, abra la línea de comandos de Hadoop.

    Si tiene un clúster de Linux, use SSH para conectarse al clúster. Consulte [conectarse a un clúster de HDInsight basados en Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster). Ejecute los comandos desde el símbolo de SSH.

3. Compruebe si puede tener acceso a la Azure almacenamiento de Blobs (WASB). Ejecute el siguiente comando:

        hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/

    Esto le ofrece una lista de contenido en el blob de almacenamiento.

4. Asimismo, compruebe si puede acceder a la cuenta de almacén de datos lago desde el clúster. Ejecute el siguiente comando:

        hdfs dfs -ls adl://<data_lake_store_account>.azuredatalakestore.net:443/

    Esto le ofrece una lista de archivos o carpetas en la cuenta de almacén de datos lago.

5. Use Distcp para copiar los datos de WASB a una cuenta de almacén de datos lago.

        hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder

    Esto copiará el contenido de la **** carpeta/ejemplo/datos/gutenberg/en WASB a **/myfolder** en la cuenta de almacén de datos lago.

6. Del mismo modo, utilice Distcp para copiar datos de la cuenta de almacén de datos lago a WASB.

        hadoop distcp adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg

    Esto copiará el contenido de **/myfolder** en la cuenta de almacén de datos lago **a/ejemplo/datos/gutenberg o** carpeta en WASB.

## <a name="see-also"></a>Vea también

- [Copiar datos de Azure almacenamiento de Blobs al almacén de datos lago](data-lake-store-copy-data-azure-storage-blob.md)
- [Proteger los datos en el almacén de datos de lago](data-lake-store-secure-data.md)
- [Usar el análisis de datos de Azure lago con el almacén de datos de lago](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Usar Azure HDInsight con el almacén de datos de lago](data-lake-store-hdinsight-hadoop-use-portal.md)
