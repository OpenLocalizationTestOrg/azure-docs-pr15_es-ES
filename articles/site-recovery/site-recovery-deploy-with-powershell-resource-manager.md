<properties
    pageTitle="Proteger los servidores de Azure con PowerShell de Azure con el Administrador de recursos de Azure | Microsoft Azure"
    description="Automatizar la protección de servidores para Azure con Azure sitio recuperación mediante PowerShell y el Administrador de recursos de Azure."
    services="site-recovery"
    documentationCenter=""
    authors="bsiva"
    manager="abhiag"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="backup-recovery"
    ms.date="09/27/2016"
    ms.author="bsiva"/>

# <a name="replicate-between-on-premises-hyper-v-virtual-machines-and-azure-by-using-powershell-and-azure-resource-manager"></a>Replicar entre máquinas virtuales de Hyper-V de local y Azure mediante PowerShell y el Administrador de recursos de Azure

> [AZURE.SELECTOR]
- [Portal de Azure](site-recovery-hyper-v-site-to-azure.md)
- [PowerShell - Administrador de recursos](site-recovery-deploy-with-powershell-resource-manager.md)
- [Portal clásica](site-recovery-hyper-v-site-to-azure-classic.md)



## <a name="overview"></a>Información general

Recuperación de sitio de Azure contribuye a su estrategia de recuperación de desastres y continuidad empresarial por coordinar replicación, migración tras error y recuperación de máquinas virtuales de un número de escenarios de implementación. Para obtener una lista completa de escenarios de implementación, vea la [Introducción a la recuperación de sitio de Azure](site-recovery-overview.md).

PowerShell Azure es un módulo que proporciona cmdlets para administrar Azure a través de Windows PowerShell. Puede trabajar con dos tipos de módulos: el módulo Azure perfil o el módulo de administrador de recursos de Azure.

Cmdlets de PowerShell de recuperación de sitios, disponibles con Azure PowerShell para el Administrador de recursos de Azure, ayudar a proteger y recuperar los servidores en Azure.

Este artículo describe cómo usar Windows PowerShell, junto con el Administrador de recursos de Azure, para implementar la recuperación de sitio para configurar y coordinar la protección del servidor en Azure. En el ejemplo se usa en este artículo le muestra cómo proteger, conmutar y recuperar máquinas virtuales en un host de Hyper-V para Azure, con PowerShell de Azure con el Administrador de recursos de Azure.

> [AZURE.NOTE] Los cmdlets de PowerShell de recuperación de sitio actualmente le permiten configurar lo siguiente: un sitio de administrador de la máquina Virtual a otro, un sitio de administrador de la máquina Virtual en Azure y un sitio de Hyper-V para Azure.

No es necesario ser un experto de PowerShell para usar este artículo, pero necesita conocer los conceptos básicos, como los módulos, cmdlets y sesiones. Para obtener más información acerca de Windows PowerShell, vea [Introducción a Windows PowerShell](http://technet.microsoft.com/library/hh857337.aspx).

También puede obtener más información sobre cómo [Usar PowerShell de Azure con el Administrador de recursos de Azure](../powershell-azure-resource-manager.md).

> [AZURE.NOTE] Los partners de Microsoft que forman parte del programa de proveedor de soluciones de nube (CSP) pueden configurar y administrar protección de servidores de sus clientes para suscripciones de CSP respectivos de sus clientes (inquilino suscripciones).

## <a name="before-you-start"></a>Antes de empezar

Asegúrese de que tiene estos requisitos previos en su lugar:

- Una cuenta de [Microsoft Azure](https://azure.microsoft.com/) . Puede empezar con una [versión de prueba gratuita](https://azure.microsoft.com/pricing/free-trial/). Además, puede leer sobre [precios de administrador de recuperación del sitio de Azure](https://azure.microsoft.com/pricing/details/site-recovery/).
- Azure PowerShell 1.0. Para obtener información acerca de esta versión y cómo instalarlo, consulte [Azure PowerShell 1.0.](https://azure.microsoft.com/)
- Los módulos [AzureRM.SiteRecovery](https://www.powershellgallery.com/packages/AzureRM.SiteRecovery/) y [AzureRM.RecoveryServices](https://www.powershellgallery.com/packages/AzureRM.RecoveryServices/) . Puede obtener las últimas versiones de estos módulos de la [Galería de PowerShell](https://www.powershellgallery.com/)

Este artículo muestra cómo usar Powershell Azure con el Administrador de recursos de Azure para configurar y administrar la protección de los servidores. En el ejemplo se usa en este artículo se muestra cómo proteger una máquina virtual, que se ejecuta en un host de Hyper-V para Azure. Los siguientes requisitos previos son específicos de este ejemplo. Para un conjunto más amplio de requisitos para las distintas situaciones de recuperación de sitios, consulte la documentación correspondiente a ese escenario.

- Un host de Hyper-V que ejecutan Windows Server 2012 R2 o que contiene una o más máquinas virtuales de Microsoft Hyper-V Server 2012 R2.
- Servidores Hyper-V conectan a Internet, ya sea directa o a través de un servidor proxy.
- Las máquinas virtuales que desea proteger deben cumplir con [los requisitos previos de máquina Virtual](site-recovery-best-practices.md#virtual-machines).


## <a name="step-1-sign-in-to-your-azure-account"></a>Paso 1: Iniciar sesión en su cuenta de Azure


1. Abrir una consola de PowerShell y ejecutar este comando para iniciar sesión en su cuenta de Azure. El cmdlet abre una página web que le solicitará las credenciales de cuenta.

        Login-AzureRmAccount

    Como alternativa, también puede incluir las credenciales de cuenta como parámetro de la `Login-AzureRmAccount` cmdlet, usando la `-Credential` parámetro.

    Si está asociado CSP trabajar en nombre de un inquilino, especifique al cliente como un inquilino a través del nombre de dominio principal tenantID o inquilino.

        Login-AzureRmAccount -Tenant "fabrikam.com"

2. Una cuenta puede tener varias suscripciones, por lo que debe asociar a la suscripción que desea usar con la cuenta.

        Select-AzureRmSubscription -SubscriptionName $SubscriptionName

3.  Compruebe que la suscripción está registrada para usar los proveedores de Azure para servicios de recuperación y recuperación del sitio, mediante los comandos siguientes:

    - `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices`
    -  `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery`

    En el resultado de estos comandos, si se establece la **RegistrationState** **registrado**, puede continuar con el paso 2. Si no es así, debe registrar el proveedor que faltan en su suscripción.

    Para registrar el proveedor de Azure recuperación de sitios y servicios de recuperación, ejecute los comandos siguientes:

        Register-AzureRmResourceProvider -ProviderNamespace Microsoft.SiteRecovery
        Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices

    Comprobar que los proveedores registran correctamente mediante los siguientes comandos: `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices` y `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery`.



## <a name="step-2-set-up-the-recovery-services-vault"></a>Paso 2: Configurar lo servicios de recuperación de cámara

1. Crear un grupo de recursos de administrador de recursos de Azure en el que deberá crear la cámara o usar un grupo de recursos existente. Puede crear un nuevo grupo de recursos con el siguiente comando:

        New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Geo  

    donde la variable $ResourceGroupName contiene el nombre del grupo de recursos que desea crear y la variable $Geo contiene el área de Azure en la que desea crear el grupo de recursos (por ejemplo, "Brasil Sur").

    Puede obtener una lista de grupos de recursos en la suscripción mediante la `Get-AzureRmResourceGroup` cmdlet.

2. Crear un nuevo depósito de servicios de recuperación de Azure como sigue:

        $vault = New-AzureRmRecoveryServicesVault -Name <string> -ResourceGroupName <string> -Location <string>

    Puede recuperar una lista de depósitos existentes mediante la `Get-AzureRmRecoveryServicesVault` cmdlet.

> [AZURE.NOTE] Si desea realizar operaciones en depósitos de recuperación de sitios creados con el portal clásico o el módulo de PowerShell de administración del servicio de Azure, puede recuperar una lista de dichos depósitos mediante la `Get-AzureRmSiteRecoveryVault` cmdlet. Debe crear un nuevo depósito de servicios de recuperación para todas las operaciones nuevos. Los depósitos de recuperación de sitios que ha creado anteriormente son compatibles, pero no tienen las características más recientes.

## <a name="step-3-set-the-recovery-services-vault-context"></a>Paso 3: Configurar el contexto de la cámara de servicios de recuperación

1.  Establecer el contexto de la cámara, ejecute el siguiente comando:

        Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault

## <a name="step-4-create-a-hyper-v-site-and-generate-a-new-vault-registration-key-for-the-site"></a>Paso 4: Crear un sitio de Hyper-V y generar una nueva clave de registro de la cámara para el sitio.

1. Crear un nuevo sitio de Hyper-V como sigue:

        $sitename = "MySite"                #Specify site friendly name
        New-AzureRmSiteRecoverySite -Name $sitename

    Este cmdlet inicia un trabajo de recuperación de sitios para crear el sitio y devuelve un objeto de trabajo de recuperación de sitios. Espere a que el trabajo completar y compruebe que la tarea que se completó correctamente.

    Puede recuperar el objeto de trabajo y, con ello, comprobar el estado actual de la tarea, mediante el cmdlet Get-AzureRmSiteRecoveryJob.
2. Generar y descargar una clave de registro para el sitio, como sigue:

        $SiteIdentifier = Get-AzureRmSiteRecoverySite -Name $sitename | Select -ExpandProperty SiteIdentifier
        Get-AzureRmRecoveryServicesVaultSettingsFile -Vault $vault -SiteIdentifier $SiteIdentifier -SiteFriendlyName $sitename -Path $Path

    Copiar la clave descargada en el host de Hyper-V. Necesita la clave para registrar el host de Hyper-V para el sitio.

## <a name="step-5-install-the-azure-site-recovery-provider-and-azure-recovery-services-agent-on-your-hyper-v-host"></a>Paso 5: Instalar el proveedor de servicios de recuperación de sitios de Azure y el agente de servicios de recuperación de Azure en el host de Hyper-V

1. Descargar al instalador de la versión más reciente del proveedor de [Microsoft](https://aka.ms/downloaddra).

2. Ejecutar el programa de instalación en el host de Hyper-V y al final de la instalación continúe con el paso de registro.

3. Cuando se le solicite, proporcione la clave de registro de sitio descargados y completar el registro del host Hyper-V para el sitio.

4. Compruebe que el host de Hyper-V está registrado en el sitio mediante el siguiente comando:

        $server =  Get-AzureRmSiteRecoveryServer -FriendlyName $server-friendlyname

## <a name="step-6-create-a-replication-policy-and-associate-it-with-the-protection-container"></a>Paso 6: Crear una directiva de replicación y asociarlo con el contenedor de protección

1. Crear una directiva de replicación de la siguiente manera:

        $ReplicationFrequencyInSeconds = "300";     #options are 30,300,900
        $PolicyName = “replicapolicy”
        $Recoverypoints = 6                 #specify the number of recovery points
        $storageaccountID = Get-AzureRmStorageAccount -Name "mystorea" -ResourceGroupName "MyRG" | Select -ExpandProperty Id

        $PolicyResult = New-AzureRmSiteRecoveryPolicy -Name $PolicyName -ReplicationProvider “HyperVReplicaAzure” -ReplicationFrequencyInSeconds $ReplicationFrequencyInSeconds  -RecoveryPoints $Recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId $storageaccountID

    Compruebe el trabajo devuelto para asegurarse de que la creación de la directiva de replicación se realiza correctamente.

    >[AZURE.IMPORTANT] La cuenta de almacenamiento especificada debe estar en la misma región Azure como su depósito de servicios de recuperación y debería tener replicación geo habilitada.
    >
    > - Si la cuenta de almacenamiento de recuperación especificada es de tipo almacenamiento de Azure (clásico), errores de los equipos protegidos recuperar la máquina a Azure IaaS (clásico).
    > - Si la cuenta de almacenamiento de recuperación especificada es de tipo Azure almacenamiento (Administrador de recursos de Azure), errores de los equipos protegidos recuperar la máquina a Azure IaaS (Administrador de recursos de Azure).

2. Obtener el contenedor de protección correspondiente al sitio, como sigue:

        $protectionContainer = Get-AzureRmSiteRecoveryProtectionContainer
3.  Iniciar la asociación del contenedor de protección con la directiva de replicación, como sigue:

        $Policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $PolicyName
        $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy $Policy -PrimaryProtectionContainer $protectionContainer

    Espere a completar el trabajo de asociación y asegúrese de que completó correctamente.

##<a name="step-7-enable-protection-for-virtual-machines"></a>Paso 7: Habilitar la protección de máquinas virtuales

1. Obtener la entidad de protección correspondiente a la máquina virtual que desea proteger, como sigue:

        $VMFriendlyName = "Fabrikam-app"                    #Name of the VM
        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

2. Empezar a proteger el equipo virtual, como sigue:

        $Ostype = "Windows"                                 # "Windows" or "Linux"
        $DRjob = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionEntity -Policy $Policy -Protection Enable -RecoveryAzureStorageAccountId $storageaccountID  -OS $OStype -OSDiskName $protectionEntity.Disks[0].Name

    >[AZURE.IMPORTANT] La cuenta de almacenamiento especificada debe estar en la misma región Azure como su depósito de servicios de recuperación y debería tener replicación geo habilitada.
    >
    > - Si la cuenta de almacenamiento de recuperación especificada es de tipo almacenamiento de Azure (clásico), errores de los equipos protegidos recuperar la máquina a Azure IaaS (clásico).
    > - Si la cuenta de almacenamiento de recuperación especificada es de tipo Azure almacenamiento (Administrador de recursos de Azure), errores de los equipos protegidos recuperar la máquina a Azure IaaS (Administrador de recursos de Azure).

    > Si la máquina virtual que desea proteger tiene más de un disco adjunto, especifique el disco de sistema operativo mediante el parámetro *OSDiskName* .

3. Espere a que los equipos virtuales llegar a un estado protegido después de la replicación inicial. Esto puede tardar, dependiendo de factores como la cantidad de datos que se pueden replicar y el ancho de banda precede a Azure. El estado del trabajo y EstadoDescripción se actualizan como se indica a continuación, en la máquina virtual de alcanzar un estado protegido.

        PS C:\> $DRjob = Get-AzureRmSiteRecoveryJob -Job $DRjob

        PS C:\> $DRjob | Select-Object -ExpandProperty State
        Succeeded

        PS C:\> $DRjob | Select-Object -ExpandProperty StateDescription
        Completed

4. Actualizar propiedades de recuperación, como el tamaño de la función de memoria virtual y la red de Azure adjuntar tarjetas de interfaz de red de la máquina virtual a tras la migración tras error.

        PS C:\> $nw1 = Get-AzureRmVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

        PS C:\> $VMFriendlyName = "Fabrikam-App"

        PS C:\> $VM = Get-AzureRmSiteRecoveryVM -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

        PS C:\> $UpdateJob = Set-AzureRmSiteRecoveryVM -VirtualMachine $VM -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

        PS C:\> $UpdateJob = Get-AzureRmSiteRecoveryJob -Job $UpdateJob

        PS C:\> $UpdateJob


        Name             : b8a647e0-2cb9-40d1-84c4-d0169919e2c5
        ID               : /Subscriptions/a731825f-4bf2-4f81-a611-c331b272206e/resourceGroups/MyRG/providers/Microsoft.RecoveryServices/vault
                           s/MyVault/replicationJobs/b8a647e0-2cb9-40d1-84c4-d0169919e2c5
        Type             : Microsoft.RecoveryServices/vaults/replicationJobs
        JobType          : UpdateVmProperties
        DisplayName      : Update the virtual machine
        ClientRequestId  : 805a22a3-be86-441c-9da8-f32685673112-2015-12-10 17:55:51Z-P
        State            : Succeeded
        StateDescription : Completed
        StartTime        : 10-12-2015 17:55:53 +00:00
        EndTime          : 10-12-2015 17:55:54 +00:00
        TargetObjectId   : 289682c6-c5e6-42dc-a1d2-5f9621f78ae6
        TargetObjectType : ProtectionEntity
        TargetObjectName : Fabrikam-App
        AllowedActions   : {Restart}
        Tasks            : {UpdateVmPropertiesTask}
        Errors           : {}



## <a name="step-8-run-a-test-failover"></a>Paso 8: Ejecutar un error de prueba

1. Ejecutar un trabajo de migración tras error de prueba, como sigue:

        $nw = Get-AzureRmVirtualNetwork -Name "TestFailoverNw" -ResourceGroupName "MyRG" #Specify Azure vnet name and resource group

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -FriendlyName $VMFriendlyName -ProtectionContainer $protectionContainer

        $TFjob = Start-AzureRmSiteRecoveryTestFailoverJob -ProtectionEntity $protectionEntity -Direction PrimaryToRecovery -AzureVMNetworkId $nw.Id

2. Compruebe que la prueba VM se crea en Azure. (El trabajo de migración tras error de prueba se suspende, después de crear la prueba VM en Azure. Complete el trabajo por limpiar los efectos creados en reanudar el trabajo, tal como se muestra en el siguiente paso.)

3. Complete la migración tras error de prueba, como sigue:

        $TFjob = Resume-AzureRmSiteRecoveryJob -Job $TFjob


##<a name="next-steps"></a>Pasos siguientes

[Obtenga más información](https://msdn.microsoft.com/library/azure/mt637930.aspx) acerca de la recuperación de sitio de Azure con los cmdlets de PowerShell de administrador de recursos de Azure.
