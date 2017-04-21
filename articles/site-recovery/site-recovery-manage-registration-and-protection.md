<properties
    pageTitle="Quitar servidores y deshabilita la protección | Microsoft Azure" 
    description="En este artículo se describe cómo anular el registro de los servidores de un depósito de recuperación del sitio y para deshabilitar la protección de máquinas virtuales y servidores físicos." 
    services="site-recovery" 
    documentationCenter="" 
    authors="rayne-wiselman" 
    manager="jwhit" 
    editor=""/>

<tags 
    ms.service="site-recovery" 
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery" 
    ms.date="10/05/2016" 
    ms.author="raynew"/>

# <a name="remove-servers-and-disable-protection"></a>Quitar servidores y deshabilita la protección

El servicio de recuperación de sitios de Azure contribuye a su estrategia de recuperación (BCDR) de desastres y continuidad empresarial por coordinar replicación, migración tras error y recuperación de máquinas virtuales y servidores físicos. Máquinas pueden replicarse en Azure o a un centro de datos secundario en local. Para obtener una introducción rápida leer [¿Qué es la recuperación de sitio de Azure?](site-recovery-overview.md)

## <a name="overview"></a>Información general

Este artículo describe cómo eliminar del registro servidores de la cámara de recuperación de sitios y cómo deshabilitar protección para máquinas virtuales protegidos por el sitio de recuperación. 

Publicar comentarios o preguntas en la parte inferior de este artículo o en el [Foro de servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="unregister-a-vmm-server"></a>Anular el registro de un servidor VMM

Eliminar el registro de un servidor VMM desde un depósito eliminando el servidor en la pestaña **servidores** en el portal de recuperación de sitios de Azure. Tenga en cuenta que:

-  **Servidor VMM conectado**: se recomienda eliminar del registro el servidor VMM cuando está conectado a Azure. Así se garantiza que los valores en el servidor VMM local y los servidores VMM asociados (VMM los servidores que contienen nubes que se asignan a nubes en el servidor que desea eliminar) se limpian correctamente. Se recomienda que quitar solo un servidor desconectado si hay un problema con la conectividad permanente.
- **Servidor VMM desconectada**: si el servidor VMM no está conectado al eliminarla deberá ejecutar manualmente para llevar a cabo la limpieza. La secuencia de comandos está disponible en la [Galería de Microsoft](http://aka.ms/asr-cleanup-script-vmm). Nota El identificador de VMM del servidor para completar el proceso de limpieza manual.
- **Servidor VMM clúster**: si necesita anular el registro de un servidor VMM se implementa en un clúster, haga lo siguiente:

    - Si el servidor está conectado, elimine el servidor VMM conectado en la pestaña **servidores** . Para desinstalar el proveedor en el servidor, inicie sesión en cada nodo del clúster y desinstalar desde el Panel de Control. Ejecute la secuencia de comandos de limpieza que hace referencia en la sección anterior en todos los nodos pasivos en el clúster para eliminar las entradas de registro.
    - Si el servidor no está conectado debe ejecutar la secuencia de comandos de Liberador de espacio en todos los nodos de clúster.

### <a name="unregister-an-unconnected-vmm-server"></a>Anular el registro de un servidor VMM no conectado

En el servidor VMM que desea quitar:

1. Eliminar del registro el servidor VMM desde el portal de Azure.
2. En el servidor VMM, descargue la secuencia de comandos de limpieza.
3. Abra el PowerShell con la ejecución como opción de administrador para cambiar la directiva de ejecución para el ámbito predeterminado (LocalMachine).
4. Siga las instrucciones de la secuencia de comandos. 

En servidores VMM que tienen nubes que se ha sincronizado con nubes en el servidor que está quitando:

1. Ejecute la secuencia de comandos de limpieza y siga los pasos 2 a 4.
2. Especifique el identificador de VMM para el servidor VMM que se ha registrado. 
3. Esta secuencia de comandos quitará la información de registro para el servidor VMM y la información de emparejamiento de nube.


## <a name="unregister-a-hyper-v-server-in-a-hyper-v-site"></a>Anular el registro de un servidor de Hyper-V en un sitio de Hyper-V

Cuando se implementa la recuperación de sitios de Azure proteger máquinas virtuales ubicados en un servidor de Hyper-V en un sitio de Hyper-V (con ningún servidor VMM) puede eliminar del registro un servidor Hyper-V desde un depósito como sigue:

1. Desactivar la protección para máquinas virtuales ubicado en el servidor de Hyper-V.
2. En la pestaña **servidores** en el portal de Azure recuperación del sitio, seleccione el servidor > eliminar. El servidor no tiene que estar conectado a Azure al hacerlo.
3. Ejecute el siguiente script para limpiar la configuración del servidor y anular desde la cámara. 

        pushd .
        try
        {
             $windowsIdentity=[System.Security.Principal.WindowsIdentity]::GetCurrent()
             $principal=new-object System.Security.Principal.WindowsPrincipal($windowsIdentity)
             $administrators=[System.Security.Principal.WindowsBuiltInRole]::Administrator
             $isAdmin=$principal.IsInRole($administrators)
             if (!$isAdmin)
             {
                "Please run the script as an administrator in elevated mode."
                $choice = Read-Host
                return;       
             }
    
            $error.Clear()    
            "This script will remove the old Azure Site Recovery Provider related properties. Do you want to continue (Y/N) ?"
            $choice =  Read-Host
        
            if (!($choice -eq 'Y' -or $choice -eq 'y'))
            {
            "Stopping cleanup."
            return;
            }
        
            $serviceName = "dra"
            $service = Get-Service -Name $serviceName
            if ($service.Status -eq "Running")
            {
                "Stopping the Azure Site Recovery service..."
                net stop $serviceName
            }
        
            $asrHivePath = "HKLM:\SOFTWARE\Microsoft\Azure Site Recovery"
            $registrationPath = $asrHivePath + '\Registration'
            $proxySettingsPath = $asrHivePath + '\ProxySettings'
            $draIdvalue = 'DraID'
        
            if (Test-Path $asrHivePath)
            {
                if (Test-Path $registrationPath)
                {
                    "Removing registration related registry keys."  
                    Remove-Item -Recurse -Path $registrationPath
                }
    
                if (Test-Path $proxySettingsPath)
            {
                    "Removing proxy settings"
                    Remove-Item -Recurse -Path $proxySettingsPath
                }
    
                $regNode = Get-ItemProperty -Path $asrHivePath
                if($regNode.DraID -ne $null)
                {            
                    "Removing DraId"
                    Remove-ItemProperty -Path $asrHivePath -Name $draIdValue
                }
                "Registry keys removed."
            }
    
            # First retrive all the certificates to be deleted
            $ASRcerts = Get-ChildItem -Path cert:\localmachine\my | where-object {$_.friendlyname.startswith('ASR_SRSAUTH_CERT_KEY_CONTAINER') -or $_.friendlyname.startswith('ASR_HYPER_V_HOST_CERT_KEY_CONTAINER')}
            # Open a cert store object
            $store = New-Object System.Security.Cryptography.X509Certificates.X509Store("My","LocalMachine")
            $store.Open('ReadWrite')
            # Delete the certs
            "Removing all related certificates"
            foreach ($cert in $ASRcerts)
            {
                $store.Remove($cert)
            }
        }catch
        {   
            [system.exception]
            Write-Host "Error occured" -ForegroundColor "Red"
            $error[0] 
            Write-Host "FAILED" -ForegroundColor "Red"
        }
        popd


## <a name="stop-protecting-a-hyper-v-virtual-machine"></a>Detener la protección de una máquina virtual Hyper-V

Si desea dejar de proteger una máquina virtual Hyper-V debe quitar la protección de ella. Según cómo quitar la protección debe limpiar la configuración de protección manualmente en el equipo. 

### <a name="remove-protection"></a>Quitar la protección

1. En la pestaña **máquinas virtuales** de las propiedades de la nube, seleccione la máquina virtual > **Quitar**.
2. En la página **Confirmar la eliminación de máquina Virtual de** tiene dos opciones:

    - **Desactivar la protección**: Si habilita y guardar esta opción la máquina virtual ya no estarán protegida por sitio de recuperación. Configuración de protección de la máquina virtual se limpia automáticamente.
    - **Quitar de la cámara**: Si selecciona esta opción se quitarán la máquina virtual solo de la cámara de recuperación del sitio. No se verá afectado la configuración de protección local de la máquina virtual. Debe limpiar configuración manualmente para eliminar la configuración de protección y quitar la máquina virtual de la suscripción de Azure y eliminar la configuración de protección que deberá limpiar el manualmente siguiendo estas instrucciones.

Si opta por eliminar la máquina virtual y su disco duro se quitarán de la ubicación de destino.

### <a name="clean-up-protection-settings-manually-between-vmm-sites"></a>Limpiar la configuración de protección manualmente (entre sitios VMM)

Si seleccionó **Detener la administración de la máquina virtual**, limpiar manualmente configuración:

1. En el servidor principal ejecutar esta secuencia de comandos de la consola VMM para limpiar la configuración de la máquina virtual principal. En la consola VMM haga clic en el botón de PowerShell para abrir la consola de VMM PowerShell. Reemplace SQLVM1 con el nombre de la máquina virtual.

         $vm = get-scvirtualmachine -Name "SQLVM1"
         Set-SCVirtualMachine -VM $vm -ClearDRProtection

2. En el servidor VMM secundario ejecutar esta secuencia de comandos para limpiar la configuración de la máquina virtual secundaria:

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Remove-SCVirtualMachine -VM $vm -Force

3. En el servidor VMM secundario, después de ejecutar la secuencia de comandos actualizar las máquinas virtuales en el servidor de host de Hyper-V para que la máquina virtual secundaria volver a obtiene detectada en la consola VMM.
4. Los pasos anteriores desactive el servidor VMM replicación sólo de configuración. Si desea quitar la replicación de la máquina virtual para la máquina virtual. Tendrá que realizar los siguientes pasos en ambos principal y secundario máquinas virtuales de Windows. Ejecutar la debajo de secuencia de comandos para quitar la replicación y reemplace SQLVM1 por el nombre de la máquina virtual.

        Remove-VMReplication –VMName “SQLVM1”


### <a name="clean-up-protection-settings-manually-between-on-premises-vmm-sites-and-azure"></a>Limpiar la configuración de protección manualmente (entre sitios VMM local y Azure)

1. En el servidor de origen VMM ejecutar esta secuencia de comandos para limpiar la configuración de la máquina virtual principal:

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Set-SCVirtualMachine -VM $vm -ClearDRProtection

2. Los pasos anteriores desactive el servidor VMM replicación sólo de configuración. Una vez que haya quitado garantizar la replicación de servidor VMM para quitar la replicación de la máquina virtual que se ejecuta en el servidor de host de Hyper-V con esta secuencia de comandos. Reemplace SQLVM1 con el nombre de la máquina virtual y host01.contoso.com con el nombre del servidor de host de Hyper-V.

        $vmName = "SQLVM1"
        $hostName  = "host01.contoso.com"
        $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'" -computername $hostName
        $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"  -computername $hostName
        $replicationService.RemoveReplicationRelationship($vm.__PATH)

### <a name="clean-up-protection-settings-manually-between-hyper-v-sites-and-azure"></a>Limpiar la configuración de protección manualmente (entre sitios de Hyper-V y Azure)

1. En el servidor de host de Hyper-V de origen, para quitar la replicación de la máquina virtual utilice esta secuencia de comandos. Reemplace SQLVM1 con el nombre de la máquina virtual.

        $vmName = "SQLVM1"
        $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'"
        $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"
        $replicationService.RemoveReplicationRelationship($vm.__PATH)

## <a name="stop-protecting-a-vmware-virtual-machine-or-a-physical-server"></a>Detener la protección de una máquina virtual VMware o un servidor físico

Si desea dejar de proteger una máquina virtual VMware o un servidor físico debe quitar la protección de ella. Según cómo quitar la protección debe limpiar la configuración de protección manualmente en el equipo. 

### <a name="remove-protection"></a>Quitar la protección

1. En la pestaña **máquinas virtuales** de las propiedades de la nube, seleccione la máquina virtual > **Quitar**.
2. En la **Máquina Virtual de quitar** seleccione una de las opciones:

    - **Desactivar la protección (usar para tamaño de obtención de detalles y el volumen de recuperación)**, verá solo y habilite esta opción si ha:
        - **El volumen de máquina virtual de resized**: cuando cambie el tamaño de un volumen de la máquina virtual entra en un estado de tareas críticas. En este caso, seleccione esta opción. Deshabilita la protección conservando los puntos de recuperación de Azure. Al habilitar la protección de la máquina se transferirán los datos para el volumen redimensionado en Azure.
        - **Ejecutar una migración tras error**: después de que ha probado su entorno ejecutando un error de máquinas virtuales de VMware local o servidores físicos en Azure, seleccione esta opción para empezar a proteger sus máquinas virtuales de local de nuevo. Esta opción deshabilita cada máquina virtual y, a continuación, tendrá que volver a habilitar protección para ellos. Tenga en cuenta que:
            - Deshabilitación de la máquina virtual con esta configuración no afecta a la máquina virtual de réplica en Azure.
            - No desinstalar el servicio de movilidad de la máquina virtual.
    
    - **Desactivar la protección**: Si habilita y guardar esta opción el equipo ya no estarán protegido por sitio de recuperación. Configuración de protección de la máquina se limpia automáticamente.
    - **Quitar de la cámara**: Si selecciona esta opción se quitarán el equipo solo de la cámara de recuperación del sitio. No se verá afectado la configuración de protección local para el equipo. Para quitar la configuración en el equipo y quitar la máquina virtual de la Azure suscripción y deberá limpiar la configuración desinstalar el servicio de movilidad.
    
        ![Quitar opciones](./media/site-recovery-manage-registration-and-protection/remove-vm.png)

















