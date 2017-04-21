<properties
    pageTitle="Replicar máquinas virtuales de Hyper-V en nubes VMM con PowerShell y recuperación de sitio de Azure | Microsoft Azure"
    description="Aprenda a automatizar la replicación de máquinas virtuales de Hyper-V en nubes VMM con PowerShell y recuperación de sitio."
    services="site-recovery"
    documentationCenter=""
    authors="bsiva"
    manager="abhiag"
    editor="tysonn"/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="bsiva"/>

# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-azure-using-powershell---classic"></a>Replicar máquinas virtuales de Hyper-V en nubes VMM en Azure con Powershell - clásica

> [AZURE.SELECTOR]
- [Portal de Azure](site-recovery-vmm-to-azure.md)
- [PowerShell - Administrador de recursos](site-recovery-vmm-to-azure-powershell-resource-manager.md)
- [Portal clásica](site-recovery-vmm-to-azure-classic.md)
- [PowerShell - clásica](site-recovery-deploy-with-powershell.md)


## <a name="overview"></a>Información general

Recuperación de sitio de Azure contribuye a su estrategia de recuperación (BCDR) de desastres y continuidad empresarial por coordinar replicación, migración tras error y recuperación de máquinas virtuales de un número de escenarios de implementación. Para obtener una lista completa de escenarios de implementación consulte la [Introducción a la recuperación de sitio de Azure](site-recovery-overview.md).

Este artículo le muestra cómo usar PowerShell para automatizar tareas comunes que se debe realizar al configurar Azure sitio recuperación para replicar máquinas virtuales de Hyper-V en nubes de System Center VMM a almacenamiento de Azure.

El artículo incluye los requisitos previos para el escenario y muestra cómo configurar un depósito de recuperación del sitio, instalar el proveedor de servicios de recuperación de sitio de Azure en el servidor de origen VMM, registrar el servidor en la caja fuerte, agregar una cuenta de almacenamiento de Azure, instale al agente de servicios de recuperación de Azure en servidores de host de Hyper-V, establecer la configuración de protección de nubes VMM que se aplicará a todos los equipos virtuales protegidos y, a continuación, habilitar la protección de esas máquinas virtuales. Finalizar configuración probando la migración tras error para asegurarse de que todo funciona según lo esperado.

Si tiene problemas de configuración de este escenario, publique sus preguntas en el [Foro de servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


> [AZURE.NOTE] Azure tiene dos diferentes modelos de implementación para crear y trabajar con recursos: [Administrador de recursos y clásica](../resource-manager-deployment-model.md). En este artículo trata sobre utiliza el modelo de implementación estándar.



## <a name="before-you-start"></a>Antes de empezar

Asegúrese de que tiene estos requisitos previos en su lugar:

### <a name="azure-prerequisites"></a>Requisitos previos de Azure

- Tendrá una cuenta de [Microsoft Azure](https://azure.microsoft.com/) . Puede empezar con una [versión de prueba gratuita](https://azure.microsoft.com/pricing/free-trial/).
- Tendrá una cuenta de almacenamiento de Azure para almacenar datos duplicados. La cuenta debe replicación geo habilitada. Debe estar en la misma región como depósito de recuperación de sitios de Azure y estar asociadas a la misma suscripción. [Obtenga más información sobre el almacenamiento de Azure](../storage/storage-introduction.md).
- Debe asegurarse de que cumplen [los requisitos previos de Azure máquina virtual](site-recovery-best-practices.md#virtual-machines)máquinas virtuales que desea proteger.

### <a name="vmm-prerequisites"></a>Requisitos previos VMM
- Necesitará servidor VMM que se ejecuta en System Center 2012 R2.
- Necesitará al menos una nube en el servidor VMM que desea proteger. Debe contener la nube:
    - Uno o varios grupos de host VMM.
    - Uno o varios servidores de host de Hyper-V o grupos de cada grupo de host.
    - Una o más máquinas virtuales de Windows en el servidor de origen Hyper-V.

### <a name="hyper-v-prerequisites"></a>Requisitos previos de Hyper-V

- Los servidores host Hyper-V deben ejecutar al menos **Windows Server 2012** con la función Hyper-V o **Microsoft Hyper-V Server 2012** y han instalado las últimas actualizaciones.
- Si está ejecutando Hyper-V en una nota de clúster ese agente de clúster no se crea automáticamente si tiene un clúster de basado en la dirección IP estático. Deberá configurar al agente de clúster manualmente. Para hacer esto, en el administrador del servidor > Administrador de clúster de migración tras error, conéctese al clúster, haga clic en **Configurar roles** y seleccione **Agente de réplica de Hyper-V** en la pantalla **Seleccionar función** del Asistente de alta disponibilidad.
- Todos los servidores de host de Hyper-V o clúster para el que desea administrar protección debe incluirse en una nube VMM.

### <a name="network-mapping-prerequisites"></a>Requisitos previos de asignación de red
Cuando proteger máquinas virtuales de mapas de asignación de red Azure entre redes VM en el servidor VMM de origen y destino Azure redes para habilitar las siguientes acciones:

- Todos los equipos que conmutar en la misma red pueden conectar entre sí, con independencia de qué plan de recuperación se encuentran en.
- Si una puerta de enlace de red está configurado en el destino de red Azure, máquinas virtuales puede conectarse a otras máquinas virtuales de local.
- Si no configurar red asignación máquinas virtuales solo que conmutar en el mismo plan de recuperación podrán conectar entre sí después de la migración a Azure.

Si desea implementar la asignación de red necesita lo siguiente:

- Las máquinas virtuales que desea proteger en el servidor VMM de origen debe estar conectadas a una red VM. Red debe vincularse a una red lógica que esté asociada a la nube.
- Una red de Azure al que puede conectarse replicadas máquinas virtuales después de la migración. En el momento de la migración tras error, seleccione esta red. La red debe estar en la misma región como su suscripción de recuperación de sitios de Azure.
- [Más información](site-recovery-network-mapping.md) acerca de la asignación de red:

###<a name="powershell-prerequisites"></a>Requisitos previos de PowerShell
Asegúrese de que tiene Azure PowerShell listo. Si ya está usando PowerShell, necesitará actualizar a la versión 0.8.10 o posterior. Para obtener información sobre cómo configurar PowerShell, vea [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md). Una vez que haya configurado y configurado PowerShell, puede ver todos los cmdlets disponibles para el servicio [aquí](https://msdn.microsoft.com/library/dn850420.aspx).

Para conocer consejos que pueden ayudarle a que usar los cmdlets, por ejemplo, cómo se gestionan normalmente salidas, entradas y valores de parámetro en Azure PowerShell, vea [Introducción a los Cmdlets de Azure](https://msdn.microsoft.com/library/azure/jj554332.aspx).

## <a name="step-1-set-the-subscription"></a>Paso 1: Configurar la suscripción

En PowerShell, ejecute los cmdlets:

```
$UserName = "<user@live.com>"
$Password = "<password>"
$AzureSubscriptionName = "prod_sub1"

$SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
$Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $securePassword
Add-AzureAccount -Credential $Cred;
$AzureSubscription = Select-AzureSubscription -SubscriptionName $AzureSubscriptionName

```

Reemplace los elementos dentro de la "< >" por su información específica.

## <a name="step-2-create-a-site-recovery-vault"></a>Paso 2: Crear un depósito de recuperación de sitios

En PowerShell, reemplace los elementos dentro de la "< >" con su información específica y ejecutar estos comandos:

```

$VaultName = "<testvault123>"
$VaultGeo  = "<Southeast Asia>"
$OutputPathForSettingsFile = "<c:\>"

```


```
New-AzureSiteRecoveryVault -Location $VaultGeo -Name $VaultName;
$vault = Get-AzureSiteRecoveryVault -Name $VaultName;

```

## <a name="step-3-generate-a-vault-registration-key"></a>Paso 3: Generar una clave de registro de cámara

Generar una clave de registro de la cámara. Después de descargar el proveedor de servicios de recuperación de sitio de Azure e instalarlo en el servidor VMM, deberá usar esta clave para registrar el servidor VMM en la cámara.

1.  Obtener el archivo de configuración de cámara y establezca el contexto:

    ```

    $VaultName = "<testvault123>"
    $VaultGeo  = "<Southeast Asia>"
    $OutputPathForSettingsFile = "<c:\>"

    $VaultSetingsFile = Get-AzureSiteRecoveryVaultSettingsFile -Location $VaultGeo -Name $VaultName -Path $OutputPathForSettingsFile;

    ```

2.  Establecer el contexto de la cámara, ejecute los comandos siguientes:

    ```

    $VaultSettingFilePath = $vaultSetingsFile.FilePath
    $VaultContext = Import-AzureSiteRecoveryVaultSettingsFile -Path $VaultSettingFilePath -ErrorAction Stop

    ```

## <a name="step-4-install-the-azure-site-recovery-provider"></a>Paso 4: Instalar el proveedor de servicios de recuperación de sitio de Azure

1.  En el equipo VMM, cree un directorio, ejecute el siguiente comando:

    ```

    pushd C:\ASR\

    ```

2.  Extraer los archivos con el proveedor descargado, ejecute el comando siguiente

    ```

    AzureSiteRecoveryProvider.exe /x:. /q

    ```

3.  Instale al proveedor mediante los siguientes comandos:

    ```

    .\SetupDr.exe /i

    ```

    ```

    $installationRegPath = "hklm:\software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
    do
    {
        $isNotInstalled = $true;
        if(Test-Path $installationRegPath)
        {
            $isNotInstalled = $false;
        }
    }While($isNotInstalled)

    ```

    Espere a que termine la instalación.

4.  Registrar el servidor en la caja fuerte mediante el siguiente comando:

    ```

    $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
    pushd $BinPath
    $encryptionFilePath = "C:\temp\"
    .\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice

    ```

## <a name="step-5-create-an-azure-storage-account"></a>Paso 5: Crear una cuenta de almacenamiento de Azure

Si no tiene una cuenta de almacenamiento de Azure, cree una cuenta de replicación geo habilitada, ejecute el siguiente comando:

```

$StorageAccountName = "teststorageacc1"
$StorageAccountGeo  = "Southeast Asia"

New-AzureStorageAccount -StorageAccountName $StorageAccountName -Label $StorageAccountName -Location $StorageAccountGeo;

```

Observe que la cuenta de almacenamiento debe estar en la misma región como el servicio de recuperación de sitios de Azure y estar asociadas a la misma suscripción.


## <a name="step-6-install-the-azure-recovery-services-agent"></a>Paso 6: Instalar al agente de servicios de recuperación de Azure

Desde el portal de Azure, instale al agente de servicios de recuperación de Azure en cada servidor de host de Hyper-V ubicado en las nubes VMM que desea proteger.

Ejecute el comando siguiente en todos los hosts VMM:

```

marsagentinstaller.exe /q /nu

```


## <a name="step-7-configure-cloud-protection-settings"></a>Paso 7: Configurar nube configuración de protección

1.  Crear un perfil de protección de la nube en Azure, ejecute el siguiente comando:

    ```

    $ReplicationFrequencyInSeconds = "300";
    $ProfileResult = New-AzureSiteRecoveryProtectionProfileObject -ReplicationProvider HyperVReplica -RecoveryAzureSubscription $AzureSubscriptionName -RecoveryAzureStorageAccount $StorageAccountName -ReplicationFrequencyInSeconds  $ReplicationFrequencyInSeconds;

    ```

2.  Obtener un contenedor de protección, ejecute los comandos siguientes:

    ```

    $PrimaryCloud = "testcloud"
    $protectionContainer = Get-AzureSiteRecoveryProtectionContainer -Name $PrimaryCloud;

    ```

3.  Iniciar la asociación del contenedor de protección con la nube:

    ```

    $associationJob = Start-AzureSiteRecoveryProtectionProfileAssociationJob -ProtectionProfile $profileResult -PrimaryProtectionContainer $protectionContainer;        

    ```

4.  Cuando haya finalizado el trabajo, ejecute el siguiente comando:


        $job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
        if($job -eq $null -or $job.StateDescription -ne "Completed")
        {
        $isJobLeftForProcessing = $true;
        }


5.  Después de que el trabajo ha finalizado el proceso, ejecute el siguiente comando:


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



Para comprobar la finalización de la operación, siga los pasos de [Supervisar la actividad](#monitor).

## <a name="step-8-configure-network-mapping"></a>Paso 8: Configurar la asignación de red
Antes de comenzar la asignación de red Compruebe que máquinas virtuales en el servidor de origen VMM están conectados a una red de la máquina virtual. Además, cree una o varias redes de Azure virtuales. Tenga en cuenta que se pueden asignar varias redes VM a una única red de Azure.

Tenga en cuenta que si la red de destino tiene varias subredes y una de esas subredes tiene el mismo nombre que la subred en la que se encuentra la máquina virtual de origen y luego la máquina virtual de réplica se conectará a esa subred de destino después de la migración. Si no hay una subred de destino con un nombre coincidente, la máquina virtual se conectará a la primera subred de la red.

El primer comando obtiene servidores para el depósito de Azure recuperación del sitio actual. El comando almacena los servidores de recuperación de sitio de Microsoft Azure en la variable de matriz $Servers.

    $Servers = Get-AzureSiteRecoveryServer


El segundo comando obtiene la red de recuperación de sitio para el primer servidor de la matriz $Servers. El comando almacena las redes en la variable $Networks.


    $Networks = Get-AzureSiteRecoveryNetwork -Server $Servers[0]

El tercer comando obtiene sus suscripciones Azure mediante el cmdlet Get-AzureSubscription y, a continuación, almacena ese valor en la variable $Subscriptions.

    $Subscriptions = Get-AzureSubscription



El comando cuarto obtiene Azure redes virtuales mediante el cmdlet Get-AzureVNetSite y, a continuación, ese valor en la variable $AzureVmNetworks.


    $AzureVmNetworks = Get-AzureVNetSite



El cmdlet final crea una asignación entre la red principal y la red de Azure máquina virtual. El cmdlet especifica la red principal como el primer elemento de $Networks. El cmdlet especifica una red de la máquina virtual como el primer elemento de $AzureVmNetworks usando su Id. El comando incluye su identificador de suscripción de Azure.


    New-AzureSiteRecoveryNetworkMapping -PrimaryNetwork $Networks[0] -AzureSubscriptionId $Subscriptions[0].SubscriptionId -AzureVMNetworkId $AzureVmNetworks[0].Id


## <a name="step-9-enable-protection-for-virtual-machines"></a>Paso 9: Habilitar la protección de máquinas virtuales

Después de servidores, nubes y redes están configuradas correctamente, puede habilitar la protección de máquinas virtuales en la nube. Tenga en cuenta lo siguiente:

Máquinas virtuales de Windows debe cumplir [los requisitos previos de Azure máquina virtual](site-recovery-best-practices.md#virtual-machines).

Para habilitar la protección del sistema operativo y el sistema operativo propiedades del disco deben estar establecidas para la máquina virtual. Cuando se crea una máquina virtual en VMM utilizando una plantilla de máquina virtual puede establecer la propiedad. También puede establecer las propiedades de máquinas virtuales existentes en las pestañas **General** y **Configuración de Hardware** de las propiedades de la máquina virtual. Si no establece estas propiedades en VMM podrá configurarlas en el portal de recuperación de sitios de Azure.



1.  Para habilitar la protección, ejecute el comando siguiente para obtener el contenedor de protección:

        $ProtectionContainer = Get-AzureSiteRecoveryProtectionContainer -Name $CloudName



2. Obtener la entidad de protección (VM), ejecute el siguiente comando:


        $protectionEntity = Get-AzureSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer



3. Habilitar el DR de la máquina virtual, ejecute el siguiente comando:



        $jobResult = Set-AzureSiteRecoveryProtectionEntity -ProtectionEntity $protectionEntity  -Protection Enable -Force



## <a name="test-your-deployment"></a>Probar la implementación

Para probar la implementación puede ejecutar un error de prueba para una sola máquina virtual, o crear un plan de recuperación que consta de varios equipos virtuales y ejecutar un error de prueba para el plan. Migración de prueba tras error simula su mecanismo de migración y recuperación en una red aislada. Tenga en cuenta que:

- Si desea conectarse a la máquina virtual en Azure con Escritorio remoto después del error, Habilitar conexión a Escritorio remoto en la máquina virtual antes de ejecutar la migración tras error de prueba.
- Después de la migración, deberá usar una dirección IP pública para conectarse a la máquina virtual en Azure mediante Escritorio remoto. Si desea hacerlo, asegúrese de que no tiene las directivas de dominio que impiden la conexión a una máquina virtual usando una dirección pública.

Para comprobar la finalización de la operación, siga los pasos de [Supervisar la actividad](#monitor).

### <a name="create-a-recovery-plan"></a>Crear un plan de recuperación

1. Crear un archivo .xml como una plantilla para su plan de recuperación de los datos siguientes y, a continuación, guárdelo como "C:\RPTemplatePath.xml".
2. Cambiar el nodo RecoveryPlan Id, nombre, PrimaryServerId y SecondaryServerId.
3. Cambiar el nodo ProtectionEntity PrimaryProtectionEntityId (vmid desde VMM).
4. Puede agregar más VM agregando más nodos de ProtectionEntity.



        <#
        <?xml version="1.0" encoding="utf-16"?>
        <RecoveryPlan Id="d0323b26-5be2-471b-addc-0a8742796610" Name="rp-test"  PrimaryServerId="9350a530-d5af-435b-9f2b-b941b5d9fcd5"  SecondaryServerId="21a9403c-6ec1-44f2-b744-b4e50b792387" Description=""     Version="V2014_07">
          <Actions />
          <ActionGroups>
            <ShutdownAllActionGroup Id="ShutdownAllActionGroup">
              <PreActionSequence />
              <PostActionSequence />
            </ShutdownAllActionGroup>
            <FailoverAllActionGroup Id="FailoverAllActionGroup">
              <PreActionSequence />
              <PostActionSequence />
            </FailoverAllActionGroup>
            <BootActionGroup Id="DefaultActionGroup">
              <PreActionSequence />
              <PostActionSequence />
              <ProtectionEntity PrimaryProtectionEntityId="d4c8ce92-a613-4c63-9b03- cf163cc36ef8" />
            </BootActionGroup>
          </ActionGroups>
          <ActionGroupSequence>
            <ActionGroup Id="ShutdownAllActionGroup" ActionId="ShutdownAllActionGroup"  Before="FailoverAllActionGroup" />
            <ActionGroup Id="FailoverAllActionGroup" ActionId="FailoverAllActionGroup"  After="ShutdownAllActionGroup" Before="DefaultActionGroup" />
            <ActionGroup Id="DefaultActionGroup" ActionId="DefaultActionGroup" After="FailoverAllActionGroup"/>
          </ActionGroupSequence>
        </RecoveryPlan>
        #>



4. Rellene los datos en la plantilla:


        $TemplatePath = "C:\RPTemplatePath.xml";



5. Crear la RecoveryPlan:

        $RPCreationJob = New-AzureSiteRecoveryRecoveryPlan -File $TemplatePath -WaitForCompletion;



### <a name="run-a-test-failover"></a>Ejecutar un error de prueba

1.  Obtener el objeto RecoveryPlan, ejecute el siguiente comando:

        $RPObject = Get-AzureSiteRecoveryRecoveryPlan -Name $RPName;


2.  Inicie la prueba de migración tras error, ejecute el siguiente comando:


        $jobIDResult = Start-AzureSiteRecoveryTestFailoverJob -RecoveryPlan $RPObject -Direction PrimaryToRecovery;


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

[Obtenga más información](https://msdn.microsoft.com/library/dn850420.aspx) acerca de los cmdlets de PowerShell de recuperación de sitio de Azure. </a>.
