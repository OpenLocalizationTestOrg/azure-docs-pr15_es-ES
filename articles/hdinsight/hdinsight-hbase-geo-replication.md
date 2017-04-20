<properties 
   pageTitle="Configurar la replicación de HBase entre dos centros de datos | Microsoft Azure" 
   description="Obtenga información sobre cómo configurar la replicación de HBase entre dos centros de datos y acerca de los casos de uso para la replicación de clúster." 
   services="hdinsight,virtual-network" 
   documentationCenter="" 
   authors="mumian" 
   manager="jhubbard" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="07/25/2016"
   ms.author="jgao"/>

# <a name="configure-hbase-geo-replication-in-hdinsight"></a>Configurar replicación geo HBase en HDInsight

> [AZURE.SELECTOR]
- [Configurar la conectividad VPN](../hdinsight-hbase-geo-replication-configure-vnets.md)
- [Configurar el DNS](hdinsight-hbase-geo-replication-configure-dns.md)
- [Configurar la replicación de HBase](hdinsight-hbase-geo-replication.md) 
 
Obtenga información sobre cómo configurar la replicación de HBase entre dos centros de datos. Algunos casos de uso para replicación de clúster incluyen:

- Copia de seguridad y recuperación de desastres
- Agregación de datos
- Distribución de datos geográficos
- Recopilación de datos en línea junto con el análisis de datos sin conexión

Replicación de clúster usa una metodología de inserción de origen. Un clúster de HBase puede ser una fuente, un destino, o puede llevar a cabo ambas funciones al mismo tiempo. Replicación es asincrónica y el objetivo de replicación es la coherencia. Cuando el origen recibe una edición a una familia de columna con la replicación habilitada, edición se propaga a todos los clústeres de destino. Cuando lo datos desde un clúster a otro, el clúster de origen y todos los clústeres que ya se hayan consumido los datos se controlan para evitar la replicación bucles. Para obtener más información, en este tutorial, va a configurar una réplica de origen y destino.  Para otras topologías de clúster, vea [Guía de referencia de HBase Apache](http://hbase.apache.org/book.html#_cluster_replication).

Esto es el tercer elemento de la serie:

- [Configurar una conexión VPN entre dos redes virtuales][hdinsight-hbase-replication-vnet]
- [Configurar el DNS para las redes virtuales][hdinsight-hbase-replication-dns]
- Configurar la replicación de geo HBase (en este tutorial)

El siguiente diagrama muestra las dos redes virtuales y la conectividad de red que creó en [Configurar una conexión VPN entre dos redes virtuales] [ hdinsight-hbase-geo-replication-vnet] y [Configurar DNS para las redes virtuales][hdinsight-hbase-replication-dns]: 

![Diagrama de red virtual de replicación de HDInsight HBase][img-vnet-diagram]

## <a id="prerequisites"></a>Requisitos previos

Antes de comenzar este tutorial, debe tener el siguiente:

- **Suscripción de un Azure**. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Una estación de trabajo con PowerShell de Azure**.

    Para ejecutar las secuencias de comandos de PowerShell, debe ejecutar Azure PowerShell como administrador y establecer la directiva de ejecución *RemoteSigned*. Vea usar el cmdlet Set-ExecutionPolicy.
    
    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

- **Azure dos virtual red con conectividad VPN y configurado DNS**.  Para obtener instrucciones, vea [Configurar una conexión VPN entre dos redes virtuales Azure][hdinsight-hbase-replication-vnet]y [Configurar DNS entre dos redes virtuales Azure][hdinsight-hbase-replication-dns].


    Antes de ejecutar secuencias de comandos de PowerShell, asegúrese de que está conectado a su suscripción de Azure mediante el siguiente cmdlet:

        Add-AzureAccount

    Si tiene varias suscripciones de Azure, use el cmdlet siguiente para establecer la suscripción actual:

        Select-AzureSubscription <AzureSubscriptionName>



## <a name="provision-hbase-clusters-in-hdinsight"></a>Aprovisionar clústeres HBase en HDInsight

Configurar [una conexión VPN entre dos redes virtuales Azure][hdinsight-hbase-replication-vnet], ha creado una red virtual de una Europa centro de datos y una red virtual en un centro de datos de Estados Unidos. La red virtual dos están conectadas a través de VPN. En esta sesión de aprovisionamiento de un clúster de HBase en cada una de las redes virtuales. Más adelante en este tutorial, hará que uno de los clústeres HBase replicar del otro clúster HBase.

El Portal de clásico de Azure no admite aprovisionamiento clústeres HDInsight con opciones de configuración personalizada. Por ejemplo, establezca *hbase.replication* en *Verdadero*. Si establece el valor en el archivo de configuración después de que se aprovisiona un clúster, perderá la configuración una vez que se vuelve el clúster. Para obtener más información, consulte [clústeres de aprovisionamiento Hadoop en HDInsight][hdinsight-provision]. Usa una de las opciones proporcionando clúster de HDInsight con las opciones personalizadas de PowerShell de Azure.


**Aprovisionar un clúster de HBase de Contoso-VNet-UE** 

1. Desde su estación de trabajo, abra Windows PowerShell ISE.
2. Establecer las variables al principio de la secuencia de comandos y, a continuación, ejecute el script.

        # create hbase cluster with replication enabled
        
        $azureSubscriptionName = "[AzureSubscriptionName]"
        
        $hbaseClusterName = "Contoso-HBase-EU" # This is the HBase cluster name to be used.
        $hbaseClusterSize = 1   # You must provision a cluster that contains at least 3 nodes for high availability of the HBase services.
        $hadoopUserLogin = "[HadoopUserName]"
        $hadoopUserpw = "[HadoopUserPassword]"
        
        $vNetName = "Contoso-VNet-EU"  # This name must match your Europe virtual network name.
        $subNetName = 'Subnet-1' # This name must match your Europe virtual network subnet name.  The default name is "Subnet-1".
        
        $storageAccountName = 'ContosoStoreEU'  # The script will create this storage account for you.  The storage account name doesn't support hyphens. 
        $storageAccountName = $storageAccountName.ToLower() #Storage account name must be in lower case.
        $blobContainerName = $hbaseClusterName.ToLower()  #Use the cluster name as the default container name.
        
        #connect to your Azure subscription
        Add-AzureAccount 
        Select-AzureSubscription $azureSubscriptionName
        
        # Create a storage account used by the HBase cluster
        $location = Get-AzureVNetSite -VNetName $vNetName | %{$_.Location} # use the virtual network location
        New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location
        
        # Create a blob container used by the HBase cluster
        $storageAccountKey = Get-AzureStorageKey -StorageAccountName $storageAccountName | %{$_.Primary}
        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        New-AzureStorageContainer -Name $blobContainerName -Context $storageContext
        
        # Create provision configuration object
        $hbaseConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightHBaseConfiguration'
            
        $hbaseConfigValues.Configuration = @{ "hbase.replication"="true" } #this modifies the hbase-site.xml file
        
        # retrieve vnet id based on vnetname
        $vNetID = Get-AzureVNetSite -VNetName $vNetName | %{$_.id}
        
        $config = New-AzureHDInsightClusterConfig `
                        -ClusterSizeInNodes $hbaseClusterSize `
                        -ClusterType HBase `
                        -VirtualNetworkId $vNetID `
                        -SubnetName $subNetName `
                    | Set-AzureHDInsightDefaultStorage `
                        -StorageAccountName $storageAccountName `
                        -StorageAccountKey $storageAccountKey `
                        -StorageContainerName $blobContainerName `
                    | Add-AzureHDInsightConfigValues `
                        -HBase $hbaseConfigValues
        
        # provision HDInsight cluster
        $hadoopUserPassword = ConvertTo-SecureString -String $hadoopUserpw -AsPlainText -Force     
        $credential = New-Object System.Management.Automation.PSCredential($hadoopUserLogin,$hadoopUserPassword)
        
        $config | New-AzureHDInsightCluster -Name $hbaseClusterName -Location $location -Credential $credential



**Aprovisionar un clúster de HBase de Contoso-VNet-US** 

- Usar la misma secuencia de comandos con los siguientes valores:

        $hbaseClusterName = "Contoso-HBase-US" # This is the HBase cluster name to be used.
        $vNetName = "Contoso-VNet-US"  # This name must match your Europe virtual network name.
        $storageAccountName = 'ContosoStoreUS'  

    Dado que ya se ha conectado a su cuenta de Azure, no es necesario ejecutar el siguiente comlets ya:

        Add-AzureAccount 
        Select-AzureSubscription $azureSubscriptionName




## <a name="configure-dns-conditional-forwarder"></a>Configurar DNS de reenviador condicional

En [Configurar el DNS para las redes virtuales][hdinsight-hbase-replication-dns], ha configurado los servidores DNS para las dos redes. Los clústeres HBase tienen sufijos de dominio diferente. Es así, debe configurar los reenviadores condicionales DNS adicionales.

Para configurar reenviador condicional, debe saber los sufijos de dominio de los dos clústeres HBase. 

**Para buscar los sufijos de dominio de los dos clústeres HBase**

1. RDP en **Contoso-HBase-UE**.  Para instrucciones, consulte [Administrar Hadoop clústeres en HDInsight a través del Portal de clásico de Azure][hdinsight-manage-portal]. Es realmente headnode0 del clúster.
2. Abra una consola de Windows PowerShell o un símbolo del sistema.
3. Ejecutar **ipconfig**y anote el **sufijo DNS específico de conexión**.
4. No cierre la sesión RDP.  Lo necesitará más adelante para probar la resolución de nombres de dominio.
5. Repita los mismos pasos para descubrir el **sufijo DNS específico de la conexión** de **Contoso-HBase-US**.


**Para configurar los reenviadores DNS**
 
1.  RDP en **UE de DNS de Contoso**. 
2.  Haga clic en la tecla Windows en la esquina inferior izquierda.
2.  Haga clic en **Herramientas administrativas**.
3.  Haga clic en **DNS**.
4.  En el panel izquierdo, expanda **DSN**, **Contoso-DNS-UE**.
5.  Haga clic con el botón **Reenviadores condicionales**y, a continuación, haga clic en **Nuevo reenviador condicional**. 
5.  Escriba la información siguiente:
    - **Dominio DNS**: escriba el sufijo DNS de Contoso-HBase-US. Por ejemplo: Contoso-HBase-US.f5.internal.cloudapp.net.
    - **Direcciones IP de los servidores maestros**: escriba 10.2.0.4, que es la dirección IP de Contoso-DNS-EE.UU.. Compruebe la dirección IP. El servidor DNS puede tener una dirección IP diferente.
6.  Presione **ENTRAR**y, a continuación, haga clic en **Aceptar**.  Ahora podrá resolver la dirección IP de Contoso-DNS-EE.UU. de Contoso-DNS-UE.
7.  Repita los pasos para agregar un reenviador condicional de DNS para el servicio DNS en la máquina virtual de Contoso-DNS-US con los siguientes valores:
    - **Dominio DNS**: escriba el sufijo DNS de la UE HBase de Contoso. 
    - **Direcciones IP de los servidores maestros**: escriba 10.2.0.4, que es la dirección IP de Contoso-DNS-de la Unión Europea.

**Para probar la resolución de nombres de dominio**

1. Cambiar a la ventana de Contoso-HBase-UE RDP.
2. Abra un símbolo del sistema.
3. Ejecute el comando ping:

        ping headnode0.[DNS suffix of Contoso-HBase-US]

    El protocolo ICM está activado en los nodos de trabajo de los clústeres HBase

4. No cierre la sesión RDP. Aún lo necesitará más adelante en el tutorial.
5. Repita los mismos pasos para hacer ping a la headnode0 de la UE de Contoso-HBase-US HBase de Contoso.

>[AZURE.IMPORTANT] Debe trabajar DNS antes de continuar con los siguientes pasos.

## <a name="enable-replication-between-hbase-tables"></a>Habilitar la replicación entre las tablas de HBase

Ahora, puede crear una tabla de HBase de ejemplo, habilite la replicación y probar con algunos datos. La tabla de ejemplo que se usa tiene dos familias de columna: Personal y Office. 

En este tutorial, hará que el clúster de Europa HBase como el origen y el clúster de Estados Unidos HBase como el clúster de destino.

Crear tablas de HBase con los mismos nombres y las familias de columna de origen y destino clústeres, para que el clúster de destino sepa dónde almacenar datos recibirá. Para obtener más información sobre el uso de la consola de HBase, consulte [Introducción a Apache HBase en HDInsight][hdinsight-hbase-get-started].

**Para crear una tabla de HBase en Contoso-HBase-UE**

1. Cambiar a la ventana RDP de **Contoso-HBase-UE** .
2. En el escritorio, haga clic en **línea de comandos de Hadoop**.
2. Cambiar la carpeta en el directorio de inicio de HBase:

        cd %HBASE_HOME%\bin
3. Abre la consola de HBase:

        hbase shell
4. Crear una tabla de HBase:

        create 'Contacts', 'Personal', 'Office'
5. No cierre la sesión RDP ni en la ventana de la línea de comandos de Hadoop. Aún necesitará más adelante en el tutorial.
    
**Para crear una tabla de HBase en Contoso-HBase-US**

- Repita los mismos pasos para crear la misma tabla en Contoso-HBase-US.


**Para agregar Contoso-HBase-US como un punto de replicación**

1. Cambiar a la ventana RDP **Contso HBase_EU** .
2. En la ventana de shell HBase, agregue el clúster de destino (Estados Unidos Contoso-HBase) como un punto, por ejemplo:

        add_peer '1', 'zookeeper0.contoso-hbase-us.d4.internal.cloudapp.net,zookeeper1.contoso-hbase-us.d4.internal.cloudapp.net,zookeeper2.contoso-hbase-us.d4.internal.cloudapp.net:2181:/hbase'

    En el ejemplo, el sufijo de dominio es *contoso-hbase-us.d4.internal.cloudapp.net*. Debe actualizar para que coincida con el sufijo del dominio del clúster nos HBase. Asegúrese de que no hay espacios entre los nombres de host.

**Para configurar la familia de cada columna replicarse en el clúster de origen**

1. En la ventana de shell HBase de la sesión RDP **Contso-HBase-UE** , configure cada familia columna replicarse:

        disable 'Contacts'
        alter 'Contacts', {NAME => 'Personal', REPLICATION_SCOPE => '1'}
        alter 'Contacts', {NAME => 'Office', REPLICATION_SCOPE => '1'}
        enable 'Contacts'

**Cargar datos a la tabla HBase de forma masiva**

Un archivo de datos de ejemplo se ha cargado en un contenedor de blobs de Windows Azure público con la siguiente URL:

        wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

El contenido del archivo:

        8396    Calvin Raji 230-555-0191    5415 San Gabriel Dr.
        16600   Karen Wu    646-555-0113    9265 La Paz
        4324    Karl Xie    508-555-0163    4912 La Vuelta
        16891   Jonathan Jackson    674-555-0110    40 Ellis St.
        3273    Miguel Miller   397-555-0155    6696 Anchor Drive
        3588    Osarumwense Agbonile    592-555-0152    1873 Lion Circle
        10272   Julia Lee   870-555-0110    3148 Rose Street
        4868    Jose Hayes  599-555-0171    793 Crawford Street
        4761    Caleb Alexander 670-555-0141    4775 Kentucky Dr.
        16443   Terry Chander   998-555-0171    771 Northridge Drive

Puede cargar el mismo archivo de datos en el clúster HBase e importar los datos desde allí.

1. Cambiar a la ventana RDP de **Contoso-HBase-UE** .
2. En el escritorio, haga clic en **línea de comandos de Hadoop**.
3. Cambiar la carpeta en el directorio de inicio de HBase:

        cd %HBASE_HOME%\bin

4. cargar los datos:

        hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name, Personal:HomePhone, Office:Address" -Dimporttsv.bulk.output=/tmpOutput Contacts wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

        hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /tmpOutput Contacts


## <a name="verify-that-data-replication-is-taking-place"></a>Compruebe que la replicación de datos produce

Puede comprobar que la replicación produce mediante la exploración de las tablas de ambos clústeres con los comandos de shell HBase siguientes:

        Scan 'Contacts'


## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido a configurar la replicación de HBase entre dos centros de datos. Para obtener más información sobre HDInsight y HBase, consulte:

- [Página de servicio de HDInsight](https://azure.microsoft.com/services/hdinsight/)
- [Documentación de HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/)
- [Introducción a Apache HBase en HDInsight][hdinsight-hbase-get-started]
- [Información general de HDInsight HBase][hdinsight-hbase-overview]
- [Aprovisionar clústeres HBase en red Virtual de Azure][hdinsight-hbase-provision-vnet]
- [Analizar la opinión de Twitter en tiempo real con HBase][hdinsight-hbase-twitter-sentiment]
- [Analizar los datos de sensor con tormenta y HBase en HDInsight (Hadoop)][hdinsight-sensor-data]

[hdinsight-hbase-geo-replication-vnet]: hdinsight-hbase-geo-replication-configure-vnets.md
[hdinsight-hbase-geo-replication-dns]: ../hdinsight-hbase-geo-replication-configure-VNet.md


[img-vnet-diagram]: ./media/hdinsight-hbase-geo-replication/HDInsight.HBase.Replication.Network.diagram.png

[powershell-install]: powershell-install-configure.md
[hdinsight-hbase-get-started]: hdinsight-hbase-tutorial-get-started.md
[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-hbase-replication-vnet]: hdinsight-hbase-geo-replication-configure-vnets.md
[hdinsight-hbase-replication-dns]: hdinsight-hbase-geo-replication-configure-dns.md
[hdinsight-hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[hdinsight-sensor-data]: hdinsight-storm-sensor-data-analysis.md
[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
