
<properties
   pageTitle="Opciones de R Server Azure almacenamiento en HDInsight (vista previa) | Microsoft Azure"
   description="Obtenga más información sobre las opciones de almacenamiento diferentes disponibles para los usuarios con el servidor de R de HDInsight (vista previa)"
   services="HDInsight"
   documentationCenter=""
   authors="jeffstokes72"
   manager="jhubbard"
   editor="cgronlun"
/>

<tags
   ms.service="HDInsight"
   ms.devlang="R"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-services"
   ms.date="09/01/2016"
   ms.author="jeffstok"
/>

# <a name="azure-storage-options-for-r-server-on-hdinsight-preview"></a>Opciones de R Server Azure almacenamiento en HDInsight (vista previa)

Microsoft R Server en HDInsight (vista preliminar) tiene acceso a ambos blobs de Windows Azure y [Azure almacenamiento de lago de datos](https://azure.microsoft.com/services/data-lake-store/), como un medio de almacenar datos, código, resultado objetos de análisis y así sucesivamente.

Cuando se crea un clúster de Hadoop en HDInsight, especifique una cuenta de almacenamiento de Azure. Un contenedor de almacenamiento de blobs específico desde esa cuenta contiene el sistema de archivos para el clúster que crear (por ejemplo, el sistema de archivos distribuido Hadoop). Para mejorar el rendimiento, se crea el clúster de HDInsight en el centro de datos de la misma que la cuenta de almacenamiento principal que especifique. Para obtener más información, vea [almacenamiento de blobs de Azure uso con HDInsight de](hdinsight-hadoop-use-blob-storage.md "almacenamiento de blobs de Azure uso con HDInsight").   


## <a name="use-multiple-azure-blob-storage-accounts"></a>Utilizar varias cuentas de almacenamiento de blobs de Windows Azure

Si es necesario, puede tener acceso a varias cuentas de Azure almacenamiento o contenedores con el clúster HDI. Para ello, debe especificar las cuentas de almacenamiento adicional en la interfaz de usuario cuando se crea el clúster y, a continuación, siga estos pasos para usarlos en R.  

1.  Crear un clúster de HDInsight con un nombre de cuenta de almacenamiento de **: storage1** y un contenedor predeterminado denominado **container1**.
2. Especifique una cuenta de almacenamiento adicional denominada **: storage2**.  
3. Copie el archivo mycsv.csv en el directorio de /share y realizar análisis en el archivo.  

    ````
    hadoop fs –mkdir /share
    hadoop fs –copyFromLocal myscsv.scv /share  
    ````

3.  En el código de R, establezca el nodo name a **predeterminada** y los directorios y archivos para procesar.  

    ````
    myNameNode <- "default"
    myPort <- 0
    ````

  Ubicación de los datos:  

    bigDataDirRoot <-"/ compartir"  

  Definir el contexto de cálculo de motor:

    mySparkCluster <-RxSpark(consoleOutput=TRUE)

  Definir el contexto de cálculo:

    rxSetComputeContext(mySparkCluster)

  Definir el sistema de archivos de sistema de archivos distribuido Hadoop (HDFS):

    hdfsFS <-RxHdfsFileSystem (hostName = myNameNode, puerto = myPort)

  Especifique el archivo de entrada para analizar en HDFS:

    archivoDeEntrada <-file.path(bigDataDirRoot,"mycsv.csv")

Todas las referencias de directorio y archivo apunten a la cuenta de almacenamiento wasbs://container1@storage1.blob.core.windows.net. Esta es la **cuenta de almacenamiento predeterminada** que está asociada con el clúster de HDInsight.

Ahora, supongamos que desean procesar un archivo llamado mySpecial.csv que se encuentra en la /private directorio de **container2** en **: storage2**.

En el código R, seleccione la referencia de nodo de nombre para la cuenta de almacenamiento **: storage2** .

    myNameNode <- "wasbs://container2@storage2.blob.core.windows.net"
    myPort <- 0

  Ubicación de los datos:

    bigDataDirRoot <- "/private"

  Definir el contexto de cálculo de motor:

    mySparkCluster <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort)

  Definir el contexto de cálculo:

    rxSetComputeContext(mySparkCluster)

  Definir el sistema de archivos HDFS:

    hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

  Especifique el archivo de entrada para analizar en HDFS:

    inputFile <-file.path(bigDataDirRoot,"mySpecial.csv")

Todas las referencias de directorios y archivos ahora seleccione la cuenta de almacenamiento wasbs://container2@storage2.blob.core.windows.net. Este es el **Nombre de nodo** que especificó.

Nota que tendrá que configurar/User/RevoShare/<SSH username> directorio **: storage2** como sigue:

    hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user
    hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user/RevoShare
    hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user/RevoShare/<RDP username>

## <a name="use-an-azure-data-lake-store"></a>Usar un almacén de lago de datos de Azure

Para usar datos lago almacena con su cuenta HDInsight, debe conceder el acceso de clúster a cada almacén de lago de datos de Azure que desea usar. Usar el almacén de la secuencia de comandos de R mucho que usa una cuenta de almacenamiento secundaria (como se describe en el procedimiento anterior).

## <a name="add-cluster-access-to-your-azure-data-lake-stores"></a>Agregar acceso de clúster a su stores lago de datos de Azure

Obtener acceso a un almacén de datos lago mediante el uso de una entidad de seguridad de servicio de Azure Active Directory (AD Azure) que está asociada a su clúster HDInsight.

### <a name="to-add-a-service-principal"></a>Para agregar una entidad de seguridad de servicio
1. Cuando se crea el clúster HDInsight, seleccione **Clúster AAD identidad** de la ficha **Origen de datos** .
2. En el cuadro de diálogo **Clúster AAD identidad** , en **Seleccione AD servicio Principal**, seleccione **Crear nuevo**.

Después de asignar un nombre de la entidad de seguridad de servicio y crear una contraseña para el mismo, se abrirá una nueva pestaña donde puede asociar la entidad de seguridad de servicio con su almacena datos lago.

Nota que también puede agregar acceso a un almacén de datos lago más adelante para abrir el almacén de datos lago en el portal de Azure y vaya al **Explorador de datos** > **acceso**.  A continuación se muestra un ejemplo de un cuadro de diálogo que muestra cómo crear una entidad de seguridad de servicio y asociarlo con el almacén de datos lago "rkadl11".

![Crear almacén de datos lago Service principio 1](./media/hdinsight-hadoop-r-server-storage/hdinsight-hadoop-r-server-storage-adls-sp1.png)


![Crear almacén de datos lago Service principio 2](./media/hdinsight-hadoop-r-server-storage/hdinsight-hadoop-r-server-storage-adls-sp2.png)

## <a name="use-the-data-lake-store-with-r-server"></a>Usar el almacén de datos lago con servidor R
Una vez que haya dado acceso a un almacén de datos lago, puede usar el almacén de servidor R en HDInsight de la manera que lo haría con una cuenta de almacenamiento de Azure secundario. La única diferencia es que el prefijo **wasb: / /** cambia a **adl: / /** como sigue:

````
# Point to the ADL store (e.g. ADLtest)
myNameNode <- "adl://rkadl1.azuredatalakestore.net"
myPort <- 0

# Location of the data (assumes a /share directory on the ADL account)
bigDataDirRoot <- "/share"  

# Define Spark compute context
mySparkCluster <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort)

# Set compute context
rxSetComputeContext(mySparkCluster)

# Define HDFS file system
hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

# Specify the input file in HDFS to analyze
inputFile <-file.path(bigDataDirRoot,"AirlineDemoSmall.csv")

# Create factors for days of the week
colInfo <- list(DayOfWeek = list(type = "factor",
               levels = c("Monday", "Tuesday", "Wednesday", "Thursday",
                          "Friday", "Saturday", "Sunday")))

# Define the data source
airDS <- RxTextData(file = inputFile, missingValueString = "M",
                    colInfo  = colInfo, fileSystem = hdfsFS)

# Run a linear regression
model <- rxLinMod(ArrDelay~CRSDepTime+DayOfWeek, data = airDS)
````

Siguientes son los comandos que se usan para configurar la cuenta de almacenamiento de datos lago con el directorio RevoShare y agregue el archivo .csv de ejemplo del ejemplo anterior:

````
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare/<user>

hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/share

hadoop fs -copyFromLocal /usr/lib64/R Server-7.4.1/library/RevoScaleR/SampleData/AirlineDemoSmall.csv adl://rkadl1.azuredatalakestore.net/share

hadoop fs –ls adl://rkadl1.azuredatalakestore.net/share
````

## <a name="use-azure-files-on-the-edge-node"></a>Usar archivos de Azure en el nodo del borde

También hay una opción de almacenamiento de datos adecuado para su uso en el nodo del borde denominado [Archivos de Azure](../storage/storage-how-to-use-files-linux.md "Archivos de Azure"). Le permite montaje de un recurso compartido de archivos de almacenamiento de Azure en el sistema de archivos de Linux. Esto puede ser útil para almacenar archivos de datos, R secuencias de comandos y los objetos de resultados que podrían ser necesarias más adelante cuando tiene sentido usar el sistema de archivos nativo en el nodo del borde en lugar de HDFS.

Ventajas principales de los archivos de Azure es que los recursos compartidos de archivos pueden ser montaje y usados por cualquier sistema que tenga un sistema operativo compatible, como Windows o Linux. Por ejemplo, puede usar otro clúster de HDInsight con usted o alguien de su equipo, por una máquina virtual de Azure o incluso un sistema local.


## <a name="next-steps"></a>Pasos siguientes

Ahora que conoce los conceptos básicos de cómo usar la consola de R desde una sesión SSH y cómo crear un nuevo clúster de HDInsight que incluye R Server, use los siguientes vínculos para descubrir otras maneras de trabajar con el servidor de R en HDInsight.

- [Información general del servidor R HDInsight](hdinsight-hadoop-r-server-overview.md)
- [Introducción a servidor R Hadoop](hdinsight-hadoop-r-server-get-started.md)
- [Agregar servidor de RStudio a HDInsight premium](hdinsight-hadoop-r-server-install-r-studio.md)
- [Calcular las opciones de contexto de servidor R en HDInsight](hdinsight-hadoop-r-server-compute-contexts.md)
