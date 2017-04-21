<properties
   pageTitle="Crear clústeres de HDInsight con almacén de lago de datos de Azure con PowerShell | Azure"
   description="Usar PowerShell de Azure para crear y usar clústeres HDInsight con lago de datos de Azure"
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

# <a name="create-an-hdinsight-cluster-with-data-lake-store-using-azure-powershell"></a>Crear un clúster de HDInsight con el almacén de datos lago con PowerShell de Azure

> [AZURE.SELECTOR]
- [Con Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Uso de PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)
- [Con el Administrador de recursos](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

Obtenga información sobre cómo usar PowerShell Azure para configurar un clúster de HDInsight (Hadoop, HBase o tormenta) con acceso al almacén de lago de datos de Azure. Algunas consideraciones importantes para esta versión:

* **Motor para clústeres (Linux) y Hadoop/tormenta clústeres (Windows y Linux)**, el almacén de datos de lago solo sirve como una cuenta de almacenamiento adicional. La cuenta de almacenamiento predeterminada para los clústeres de este tipo seguirán siendo Azure almacenamiento de Blobs (WASB).

* **HBase para clústeres (Windows y Linux)**, el almacén de datos de lago puede usarse como almacenamiento predeterminado o almacenamiento adicional.

> [AZURE.NOTE] Aspectos importantes que tenga en cuenta. 
> 
> * Opción para crear clústeres HDInsight con acceso al almacén de datos lago sólo está disponible para las versiones de HDInsight 3.2 y 3.4 (para clústeres Hadoop, HBase y tormenta en Windows como Linux). Para clústeres motor en Linux, esta opción solo está disponible en clústeres HDInsight 3.4.
>
> * Como se mencionó anteriormente, el almacén de datos de lago está disponible como almacenamiento predeterminado para algunos tipos de clúster (HBase) y almacenamiento adicional para otros tipos de clúster (Hadoop, motor, tormenta). Usar el almacén de lago de datos como una cuenta de almacenamiento adicional no afecta a rendimiento o la capacidad de lectura y escritura para el almacenamiento de clúster. En un escenario donde se usa el almacén de datos de lago como almacenamiento adicional, archivos relacionados con el clúster (por ejemplo, registros, etc.) se escriben en el almacenamiento predeterminado (Blobs de Azure), mientras que los datos que se desean procesar pueden almacenarse en una cuenta de almacén de datos lago.


En este artículo, hemos aprovisionar un clúster de Hadoop con el almacén de datos de lago como almacenamiento adicional.

Configurar HDInsight para trabajar con datos de la tienda lago con PowerShell implica los siguientes pasos:

* Crear un almacén de lago de datos de Azure
* Configurar la autenticación de acceso basado en roles al almacén de datos lago
* Crear clúster HDInsight con la autenticación de almacén de datos de lago
* Ejecutar un trabajo de prueba en el clúster

## <a name="prerequisites"></a>Requisitos previos

Antes de comenzar este tutorial, debe tener el siguiente:

- **Suscripción de un Azure**. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/pricing/free-trial/).

- **Azure PowerShell 1.0 o mayor**. Vea [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md).

- **Windows SDK**. Puede instalarlo desde [aquí](https://dev.windows.com/en-us/downloads). Utilícelo para crear un certificado de seguridad.

- **Principales de servicio de azure Active Directory**. Pasos de este tutorial proporcionan instrucciones sobre cómo crear una entidad de seguridad de servicio en Azure AD. Sin embargo, debe ser un administrador de Azure AD para poder crear una entidad de seguridad del servicio. Si es un administrador de Azure AD, puede omitir este requisito previo y continuar con el tutorial.
    
    **Si es un administrador de Azure AD no**, no podrá realizar los pasos necesarios para crear una entidad de seguridad del servicio. En este caso, el Administrador de Azure AD debe crear una entidad de seguridad de servicio para poder crear un clúster de HDInsight con el almacén de datos de lago. Además, servicio principal debe crearse mediante un certificado, como se describe en [crear un servicio principal con certificado](../resource-group-authenticate-service-principal.md#create-service-principal-with-certificate). 


## <a name="create-an-azure-data-lake-store"></a>Crear un almacén de lago de datos de Azure

Siga estos pasos para crear un almacén de datos de lago.

1. Desde el escritorio, abra una nueva ventana de PowerShell de Azure y escriba el fragmento de código siguiente. Cuando se solicita que inicie sesión, asegúrese de que inicie sesión como uno de lo admininistrators o propietario de la suscripción:

        # Log in to your Azure account
        Login-AzureRmAccount

        # List all the subscriptions associated to your account
        Get-AzureRmSubscription

        # Select a subscription
        Set-AzureRmContext -SubscriptionId <subscription ID>

        # Register for Data Lake Store
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

    >[AZURE.NOTE] Si recibe un error similar al `Register-AzureRmResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid` cuando se registra el proveedor de recursos de almacén de lago de datos, es posible que su subsrcription no está en la lista blanca para lago almacén de datos de Azure. Asegúrese de que activar la suscripción Azure de versión preliminar pública de almacén de datos lago siguiendo estas [instrucciones](data-lake-store-get-started-portal.md#signup).

3. Una cuenta de Azure datos lago almacén está asociada con un grupo de recursos de Azure. Empiece por crear un grupo de recursos de Azure.

        $resourceGroupName = "<your new resource group name>"
        New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

    ![Crear un grupo de recursos de Azure] (./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.PS.CreateResourceGroup.png "Crear un grupo de recursos de Azure")

2. Cree una cuenta de la tienda de lago de datos de Azure. El nombre de cuenta que especifique solo debe contener letras minúsculas y números.

        $dataLakeStoreName = "<your new Data Lake Store name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

    ![Crear una cuenta de lago de datos de Azure] (./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.PS.CreateADLAcc.png "Crear una cuenta de lago de datos de Azure")

3. Compruebe que la cuenta se ha creado correctamente.

        Test-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

    El resultado de esta debe ser **Verdadero**.

4. Cargar datos de ejemplo en lago de datos de Azure. Usaremos este más adelante en este artículo para verificar que los datos sean accesibles desde un clúster de HDInsight. Si está buscando algunos datos de ejemplo cargar, puede obtener la carpeta **Ambulancia datos** del [Repositorio de Azure datos lago Git](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).


        $myrootdir = "/"
        Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "C:\<path to data>\vehicle1_09142014.csv" -Destination $myrootdir\vehicle1_09142014.csv


## <a name="set-up-authentication-for-role-based-access-to-data-lake-store"></a>Configurar la autenticación de acceso basado en roles al almacén de datos lago

Cada suscripción Azure está asociado con Azure Active Directory. Los usuarios y servicios que tienen acceso a recursos de la suscripción con el Portal de clásico de Azure o las API de administrador de recursos de Azure en primer lugar deben autenticar con que Azure Active Directory. Acceso a servicios y suscripciones Azure asignándoles el rol correspondiente en un recurso de Azure.  Para los servicios, una entidad de seguridad de servicio identifica el servicio en Azure Active Directory (AAD). Esta sección muestra cómo conceder a un servicio de aplicación, como HDInsight de acceso a un recurso de Azure (la cuenta de la tienda de lago de datos de Azure que creó anteriormente) mediante la creación de una entidad de seguridad de servicio de la aplicación y la asignación de roles a esa a través de PowerShell de Azure.

Para configurar la autenticación de Active Directory para lago de datos de Azure, debe realizar las siguientes tareas.

* Crear un certificado autofirmado
* Crear una aplicación en Azure Active Directory y una entidad de seguridad de servicio

### <a name="create-a-self-signed-certificate"></a>Crear un certificado autofirmado

Asegúrese de que tiene instalado de [Windows SDK](https://dev.windows.com/en-us/downloads) antes de continuar con los pasos descritos en esta sección. Debe haber creado también un directorio, como **C:\mycertdir**, donde se creará el certificado.

1. En la ventana de PowerShell, desplácese hasta la ubicación donde ha instalado el SDK de Windows (normalmente, `C:\Program Files (x86)\Windows Kits\10\bin\x86` y use la [MakeCert] [ makecert] utilidad para crear un certificado autofirmado y una clave privada. Use los comandos siguientes.

        $certificateFileDir = "<my certificate directory>"
        cd $certificateFileDir
        $startDate = (Get-Date).ToString('MM/dd/yyyy')
        $endDate = (Get-Date).AddDays(365).ToString('MM/dd/yyyy')

        makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -b $startDate -e $endDate -r -len 2048

    Se le pedirá que escriba la contraseña de clave privada. Después de ejecutar el comando correctamente, verá un **CertFile.cer** y **mykey.pvk** en el directorio de certificado especificado.

4. Utilice la [Pvk2Pfx] [ pvk2pfx] utilidad para convertir los archivos .pvk y .cer que MakeCert creado en un archivo .pfx. Ejecute el siguiente comando.

        pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

    Cuando se le solicite escribir la contraseña de clave privada que especificó anteriormente. El valor especificado para el parámetro de **PC** es la contraseña que está asociada con el archivo .pfx. Cuando el comando finaliza correctamente, verá un CertFile.pfx en el directorio de certificado especificado.

###  <a name="create-an-azure-active-directory-and-a-service-principal"></a>Crear una entidad de seguridad de servicio y de Azure Active Directory

En esta sección, realice los pasos para crear un servicio principal para una aplicación de Azure Active Directory, asignar un rol al servicio principal y autenticar como el principal servicio proporcionando un certificado. Ejecute los comandos siguientes para crear una aplicación de Azure Active Directory.

1. Pegue los siguientes cmdlets en la ventana de la consola de PowerShell. Asegúrese de que el valor que especifique para la propiedad **- DisplayName** es único. Además, los valores de **- Página principal** y **IdentiferUris -** son valores de marcador de posición y no sean verificados.

        $certificateFilePath = "$certificateFileDir\CertFile.pfx"

        $password = Read-Host –Prompt "Enter the password" # This is the password you specified for the .pfx file

        $certificatePFX = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($certificateFilePath, $password)

        $rawCertificateData = $certificatePFX.GetRawCertData()

        $credential = [System.Convert]::ToBase64String($rawCertificateData)

        $application = New-AzureRmADApplication `
                    -DisplayName "HDIADL" `
                    -HomePage "https://contoso.com" `
                    -IdentifierUris "https://mycontoso.com" `
                    -KeyValue $credential  `
                    -KeyType "AsymmetricX509Cert"  `
                    -KeyUsage "Verify"  `
                    -StartDate $startDate  `
                    -EndDate $endDate

        $applicationId = $application.ApplicationId

2. Crear una entidad de seguridad de servicio con el identificador de aplicación.

        $servicePrincipal = New-AzureRmADServicePrincipal -ApplicationId $applicationId

        $objectId = $servicePrincipal.Id

3. Conceda acceso principal servicio a la que tendrá acceso desde el clúster HDInsight almacén de datos lago archivo o carpeta. El fragmento de código siguiente proporciona acceso a la raíz de la cuenta de almacén de datos lago.

        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path / -AceType User -Id $objectId -Permissions All

    Cuando se le solicite, escriba **Y** para confirmar.

## <a name="create-an-hdinsight-cluster-with-authentication-to-data-lake-store"></a>Crear un clúster de HDInsight con la autenticación de almacén de datos de lago

En esta sección, creamos un clúster de HDInsight Hadoop. En esta versión, el clúster de HDInsight y el almacén de datos de lago deben estar en la misma ubicación (Estados Unidos oriental 2).

1. Iniciar con recuperar el identificador del inquilino de suscripción. ¿Necesita más adelante.

        $tenantID = (Get-AzureRmContext).Tenant.TenantId

2. En esta versión, para un clúster de Hadoop, almacén de datos de lago solo sirve como un almacenamiento adicional para el clúster. El almacenamiento predeterminado seguirán siendo el BLOB de almacenamiento de Azure (WASB). Por lo tanto, primero crearemos la cuenta de almacenamiento y los contenedores de almacenamiento necesarios para el clúster.

        # Create an Azure storage account
        $location = "East US 2"
        $storageAccountName = "<StorageAcccountName>"   # Provide a Storage account name

        New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -Location $location -Type Standard_GRS

        # Create an Azure Blob Storage container
        $containerName = "<ContainerName>"              # Provide a container name
        $storageAccountKey = Get-AzureRmStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName | %{ $_.Key1 }
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        New-AzureStorageContainer -Name $containerName -Context $destContext

3. Cree el clúster de HDInsight. Use los siguientes cmdlets.

        # Set these variables
        $clusterName = $containerName                   # As a best practice, have the same name for the cluster and container
        $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
        $httpCredentials = Get-Credential
        $rdpCredentials = Get-Credential

        New-AzureRmHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $httpCredentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop -Version "3.2" -RdpCredential $rdpCredentials -RdpAccessExpiry (Get-Date).AddDays(14) -ObjectID $objectId -AadTenantId $tenantID -CertificateFilePath $certificateFilePath -CertificatePassword $password

    Cuando el cmdlet finaliza correctamente, verá un resultado similar a esta:

        Name                      : hdiadlcluster
        Id                        : /subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/hdiadlgroup/providers/Mi
                                    crosoft.HDInsight/clusters/hdiadlcluster
        Location                  : East US 2
        ClusterVersion            : 3.2.7.707
        OperatingSystemType       : Windows
        ClusterState              : Running
        ClusterType               : Hadoop
        CoresUsed                 : 16
        HttpEndpoint              : hdiadlcluster.azurehdinsight.net
        Error                     :
        DefaultStorageAccount     :
        DefaultStorageContainer   :
        ResourceGroup             : hdiadlgroup
        AdditionalStorageAccounts :

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-the-data-lake-store"></a>Ejecutar trabajos de prueba en el clúster HDInsight usar el almacén de datos de lago

Después de configurar un clúster de HDInsight, puede ejecutar trabajos de prueba en el clúster para comprobar que el clúster HDInsight acceso al almacén de datos de lago. Para ello, se ejecutará un trabajo de sección de ejemplo que crea una tabla con los datos de ejemplo que ha cargado anteriormente en el almacén de datos de lago.

### <a name="for-a-linux-cluster"></a>Para un clúster de Linux

En esta sección le SSH en el clúster y ejecutar la una consulta de la sección de ejemplo. Windows no proporciona a un cliente SSH integrado. Se recomienda usar **PuTTY**, que puede descargarse desde [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Para obtener más información sobre el uso de PuTTY, vea [Usar SSH con basados en Linux Hadoop en HDInsight de Windows ](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

1. Una vez conectado, iniciar CLI subárbol mediante el siguiente comando:

        hive

2. Con la CLI, escriba las siguientes instrucciones para crear una nueva tabla denominada **vehículos** usando los datos de ejemplo en la tienda de lago de datos:

        DROP TABLE vehicles;
        CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestore>.azuredatalakestore.net:443/';
        SELECT * FROM vehicles LIMIT 10;

    Debe ver un resultado similar al siguiente:

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


### <a name="for-a-windows-cluster"></a>Para un clúster de Windows

Use los siguientes cmdlets para ejecutar la consulta de la sección. En esta consulta creamos una tabla de los datos del almacén de datos lago y, a continuación, ejecutar una consulta de selección en la tabla creada.

    $queryString = "DROP TABLE vehicles;" + "CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://$dataLakeStoreName.azuredatalakestore.net:443/';" + "SELECT * FROM vehicles LIMIT 10;"

    $hiveJobDefinition = New-AzureRmHDInsightHiveJobDefinition -Query $queryString

    $hiveJob = Start-AzureRmHDInsightJob -ResourceGroupName $resourceGroupName -ClusterName $clusterName -JobDefinition $hiveJobDefinition -ClusterCredential $httpCredentials

    Wait-AzureRmHDInsightJob -ResourceGroupName $resourceGroupName -ClusterName $clusterName -JobId $hiveJob.JobId -ClusterCredential $httpCredentials

Esto tendrá el siguiente resultado. **ExitValue** de 0 en el resultado sugiere que la tarea se completó correctamente.

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

Recuperar el resultado de la tarea mediante el siguiente cmdlet:

    Get-AzureRmHDInsightJobOutput -ClusterName $clusterName -JobId $hiveJob.JobId -DefaultContainer $containerName -DefaultStorageAccountName $storageAccountName -DefaultStorageAccountKey $storageAccountKey -ClusterCredential $httpCredentials

El resultado de trabajo parecida a la siguiente:

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


## <a name="access-data-lake-store-using-hdfs-commands"></a>Almacén de lago de datos de Access con los comandos HDFS

Una vez que haya configurado el clúster de HDInsight para usar el almacén de datos de lago, puede usar los comandos de shell HDFS para tener acceso a la tienda.

### <a name="for-a-linux-cluster"></a>Para un clúster de Linux

En esta sección se SSH en el clúster y ejecute los comandos HDFS. Windows no proporciona a un cliente SSH integrado. Se recomienda usar **PuTTY**, que puede descargarse desde [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Para obtener más información sobre el uso de PuTTY, vea [Usar SSH con basados en Linux Hadoop en HDInsight de Windows ](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

Una vez conectado, utilice el siguiente comando de sistema de archivos HDFS para enumerar los archivos del almacén de datos lago.

    hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/

Esto debe mostrar el archivo que ha cargado anteriormente en el almacén de datos de lago.

    15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
    Found 1 items
    -rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder

También puede usar el `hdfs dfs -put` comando cargar algunos archivos en el almacén de lago de datos y, a continuación, usar `hdfs dfs -ls` para comprobar si los archivos se han cargado correctamente.


### <a name="for-a-windows-cluster"></a>Para un clúster de Windows

1. Inicie sesión el nuevo [Portal de Azure](https://portal.azure.com).

2. Haga clic en **Examinar**, haga clic en **HDInsight clústeres**y, a continuación, haga clic en el clúster HDInsight que ha creado.

3. En el módulo de clúster, haga clic en **Escritorio remoto**y, a continuación, en el módulo de **Escritorio remoto** , haga clic en **Conectar**.

    ![Remoto en clúster HDI] (./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.HDI.PS.Remote.Desktop.png "Crear un grupo de recursos de Azure")

    Cuando se le pida, escriba las credenciales proporcionadas para el usuario de escritorio remoto.

4. En la sesión remota, iniciar Windows PowerShell y use los comandos de sistema de archivos HDFS para enumerar los archivos en el almacén de lago de datos de Azure.

        hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/

    Esto debe mostrar el archivo que ha cargado anteriormente en el almacén de datos de lago.

        15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
        Found 1 items
        -rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/vehicle1_09142014.csv

    También puede usar el `hdfs dfs -put` comando cargar algunos archivos en el almacén de lago de datos y, a continuación, usar `hdfs dfs -ls` para comprobar si los archivos se han cargado correctamente.

## <a name="see-also"></a>Vea también

* [Portal: Crear un clúster de HDInsight para usar el almacén de datos de lago](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
