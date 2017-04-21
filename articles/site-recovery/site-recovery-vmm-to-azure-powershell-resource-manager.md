<properties
    pageTitle="Replicar máquinas virtuales de Hyper-V en nubes VMM con PowerShell (jefe de recursos) y la recuperación de sitio de Azure | Microsoft Azure"
    description="Replicar máquinas virtuales de Hyper-V en nubes VMM con PowerShell y recuperación de sitio de Azure"
    services="site-recovery"
    documentationCenter=""
    authors="Rajani-Janaki-Ram"
    manager="rochakm"
    editor="raynew"/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="rajanaki"/>

# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-azure-using-powershell-and-azure-resource-manager"></a>Replicar máquinas virtuales de Hyper-V en nubes VMM en Azure con PowerShell y el Administrador de recursos de Azure

> [AZURE.SELECTOR]
- [Portal de Azure](site-recovery-vmm-to-azure.md)
- [PowerShell - Administrador de recursos](site-recovery-vmm-to-azure-powershell-resource-manager.md)
- [Portal clásica](site-recovery-vmm-to-azure-classic.md)
- [PowerShell - clásica](site-recovery-deploy-with-powershell.md)



## <a name="overview"></a>Información general

Recuperación de sitio de Azure contribuye a su estrategia de recuperación (BCDR) de desastres y continuidad empresarial por coordinar replicación, migración tras error y recuperación de máquinas virtuales de un número de escenarios de implementación. Para obtener una lista completa de escenarios de implementación consulte la [Introducción a la recuperación de sitio de Azure](site-recovery-overview.md).

Este artículo le muestra cómo usar PowerShell para automatizar tareas comunes que se debe realizar al configurar Azure sitio recuperación para replicar máquinas virtuales de Hyper-V en nubes de System Center VMM a almacenamiento de Azure.

El artículo incluye los requisitos previos para el escenario y se muestra

- Cómo configurar un depósito de servicios de recuperación
- Instalar el proveedor de servicios de recuperación de sitio de Azure en el servidor VMM de origen
- Registrar el servidor de la caja fuerte, agregar una cuenta de almacenamiento de Azure
- Instalar al agente de servicios de recuperación de Azure en servidores de host de Hyper-V
- Establecer la configuración de protección de nubes VMM, que se aplicará a todos los equipos virtuales protegidos
- Habilitar la protección de esas máquinas virtuales.
- Probar la conmutar para asegurarse de que todo funciona según lo esperado.

Si tiene problemas de configuración de este escenario, publique sus preguntas en el [Foro de servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

> [AZURE.NOTE] Azure tiene dos diferentes modelos de implementación para crear y trabajar con recursos: [Administrador de recursos y clásica](../resource-manager-deployment-model.md). En este artículo trata sobre utiliza el modelo de implementación de administrador de recursos.

## <a name="before-you-start"></a>Antes de empezar

Asegúrese de que tiene estos requisitos previos en su lugar:

### <a name="azure-prerequisites"></a>Requisitos previos de Azure

- Tendrá una cuenta de [Microsoft Azure](https://azure.microsoft.com/) . Si no tiene una, comience con una [cuenta gratuita](https://azure.microsoft.com/free). Además, puede leer sobre el [Administrador de recuperación del sitio de Azure precios](https://azure.microsoft.com/pricing/details/site-recovery/).
- Necesitará una suscripción CSP si está probando la replicación a un escenario de suscripción CSP. Más información sobre el programa CSP en [cómo inscribirse en el programa CSP](https://msdn.microsoft.com/library/partnercenter/mt156995.aspx).
- Tendrá una cuenta de almacenamiento (Administrador de recursos) v2 Azure para almacenar datos duplicados en Azure. La cuenta debe replicación geo habilitada. Debe estar en la misma región como el servicio de recuperación de sitios de Azure y estar asociadas a la misma suscripción o la suscripción de CSP. Para obtener más información sobre la configuración de almacenamiento de Azure, consulte la [Introducción a Microsoft Azure almacenamiento](../storage/storage-introduction.md) de referencia.
- Debe asegurarse de que cumplen los [requisitos previos de Azure máquina virtual](site-recovery-best-practices.md#azure-virtual-machine-requirements)máquinas virtuales que desea proteger.

> [AZURE.NOTE] Actualmente, sólo operaciones de nivel de máquina virtual son posibles a través de Powershell. Soporte para operaciones de nivel de plan de recuperación se realizará pronto.  Por ahora, está limitado a la realización de éxito de productos en solo en un detalle 'protegido VM' y no en un nivel de Plan de recuperación.

### <a name="vmm-prerequisites"></a>Requisitos previos VMM
- Necesitará servidor VMM que se ejecuta en System Center 2012 R2.
- Debe ejecutar el proveedor de servicios de recuperación de sitio de Azure cualquier servidor VMM que contiene máquinas virtuales que desea proteger. Esto se instala durante la implementación de recuperación de sitios de Azure.
- Necesitará al menos una nube en el servidor VMM que desea proteger. Debe contener la nube:
    - Uno o varios grupos de host VMM.
    - Uno o varios servidores de host de Hyper-V o grupos de cada grupo de host.
    - Una o más máquinas virtuales de Windows en el servidor de origen Hyper-V.
- Más información sobre la configuración de nubes de VMM:
    - Obtenga más información acerca de nubes privadas de VMM en [Novedades de nube a privada con System Center 2012 R2 VMM](http://go.microsoft.com/fwlink/?LinkId=324952) y en [VMM 2012 y las nubes](http://go.microsoft.com/fwlink/?LinkId=324956).
    - Obtenga información acerca de cómo [configurar a la estructura de la nube VMM](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)
    - Una vez los elementos de la estructura de nube de obtener información sobre la creación de nubes privadas en la [creación de una nube privada en VMM](http://go.microsoft.com/fwlink/p/?LinkId=324953) y en [Tutorial: crear nubes privadas con System Center 2012 SP1 VMM](http://go.microsoft.com/fwlink/p/?LinkId=324954).


### <a name="hyper-v-prerequisites"></a>Requisitos previos de Hyper-V

- Los servidores host Hyper-V deben ejecutar al menos **Windows Server 2012** con la función Hyper-V o **Microsoft Hyper-V Server 2012** y han instalado las últimas actualizaciones.
- Si está ejecutando Hyper-V en una nota de clúster ese agente de clúster no se crea automáticamente si tiene un clúster de basado en la dirección IP estático. Deberá configurar al agente de clúster manualmente. Para
- Para obtener instrucciones, consulte [cómo configurar agente de réplica de Hyper-V](http://blogs.technet.com/b/haroldwong/archive/2013/03/27/server-virtualization-series-hyper-v-replica-broker-explained-part-15-of-20-by-yung-chou.aspx).
- Todos los servidores de host de Hyper-V o clúster para el que desea administrar protección debe incluirse en una nube VMM.

### <a name="network-mapping-prerequisites"></a>Requisitos previos de asignación de red
Al proteger máquinas virtuales de Azure, los mapas de asignación de red redes de la máquina virtual en el servidor VMM de origen y destino Azure redes para habilitar la siguiente:

- Todos los equipos que conmutar en la misma red pueden conectar entre sí, con independencia de qué plan de recuperación se encuentran en.
- Si una puerta de enlace de red está configurado en el destino de red Azure, máquinas virtuales puede conectarse a otras máquinas virtuales de local.
- Si no establece la asignación de red, las máquinas virtuales conmutar en el mismo plan de recuperación podrá conectar entre sí después de conmutar a Azure.

Si desea implementar la asignación de red necesita lo siguiente:

- Las máquinas virtuales que desea proteger en el servidor VMM de origen debe estar conectadas a una red VM. Red debe vincularse a una red lógica que esté asociada a la nube.
- Una red de Azure al que puede conectarse replicadas máquinas virtuales después de fallos. En el momento de conmutar seleccionará esta red. La red debe estar en la misma región como su suscripción de recuperación de sitios de Azure.

Más información acerca de la asignación de red en

- [Cómo configurar redes lógicas en VMM](http://go.microsoft.com/fwlink/p/?LinkId=386307)
- [Cómo configurar puertas de enlace y redes de máquina virtual en VMM](http://go.microsoft.com/fwlink/p/?LinkId=386308)
- [Cómo configurar y supervisar redes virtuales en Azure](https://azure.microsoft.com/documentation/services/virtual-network/)


###<a name="powershell-prerequisites"></a>Requisitos previos de PowerShell
Asegúrese de que tiene Azure PowerShell listo. Si ya está usando PowerShell, necesitará actualizar a la versión 0.8.10 o posterior. Para obtener información sobre cómo configurar PowerShell, consulte la [Guía para instalar y configurar Azure PowerShell](../powershell-install-configure.md). Una vez que haya configurado y configurado PowerShell, puede ver todos los cmdlets disponibles para el servicio [aquí](https://msdn.microsoft.com/library/dn850420.aspx).

Para conocer consejos que pueden ayudarle a que usar los cmdlets, por ejemplo, cómo se gestionan normalmente salidas, entradas y valores de parámetro en Azure PowerShell, consulte la [Guía de introducción a los Cmdlets de Azure](https://msdn.microsoft.com/library/azure/jj554332.aspx).

## <a name="step-1-set-the-subscription"></a>Paso 1: Configurar la suscripción

1. Desde powershell Azure, inicie sesión en su cuenta de Azure: mediante los siguientes cmdlets

        $UserName = "<user@live.com>"
        $Password = "<password>"
        $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
        $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
        Login-AzureRmAccount #-Credential $Cred


2. Obtener una lista de las suscripciones. Esto también mostrará el subscriptionIDs para cada una de las suscripciones. Anote la subscriptionID de la suscripción en la que desea crear el depósito de servicios de recuperación

        Get-AzureRmSubscription

3. Establecer la suscripción en la que la cámara de servicios de recuperación es a crearse mencionar el identificador de la suscripción

        Set-AzureRmContext –SubscriptionID <subscriptionId>


## <a name="step-2-create-a-recovery-services-vault"></a>Paso 2: Crear un depósito de servicios de recuperación

1. Crear un grupo de recursos en el Administrador de recursos de Azure si aún no tiene una

        New-AzureRmResourceGroup -Name #ResourceGroupName -Location #location

2. Crear un nuevo depósito de servicios de recuperación y guardar el objeto de depósito ASR creado en una variable (se utilizará más adelante). También puede recuperar la creación de entrada del objeto de depósito ASR mediante el cmdlet Get-AzureRMRecoveryServicesVault:-

        $vault = New-AzureRmRecoveryServicesVault -Name #vaultname -ResouceGroupName #ResourceGroupName -Location #location

## <a name="step-3-set-the-recovery-services-vault-context"></a>Paso 3: Configurar el contexto de la cámara de servicios de recuperación

1.  Establecer el contexto de la cámara ejecutando el debajo de comando.

        Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault

## <a name="step-4-install-the-azure-site-recovery-provider"></a>Paso 4: Instalar el proveedor de servicios de recuperación de sitio de Azure

1.  En el equipo VMM, cree un directorio, ejecute el siguiente comando:

        New-Item c:\ASR -type directory

2.  Extraer los archivos con el proveedor descargado, ejecute el comando siguiente

        pushd C:\ASR\
        .\AzureSiteRecoveryProvider.exe /x:. /q


3.  Instale al proveedor mediante los siguientes comandos:

        .\SetupDr.exe /i
        $installationRegPath = "hklm:\software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
        do
        {
                        $isNotInstalled = $true;
                        if(Test-Path $installationRegPath)
                        {
                                        $isNotInstalled = $false;
                        }
        }While($isNotInstalled)

    Espere a que termine la instalación.

4.  Registrar el servidor en la caja fuerte mediante el siguiente comando:

        $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
        pushd $BinPath
        $encryptionFilePath = "C:\temp\".\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice


## <a name="step-5-create-an-azure-storage-account"></a>Paso 5: Crear una cuenta de almacenamiento de Azure

1. Si no tiene una cuenta de almacenamiento de Azure, cree una cuenta de replicación geo habilitada en el mismo geo como la cámara, ejecute el siguiente comando:

        $StorageAccountName = "teststorageacc1" #StorageAccountname
        $StorageAccountGeo  = "Southeast Asia"  
        $ResourceGroupName =  “myRG”            #ResourceGroupName
        $RecoveryStorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -Type “StandardGRS” -Location $StorageAccountGeo

Observe que la cuenta de almacenamiento debe estar en la misma región como el servicio de recuperación de sitios de Azure y estar asociadas a la misma suscripción.

## <a name="step-6-install-the-azure-recovery-services-agent"></a>Paso 6: Instalar al agente de servicios de recuperación de Azure

1. Descargar al agente de servicios de recuperación de Azure en [http://aka.ms/latestmarsagent](http://aka.ms/latestmarsagent) e instálelo en cada servidor de host de Hyper-V ubicado en las nubes VMM que desea proteger.

2. Ejecute el comando siguiente en todos los hosts VMM:

    marsagentinstaller.exe/q /nu

## <a name="step-7-configure-cloud-protection-settings"></a>Paso 7: Configurar nube configuración de protección

1.  Crear una directiva de replicación de Azure, ejecute el siguiente comando:


        $ReplicationFrequencyInSeconds = "300";     #options are 30,300,900
        $PolicyName = “replicapolicy”
        $Recoverypoints = 6                 #specify the number of recovery points

        $policryresult = New-AzureRmSiteRecoveryPolicy -Name $policyname -ReplicationProvider HyperVReplicaAzure -ReplicationFrequencyInSeconds $replicationfrequencyinseconds -RecoveryPoints $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId "/subscriptions/q1345667/resourceGroups/test/providers/Microsoft.Storage/storageAccounts/teststorageacc1"

2.  Obtener un contenedor de protección, ejecute los comandos siguientes:

        $PrimaryCloud = "testcloud"
        $protectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  

3.  Obtengan los detalles de la directiva a una variable mediante el trabajo que se creó y mencionar el nombre descriptivo de directiva:

        $policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $policyname

4.  Iniciar la asociación del contenedor de protección con la directiva de replicación:

        $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $protectionContainer  

5.  Cuando haya finalizado el trabajo, ejecute el siguiente comando:

        $job = Get-AzureRmSiteRecoveryJob -Job $associationJob
        if($job -eq $null -or $job.StateDescription -ne "Completed")
         {
        $isJobLeftForProcessing = $true;
        }

6.  Después de que el trabajo ha finalizado el proceso, ejecute el siguiente comando:

        if($isJobLeftForProcessing)
        {
        Start-Sleep -Seconds 60
        }
        }While($isJobLeftForProcessing)

Para comprobar la finalización de la operación, siga los pasos de [Supervisar la actividad](#monitor).

## <a name="step-8-configure-network-mapping"></a>Paso 8: Configurar la asignación de red

Antes de comenzar la asignación de red Compruebe que máquinas virtuales en el servidor de origen VMM están conectados a una red de la máquina virtual. Además, cree una o varias redes de Azure virtuales.

Obtenga más información sobre cómo crear una red virtual mediante el Administrador de recursos de Azure y PowerShell, en [crear una red virtual con una conexión VPN de sitio a sitio con el Administrador de recursos de Azure y PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

Tenga en cuenta que se pueden asignar varias redes de máquina Virtual a una sola red de Azure. Si la red de destino tiene varias subredes y una de esas subredes tiene el mismo nombre que la subred en la que se encuentra la máquina virtual de origen, a continuación, la máquina virtual de réplica se conectará a esa subred de destino después de fallos. Si no hay ninguna subred de destino con un nombre coincidente, la máquina virtual se conectará a la primera subred de la red.

1. El primer comando obtiene servidores para el depósito de Azure recuperación del sitio actual. El comando almacena los servidores de recuperación de sitio de Microsoft Azure en la variable de matriz $Servers.

        $Servers = Get-AzureRmSiteRecoveryServer

2. El segundo comando obtiene la red de recuperación de sitio para el primer servidor de la matriz $Servers. El comando almacena las redes en la variable $Networks.


        $Networks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[0]

3. El tercer comando obtiene Azure redes virtuales y, a continuación, ese valor en la variable $AzureVmNetworks.

        $AzureVmNetworks =  Get-AzureRmVirtualNetwork

4. El cmdlet final crea una asignación entre la red principal y la red de Azure máquina virtual. El cmdlet especifica la red principal como el primer elemento de $Networks. El cmdlet especifica una red de la máquina virtual como el primer elemento de $AzureVmNetworks.

        New-AzureRmSiteRecoveryNetworkMapping -PrimaryNetwork $Networks[0] -AzureVMNetworkId $AzureVmNetworks[0]


## <a name="step-9-enable-protection-for-virtual-machines"></a>Paso 9: Habilitar la protección de máquinas virtuales

Después de los servidores, nubes y redes están configuradas correctamente, puede habilitar la protección de máquinas virtuales en la nube.

 Tenga en cuenta lo siguiente:

 - Máquinas virtuales de Windows debe cumplir los requisitos de Azure. Compruebe en [los requisitos previos y soporte técnico](site-recovery-best-practices.md) de la Guía de planeación.

 - Para habilitar la protección, el sistema operativo y el sistema operativo propiedades del disco deben estar establecidas para la máquina virtual. Cuando se crea una máquina virtual en VMM utilizando una plantilla de máquina virtual puede establecer la propiedad. También puede establecer las propiedades de máquinas virtuales existentes en las pestañas **General** y **Configuración de Hardware** de las propiedades de la máquina virtual. Si no establece estas propiedades en VMM podrá configurarlas en el portal de recuperación de sitios de Azure.


  1. Para habilitar la protección, ejecute el comando siguiente para obtener el contenedor de protección:

            $ProtectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $CloudName

  2. Obtener la entidad de protección (VM), ejecute el siguiente comando:

            $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $protectionContainer

  3. Habilitar el DR de la máquina virtual, ejecute el siguiente comando:

            $jobResult = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable –Force -Policy $policy -RecoveryAzureStorageAccountId  $storageID "/subscriptions/217653172865hcvkchgvd/resourceGroups/rajanirgps/providers/Microsoft.Storage/storageAccounts/teststorageacc1



## <a name="test-your-deployment"></a>Probar la implementación

Para probar la implementación, puede ejecutar una prueba éxito durante una sola máquina virtual, o crear un plan de recuperación que consta de varios equipos virtuales y ejecutar una conmutar prueba para el plan. Prueba conmutar simula el mecanismo de fallos y recuperación en una red aislada. Tenga en cuenta que:

- Si desea conectarse a la máquina virtual en Azure usando Escritorio remoto tras la conmutar, Habilitar conexión a Escritorio remoto en la máquina virtual antes de ejecutar la migración tras error de prueba.
- Después de conmutar, deberá usar una dirección IP pública para conectarse a la máquina virtual en Azure mediante Escritorio remoto. Si desea hacerlo, asegúrese de que no tiene las directivas de dominio que impiden la conexión a una máquina virtual usando una dirección pública.

Para comprobar la finalización de la operación, siga los pasos de [Supervisar la actividad](#monitor).


### <a name="run-a-test-failover"></a>Ejecutar un error de prueba

1.  Inicie la prueba de migración tras error, ejecute el siguiente comando:

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -AzureVMNetworkId <string>  

### <a name="run-a-planned-failover"></a>Ejecutar una migración tras error planeada

1. Iniciar la migración tras error planeada, ejecute el siguiente comando:

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -AzureVMNetworkId <string>  

### <a name="run-an-unplanned-failover"></a>Ejecutar una migración tras error planificado

1. Iniciar la migración tras error no planeada, ejecute el siguiente comando:

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -AzureVMNetworkId <string>  


## <a name=monitor></a>Supervisar la actividad

Use los comandos siguientes para supervisar la actividad. Tenga en cuenta que deba esperar entre los trabajos para el procesamiento Finalizar.

    Do
    {
        $job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
        Write-Host "Job State:{0}, StateDescription:{1}" -f Job.State, $job.StateDescription;
        if($job -eq $null -or $job.StateDescription -ne "Completed")
        {
            $isJobLeftForProcessing = $true;
        }

    if($isJobLeftForProcessing)
        {
            Start-Sleep -Seconds 60
        }
    }While($isJobLeftForProcessing)



## <a name="next-steps"></a>Pasos siguientes

[Obtenga más información](https://msdn.microsoft.com/library/azure/mt637930.aspx) acerca de la recuperación de sitio de Azure con los cmdlets de PowerShell de administrador de recursos de Azure.
