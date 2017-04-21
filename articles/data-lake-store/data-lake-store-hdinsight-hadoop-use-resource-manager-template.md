<properties
   pageTitle="Crear clústeres de HDInsight con plantillas de administrador de recursos de Azure datos lago almacén | Microsoft Azure"
   description="Use las plantillas de administrador de recursos de Azure para crear y usar clústeres HDInsight con lago almacén de datos de Azure"
   services="data-lake-store,hdinsight"
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
   ms.date="10/21/2016"
   ms.author="nitinme"/>

# <a name="create-an-hdinsight-cluster-with-data-lake-store-using-azure-resource-manager-template"></a>Crear un clúster de HDInsight con el almacén de lago de datos mediante el Administrador de recursos de Azure plantilla

> [AZURE.SELECTOR] - [Con Portal](data-lake-store-hdinsight-hadoop-use-portal.md) - [con PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md) - [con el Administrador de recursos](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

Aprenda a usar una plantilla de administrador de recursos de Azure para configurar un clúster de HDInsight con acceso al almacén de lago de datos de Azure. Algunas consideraciones importantes para esta versión:

-   **Motor para clústeres (Linux) y Hadoop/tormenta clústeres (Windows y Linux)**, el almacén de datos de lago solo sirve como una cuenta de almacenamiento adicional. La cuenta de almacenamiento predeterminada para los clústeres de este tipo seguirán siendo Azure almacenamiento de Blobs (WASB).

-   **HBase para clústeres (Windows y Linux)**, el almacén de datos de lago puede usarse como almacenamiento predeterminado o almacenamiento adicional.

> [AZURE.NOTE] Aspectos importantes que tenga en cuenta.
>
> - Opción para crear clústeres HDInsight con acceso al almacén de datos lago sólo está disponible para las versiones de HDInsight 3.2 y 3.4 (para clústeres Hadoop, HBase y tormenta en Windows como Linux). Para clústeres motor en Linux, esta opción solo está disponible en clústeres HDInsight 3.4.
>
> - Como se mencionó anteriormente, el almacén de datos de lago está disponible como almacenamiento predeterminado para algunos tipos de clúster (HBase) y almacenamiento adicional para otros tipos de clúster (Hadoop, motor, tormenta). Usar el almacén de lago de datos como una cuenta de almacenamiento adicional no afecta a rendimiento o la capacidad de lectura y escritura para el almacenamiento de clúster. En un escenario donde se usa el almacén de datos de lago como almacenamiento adicional, archivos relacionados con el clúster (por ejemplo, registros, etc.) se escriben en el almacenamiento predeterminado (Blobs de Azure), mientras que los datos que se desean procesar pueden almacenarse en una cuenta de almacén de datos lago.
>

En este artículo, hemos aprovisionar un clúster de Hadoop con el almacén de datos de lago como almacenamiento adicional.

## <a name="prerequisites"></a>Requisitos previos

Antes de comenzar este tutorial, debe tener el siguiente:

-   **Suscripción de un Azure**. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/pricing/free-trial/).

-   **Azure PowerShell 1.0 o mayor**. Vea [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md).

- **Principales de servicio de azure Active Directory**. Pasos de este tutorial proporcionan instrucciones sobre cómo crear una entidad de seguridad de servicio en Azure AD. Sin embargo, debe ser un administrador de Azure AD para poder crear una entidad de seguridad del servicio. Si es un administrador de Azure AD, puede omitir este requisito previo y continuar con el tutorial.
    
    **Si es un administrador de Azure AD no**, no podrá realizar los pasos necesarios para crear una entidad de seguridad del servicio. En este caso, el Administrador de Azure AD debe crear una entidad de seguridad de servicio para poder crear un clúster de HDInsight con el almacén de datos de lago. Además, servicio principal debe crearse mediante un certificado, como se describe en [crear un servicio principal con certificado](../resource-group-authenticate-service-principal.md#create-service-principal-with-certificate).

## <a name="create-an-hdinsight-cluster-with-azure-data-lake-store"></a>Crear un clúster de HDInsight con lago almacén de datos de Azure

La plantilla de administrador de recursos y los requisitos previos para usar la plantilla, están disponibles en GitHub al [implementar un clúster de HDInsight Linux con nuevo almacén de lago de datos](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage). Siga las instrucciones proporcionadas en este vínculo para crear un clúster de HDInsight con lago almacén de datos de Azure como el almacenamiento adicional.

Las instrucciones que aparecen en el vínculo mencionados requieren PowerShell. Antes de comenzar con las instrucciones, asegúrese de que iniciar sesión su cuenta de Azure. Desde el escritorio, abra una nueva ventana de PowerShell de Azure y escriba los fragmentos de código siguientes. Cuando se solicita que inicie sesión, asegúrese de que inicie sesión como uno de lo admininistrators o propietario de la suscripción:

```
# Log in to your Azure account
Login-AzureRmAccount

# List all the subscriptions associated to your account
Get-AzureRmSubscription

# Select a subscription
Set-AzureRmContext -SubscriptionId <subscription ID>
```

## <a name="upload-sample-data-to-the-azure-data-lake-store"></a>Cargar datos de ejemplo en la tienda de lago de datos de Azure

La plantilla de administrador de recursos crea una nueva cuenta de almacén de datos lago y asocia el clúster HDInsight. Ahora debe cargar algunos datos de ejemplo en el almacén de datos de lago. Necesitará estos datos más adelante en el tutorial para ejecutar trabajos desde un clúster de HDInsight acceso a los datos del almacén de datos lago. Para obtener instrucciones sobre cómo cargar datos, vea [cargar un archivo en el almacén de datos de lago](data-lake-store-get-started-portal.md#uploaddata). Si está buscando algunos datos de ejemplo cargar, puede obtener la carpeta **Ambulancia datos** del [Repositorio de Azure datos lago Git](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData).

## <a name="set-relevant-acls-on-the-sample-data"></a>Establecer ACL relevantes en los datos de ejemplo

Para asegurarse de que los datos de ejemplo que carga están accesibles desde el clúster HDInsight, debe asegurarse de que la aplicación de Azure AD que se usa para establecer la identidad entre el clúster de HDInsight y el almacén de datos de lago tiene acceso para el archivo o carpeta que está intentando tener acceso. Para ello, realice los pasos siguientes.

1.  Busque el nombre de la aplicación de Azure AD que está asociado a HDInsight clúster y el almacén de datos de lago. Busque el nombre de una forma es abrir el módulo de clúster HDInsight creada con la plantilla de administrador de recursos, haga clic en la ficha **Clúster AAD identidad** y buscar el valor de **Nombre a Principal de servicio para mostrar**.

2.  Ahora, proporcionar acceso a esta aplicación de Azure AD en el archivo o carpeta que desea tener acceso desde el clúster HDInsight. Para configurar las ACL derecho en el archivo o carpeta en el almacén de datos de lago, vea [proteger datos almacén de datos lago](data-lake-store-secure-data.md#assign-users-or-security-group-as-acls-to-the-azure-data-lake-store-file-system).

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-the-data-lake-store"></a>Ejecutar trabajos de prueba en el clúster HDInsight usar el almacén de datos de lago

Después de configurar un clúster de HDInsight, puede ejecutar trabajos de prueba en el clúster para comprobar que el clúster HDInsight acceso al almacén de datos de lago. Para ello, se ejecutará un trabajo de sección de ejemplo que crea una tabla con los datos de ejemplo que ha cargado anteriormente en el almacén de datos de lago.

### <a name="for-a-linux-cluster"></a>Para un clúster de Linux

En esta sección le SSH en el clúster y ejecutar la una consulta de la sección de ejemplo. Windows no proporciona a un cliente SSH integrado. Se recomienda usar **PuTTY**, que puede descargarse desde [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Para obtener más información sobre el uso de PuTTY, vea [Usar SSH con basados en Linux Hadoop en HDInsight de Windows ](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

1.  Una vez conectado, iniciar CLI subárbol mediante el siguiente comando:

    ```
    hive
    ```

2.  Con la CLI, escriba las siguientes instrucciones para crear una nueva tabla denominada **vehículos** usando los datos de ejemplo en la tienda de lago de datos:

    ```
    DROP TABLE vehicles;
    CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestore>.azuredatalakestore.net:443/';
    SELECT * FROM vehicles LIMIT 10;
    ```

    Debe ver un resultado similar al siguiente:

    ```
    1,1,2014-09-14 00:00:03,46.81006,-92.08174,51,S,1
    1,2,2014-09-14 00:00:06,46.81006,-92.08174,13,NE,1
    1,3,2014-09-14 00:00:09,46.81006,-92.08174,48,NE,1
    1,4,2014-09-14 00:00:12,46.81006,-92.08174,30,W,1
    1,5,2014-09-14 00:00:15,46.81006,-92.08174,47,S,1
    1,6,2014-09-14 00:00:18,46.81006,-92.08174,9,S,1
    1,7,2014-09-14 00:00:21,46.81006,-92.08174,53,N,1
    1,8,2014-09-14 00:00:24,46.81006,-92.08174,63,SW,1
    1,9,2014-09-14 00:00:27,46.81006,-92.08174,4,NE,1
    1,10,2014-09-14 00:00:30,46.81006,-92.08174,31,N,1
    ```

### <a name="for-a-windows-cluster"></a>Para un clúster de Windows

Use los siguientes cmdlets para ejecutar la consulta de la sección. En esta consulta creamos una tabla de los datos del almacén de datos lago y, a continuación, ejecutar una consulta de selección en la tabla creada.

```
$queryString = "DROP TABLE vehicles;" + "CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://$dataLakeStoreName.azuredatalakestore.net:443/';" + "SELECT * FROM vehicles LIMIT 10;"

$hiveJobDefinition = New-AzureRmHDInsightHiveJobDefinition -Query $queryString

$hiveJob = Start-AzureRmHDInsightJob -ResourceGroupName $resourceGroupName -ClusterName $clusterName -JobDefinition $hiveJobDefinition -ClusterCredential $httpCredentials

Wait-AzureRmHDInsightJob -ResourceGroupName $resourceGroupName -ClusterName $clusterName -JobId $hiveJob.JobId -ClusterCredential $httpCredentials
```

Esto tendrá el siguiente resultado. **ExitValue** de 0 en el resultado sugiere que la tarea se completó correctamente.

```
Cluster         : hdiadlcluster.
HttpEndpoint    : hdiadlcluster.azurehdinsight.net
State           : SUCCEEDED
JobId           : job_1445386885331_0012
ParentId        :
PercentComplete :
ExitValue       : 0
User            : admin
Callback        :
Completed       : done
```

Recuperar el resultado de la tarea mediante el siguiente cmdlet:

```
Get-AzureRmHDInsightJobOutput -ClusterName $clusterName -JobId $hiveJob.JobId -DefaultContainer $containerName -DefaultStorageAccountName $storageAccountName -DefaultStorageAccountKey $storageAccountKey -ClusterCredential $httpCredentials
```

El resultado de trabajo parecida a la siguiente:

```
1,1,2014-09-14 00:00:03,46.81006,-92.08174,51,S,1
1,2,2014-09-14 00:00:06,46.81006,-92.08174,13,NE,1
1,3,2014-09-14 00:00:09,46.81006,-92.08174,48,NE,1
1,4,2014-09-14 00:00:12,46.81006,-92.08174,30,W,1
1,5,2014-09-14 00:00:15,46.81006,-92.08174,47,S,1
1,6,2014-09-14 00:00:18,46.81006,-92.08174,9,S,1
1,7,2014-09-14 00:00:21,46.81006,-92.08174,53,N,1
1,8,2014-09-14 00:00:24,46.81006,-92.08174,63,SW,1
1,9,2014-09-14 00:00:27,46.81006,-92.08174,4,NE,1
1,10,2014-09-14 00:00:30,46.81006,-92.08174,31,N,1
```

## <a name="access-data-lake-store-using-hdfs-commands"></a>Almacén de lago de datos de Access con los comandos HDFS

Una vez que haya configurado el clúster de HDInsight para usar el almacén de datos de lago, puede usar los comandos de shell HDFS para tener acceso a la tienda.

### <a name="for-a-linux-cluster"></a>Para un clúster de Linux

En esta sección se SSH en el clúster y ejecute los comandos HDFS. Windows no proporciona a un cliente SSH integrado. Se recomienda usar **PuTTY**, que puede descargarse desde [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Para obtener más información sobre el uso de PuTTY, vea [Usar SSH con basados en Linux Hadoop en HDInsight de Windows ](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

Una vez conectado, utilice el siguiente comando de sistema de archivos HDFS para enumerar los archivos del almacén de datos lago.

```
hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/
```

Esto debe mostrar el archivo que ha cargado anteriormente en el almacén de datos de lago.

```
15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
Found 1 items
-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder
```

También puede usar el `hdfs dfs -put` comando cargar algunos archivos en el almacén de lago de datos y, a continuación, usar `hdfs dfs -ls` para comprobar si los archivos se han cargado correctamente.

### <a name="for-a-windows-cluster"></a>Para un clúster de Windows

1.  Inicie sesión el nuevo [Portal de Azure](https://portal.azure.com).

2.  Haga clic en **Examinar**, haga clic en **HDInsight clústeres**y, a continuación, haga clic en el clúster HDInsight que ha creado.

3.  En el módulo de clúster, haga clic en **Escritorio remoto**y, a continuación, en el módulo de **Escritorio remoto** , haga clic en **Conectar**.

    ![Remoto en clúster HDI](./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.HDI.PS.Remote.Desktop.png)

    Cuando se le pida, escriba las credenciales proporcionadas para el usuario de escritorio remoto.

4.  En la sesión remota, iniciar Windows PowerShell y use los comandos de sistema de archivos HDFS para enumerar los archivos en el almacén de lago de datos de Azure.

    ```
    hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/
    ```

    Esto debe mostrar el archivo que ha cargado anteriormente en el almacén de datos de lago.

    ```
    15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
    Found 1 items
    -rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/vehicle1_09142014.csv
    ```

    También puede usar el `hdfs dfs -put` comando cargar algunos archivos en el almacén de lago de datos y, a continuación, usar `hdfs dfs -ls` para comprobar si los archivos se han cargado correctamente.

## <a name="next-steps"></a>Pasos siguientes

-   [Copiar datos de Azure almacenamiento de Blobs al almacén de datos lago](data-lake-store-copy-data-wasb-distcp.md)
