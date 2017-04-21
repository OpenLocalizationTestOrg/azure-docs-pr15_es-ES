<properties
    pageTitle="Cargar datos para trabajos de Hadoop en HDInsight | Microsoft Azure"
    description="Obtenga información sobre cómo cargar y obtener acceso a datos para trabajos de Hadoop en HDInsight con CLI Azure, Explorador de almacenamiento de Azure, Azure PowerShell, la línea de comandos de Hadoop o Sqoop."
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
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="jgao"/>



#<a name="upload-data-for-hadoop-jobs-in-hdinsight"></a>Cargar datos para trabajos de Hadoop en HDInsight

HDInsight de Azure proporciona un completo distribuido sistema de archivos Hadoop (HDFS) sobre el almacenamiento de blobs de Windows Azure. Está diseñada como una extensión HDFS proporcionar una experiencia perfecta a sus clientes. Permite el conjunto completo de componentes del ecosistema Hadoop para que funcione directamente en los datos que administra. Almacenamiento de blobs de Azure y HDFS son los sistemas de archivo distintos que están optimizados para el almacenamiento de datos y cálculos con los datos. Para obtener información sobre las ventajas del uso de almacenamiento de blobs de Windows Azure, consulte [almacenamiento de blobs de Azure uso con HDInsight][hdinsight-storage].

**Requisitos previos**

Antes de empezar, tenga en cuenta los siguientes requisitos:

* Un clúster de HDInsight de Azure. Para obtener instrucciones, consulte [Introducción a Azure HDInsight] [ hdinsight-get-started] o [agrupaciones de aprovisionamiento HDInsight][hdinsight-provision].

##<a name="why-blob-storage"></a>¿Por qué almacenamiento de blobs?

Normalmente se implementan clústeres de Azure HDInsight para ejecutar MapReduce trabajos y, a continuación, se eliminan los clústeres después de completar estos trabajos. Mantener los datos en la HDFS clústeres tras completar la cálculos sería una forma muy costosa para almacenar los datos. Almacenamiento de blobs de Windows Azure es una alta disponibilidad, alta escalabilidad y alta capacidad, la opción de almacenamiento de bajo costo y que se pueden compartir para los datos que deben procesarse con HDInsight. Almacenar los datos en un blob permite a los clústeres HDInsight que se usan para el cálculo publicará sin perder los datos de forma segura.

###<a name="directories"></a>Directorios

Contenedores de almacenamiento de blobs de Windows Azure almacenan datos como pares de clave y valor, y no hay ninguna jerarquía de directorio. Sin embargo puede utilizarse el carácter "/" en el nombre de la clave para que aparezca como si un archivo se almacena en una estructura de directorios. HDInsight estos ve como si fueran directorios reales.

Por ejemplo, la clave de un blob puede ser *input/log1.txt*. No existe ningún directorio de "entrada" real, pero debido a la presencia del carácter "/" en el nombre de la clave, tiene el aspecto de una ruta de acceso del archivo.

Por este motivo, si utiliza el Explorador de Azure herramientas Observe algunos archivos de 0 bytes. Estos archivos utilizan con dos propósitos:

- Si hay carpetas vacías, que marcan la existencia de la carpeta. Almacenamiento de blobs de Windows Azure es lo suficientemente inteligente saber que, si existe un blob denominado foo/barra, hay una carpeta denominada **foo**. Pero es la única manera de indicar una carpeta vacía denominada **foo** haciendo que este archivo especial 0 bytes en su lugar.

- Contienen metadatos especiales que necesitan por el sistema de archivos Hadoop, en particular los permisos y los propietarios de las carpetas.

##<a name="command-line-utilities"></a>Utilidades de línea de comandos

Microsoft proporciona las siguientes utilidades para trabajar con el almacenamiento de blobs de Windows Azure:

| Herramienta | Linux | OS X | Windows |
| ---- |:-----:|:----:|:-------:|
| [Interfaz de línea de comandos de Azure][azurecli] | ✔ | ✔ | ✔ |
| [Azure PowerShell][azure-powershell] | | | ✔ |
| [AzCopy][azure-azcopy] | | | ✔ |
| [Comando Hadoop](#commandline) | ✔ | ✔ | ✔ |

> [AZURE.NOTE] Mientras el CLI de Azure y Azure PowerShell AzCopy todo pueden utilizarse de Azure exterior, el Hadoop comando solo está disponible en el clúster HDInsight y sólo permite cargar datos desde el sistema de archivos locales en el almacenamiento de blobs de Windows Azure.

###<a id="xplatcli"></a>CLI de Azure

CLI Azure es una herramienta de varias plataformas que le permite administrar servicios de Azure. Realice los siguientes pasos para cargar los datos en el almacenamiento de blobs de Windows Azure:

[AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

1. [Instalar y configurar la CLI Azure para Mac, Linux y Windows](../xplat-cli-install.md).

2. Abra un símbolo del sistema, fiesta u otro shell y use las siguientes acciones para autenticar a su suscripción de Azure.

        azure login

    Cuando se le pida, escriba el nombre de usuario y la contraseña para la suscripción.

3. Escriba el comando siguiente para las cuentas de almacenamiento para la suscripción de la lista:

        azure storage account list

4. Seleccione la cuenta de almacenamiento que contiene el blob que desea trabajar y luego use el comando siguiente para recuperar la clave para esta cuenta:

        azure storage account keys list <storage-account-name>

    Debe devolver claves **principales** y **secundarias** . Copie el valor de clave **principal** ya se utilizará en los pasos siguientes.

5. Use el comando siguiente para recuperar una lista de contenedores de blob dentro de la cuenta de almacenamiento:

        azure storage container list -a <storage-account-name> -k <primary-key>

6. Cargar y descargar archivos en el blob, utilice los comandos siguientes:

    * Para cargar un archivo:

            azure storage blob upload -a <storage-account-name> -k <primary-key> <source-file> <container-name> <blob-name>

    * Para descargar un archivo:

            azure storage blob download -a <storage-account-name> -k <primary-key> <container-name> <blob-name> <destination-file>

> [AZURE.NOTE] Si siempre va a trabajar con la misma cuenta de almacenamiento, puede establecer las siguientes variables de entorno en lugar de especificar la cuenta y clave para cada comando:
>
> * **AZURE\_almacenamiento\_cuenta**: el nombre de la cuenta de almacenamiento
>
> * **AZURE\_almacenamiento\_ACCESS\_clave**: la clave de cuenta de almacenamiento

###<a id="powershell"></a>Azure PowerShell

PowerShell Azure es un entorno de secuencias de comandos que puede usar para controlar y automatizar la implementación y administración de las cargas de trabajo en Azure. Para obtener información acerca de cómo configurar su estación de trabajo para ejecutar Azure PowerShell, vea [instalar y configurar Azure PowerShell](../powershell-install-configure.md).

[AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell.md)]

**Para cargar un archivo local con el almacenamiento de blobs de Windows Azure**

1. Abra la consola de Azure PowerShell como se indica en [instalar y configurar Azure PowerShell](../powershell-install-configure.md).
2. Establecer los valores de las cinco primeros variables en la siguiente secuencia de comandos:

        $resourceGroupName = "<AzureResourceGroupName>"
        $storageAccountName = "<StorageAccountName>"
        $containerName = "<ContainerName>"

        $fileName ="<LocalFileName>"
        $blobName = "<BlobName>"

        # Get the storage account key
        $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName)[0].Value
        # Create the storage context object
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

        # Copy the file from local workstation to the Blob container
        Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob $blobName -context $destContext

3. Pegue la secuencia de comandos en la consola de Azure PowerShell para ejecutarlo para copiar el archivo.

Por ejemplo los scripts de PowerShell que creó para trabajar con HDInsight, consulte [HDInsight herramientas](https://github.com/blackmist/hdinsight-tools).

###<a id="azcopy"></a>AzCopy

AzCopy es una herramienta de línea de comandos que está diseñada para simplificar la tarea de transferencia de datos dentro y fuera de una cuenta de almacenamiento de Azure. Puede usar como una herramienta independiente o incorporar esta herramienta en una aplicación existente. [Descargar AzCopy][azure-azcopy-download].

La sintaxis de AzCopy es:

    AzCopy <Source> <Destination> [filePattern [filePattern...]] [Options]

Para obtener más información, vea [AzCopy - cargar o descargar archivos para Blobs de Azure][azure-azcopy].


###<a id="commandline"></a>Línea de comandos de Hadoop

La línea de comandos de Hadoop solo es útil para almacenar los datos en el almacenamiento de blobs cuando los datos ya están presentes en el nodo de cabeza.

Para utilizar el comando Hadoop, debe conectarse primero a la headnode usando uno de los métodos siguientes:

* **HDInsight de Windows**: [Conectar utilizando Escritorio remoto](hdinsight-administer-use-management-portal.md#connect-to-hdinsight-clusters-by-using-rdp)

* **HDInsight de Linux**: conectar utilizando SSH ([el comando SSH](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster) o [PuTTY](hdinsight-hadoop-linux-use-ssh-windows.md#connect-to-a-linux-based-hdinsight-cluster))

Una vez conectado, puede usar la siguiente sintaxis para cargar un archivo al almacenamiento.

    hadoop -copyFromLocal <localFilePath> <storageFilePath>

Por ejemplo,`hadoop fs -copyFromLocal data.txt /example/data/data.txt`

Dado que es el sistema de archivo predeterminado para HDInsight de almacenamiento de blobs de Windows Azure, /example/data.txt es realmente en el almacenamiento de blobs de Windows Azure. También puede consultar el archivo como:

    wasbs:///example/data/data.txt

o

    wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

Para obtener una lista de comandos Hadoop trabajar con archivos, consulte [http://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html](http://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html)

> [AZURE.WARNING] En clústeres de HBase, el valor predeterminado Bloquear tamaño utilizado al escribir datos es 256KB. Mientras funciona bien cuando se utiliza APIs HBase o las API de REST, usando la `hadoop` o `hdfs dfs` comandos para escribir datos mayores de 12 ~ GB genera un error. Vea la sección [excepciones de almacenamiento de escritura en blob](#storageexception) a continuación para obtener más información.

##<a name="graphical-clients"></a>Clientes gráficos

También hay varias aplicaciones que proporcionan una interfaz gráfica para trabajar con el almacenamiento de Azure. A continuación se muestra una lista de algunas de estas aplicaciones:

| Cliente | Linux | OS X | Windows |
| ------ |:-----:|:----:|:-------:|
| [Microsoft Visual Studio Tools para HDInsight](hdinsight-hadoop-visual-studio-tools-get-started.md#navigate-the-linked-resources) | ✔ | ✔ | ✔ |
| [Explorador de almacenamiento de Azure](http://storageexplorer.com/) | ✔ | ✔ | ✔ |
| [Almacenamiento de nube Studio 2](http://www.cerebrata.com/Products/CloudStorageStudio/) | | | ✔ |
| [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer) | | | ✔ |
| [Explorador de Azure](http://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | | ✔ |
| [Cyberduck](https://cyberduck.io/) |  | ✔ | ✔ |

###<a name="visual-studio-tools-for-hdinsight"></a>Visual Studio Tools para HDInsight

Para obtener más información, vea [desplazarse los recursos vinculados](hdinsight-hadoop-visual-studio-tools-get-started.md#navigate-the-linked-resources).

###<a id="storageexplorer"></a>Explorador de almacenamiento de Azure

*Explorador de almacenamiento de Azure* es una herramienta muy útil para inspeccionar y modificar los datos de BLOB. Es una herramienta gratuita y de código abierto que puede descargarse desde [http://storageexplorer.com/](http://storageexplorer.com/). El código fuente está disponible en este vínculo también.

Antes de usar la herramienta, debe conocer la clave de nombre y la cuenta de la cuenta de almacenamiento de Azure. Para obtener instrucciones sobre cómo obtener esta información, vea la "Cómo: las teclas de acceso de la vista, copiar y volver a crear almacenamiento" sección de [crear, administrar, o eliminar una cuenta de almacenamiento][azure-create-storage-account].  

1. Ejecutar el Explorador de almacenamiento de Azure. Si esta es la primera vez que ejecutó el Explorador de almacenamiento, se le pedirá la ___nombre de la cuenta de almacenamiento__ y __clave de cuenta de almacenamiento__. Si tiene ejecutó antes de usar el botón __Agregar__ para agregar un nuevo nombre de la cuenta de almacenamiento y una clave.

    Escriba el nombre y de la cuenta de almacenamiento utilizado por el clúster HDinsight y, a continuación, seleccione __Guardar y abrir__.

    ![HDI. AzureStorageExplorer][image-azure-storage-explorer]

5. En la lista de contenedores a la izquierda de la interfaz, haga clic en el nombre del contenedor que está asociado con el clúster de HDInsight. De forma predeterminada, es el nombre del clúster HDInsight, pero puede ser diferente si escribe un nombre específico al crear el clúster.

6. En la barra de herramientas, seleccione el icono de carga.

    ![Barra de herramientas con el icono de carga resaltado](./media/hdinsight-upload-data/toolbar.png)

7. Especificar un archivo para cargar y, a continuación, haga clic en **Abrir**. Cuando se le solicita, seleccione __cargar__ para cargar el archivo en la raíz del contenedor de almacenamiento. Si desea cargar el archivo en una ruta de acceso específica, escriba la ruta de acceso en el campo de __destino__ y, a continuación, seleccione __cargar__.

    ![Cuadro de diálogo de carga de archivo](./media/hdinsight-upload-data/fileupload.png)
    
    Una vez que ha terminado de cargar el archivo, puede usarlo de trabajos en el clúster HDInsight.

##<a name="mount-azure-blob-storage-as-local-drive"></a>Almacenamiento de blobs de Azure montaje como unidad Local

Ver el [almacenamiento de blobs de Azure montaje como unidad Local](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/09/mount-azure-blob-storage-as-local-drive.aspx).

##<a name="services"></a>Servicios

###<a name="azure-data-factory"></a>Generador de datos de Azure

Servicio del generador de datos de Azure es un servicio totalmente gestionado para redactar servicios de movimiento de almacenamiento, procesamiento de datos y datos de los datos en datos simplificada, scalable y confiable producción canalizaciones.

Generador de datos de Azure puede usarse para mover los datos en el almacenamiento de blobs de Windows Azure, o para crear canalizaciones de datos que usan directamente HDInsight características como subárbol y cerdo.

Para obtener más información, consulte la [documentación del generador de datos de Azure](https://azure.microsoft.com/documentation/services/data-factory/).

###<a id="sqoop"></a>Apache Sqoop

Sqoop es una herramienta diseñada para transferir datos entre Hadoop y bases de datos relacionales. Puede usarlo para importar datos de un sistema de administración de la base de datos relacional (RDBMS), como SQL Server, MySQL u Oracle en el sistema de archivos distribuido Hadoop (HDFS), transformar los datos en Hadoop con MapReduce o subárbol y, a continuación, exporte los datos en un RDBMS.

Para obtener más información, vea [Usar Sqoop con HDInsight][hdinsight-use-sqoop].

##<a name="development-sdks"></a>SDK de desarrollo

Almacenamiento de blobs de Windows Azure también puede tener acceso mediante un SDK de Azure desde los lenguajes de programación siguientes:

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

Para obtener más información sobre cómo instalar el SDK de Azure, vea [descargas de Azure](https://azure.microsoft.com/downloads/)

## <a name="troubleshooting"></a>Solución de problemas

### <a id="storageexception"></a>Excepción de almacenamiento de escritura en blob

__Síntomas__: cuando se usa el `hadoop` o `hdfs dfs` comandos para escribir archivos ~ 12 GB o más grande en un clúster de HBase, que puede encontrarse el siguiente error: 

    ERROR azure.NativeAzureFileSystem: Encountered Storage Exception for write on Blob : example/test_large_file.bin._COPYING_ Exception details: null Error Code : RequestBodyTooLarge
    copyFromLocal: java.io.IOException
            at com.microsoft.azure.storage.core.Utility.initIOException(Utility.java:661)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:366)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:350)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:471)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
            at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
            at java.lang.Thread.run(Thread.java:745)
    Caused by: com.microsoft.azure.storage.StorageException: The request body is too large and exceeds the maximum permissible limit.
            at com.microsoft.azure.storage.StorageException.translateException(StorageException.java:89)
            at com.microsoft.azure.storage.core.StorageRequest.materializeException(StorageRequest.java:307)
            at com.microsoft.azure.storage.core.ExecutionEngine.executeWithRetry(ExecutionEngine.java:182)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlockInternal(CloudBlockBlob.java:816)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlock(CloudBlockBlob.java:788)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:354)
            ... 7 more

__Causa__: HBase en HDInsight clústeres predeterminado a un tamaño de bloque de 256 KB al escribir en el almacenamiento de Azure. Aunque esto funciona para HBase APIs o las API de REST, se producirá un error cuando se usa el `hadoop` o `hdfs dfs` utilidades de la línea de comandos.

__Resolución__: usar `fs.azure.write.request.size` para especificar un tamaño de bloque más grande. Puede hacerlo en una base por uso mediante la `-D` parámetro. El siguiente es un ejemplo de cómo utilizar este parámetro con el `hadoop` comando:

    hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data

También puede aumentar el valor de `fs.azure.write.request.size` globalmente mediante el uso de Ambari. Los siguientes pasos pueden usarse para cambiar el valor de la interfaz de usuario de Ambari Web:

1. En el explorador, vaya a la interfaz de usuario de Web Ambari para el clúster. Esto es https://CLUSTERNAME.azurehdinsight.net, donde __NOMBREDECLÚSTER__ es el nombre del clúster.

    Cuando se le pida, escriba el nombre de administrador y la contraseña para el clúster.

2. Desde el lado izquierdo de la pantalla, seleccione __HDFS__y, a continuación, seleccione la ficha de __configuraciones__ .

3. En el campo __de filtro...__ , escriba `fs.azure.write.request.size`. Esta opción mostrará el campo y el valor actual en el medio de la página.

4. Cambie el valor de 262144 (256KB) para el nuevo valor. Por ejemplo, 4194304 (4MB).

![Imagen de cambiar el valor a través de la interfaz de usuario de Ambari Web](./media/hdinsight-upload-data/hbase-change-block-write-size.png)

Para obtener más información sobre el uso de Ambari, consulte [clústeres de HDInsight administrar mediante la interfaz de usuario de Web Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="next-steps"></a>Pasos siguientes

Ahora que sabe cómo incorporar datos a HDInsight, consulte los siguientes artículos para obtener información sobre cómo llevar a cabo análisis:

* [Introducción a HDInsight de Azure][hdinsight-get-started]
* [Enviar trabajos de Hadoop mediante programación][hdinsight-submit-jobs]
* [Usar subárbol con HDInsight][hdinsight-use-hive]
* [Usar cerdo con HDInsight][hdinsight-use-pig]




[azure-management-portal]: https://porta.azure.com
[azure-powershell]: http://msdn.microsoft.com/library/windowsazure/jj152841.aspx

[azure-storage-client-library]: /develop/net/how-to-guides/blob-storage/
[azure-create-storage-account]: ../storage/storage-create-storage-account.md
[azure-azcopy-download]: ../storage/storage-use-azcopy.md
[azure-azcopy]: ../storage/storage-use-azcopy.md

[hdinsight-use-sqoop]: hdinsight-use-sqoop.md

[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-provision]: hdinsight-provision-clusters.md

[sqldatabase-create-configure]: ../sql-database-create-configure.md

[apache-sqoop-guide]: http://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

[Powershell-install-configure]: ../powershell-install-configure.md

[azurecli]: ../xplat-cli-install.md


[image-azure-storage-explorer]: ./media/hdinsight-upload-data/HDI.AzureStorageExplorer.png
[image-ase-addaccount]: ./media/hdinsight-upload-data/HDI.ASEAddAccount.png
[image-ase-blob]: ./media/hdinsight-upload-data/HDI.ASEBlob.png
