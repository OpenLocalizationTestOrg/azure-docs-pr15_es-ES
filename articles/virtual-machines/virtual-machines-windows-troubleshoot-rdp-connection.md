<properties
    pageTitle="No puede RDP a una máquina virtual de Azure | Microsoft Azure"
    description="Solucionar problemas cuando no puede conectarse a su equipo virtual de Windows en Azure mediante Escritorio remoto"
    keywords="Error de escritorio remoto, error de conexión a Escritorio remoto, no puede conectarse a máquina virtual, solución de problemas de escritorio remoto"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="support-article"
    ms.date="10/26/2016"
    ms.author="iainfou"/>

# <a name="troubleshoot-remote-desktop-connections-to-an-azure-virtual-machine"></a>Solucionar problemas de conexión de escritorio remoto a una máquina virtual Azure

La conexión de protocolo de escritorio remoto (RDP) a su basado en Windows Azure máquina virtual (VM) puede fallar por varios motivos, lo que no se puede acceder a su máquina virtual. Puede ser el problema con el servicio de escritorio remoto en la máquina virtual, la conexión de red o en el cliente de escritorio remoto en el equipo host. En este artículo le guía a través de algunos de los métodos más comunes para resolver problemas de conexión RDP. 

Si necesita más ayuda en cualquier punto de este artículo, póngase en contacto con los expertos de Azure en [los foros de desbordamiento de pila y Azure de MSDN](https://azure.microsoft.com/support/forums/). Como alternativa, puede abrir un incidente de soporte de Azure. Vaya el [sitio de soporte de Azure](https://azure.microsoft.com/support/options/) y seleccione **Obtener soporte técnico**.

<a id="quickfixrdp"></a>

## <a name="quick-troubleshooting-steps"></a>Pasos rápidos de solución de problemas
Después de cada paso de solución de problemas, intente volver a conectarse a la máquina virtual:

1. Restablecer la configuración de escritorio remoto.
2. Comprobar el grupo de seguridad de red reglas / Cloud Services extremos.
3. Revise los registros de la consola de máquina virtual.
4. Comprobar el estado del recurso de máquina virtual.
5. Restablecer la contraseña de la máquina virtual.
6. Reinicie la máquina virtual.
7. Volver a la máquina virtual.

Si necesita instrucciones más detalladas y explicaciones, continúe leyendo.

> [AZURE.TIP] Si el botón **Conectar** para su máquina virtual está atenuado en el portal y no está conectado a Azure a través de una conexión [VPN de sitio a sitio](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) o [Express ruta](../expressroute/expressroute-introduction.md) , debe crear y asignar una dirección IP pública de la máquina virtual para poder usar RDP. Puede obtener más información acerca de [las direcciones IP públicas en Azure](../virtual-network/virtual-network-ip-addresses-overview-arm.md).


## <a name="ways-to-troubleshoot-rdp-issues"></a>Formas para solucionar problemas de RDP
Puede solucionar máquinas virtuales creadas con el modelo de implementación de administrador de recursos mediante uno de los métodos siguientes:

- [Portal de azure](#using-the-azure-portal) - excelente si necesita restablecer rápidamente las credenciales de usuario o configuración de RDP y no tiene instaladas las herramientas de Azure.
- [Azure PowerShell](#using-azure-powershell) - si se siente cómodo con un símbolo del sistema de PowerShell, restablecer rápidamente las credenciales de usuario o configuración de RDP mediante los cmdlets de PowerShell de Azure.

También puede encontrar pasos en máquinas virtuales creadas con el [modelo de implementación de clásico](#troubleshoot-vms-created-using-the-classic-deployment-model)de solución de problemas.


<a id="fix-common-remote-desktop-errors"></a>
## <a name="troubleshoot-using-the-azure-portal"></a>Solucionar problemas con el portal de Azure
Después de cada paso de solución de problemas, intente volver a conectarse a su máquina virtual. Si aún no puede conectarse, pruebe el siguiente paso.

1. **Restablecer su conexión RDP**. Este paso de solución de problemas restablece la configuración de RDP cuando se deshabilitan las conexiones remotas o las reglas de Firewall de Windows están bloqueando RDP, por ejemplo.

    Seleccione la máquina virtual en el portal de Azure. Desplácese por el panel de configuración de la sección de **soporte técnico + solución de problemas** cerca de la parte inferior de la lista. Haga clic en el botón **Restablecer la contraseña** . Establezca el **modo de** restablecer **solo la configuración** y, a continuación, haga clic en el botón de **actualización** :

    ![Restablecer la configuración de RDP en el portal de Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/reset-rdp.png)

2. **Reglas de comprobar el grupo de seguridad de red**. Este paso de solución de problemas, comprueba que tiene una regla en el grupo de seguridad de la red para permitir el tráfico RDP. El puerto predeterminado para RDP es el puerto TCP 3389. Una regla para permitir el tráfico RDP no se crean automáticamente cuando se crea la máquina virtual.

    Seleccione la máquina virtual en el portal de Azure. Haga clic en las **interfaces de red** desde el panel Configuración.

    ![Interfaces de red de la vista de una máquina virtual en el portal de Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/select-network-interfaces.png)

    Seleccione la interfaz de red de la lista (normalmente sólo hay una):

    ![Seleccione la interfaz de red en el portal de Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/select-interface.png)

    Seleccione el **grupo de seguridad de red** para ver el grupo de seguridad de la red asociada a la interfaz de red:

    ![Seleccione grupo de seguridad de red en el portal de Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/select-nsg.png)

    Compruebe que existe una regla entrante que permite el tráfico RDP en el puerto TCP 3389. En el ejemplo siguiente se muestra una regla de seguridad válido que permite tráfico RDP. Puede ver `Service` y `Action` están configurados correctamente:

    ![Comprobar RDP NSG regla en el portal de Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/verify-nsg-rules.png)

    Si no tiene una regla que permite el tráfico RDP, [crear una regla de grupo de seguridad de red](virtual-machines-windows-nsg-quickstart-portal.md). Permitir el puerto TCP 3389.

3. **Diagnósticos de inicio de máquina virtual de revisión**. Este paso de solución de problemas revisa los registros de la consola de máquina virtual para determinar si la máquina virtual es informar de un problema. No todas máquinas virtuales tienen diagnósticos de arranque habilitadas, de modo que este paso de solución de problemas puede ser opcional.
    
    Pasos de solución de problemas específicos quedan fuera del ámbito de este artículo, pero pueden indicar un problema mayor que afecta la conectividad RDP. Para obtener más información acerca de cómo revisar los registros de consola y la captura de pantalla VM, vea [Diagnósticos de inicio para máquinas virtuales](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/).

4. **Comprobar el estado del recurso de máquina virtual**. Este paso de solución de problemas comprueba que no existen problemas conocidos con la plataforma de Windows Azure que pueden afectar al conectividad con la máquina virtual.

    Seleccione la máquina virtual en el portal de Azure. Desplácese por el panel de configuración de la sección de **soporte técnico + solución de problemas** cerca de la parte inferior de la lista. Haga clic en el botón de **estado de los recursos** . Una buen estada VM informes como **disponibles**:

    ![Comprobar el estado de los recursos en el portal de Azure VM](./media/virtual-machines-windows-troubleshoot-rdp-connection/check-resource-health.png)

5. **Restablecer las credenciales de usuario**. Este paso de solución de problemas restablece la contraseña de una cuenta de administrador local cuando no está seguro o ha olvidado las credenciales.

    Seleccione la máquina virtual en el portal de Azure. Desplácese por el panel de configuración de la sección de **soporte técnico + solución de problemas** cerca de la parte inferior de la lista. Haga clic en el botón **Restablecer la contraseña** . Asegúrese de que el **modo** está establecido para **Restablecer la contraseña** y, a continuación, escriba el nombre de usuario y una contraseña nueva. Por último, haga clic en el botón de **actualización** :

    ![Restablecer las credenciales de usuario en el portal de Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/reset-password.png)

6. **Reinicie la máquina virtual**. Este paso de solución de problemas puede corregir problemas subyacentes que está teniendo la máquina virtual propiamente dicho.

    Seleccione la máquina virtual en el portal de Azure y haga clic en la ficha **Visión general** . Haga clic en el botón **reiniciar** :

    ![Reinicie la máquina virtual en el portal de Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/restart-vm.png)

7. **Volver a implementar la máquina virtual**. Este paso de solución de problemas implementa de nuevo la máquina virtual a otro host Azure para corregir problemas de red o de la plataforma subyacente.

    Seleccione la máquina virtual en el portal de Azure. Desplácese por el panel de configuración de la sección de **soporte técnico + solución de problemas** cerca de la parte inferior de la lista. Haga clic en el botón **Redistribuir** y, a continuación, haga clic en **volver a instalar**:

    ![Volver a implementar la VM en el portal de Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/redeploy-vm.png)

    Cuando termine esta operación, se pierden datos efímeros disco y se actualizan las direcciones IP dinámicas que están asociadas a la máquina virtual.

Si aún tiene problemas RDP, puede [Abrir una solicitud de soporte técnico](https://azure.microsoft.com/support/options/) o leer [conceptos más detallados de solución de problemas de RDP y pasos](virtual-machines-windows-detailed-troubleshoot-rdp.md).


## <a name="troubleshoot-using-azure-powershell"></a>Solucionar problemas con PowerShell de Azure
Si no lo ha hecho ya, [instale y configure la última PowerShell de Azure](../powershell-install-configure.md).

Los ejemplos siguientes utilizan variables como `myResourceGroup`, `myVM`, y `myVMAccessExtension`. Reemplace los nombres de variable y ubicaciones con sus propios valores.

> [AZURE.NOTE] Restablecer las credenciales de usuario y la configuración de RDP mediante el cmdlet de PowerShell [Conjunto AzureRmVMAccessExtension](https://msdn.microsoft.com/library/mt619447.aspx) . En los ejemplos siguientes, `myVMAccessExtension` es un nombre que se especifique como parte del proceso. Si previamente ha trabajado con la VMAccessAgent, puede obtener el nombre de la extensión existente usando `Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"` para comprobar las propiedades de la máquina virtual. Para ver el nombre, busque en la sección 'Extensiones' del resultado.

Después de cada paso de solución de problemas, intente volver a conectarse a su máquina virtual. Si aún no puede conectarse, pruebe el siguiente paso.

1. **Restablecer su conexión RDP**. Este paso de solución de problemas restablece la configuración de RDP cuando se deshabilitan las conexiones remotas o las reglas de Firewall de Windows están bloqueando RDP, por ejemplo.

    En el siguiente ejemplo se restablece la conexión RDP en una máquina virtual denominada `myVM` en la `WestUS` ubicación y, en el grupo de recursos denominado `myResourceGroup`:

    ```powershell
    Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location Westus -Name "myVMAccessExtension"
    ```

2. **Reglas de comprobar el grupo de seguridad de red**. Este paso de solución de problemas, comprueba que tiene una regla en el grupo de seguridad de la red para permitir el tráfico RDP. El puerto predeterminado para RDP es el puerto TCP 3389. Una regla para permitir el tráfico RDP no se crean automáticamente cuando se crea la máquina virtual.

    En primer lugar, asignar todos los datos de configuración para el grupo de seguridad de la red para la `$rules` variable. En el ejemplo siguiente se obtiene información sobre el grupo de seguridad de red denominado `myNetworkSecurityGroup` en el grupo de recursos denominado `myResourceGroup`:

    ```powershell
    $rules = Get-AzureRmNetworkSecurityGroup -ResourceGroupName "myResourceGroup" `
        -Name "myNetworkSecurityGroup"
    ```

    Ahora, ver las reglas que están configuradas para este grupo de seguridad de la red. Compruebe que existe una regla para permitir que el puerto TCP 3389 para las conexiones entrantes como sigue:

    ```powershell
    $rules.SecurityRules
    ```

    En el ejemplo siguiente se muestra una regla de seguridad válido que permite tráfico RDP. Puede ver `Protocol`, `DestinationPortRange`, `Access`, y `Direction` están configurados correctamente:

    ```powershell
    Name                     : default-allow-rdp
    Id                       : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/default-allow-rdp
    Etag                     : 
    ProvisioningState        : Succeeded
    Description              : 
    Protocol                 : TCP
    SourcePortRange          : *
    DestinationPortRange     : 3389
    SourceAddressPrefix      : *
    DestinationAddressPrefix : *
    Access                   : Allow
    Priority                 : 1000
    Direction                : Inbound
    ```

    Si no tiene una regla que permite el tráfico RDP, [crear una regla de grupo de seguridad de red](virtual-machines-windows-nsg-quickstart-powershell.md). Permitir el puerto TCP 3389.

3. **Restablecer las credenciales de usuario**. Este paso de solución de problemas restablece la contraseña de la cuenta de administrador local que especifique cuando no está seguro de o ha olvidado las credenciales.

    En primer lugar, especifique el nombre de usuario y una contraseña nueva mediante la asignación de credenciales para la `$cred` variable como sigue:

    ```powershell
    $cred=Get-Credential
    ```

    Ahora, actualice las credenciales de la máquina virtual. En el ejemplo siguiente se actualiza las credenciales en una máquina virtual denominada `myVM` en la `WestUS` ubicación y, en el grupo de recursos denominado `myResourceGroup`:

    ```powershell
    Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location WestUS -Name "myVMAccessExtension" `
        -UserName $cred.GetNetworkCredential().Username `
        -Password $cred.GetNetworkCredential().Password
    ```

4. **Reinicie la máquina virtual**. Este paso de solución de problemas puede corregir problemas subyacentes que está teniendo la máquina virtual propiamente dicho.

    En el ejemplo siguiente se reinicia la máquina virtual denominada `myVM` en el grupo de recursos denominado `myResourceGroup`:

    ```powershell
    Restart-AzureRmVM -ResourceGroup "myResourceGroup" -Name "myVM"
    ```

5. **Volver a implementar la máquina virtual**. Este paso de solución de problemas implementa de nuevo la máquina virtual a otro host Azure para corregir problemas de red o de la plataforma subyacente.

    En el ejemplo siguiente se implementa de nuevo la máquina virtual denominada `myVM` en la `WestUS` ubicación y, en el grupo de recursos denominado `myResourceGroup`:

    ```powershell
    Set-AzureRmVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

Si aún tiene problemas RDP, puede [Abrir una solicitud de soporte técnico](https://azure.microsoft.com/support/options/) o leer [conceptos más detallados de solución de problemas de RDP y pasos](virtual-machines-windows-detailed-troubleshoot-rdp.md).


## <a name="troubleshoot-vms-created-using-the-classic-deployment-model"></a>Solucionar problemas de máquinas virtuales creadas con el modelo de implementación de clásico

Después de cada paso de solución de problemas, intente volver a conectarse a la máquina virtual.

1. **Restablecer su conexión RDP**. Este paso de solución de problemas restablece la configuración de RDP cuando se deshabilitan las conexiones remotas o las reglas de Firewall de Windows están bloqueando RDP, por ejemplo.

    Seleccione la máquina virtual en el portal de Azure. Haga clic en el **... Más** , a continuación, haga clic en **Restablecer acceso remoto**:

    ![Restablecer la configuración de RDP en el portal de Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/classic-reset-rdp.png)

2.  **Comprobar los servicios de nube extremos**. Este paso de solución de problemas, comprueba que tiene extremos en los servicios de nube para permitir el tráfico RDP. El puerto predeterminado para RDP es el puerto TCP 3389. Una regla para permitir el tráfico RDP no se crean automáticamente cuando se crea la máquina virtual.

    Seleccione la máquina virtual en el portal de Azure. Haga clic en el botón **extremos** para ver los extremos están configurados para su máquina virtual. Compruebe que existen extremos que permitir el tráfico RDP en el puerto TCP 3389.
    
    En el ejemplo siguiente se muestra extremos válidos que permitan el tráfico RDP:

    ![Comprobar los extremos de servicios en la nube en el portal de Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/classic-verify-cloud-services-endpoints.png)

    Si no tiene un punto final que permite tráfico RDP, [crear un extremo de servicios en la nube](virtual-machines-windows-classic-setup-endpoints.md). Permitir TCP al puerto privado 3389.

3. **Diagnósticos de inicio de máquina virtual de revisión**. Este paso de solución de problemas revisa los registros de la consola de máquina virtual para determinar si la máquina virtual es informar de un problema. No todas máquinas virtuales tienen diagnósticos de arranque habilitadas, de modo que este paso de solución de problemas puede ser opcional.
    
    Pasos de solución de problemas específicos quedan fuera del ámbito de este artículo, pero pueden indicar un problema mayor que afecta la conectividad RDP. Para obtener más información acerca de cómo revisar los registros de consola y la captura de pantalla VM, vea [Diagnósticos de inicio para máquinas virtuales](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/).

4. **Comprobar el estado del recurso de máquina virtual**. Este paso de solución de problemas comprueba que no existen problemas conocidos con la plataforma de Windows Azure que pueden afectar al conectividad con la máquina virtual.

    Seleccione la máquina virtual en el portal de Azure. Desplácese por el panel de configuración de la sección de **soporte técnico + solución de problemas** cerca de la parte inferior de la lista. Haga clic en el botón de **Estado de los recursos** . Una buen estada VM informes como **disponibles**:

    ![Comprobar el estado de los recursos en el portal de Azure VM](./media/virtual-machines-windows-troubleshoot-rdp-connection/classic-check-resource-health.png)

5. **Restablecer las credenciales de usuario**. Este paso de solución de problemas restablece la contraseña de la cuenta de administrador local que especifique cuando no está seguro o ha olvidado las credenciales.

    Seleccione la máquina virtual en el portal de Azure. Desplácese por el panel de configuración de la sección de **soporte técnico + solución de problemas** cerca de la parte inferior de la lista. Haga clic en el botón **Restablecer la contraseña** . Escriba el nombre de usuario y una contraseña nueva. Por último, haga clic en el botón **Guardar** :

    ![Restablecer las credenciales de usuario en el portal de Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/classic-reset-password.png)

6. **Reinicie la máquina virtual**. Este paso de solución de problemas puede corregir problemas subyacentes que está teniendo la máquina virtual propiamente dicho.

    Seleccione la máquina virtual en el portal de Azure y haga clic en la ficha **Visión general** . Haga clic en el botón **reiniciar** :

    ![Reinicie la máquina virtual en el portal de Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/classic-restart-vm.png)
    
Si aún tiene problemas RDP, puede [Abrir una solicitud de soporte técnico](https://azure.microsoft.com/support/options/) o leer [conceptos más detallados de solución de problemas de RDP y pasos](virtual-machines-windows-detailed-troubleshoot-rdp.md).


## <a name="troubleshoot-specific-rdp-errors"></a>Solucionar errores RDP específicos
Puede encontrar un mensaje de error específico cuando intenta conectarse a su máquina virtual a través de RDP. Los siguientes son los mensajes de error más comunes:

- [La sesión remota se ha desconectado porque no hay ningún servidor de licencias de escritorio remoto disponibles para proporcionar una licencia](virtual-machines-windows-troubleshoot-specific-rdp-errors.md#rdplicense).
- [Escritorio remoto no puede encontrar el equipo "nombre"](virtual-machines-windows-troubleshoot-specific-rdp-errors.md#rdpname).
- Se ha producido error [una autenticación. No puede ponerse en contacto con la autoridad de seguridad Local](virtual-machines-windows-troubleshoot-specific-rdp-errors.md#rdpauth).
- [Error de seguridad de Windows: las credenciales no funcionaron](virtual-machines-windows-troubleshoot-specific-rdp-errors.md#wincred).
- [Este equipo no puede conectarse al equipo remoto](virtual-machines-windows-troubleshoot-specific-rdp-errors.md#rdpconnect).


## <a name="additional-resources"></a>Recursos adicionales
Si ninguno de estos errores ocurrieron y aún no puede conectarse a la máquina virtual a través de escritorio remoto, lea la [Guía para escritorio remoto](virtual-machines-windows-detailed-troubleshoot-rdp.md)detallada.

- [Paquete de diagnósticos de Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)
- Para pasos para tener acceso a aplicaciones que se ejecutan en una máquina virtual de solución de problemas, consulte [solucionar problemas de acceso a una aplicación que se ejecuta en una máquina virtual de Azure](virtual-machines-linux-troubleshoot-app-connection.md).
- Si está teniendo problemas con Secure Shell (SSH) para conectarse a una VM Linux en Azure, consulte [solucionar problemas de SSH conexiones a una máquina virtual de Linux en Azure](virtual-machines-linux-troubleshoot-ssh-connection.md).
