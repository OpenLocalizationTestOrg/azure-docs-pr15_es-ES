<properties
    pageTitle="Replicar máquinas virtuales de Hyper-V en nubes VMM en un sitio secundario de VMM con PowerShell (jefe de recursos) | Microsoft Azure"
    description="Describe cómo implementar Azure recuperación de sitio para coordinar replicación, migración tras error y recuperación de máquinas virtuales de Hyper-V en nubes de VMM en un sitio secundario de VMM con PowerShell (jefe de recursos)"
    services="site-recovery"
    documentationCenter=""
    authors="sujaytalasila"
    manager="rochakm"
    editor="raynew"/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/19/2016"
    ms.author="sutalasi"/>

# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-a-secondary-vmm-site-using-powershell-resource-manager"></a>Replicar máquinas virtuales de Hyper-V en nubes VMM en un sitio secundario de VMM con PowerShell (jefe de recursos)

> [AZURE.SELECTOR]
- [Portal de Azure](site-recovery-vmm-to-vmm.md)
- [Portal clásica](site-recovery-vmm-to-vmm-classic.md)
- [PowerShell - Administrador de recursos](site-recovery-vmm-to-vmm-powershell-resource-manager.md)

Bienvenido a Azure sitio recuperación! Utilice este artículo si desea replicar máquinas virtuales de Hyper-V de local administrado en nubes de System Center Virtual Machine Manager (VMM) a un sitio secundario. 

Este artículo le muestra cómo usar PowerShell para automatizar tareas comunes que se debe realizar al configurar Azure sitio recuperación para replicar máquinas virtuales de Hyper-V en nubes de System Center VMM en nubes de System Center VMM en un sitio secundario.

El artículo incluye los requisitos previos para el escenario y se muestra 

- Cómo configurar un depósito de servicios de recuperación
- Instalar el proveedor de servicios de recuperación de sitio de Azure en el servidor de origen VMM y el servidor VMM de destino
- Registrar los servidores VMM en la caja fuerte
- Configurar la directiva de replicación de la nube de VMM. La configuración de replicación de la directiva se aplicará a todos los equipos virtuales protegidos 
- Habilitar la protección de los equipos virtuales. 
- Probar la migración tras error de máquinas virtuales de forma individual o como parte de un plan de recuperación para asegurarse de que todo funciona según lo esperado.
- Realizar una planeadas o una migración tras error planificado de VM individualmente o como parte de un plan de recuperación para asegurarse de que todo funciona según lo esperado.

Si tiene problemas de configuración de este escenario, publique sus preguntas en el [Foro de servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

> [AZURE.NOTE] Azure tiene dos distintos [modelos de implementación](../resource-manager-deployment-model.md) para crear y trabajar con recursos: Administrador de recursos de Azure y clásica. Azure también tiene dos portales – el portal clásico Azure que admita el modelo de implementación clásica y el portal de Azure con soporte para ambos modelos de implementación. En este artículo trata sobre el modelo de implementación de administrador de recursos.



## <a name="on-premises-prerequisites"></a>Requisitos previos de local

Esto es lo tiene en los sitios primarios y secundarios local para implementar este escenario:

**Requisitos previos** | **Detalles** 
--- | ---
**VMM** | Se recomienda que implementar un servidor VMM en el sitio principal y un servidor VMM en el sitio secundario.<br/><br/> También puede [replicar entre nubes en un único servidor VMM](site-recovery-single-vmm.md). Para ello, necesitará al menos dos nubes configurados en el servidor VMM.<br/><br/> Servidores VMM deben ejecutar al menos System Center 2012 SP1 con las últimas actualizaciones.<br/><br/> Cada servidor VMM debe tener uno o más nubes configurados y en todas las nubes deben tener el perfil de capacidad de Hyper-V establecer. <br/><br/>Nubes deben contener uno o varios grupos de host VMM.<br/><br/>Más información sobre la configuración de nubes de VMM en [configuración de la estructura de la nube VMM](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric), y [Tutorial: crear nubes privadas con System Center 2012 SP1 VMM](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx).<br/><br/> Servidores VMM deben tener acceso a internet. 
**Hyper-V** | Servidores Hyper-V deben ejecutar al menos Windows Server 2012 con la función Hyper-V y han instalado las últimas actualizaciones.<br/><br/> Un servidor de Hyper-V debe contener uno o más máquinas virtuales.<br/><br/>  Servidores de host de Hyper-V deben ubicarse en grupos de host de las nubes VMM primario y secundarios.<br/><br/> Si está ejecutando Hyper-V en un clúster de Windows Server 2012 R2 debe instalar la [actualización 2961977](https://support.microsoft.com/kb/2961977)<br/><br/> Si está ejecutando Hyper-V en un clúster de Windows Server 2012 Nota ese agente de clúster no se crea automáticamente si tiene un clúster de basado en la dirección IP estático. Deberá configurar al agente de clúster manualmente. [Obtenga más información](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx).
**Proveedor** | Durante la implementación de recuperación de sitios instalar el proveedor de servicios de recuperación de Azure sitio en servidores VMM. El proveedor se comunica con sitio de recuperación sobre HTTPS 443 para organizar la replicación. Se produce la replicación de datos entre los servidores de Hyper-V primario y secundarios en la LAN o una conexión VPN.<br/><br/> El proveedor se ejecuta en el servidor VMM necesita obtener acceso a estas direcciones URL: *. hypervrecoverymanager.windowsazure.com; *. AccessControl.Windows.NET; *. backup.windowsazure.com; *. BLOB.Core.Windows.NET; *. store.core.windows.net.<br/><br/> Además, permiten la comunicación de firewall desde los servidores VMM a los [intervalos de IP del centro de datos de Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) y permitir el protocolo HTTPS (443).

### <a name="network-mapping-prerequisites"></a>Requisitos previos de asignación de red
Mapas de asignación de red entre redes de VMM VM en los servidores VMM principales y secundarios para:

- Coloque óptima máquinas virtuales de réplica en hosts Hyper-V secundarios después de la migración.
- Conectar máquinas virtuales de réplica a redes VM correspondientes.
- Si no configura réplica de asignación de red que máquinas virtuales no se pueden conectar a cualquier red después de la migración.
- Si desea configurar la red asignación durante la recuperación de sitio de implementación aquí es lo que necesita:

    - Asegúrese de que máquinas virtuales en el servidor de host de Hyper-V de origen están conectadas a una red de VM VMM. Red debe vincularse a una red lógica que esté asociada a la nube.
    - Compruebe que la nube secundaria que usará para la recuperación tiene configurada una red VM correspondiente. Red VM debe vincularse a una red lógica que está asociada a la nube secundaria.


Más información acerca de cómo configurar redes VMM en la debajo de artículos

- [Cómo configurar redes lógicas en VMM](http://go.microsoft.com/fwlink/p/?LinkId=386307)
- [Cómo configurar puertas de enlace y redes de máquina virtual en VMM](http://go.microsoft.com/fwlink/p/?LinkId=386308)

[Más información](site-recovery-network-mapping.md) acerca de cómo funciona la asignación de red.

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

1. Crear un grupo de recursos de administrador de recursos de Azure si aún no tiene una

        New-AzureRmResourceGroup -Name #ResourceGroupName -Location #location

2. Crear un nuevo depósito de servicios de recuperación y guardar el objeto de depósito ASR creado en una variable (se utilizará más adelante). También puede recuperar la creación de entrada del objeto de depósito ASR mediante el cmdlet Get-AzureRMRecoveryServicesVault:-

        $vault = New-AzureRmRecoveryServicesVault -Name #vaultname -ResouceGroupName #ResourceGroupName -Location #location 

## <a name="step-3-set-the-recovery-services-vault-context"></a>Paso 3: Configurar el contexto de la cámara de servicios de recuperación

1.  Si tiene un depósito ya ha creado, ejecute el debajo de comando para obtener la cámara.

        $vault = Get-AzureRmRecoveryServicesVault -Name #vaultname

2.  Establecer el contexto de la cámara ejecutando el debajo de comando.

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


## <a name="step-5-create-and-associate-a-replication-policy"></a>Paso 5: Crear y asociar una directiva de replicación

1.  Crear una directiva de replicación de Hyper-V 2012 R2 ejecutando el siguiente comando:

    
        $ReplicationFrequencyInSeconds = "300";     #options are 30,300,900
        $PolicyName = “replicapolicy”
        $RepProvider = HyperVReplica2012R2
        $Recoverypoints = 24                    #specify the number of hours to retain recovery pints
        $AppConsistentSnapshotFrequency = 4 #specify the frequency (in hours) at which app consistent snapshots are taken
        $AuthMode = "Kerberos"  #options are "Kerberos" or "Certificate"
        $AuthPort = "8083"  #specify the port number that will be used for replication traffic on Hyper-V hosts
        $InitialRepMethod = "Online" #options are "Online" or "Offline"

        $policyresult = New-AzureRmSiteRecoveryPolicy -Name $policyname -ReplicationProvider $RepProvider -ReplicationFrequencyInSeconds $Replicationfrequencyinseconds -RecoveryPoints $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours $AppConsistentSnapshotFrequency -Authentication $AuthMode -ReplicationPort $AuthPort -ReplicationMethod $InitialRepMethod 

    > [AZURE.NOTE] La nube VMM puede contener hosts Hyper-V con diferentes versiones de Windows Server (como se mencionó en los requisitos previos de Hyper-V), pero la directiva de replicación es la versión del sistema operativo específica. Si tiene hosts diferentes versiones de sistema operativo diferente, a continuación, crear directivas de replicación independiente para cada tipo de versión del SO. Para, por ejemplo: si tiene cinco hosts que se ejecutan en servidores de Windows 2012 y tres en Windows Server 2012 R2, crear dos directivas de replicación: uno para cada tipo de versiones de sistema operativo.

2.  Obtener el contenedor de protección principal (nube de VMM principal) y el contenedor de protección de recuperación (recuperación VMM nube), ejecute los comandos siguientes:
    
        $PrimaryCloud = "testprimarycloud"
        $primaryprotectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  

        $RecoveryCloud = "testrecoverycloud"
        $recoveryprotectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $RecoveryCloud;  
  
3.  Recuperar la directiva que creó en el paso 1 con el nombre descriptivo de la directiva

        $policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $policyname

4.  Iniciar la asociación del contenedor de protección (VMM nube) con la directiva de replicación:

        $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $primaryprotectionContainer -RecoveryProtectionContainer $recoveryprotectionContainer

5.  Espere a que el trabajo de asociación de directiva se complete. Puede comprobar si el trabajo ha terminado de usar el siguiente fragmento de PowerShell.
   
        $job = Get-AzureRmSiteRecoveryJob -Job $associationJob
        if($job -eq $null -or $job.StateDescription -ne "Completed")
         {
            $isJobLeftForProcessing = $true;
        }

    Después de que el trabajo ha finalizado el proceso, ejecute el siguiente comando:

        if($isJobLeftForProcessing)
        {
        Start-Sleep -Seconds 60
        }
        }While($isJobLeftForProcessing)


Para comprobar la finalización de la operación, siga los pasos de [Supervisar la actividad](#monitor).

## <a name="step-5-configure-network-mapping"></a>Paso 5: Configurar la asignación de red

1. El primer comando obtiene servidores para el depósito de Azure recuperación del sitio actual. El comando almacena los servidores de recuperación de sitio de Microsoft Azure en la variable de matriz $Servers.

        $Servers = Get-AzureRmSiteRecoveryServer

2. La debajo de comandos de obtener la red de recuperación de sitio para el servidor de origen VMM y el servidor VMM de destino.

        $PrimaryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[0]        

        $RecoveryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[1]

    
    > [AZURE.NOTE] Puede ser el servidor de origen VMM primero o el segundo de la matriz de servidores. Comprobar los nombres de los servidores VMM y obtener las redes correctamente


4. El cmdlet final crea una asignación entre la red principal y la red de recuperación. El cmdlet especifica la red principal como el primer elemento de la red de recuperación como el primer elemento de $RecoveryNetworks e $PrimaryNetworks.

        New-AzureRmSiteRecoveryNetworkMapping -PrimaryNetwork $PrimaryNetworks[0] -RecoveryNetwork $RecoveryNetworks[0]

## <a name="step-6-configure-storage-mapping"></a>Paso 6: Configurar la asignación de almacenamiento

1. La debajo de comando obtiene la lista de clasificaciones de almacenamiento en $storageclassifications variable.

        $storageclassifications = Get-AzureRmSiteRecoveryStorageClassification


2. La debajo de comandos de obtener la clasificación de origen en la clasificación de variable y de destino de $SourceClassificaion en $TargetClassification variable. 

        $SourceClassificaion = $storageclassifications[0]

        $TargetClassification = $storageclassifications[1]

    
    > [AZURE.NOTE] Las clasificaciones de origen y destino pueden ser cualquier elemento de la matriz. Hacer referencia a la salida de la debajo de comando para calcular el índice de origen y destino clasificaciones de la matriz $storageclassifications. 
    
    > Get-AzureRmSiteRecoveryStorageClassification | Select-Object - propiedad FriendlyName, Id | Formato de tabla


3. La debajo de cmdlet crea una asignación entre la clasificación de origen y la clasificación de destino. 

        New-AzureRmSiteRecoveryStorageClassificationMapping -PrimaryStorageClassification $SourceClassificaion -RecoveryStorageClassification $TargetClassification

## <a name="step-7-enable-protection-for-virtual-machines"></a>Paso 7: Habilitar la protección de máquinas virtuales

Después de los servidores, nubes y redes están configuradas correctamente, puede habilitar la protección de máquinas virtuales en la nube. 


  1. Para habilitar la protección, ejecute el comando siguiente para obtener el contenedor de protección:
    
            $PrimaryProtectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloudName
    
  2. Obtener la entidad de protección (VM), ejecute el siguiente comando:
    
            $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $PrimaryProtectionContainer
        
  3. Habilitar la replicación de la máquina virtual, ejecute el siguiente comando:

            $jobResult = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable -Policy $policy


## <a name="test-your-deployment"></a>Probar la implementación

Para probar la implementación puede ejecutar un error de prueba para una sola máquina virtual, o crear un plan de recuperación que consta de varios equipos virtuales y ejecutar un error de prueba para el plan. Migración de prueba tras error simula su mecanismo de migración y recuperación en una red aislada. 

> [AZURE.NOTE] Puede crear un plan de recuperación de la aplicación en el portal de Azure.

Para comprobar la finalización de la operación, siga los pasos de [Supervisar la actividad](#monitor).


### <a name="run-a-test-failover"></a>Ejecutar un error de prueba

1.  Ejecutar la debajo de cmdlets para llegar a la red de la máquina virtual a la que desea probar migración tras error sus máquinas virtuales a.

        $Servers = Get-AzureRmSiteRecoveryServer
        $RecoveryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[1]

2.  Realizar una migración tras error de prueba de una máquina virtual haciendo lo siguiente:
    
        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -FriendlyName $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -VMNetwork $RecoveryNetworks[1] 

2.  Realizar una migración tras error de prueba de un plan de recuperación haciendo lo siguiente:
    
        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan -VMNetwork $RecoveryNetworks[1] 

### <a name="run-a-planned-failover"></a>Ejecutar una migración tras error planeada

1. Realizar una migración tras error planeada de una máquina virtual haciendo lo siguiente:
    
        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

2. Realizar una migración tras error planeada de un plan de recuperación haciendo lo siguiente:
    
        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan

### <a name="run-an-unplanned-failover"></a>Ejecutar una migración tras error planificado

1. Realizar una migración tras error no planeado de una máquina virtual haciendo lo siguiente:
        
        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity 

2.haga realizar una migración tras error planificado de un plan de recuperación haciendo lo siguiente:
        
        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity 
    
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

