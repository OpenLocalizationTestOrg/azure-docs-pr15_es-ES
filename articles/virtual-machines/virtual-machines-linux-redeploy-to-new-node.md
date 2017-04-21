<properties 
    pageTitle="Implementar máquinas virtuales Linux | Microsoft Azure" 
    description="Describe cómo implementar máquinas virtuales de Linux para mitigar problemas de conexión SSH." 
    services="virtual-machines-linux" 
    documentationCenter="virtual-machines" 
    authors="iainfoulds" 
    manager="timlt"
    tags="azure-resource-manager,top-support-issue" 
/>
    

<tags 
    ms.service="virtual-machines-linux" 
    ms.devlang="na" 
    ms.topic="support-article" 
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure" 
    ms.date="09/19/2016" 
    ms.author="iainfou" 
/>

# <a name="redeploy-virtual-machine-to-new-azure-node"></a>Implementar la máquina virtual para el nuevo nodo de Azure

Si tiene ha opuestas dificultades SSH de solución de problemas o acceso de la aplicación a una máquina virtual Azure (VM), volver a implementar la máquina virtual puede ayudar. Al implementar una máquina virtual, se mueve la máquina virtual a un nuevo nodo dentro de la infraestructura de Azure y, a continuación, la potencia en, conservar todas las opciones de configuración y recursos asociados. Este artículo le muestra cómo volver a implementar una máquina virtual con Azure CLI o el portal de Azure.

> [AZURE.NOTE] Después de implementar una máquina virtual, se pierde el disco temporal y se actualizan las direcciones IP dinámicas asociadas a la interfaz de red virtual. 


## <a name="using-azure-cli"></a>Utilice CLI Azure

Asegúrese de que tiene la [Última CLI de Azure instalado](../xplat-cli-install.md) en su equipo y se encuentra en modo de administrador de recursos (`azure config mode arm`).

Use el siguiente comando CLI Azure para implementar la máquina virtual:

```bash
azure vm redeploy --resourcegroup <resourcegroup> --vm-name <vmname> 
```

Puede ver el estado de cambio de máquina virtual al pasar por el proceso de bola. La `PowerState` de la máquina virtual pasa del 'Running' 'Actualización' después 'inicio' y por último 'ejecución' al pasar por el proceso de implementar a un nuevo host. Comprobar el estado de las máquinas virtuales dentro de un grupo de recursos con:

```bash
azure vm list -g <resourcegroup>
```


[AZURE.INCLUDE [virtual-machines-common-redeploy-to-new-node](../../includes/virtual-machines-common-redeploy-to-new-node.md)]


## <a name="next-steps"></a>Pasos siguientes
Si está teniendo problemas para conectarse a su máquina virtual, puede encontrar ayuda específica sobre [solución de problemas de conexiones de SSH](virtual-machines-linux-troubleshoot-ssh-connection.md) o [SSH detallada pasos de solución de problemas](virtual-machines-linux-detailed-troubleshoot-ssh-connection.md). Si no puede acceder a una aplicación que se ejecuta en la máquina virtual, también puede leer [solucionando problemas de la aplicación](virtual-machines-linux-troubleshoot-app-connection.md).