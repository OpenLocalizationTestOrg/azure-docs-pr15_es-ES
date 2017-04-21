<properties
    pageTitle="La consulta de datos de almacenamiento de blobs de HDFS compatible | Microsoft Azure"
    description="HDInsight usa almacenamiento de blobs de Windows Azure como el almacén de datos grande para HDFS. Aprenda a consultar datos desde el almacenamiento de blobs y almacenar los resultados del análisis."
    keywords="BLOB almacenamiento, hdfs, datos estructurados, datos no estructurados"
    services="hdinsight,storage"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/06/2016"
    ms.author="jgao"/>


# <a name="use-hdfs-compatible-azure-blob-storage-with-hadoop-in-hdinsight"></a>Usar el almacenamiento de blobs de Azure HDFS compatible con Hadoop en HDInsight

Obtenga información sobre cómo usar el almacenamiento de blobs de Windows Azure bajo costo con HDInsight, crear cuenta de almacenamiento de Azure y contenedor de almacenamiento de blobs y, a continuación, los datos dentro de direcciones.

Almacenamiento de blobs de Windows Azure es una solución de almacenamiento sólido y general que se integra con HDInsight. Mediante una interfaz (HDFS) del sistema de archivos distribuido Hadoop, el conjunto completo de componentes de HDInsight puede trabajar directamente con los datos estructurados o en el almacenamiento de blobs de Windows.

Almacenar los datos en el almacenamiento de blobs le permite eliminar de forma segura los clústeres HDInsight que se usan para el cálculo sin perder los datos de usuario.

> [AZURE.IMPORTANT] HDInsight sólo admite BLOB de bloque. No admite la página o anexar BLOB.

Para obtener información sobre la creación de un clúster de HDInsight, consulte [Introducción a HDInsight] [ hdinsight-get-started] o [HDInsight crear clústeres][hdinsight-creation].


## <a name="hdinsight-storage-architecture"></a>Arquitectura de almacenamiento de HDInsight
El siguiente diagrama proporciona una vista de la arquitectura de almacenamiento de HDInsight abstracta:

![Hadoop clústeres utilizan la API de HDFS para obtener acceso y almacenar datos estructurados y en el almacenamiento de blobs.] (./media/hdinsight-hadoop-use-blob-storage/HDI.WASB.Arch.png "Arquitectura de almacenamiento de HDInsight")

HDInsight proporciona acceso a un sistema de archivos distribuido que está conectado localmente a los nodos de cálculo. Puede tener acceso a este sistema de archivos utilizando el URI completo, por ejemplo:

    hdfs://<namenodehost>/<path>

Además, HDInsight proporciona la capacidad de datos de access que se almacenan en el almacenamiento de blobs de Windows Azure. La sintaxis es:

    wasb[s]://<containername>@<accountname>.blob.core.windows.net/<path>

> [AZURE.NOTE] En versiones anteriores a 3.0, de HDInsight `asv://` se ha usado en lugar de `wasb://`. `asv://`no se recomienda con HDInsight clústeres 3.0 o posterior, tal como producirá un error.

Hadoop es compatible con la información del sistema de archivos de forma predeterminada. El sistema de archivos de forma predeterminada implica una combinación predeterminada y entidad emisora. También puede usarse para resolver rutas de acceso relativas. Durante el proceso de creación de HDInsight, una cuenta de almacenamiento de Azure y un almacenamiento de blobs de Windows Azure específico contenedor desde esa cuenta está designado como el sistema de archivos de forma predeterminada.

Además de esta cuenta de almacenamiento, puede agregar cuentas de almacenamiento adicional de la misma suscripción Azure o varias suscripciones a Azure durante el proceso de creación o después de haber creado un clúster. Para obtener instrucciones sobre cómo agregar cuentas de almacenamiento adicional, vea [crear HDInsight clústeres][hdinsight-creation].

- **Contenedores en las cuentas de almacenamiento que están conectados a un clúster:** Porque el nombre de la cuenta y la clave están asociados con el clúster durante la creación, tiene acceso completo a los BLOB en esos contenedores.

- **Contenedores públicos o público BLOB en cuentas de almacenamiento que no están conectadas a un clúster:** Tener permiso de solo lectura a los BLOB en los contenedores.

    > [AZURE.NOTE]
        > Contenedores públicos permiten obtener una lista de todos los blobs que están disponibles en ese contenedor y obtener metadatos de contenedor. BLOB público le permite acceder a los BLOB solo si conoce la dirección URL exacta. Para obtener más información, vea <a href="http://msdn.microsoft.com/library/windowsazure/dd179354.aspx">restringir el acceso a los contenedores y BLOB</a>.

- **Privados contenedores en cuentas de almacenamiento que no están conectados a un clúster:** No puede tener acceso a los BLOB en los contenedores a menos que defina la cuenta de almacenamiento, al enviar trabajos de WebHCat. Esto se explica más adelante en este artículo.


Las cuentas de almacenamiento que se definen en el proceso de creación y sus claves se almacenan en %HADOOP_HOME%/conf/core-site.xml en los nodos de clúster. El comportamiento predeterminado de HDInsight es utilizar las cuentas de almacenamiento definidas en el archivo core site.xml. No se recomienda para editar el archivo de núcleo site.xml porque el node(master) central de clúster puede ser renovado o migrado en cualquier momento y se perderán los cambios a los archivos.

Varias tareas de WebHCat, incluidos subárbol, MapReduce, Hadoop streaming y cerdo, pueden llevar a cabo una descripción de las cuentas de almacenamiento y metadatos con ellos. (Esto actualmente funciona para cerdo con cuentas de almacenamiento, pero no para los metadatos.) En la sección de [blobs de Access con Azure PowerShell](#powershell) de este artículo, hay un ejemplo de esta característica. Para obtener más información, vea [usar un HDInsight clúster con cuentas de almacenamiento alternativo y Metastores](http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx).

Almacenamiento de blobs de puede usarse para datos estructurados y. Contenedores de almacenamiento de blobs almacenan datos como pares de clave y valor, y no hay ninguna jerarquía de directorio. Sin embargo puede utilizarse el carácter de barra diagonal (/) en el nombre de la clave para que aparezca como si un archivo se almacena en una estructura de directorios. Por ejemplo, la clave de un blob puede ser *input/log1.txt*. Ningún directorio real *entrada* existe, pero debido a la presencia del carácter de barra diagonal en el nombre de la clave, tiene el aspecto de una ruta de acceso del archivo.

###<a id="benefits"></a>Ventajas de almacenamiento de blobs
El costo de no situación rendimiento implícitas clústeres de cálculo y se soluciona recursos de almacenamiento a la forma en que se crean los clústeres de cálculo cerca de la cuenta de recursos de almacenamiento dentro de la región de Azure, donde la red de alta velocidad hace muy eficaces para los nodos de cálculo tener acceso a los datos de almacenamiento de blobs de Windows Azure.

Hay varias ventajas asociadas a almacenar los datos en el almacenamiento de blobs de Windows Azure en lugar de HDFS:

* **Compartir y reutilizar los datos:** Los datos de HDFS se encuentran dentro del clúster de cálculo. Solo las aplicaciones que tienen acceso al clúster de cálculo pueden usar los datos mediante el uso de las API de HDFS. Se pueden acceder a los datos en el almacenamiento de blobs de Windows Azure a través de las API de HDFS o las [API de REST de almacenamiento de blobs][blob-storage-restAPI]. Por lo tanto, un conjunto de herramientas y aplicaciones (incluidos otros clústeres HDInsight) mayor puede utilizarse para generar y consumir los datos.
* **Archivado de datos:** Almacenar los datos en el almacenamiento de blobs de Windows Azure permite a los clústeres HDInsight utilizados para el cálculo eliminarse sin perder los datos de usuario.
* **Costo de almacenamiento de datos:** Almacenar datos en DFS a largo plazo es mayores que almacenar los datos en el almacenamiento de blobs de Windows Azure, porque el coste de un clúster de cálculo es mayor que el coste de un contenedor de almacenamiento de blobs de Windows Azure. Además, porque los datos no tiene que volver a cargar para la generación de cada clúster de cálculo, se están guardando los costos de carga de datos.
* **Elástico escalado:** Aunque HDFS le proporciona un sistema de archivos de salida de la escala, la escala se determina el número de nodos que se crean para el clúster. Cambiar la escala puede convertirse en un proceso más complicado que depender la elástica escala capacidades que recibe automáticamente en el almacenamiento de blobs de Windows Azure.
* **Replicación Geo:** Los contenedores de almacenamiento de blobs de Windows Azure pueden replicar geo. Aunque esta opción permite recuperación geográfica y redundancia de datos, una migración tras error a la ubicación geográfica replicada afecta gravemente el rendimiento y pueden contraer costos adicionales. Por lo que es elegir la replicación de geo sabiamente nuestra recomendación y solo si el valor de los datos vale más que el costo adicional.

Determinados paquetes y trabajos MapReduce pueden crear resultados intermedios que realmente no desea almacenar en el almacenamiento de blobs de Windows Azure. En ese caso, puede optar por almacenar los datos en la HDFS local. De hecho, HDInsight usa DFS de varios de estos resultados intermedios en la sección trabajos y otros procesos.

> [AZURE.NOTE] La mayoría de los comandos HDFS (por ejemplo, <b>ls</b>, <b>copyFromLocal</b> y <b>mkdir</b>) seguirán funcionan según lo esperado. Solo los comandos específicos a la HDFS implementación nativa (que se conoce como DFS), como <b>fschk</b> y <b>dfsadmin</b>, que se mostrará un comportamiento diferente en el almacenamiento de blobs de Windows Azure.

## <a name="create-blob-containers"></a>Crear contenedores de Blob

Para usar BLOB, crea una [cuenta de almacenamiento de Azure][azure-storage-create]. Como parte de esto, especifique un área de Azure que almacena los objetos que se crean con esta cuenta. El clúster y la cuenta de almacenamiento deben estar hospedadas en la misma región. La base de datos de SQL Server de subárbol metastore y la base de datos de SQL Server de Oozie metastore también se encuentra en la misma región.

Donde vive, pertenece cada blob que crear un contenedor de la cuenta de almacenamiento de Azure. Este contenedor puede ser un blob existente que se creó fuera HDInsight, o puede ser un contenedor en el que se crea un clúster de HDInsight.


El contenedor de Blob predeterminado almacena información específica de clúster como historial de trabajos y registros. No compartir un contenedor de Blob predeterminado con varios clústeres HDInsight. Esto podría dañar el historial de trabajos y, a continuación, el clúster se alojarla. Se recomienda usar un contenedor diferente para cada clúster y colocar datos compartidos en una cuenta de almacenamiento vinculado especificada en la implementación de todos los clústeres relevantes en lugar de la cuenta de almacenamiento predeterminada. Para obtener más información sobre cómo configurar cuentas de almacenamiento vinculado, consulte [crear HDInsight clústeres][hdinsight-creation]. Sin embargo puede volver a usar un contenedor de almacenamiento predeterminada después de que se ha eliminado el clúster HDInsight original. Para clústeres HBase, realmente puede conservar el esquema de la tabla HBase y datos por creación un nuevo clúster de HBase con el contenedor de almacenamiento de blobs de predeterminada que se usa en un clúster de HBase que se ha eliminado.


### <a name="using-the-azure-portal"></a>Con el Portal de Azure

Al crear un clúster HDInsight desde el Portal, tendrá las opciones para usar una cuenta de almacenamiento existente o crear una nueva cuenta de almacenamiento:

![origen de datos de creación de hdinsight hadoop](./media/hdinsight-hadoop-use-blob-storage/hdinsight.provision.data.source.png)

###<a name="using-azure-cli"></a>Utilice CLI Azure

[AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

Si tiene [instalado y configurado CLI Azure](../xplat-cli-install.md), el siguiente comando puede usarse para una cuenta de almacenamiento y el contenedor.

    azure storage account create <storageaccountname> --type LRS

> [AZURE.NOTE] La `--type` parámetro indica cómo se van a replicar la cuenta de almacenamiento. Para obtener más información, vea [Replicación de almacenamiento de Azure](../storage/storage-redundancy.md). No use ZRS como ZRS no admite blob de página, archivo, tabla o cola.

Se le pedirá para especificar la región geográfica que estará ubicada en la cuenta de almacenamiento. Debe crear la cuenta de almacenamiento en la misma región que va a crear el clúster de HDInsight.

Una vez creada la cuenta de almacenamiento, use el comando siguiente para recuperar las claves de la cuenta de almacenamiento:

    azure storage account keys list <storageaccountname>

Para crear un contenedor, utilice el siguiente comando:

    azure storage container create <containername> --account-name <storageaccountname> --account-key <storageaccountkey>

### <a name="using-azure-powershell"></a>Uso de PowerShell de Azure

Si usted [instalado y configurado Azure PowerShell][powershell-install], puede usar el siguiente desde el símbolo del sistema de PowerShell de Azure para crear una cuenta de almacenamiento y el contenedor:

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    $SubscriptionID = "<Your Azure Subscription ID>"
    $ResourceGroupName = "<New Azure Resource Group Name>"
    $Location = "EAST US 2"
    
    $StorageAccountName = "<New Azure Storage Account Name>"
    $containerName = "<New Azure Blob Container Name>"
    
    Add-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionId $SubscriptionID
    
    # Create resource group
    New-AzureRmResourceGroup -name $ResourceGroupName -Location $Location
    
    # Create default storage account
    New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -Location $Location -Type Standard_LRS 
    
    # Create default blob containers
    $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -StorageAccountName $StorageAccountName)[0].Value
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
    New-AzureStorageContainer -Name $containerName -Context $destContext

## <a name="address-files-in-blob-storage"></a>Archivos de direcciones en el almacenamiento de blobs

El esquema URI para tener acceso a archivos de almacenamiento de blobs de HDInsight es:

    wasb[s]://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>


El esquema de URI proporciona acceso sin cifrar (con el *wasb:* prefijo) y SSL cifrado acceso (con *wasbs*). Se recomienda usar *wasbs* siempre que sea posible, incluso cuando el acceso a los datos que se encuentra dentro de la misma región en Azure.

La &lt;BlobStorageContainerName&gt; identifica el nombre del contenedor de almacenamiento de blobs de Windows Azure.
La &lt;StorageAccountName&gt; identifica el nombre de la cuenta de almacenamiento de Azure. Se requiere un nombre de dominio completo (FQDN).

Si no &lt;BlobStorageContainerName&gt; ni &lt;StorageAccountName&gt; se ha especificado, se usa el sistema de archivos de forma predeterminada. Los archivos en el sistema de archivos de forma predeterminada, puede usar una ruta de acceso relativa o absoluta. Por ejemplo, el archivo de *hadoop-mapreduce-examples.jar* que se suministra con HDInsight clústeres puede hacer referencia a usando uno de estos procedimientos:

    wasbs://mycontainer@myaccount.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
    wasbs:///example/jars/hadoop-mapreduce-examples.jar
    /example/jars/hadoop-mapreduce-examples.jar

> [AZURE.NOTE] El nombre de archivo es <i>hadoop examples.jar</i> en clústeres de versiones 2.1 y 1,6 HDInsight.


La &lt;ruta&gt; es el nombre de ruta de acceso HDFS archivo o directorio. Debido a los contenedores de almacenamiento de blobs de Windows Azure son simplemente clave valor stores, no hay ningún sistema de archivos jerárquico true. Un carácter de barra diagonal (/) dentro de una clave de blob se interpreta como un separador de directorio. Por ejemplo, el nombre de blobs para *mapreduce de hadoop de examples.jar* es:

    example/jars/hadoop-mapreduce-examples.jar

> [AZURE.NOTE] Al trabajar con BLOB fuera HDInsight, la mayoría de utilidades no reconoce el formato WASB y espera en su lugar que un formato básico de la ruta de acceso, como por ejemplo `example/jars/hadoop-mapreduce-examples.jar`.

## <a name="access-blobs-using-azure-cli"></a>BLOB de Access mediante CLI de Azure

Use el comando siguiente para mostrar los comandos relacionados con blob:

    azure storage blob

**Ejemplo de uso de Azure CLI para cargar un archivo**

    azure storage blob upload <sourcefilename> <containername> <blobname> --account-name <storageaccountname> --account-key <storageaccountkey>

**Ejemplo de uso de Azure CLI para descargar un archivo**

    azure storage blob download <containername> <blobname> <destinationfilename> --account-name <storageaccountname> --account-key <storageaccountkey>

**Ejemplo de uso de Azure CLI para eliminar un archivo**

    azure storage blob delete <containername> <blobname> --account-name <storageaccountname> --account-key <storageaccountkey>

**Ejemplo de uso de Azure CLI a archivos de la lista**

    azure storage blob list <containername> <blobname|prefix> --account-name <storageaccountname> --account-key <storageaccountkey>

## <a name="access-blobs-using-azure-powershell"></a>BLOB de Access con PowerShell de Azure

> [AZURE.NOTE] Los comandos de esta sección proporcionan un ejemplo de uso de PowerShell para obtener acceso a los datos almacenados en BLOB básico. Para obtener un ejemplo más completo que se personaliza para trabajar con HDInsight, consulte las [Herramientas de HDInsight](https://github.com/Blackmist/hdinsight-tools).

Use el comando siguiente para mostrar los cmdlets de blobs de Windows:

    Get-Command *blob*

![Lista de los cmdlets de PowerShell relacionados con blob.][img-hdi-powershell-blobcommands]

###<a name="upload-files"></a>Cargar archivos

Vea [cargar datos a HDInsight][hdinsight-upload-data].

###<a name="download-files"></a>Descargar archivos

El siguiente script descarga un blob bloque a la carpeta actual. Antes de ejecutar la secuencia de comandos, cambie el directorio a una carpeta que tenga permisos de escritura.

    $resourceGroupName = "<AzureResourceGroupName>"
    $storageAccountName = "<AzureStorageAccountName>"   # The storage account used for the default file system specified at creation.
    $containerName = "<BlobStorageContainerName>"  # The default file system container has the same name as the cluster.
    $blob = "example/data/sample.log" # The name of the blob to be downloaded.
    
    # Use Add-AzureAccount if you haven't connected to your Azure subscription
    Login-AzureRmAccount 
    Select-AzureRmSubscription -SubscriptionID "<Your Azure Subscription ID>"
    
    Write-Host "Create a context object ... " -ForegroundColor Green
    $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName)[0].Value
    $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
    
    Write-Host "Download the blob ..." -ForegroundColor Green
    Get-AzureStorageBlobContent -Container $ContainerName -Blob $blob -Context $storageContext -Force
    
    Write-Host "List the downloaded file ..." -ForegroundColor Green
    cat "./$blob"

Proporcionar el nombre del grupo de recursos y el nombre del clúster, puede usar el siguiente código:

    $resourceGroupName = "<AzureResourceGroupName>"
    $clusterName = "<HDInsightClusterName>"
    $blob = "example/data/sample.log" # The name of the blob to be downloaded.
    
    $cluster = Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName
    $defaultStorageAccount = $cluster.DefaultStorageAccount -replace '.blob.core.windows.net'
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccount)[0].Value
    $defaultStorageContainer = $cluster.DefaultStorageContainer
    $storageContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccount -StorageAccountKey $defaultStorageAccountKey 
    
    Write-Host "Download the blob ..." -ForegroundColor Green
    Get-AzureStorageBlobContent -Container $defaultStorageContainer -Blob $blob -Context $storageContext -Force

###<a name="delete-files"></a>Eliminar archivos


    Remove-AzureStorageBlob -Container $containerName -Context $storageContext -blob $blob

###<a name="list-files"></a>Lista de archivos

    Get-AzureStorageBlob -Container $containerName -Context $storageContext -prefix "example/data/"

###<a name="run-hive-queries-using-an-undefined-storage-account"></a>Ejecutar consultas de sección con una cuenta de almacenamiento definido

Este ejemplo muestra cómo una carpeta de la cuenta de almacenamiento que no está definido durante el proceso de creación de la lista.
$clusterName = "<HDInsightClusterName>"

    $undefinedStorageAccount = "<UnboundedStorageAccountUnderTheSameSubscription>"
    $undefinedContainer = "<UnboundedBlobContainerAssociatedWithTheStorageAccount>"

    $undefinedStorageKey = Get-AzureStorageKey $undefinedStorageAccount | %{ $_.Primary }

    Use-AzureRmHDInsightCluster $clusterName

    $defines = @{}
    $defines.Add("fs.azure.account.key.$undefinedStorageAccount.blob.core.windows.net", $undefinedStorageKey)

    Invoke-AzureRmHDInsightHiveJob -Defines $defines -Query "dfs -ls wasbs://$undefinedContainer@$undefinedStorageAccount.blob.core.windows.net/;"

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido a usar el almacenamiento de blobs de Azure HDFS compatible con HDInsight y que ha aprendido que el almacenamiento de blobs de Windows Azure es un componente fundamental de HDInsight. Esto le permite crear soluciones de adquisición de datos scalable, archivado a largo plazo con el almacenamiento de blobs de Windows Azure y usa HDInsight para desbloquear la información contenida en los datos estructurados y almacenados.

Para obtener más información, consulte:

* [Introducción a HDInsight de Azure][hdinsight-get-started]
* [Cargar datos a HDInsight][hdinsight-upload-data]
* [Usar subárbol con HDInsight][hdinsight-use-hive]
* [Usar cerdo con HDInsight][hdinsight-use-pig]
* [Usar firmas de Azure almacenamiento compartido acceso para restringir el acceso a datos con HDInsight][hdinsight-use-sas]

[hdinsight-use-sas]: hdinsight-storage-sharedaccesssignature-permissions.md
[powershell-install]: ../powershell-install-configure.md
[hdinsight-creation]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-tutorial-get-started-windows.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[blob-storage-restAPI]: http://msdn.microsoft.com/library/windowsazure/dd135733.aspx
[azure-storage-create]: ../storage/storage-create-storage-account.md

[img-hdi-powershell-blobcommands]: ./media/hdinsight-hadoop-use-blob-storage/HDI.PowerShell.BlobCommands.png
[img-hdi-quick-create]: ./media/hdinsight-hadoop-use-blob-storage/HDI.QuickCreateCluster.png
[img-hdi-custom-create-storage-account]: ./media/hdinsight-hadoop-use-blob-storage/HDI.CustomCreateStorageAccount.png  
