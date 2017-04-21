<properties 
    pageTitle="Implementar máquinas virtuales de Windows | Microsoft Azure" 
    description="Describe cómo implementar máquinas virtuales de Windows para mitigar problemas de conexión RDP." 
    services="virtual-machines-windows" 
    documentationCenter="virtual-machines" 
    authors="iainfoulds" 
    manager="timlt"
    tags="azure-resource-manager,top-support-issue" 
/>
    

<tags 
    ms.service="virtual-machines-windows" 
    ms.devlang="na" 
    ms.topic="support-article" 
    ms.tgt_pltfrm="vm-windows"
    ms.workload="infrastructure" 
    ms.date="09/19/2016" 
    ms.author="iainfou" 
/>


# <a name="redeploy-virtual-machine-to-new-azure-node"></a>Implementar la máquina virtual para el nuevo nodo de Azure

Si ha sido opuestas dificultades solución de problemas de escritorio remoto (RDP) acceso de conexión o aplicación a basado en Windows Azure máquina virtual (), volver a implementar la máquina virtual puede ayudar. Al implementar una máquina virtual, se mueve la máquina virtual a un nuevo nodo dentro de la infraestructura de Azure y, a continuación, la potencia en, conservar todas las opciones de configuración y recursos asociados. Este artículo le muestra cómo volver a implementar una máquina virtual con Azure PowerShell o el portal de Azure.

> [AZURE.NOTE] Después de implementar una máquina virtual, se pierde el disco temporal y se actualizan las direcciones IP dinámicas asociadas a la interfaz de red virtual. 

## <a name="using-azure-powershell"></a>Uso de PowerShell de Azure

Asegúrese de que tiene la última Azure PowerShell 1.x instalado en su equipo. Para obtener más información, vea [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md).

Utilice este comando de Azure PowerShell para implementar la máquina virtual:

    Set-AzureRmVM -Redeploy -ResourceGroupName $rgname -Name $vmname 


[AZURE.INCLUDE [virtual-machines-common-redeploy-to-new-node](../../includes/virtual-machines-common-redeploy-to-new-node.md)]


## <a name="next-steps"></a>Pasos siguientes
Si está teniendo problemas para conectarse a su máquina virtual, puede encontrar ayuda específica sobre [solución de problemas de conexiones RDP](virtual-machines-windows-troubleshoot-rdp-connection.md) o [RDP detallada pasos de solución de problemas](virtual-machines-windows-detailed-troubleshoot-rdp.md). Si no puede acceder a una aplicación que se ejecuta en la máquina virtual, también puede leer [solucionando problemas de la aplicación](virtual-machines-windows-troubleshoot-app-connection.md).