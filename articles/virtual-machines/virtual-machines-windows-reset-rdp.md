<properties
    pageTitle="Restablecer la contraseña o la configuración de escritorio remoto en una máquina virtual de Windows | Microsoft Azure"
    description="Obtenga información sobre cómo restablecer la contraseña de una cuenta o servicios de escritorio remoto en una máquina virtual de Windows mediante el portal de Azure o PowerShell de Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/01/2016"
    ms.author="iainfou"/>

# <a name="how-to-reset-the-remote-desktop-service-or-its-login-password-in-a-windows-vm"></a>Cómo restablecer la contraseña de inicio de sesión en una máquina virtual de Windows o el servicio de escritorio remoto

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Si no puede conectarse a una máquina virtual de Windows (VM), puede restablecer la contraseña de administrador local o restablecer la configuración del servicio de escritorio remoto. Puede usar el portal de Azure o la extensión de acceso de VM en Azure PowerShell para restablecer la contraseña. Si está utilizando PowerShell, asegúrese de tener instalado en el equipo de trabajo el módulo de PowerShell más reciente y ha iniciado sesión su suscripción de Azure. Para conocer los pasos detallados, lea [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md).

> [AZURE.TIP] Puede comprobar la versión de PowerShell que se han instalado mediante`Import-Module Azure, AzureRM; Get-Module Azure, AzureRM | Format-Table Name, Version`

## <a name="windows-vms-in-resource-manager-deployment-model"></a>Máquinas virtuales de Windows en el modelo de implementación de administrador de recursos

### <a name="azure-portal"></a>Portal de Azure
Seleccione la máquina virtual haciendo clic en **Examinar** > **máquinas virtuales** > *la máquina virtual de Windows* > **toda la configuración** > **Restablecer la contraseña**. Se muestra el módulo de restablecimiento de contraseña:

![Página de restablecimiento de contraseña](./media/virtual-machines-windows-reset-rdp/Portal-RM-PW-Reset-Windows.png)

Escriba el nombre de usuario y una contraseña nueva y luego haga clic en **Guardar**. Intente volver a conectarse a su máquina virtual.

### <a name="vmaccess-extension-and-powershell"></a>Extensión de VMAccess y PowerShell

Asegúrese de que tiene Azure PowerShell 1.0 o una versión posterior instalada y que ha iniciado sesión con su cuenta mediante la `Login-AzureRmAccount` cmdlet.

#### <a name="reset-the-local-administrator-account-password"></a>**Restablecer la contraseña de la cuenta de administrador local**

Puede restablecer el nombre de usuario o contraseña de administrador mediante el comando [Conjunto AzureRmVMAccessExtension](https://msdn.microsoft.com/library/mt619447.aspx) PowerShell.

Crear su administrador local credenciales de la cuenta mediante el siguiente comando:

    $cred=Get-Credential

Si escribe un nombre distinto de la cuenta actual, el siguiente comando de extensión VMAccess cambia el nombre de la cuenta de administrador local, asigna la contraseña a esa cuenta y emite un evento de cierre de sesión de escritorio remoto. Si la cuenta de administrador local está deshabilitada, la extensión VMAccess la habilita.

Usar la extensión de acceso a VM para establecer las credenciales nuevas como sigue:

    Set-AzureRmVMAccessExtension -ResourceGroupName "myRG" -VMName "myVM" -Name "myVMAccess" `
        -Location WestUS -UserName $cred.GetNetworkCredential().Username `
        -Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"


Reemplazar `myRG`, `myVM`, `myVMAccess`y la ubicación con los valores correspondientes en la configuración.


#### <a name="reset-the-remote-desktop-service-configuration"></a>**Restablecer la configuración del servicio de escritorio remoto**

Puede restablecer acceso remoto a su máquina virtual mediante [Conjunto AzureRmVMExtension](https://msdn.microsoft.com/library/mt603745.aspx) o [AzureRmVMAccessExtension de conjunto](https://msdn.microsoft.com/library/mt619447.aspx), como sigue. (Reemplazar el `myRG`, `myVM`, `myVMAccess` y ubicación con sus propios valores.)

    Set-AzureRmVMExtension -ResourceGroupName "myRG" -VMName "myVM" `
        -Name "myVMAccess" -ExtensionType "VMAccessAgent" -Location WestUS `
        -Publisher "Microsoft.Compute" -typeHandlerVersion "2.0"

O:<br>

    Set-AzureRmVMAccessExtension -ResourceGroupName "myRG" -VMName "myVM" `
        -Name "myVMAccess" -Location WestUS -typeHandlerVersion "2.0


> [AZURE.TIP] Ambos comandos agregan a un nuevo agente de acceso VM con nombre en la máquina virtual. En cualquier momento, una máquina virtual puede tener solo un único agente de acceso de máquina virtual. Para configurar el acceso de VM propiedades de agente correctamente, quite el agente de acceso definido previamente usando `Remove-AzureRmVMAccessExtension` o `Remove-AzureRmVMExtension`. A partir de Azure PowerShell versión 1.2.2, puede omitir este paso al usar `Set-AzureRmVMExtension` con una `-ForceRerun` opción. Cuando se usa `-ForceRerun`, asegúrese de usar el mismo nombre para el agente de acceso VM según esté definido por el comando anterior.

Si aún no puede conectarse remotamente a su equipo virtual, consulte los pasos más para probar en [conexiones de solución de problemas de escritorio remoto a un equipo de virtual basado en Windows Azure](virtual-machines-windows-troubleshoot-rdp-connection.md).


## <a name="windows-vms-in-the-classic-deployment-model"></a>Máquinas virtuales de Windows en el modelo de implementación clásica

### <a name="azure-portal"></a>Portal de Azure

Para las máquinas virtuales creadas con el modelo de implementación clásico, puede usar el [portal de Azure](https://portal.azure.com) para restablecer el servicio de escritorio remoto. Haga clic en: **Examinar** > **máquinas virtuales de Windows (clásico)** > *la máquina virtual de Windows* > **Restablecer remoto...**. Aparece la página siguiente.

![Restablecer página de configuración de RDP](./media/virtual-machines-windows-reset-rdp/Portal-RDP-Reset-Windows.png)

También puede intentar restablecer el nombre y la contraseña de la cuenta de administrador local. Haga clic en: **Examinar** > **máquinas virtuales de Windows (clásico)** > *la máquina virtual de Windows* > **toda la configuración** > **Restablecer la contraseña**. Aparece la página siguiente.

![Página de restablecimiento de contraseña](./media/virtual-machines-windows-reset-rdp/Portal-PW-Reset-Windows.png)

Después de escribir el nuevo nombre de usuario y la contraseña, haga clic en **Guardar**.

### <a name="vmaccess-extension-and-powershell"></a>Extensión de VMAccess y PowerShell

Asegúrese de que está instalado el agente de máquina virtual en la máquina virtual. La extensión VMAccess no debe instalarse antes de usarla, siempre y cuando el agente de VM está disponible. Compruebe que ya está instalado el agente de VM mediante el comando siguiente. (Reemplace "myCloudService" y "myVM" por el nombre de su servicio de nube y la máquina virtual, respectivamente. Puede obtener estos nombres ejecutando `Get-AzureVM` sin parámetros.)

    $vm = Get-AzureVM -ServiceName "myCloudService" -Name "myVM"
    write-host $vm.VM.ProvisionGuestAgent

Si el comando de **escritura host** muestra **True**, está instalado el agente de máquina virtual. Si muestra **False**, consulte las instrucciones y un vínculo a la descarga en la entrada de blog de Azure [VM agente y extensiones - parte 2](http://go.microsoft.com/fwlink/p/?linkid=403947&clcid=0x409) .

Si ha creado la máquina virtual a través del portal, compruebe si `$vm.GetInstance().ProvisionGuestAgent` devuelve **True**. Si no es así, puede configurarlo con este comando:

    $vm.GetInstance().ProvisionGuestAgent = $true

Este comando impide el siguiente error cuando se está ejecutando el comando de **Conjunto AzureVMExtension** en los pasos siguientes: "Agente de aprovisionamiento invitado debe estar habilitado en el objeto de la máquina virtual antes de establecer la extensión de acceso de VM IaaS."

#### <a name="reset-the-local-administrator-account-password"></a>**Restablecer la contraseña de la cuenta de administrador local**

Crear una credencial de inicio de sesión con el nombre de cuenta de administrador local actual y una nueva contraseña y vuelva a ejecutar la `Set-AzureVMAccessExtension` como sigue.

    $cred=Get-Credential
    Set-AzureVMAccessExtension –vm $vm -UserName $cred.GetNetworkCredential().Username `
        -Password $cred.GetNetworkCredential().Password  | Update-AzureVM

Si escribe un nombre distinto de la cuenta actual, la extensión VMAccess cambia el nombre de la cuenta de administrador local, asigna la contraseña a esa cuenta y problemas de una sesión de escritorio remoto. Si la cuenta de administrador local está deshabilitada, la extensión VMAccess la habilita.

Estos comandos también restablecer la configuración del servicio de escritorio remoto.

#### <a name="reset-the-remote-desktop-service-configuration"></a>**Restablecer la configuración del servicio de escritorio remoto**

Para restablecer la configuración del servicio de escritorio remoto, ejecute el siguiente comando:

    Set-AzureVMAccessExtension –vm $vm | Update-AzureVM

La extensión VMAccess ejecuta dos comandos en la máquina virtual:

- `netsh advfirewall firewall set rule group="Remote Desktop" new enable=Yes`

Este comando permite que el grupo de Firewall de Windows integrado que permite el tráfico entrante escritorio remoto, que usa el puerto TCP 3389.

- `Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0`

Este comando establece la fDenyTSConnections valor del registro a 0, habilitar las conexiones de escritorio remoto.


## <a name="next-steps"></a>Pasos siguientes

Si no responde la extensión de acceso a Azure VM y no puede restablecer la contraseña, puede [Restablecer la contraseña de Windows local sin conexión](virtual-machines-windows-reset-local-password-without-agent.md). Este método es un proceso más avanzado y requiere que se conecte el disco duro virtual de VM problemático a otra VM. Siga los pasos documentados en este artículo en primer lugar y solo intentar el método de restablecimiento de contraseña sin conexión como último recurso.

[Características y extensiones de azure VM](virtual-machines-windows-extensions-features.md)

[Conectarse a una máquina virtual Azure con RDP o SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx)

[Solucionar problemas de conexión de escritorio remoto a un equipo de virtual basado en Windows Azure](virtual-machines-windows-troubleshoot-rdp-connection.md)
