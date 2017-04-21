<properties
    pageTitle="Cambiar el tamaño de una máquina virtual de Windows clásico | Microsoft Azure"
    description="Cambiar el tamaño de una máquina virtual de Windows creada en el modelo de implementación clásica con Powershell de Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="Drewm3"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="drewm"/>


# <a name="resize-a-windows-vm-created-in-the-classic-deployment-model"></a>Cambiar el tamaño de una máquina virtual de Windows creados en el modelo de implementación clásica

Este artículo le muestra cómo cambiar el tamaño de una máquina virtual de Windows, creado en el modelo de implementación clásica con Powershell de Azure.

Cuando considere la posibilidad de cambiar el tamaño de una máquina virtual, hay dos conceptos que controlan la gama de tamaños disponibles para cambiar el tamaño de la máquina virtual. El primer concepto es la región en la que se implementa la máquina virtual. Es la lista de tamaños VM disponibles en la región en la pestaña servicios de la página web de regiones de Azure. El concepto de la segundo es el hardware físico hospedando su máquina virtual. Los servidores físicos máquinas virtuales de hospedaje se agrupan en clústeres de hardware físico comunes. El método de cambio de tamaño de memoria virtual varía dependiendo de si el nuevo tamaño VM deseado es compatible con el clúster de hardware hospedando la máquina virtual.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]También puede [cambiar el tamaño de una máquina virtual que creó en el modelo de implementación de administrador de recursos](virtual-machines-windows-resize-vm.md).


## <a name="add-your-account"></a>Agregar su cuenta

Configure Azure PowerShell para trabajar con recursos de Azure clásicos. Siga los pasos siguientes para configurar Azure PowerShell para administrar recursos clásicos.

1. En el símbolo del sistema de PowerShell, escriba `Add-AzureAccount` y haga clic en **ENTRAR**. 
2. Escriba la dirección de correo electrónico asociada a su suscripción de Azure y haga clic en **continuar**. 
3. Escriba la contraseña de su cuenta. 
4. Haga clic en **iniciar sesión**. 


## <a name="resize-in-the-same-hardware-cluster"></a>Cambiar el tamaño en el mismo clúster de hardware

Para cambiar el tamaño de una máquina virtual a un tamaño disponible en el clúster de hardware la máquina virtual de hospedaje, realice los pasos siguientes.

1. Ejecute el siguiente comando PowerShell para enumerar los tamaños VM disponibles en el clúster de hardware que aloja el servicio de nube que contiene la máquina virtual.

    ```powershell
    Get-AzureService | where {$_.ServiceName -eq "<cloudServiceName>"}
    ```

2. Ejecute los comandos siguientes para cambiar el tamaño de la máquina virtual.

    ```powershell
    Get-AzureVM -ServiceName <cloudServiceName> -Name <vmName> | Set-AzureVMSize -InstanceSize <newVMSize> | Update-AzureVM
    ```

## <a name="resize-on-a-new-hardware-cluster"></a>Cambiar el tamaño en un nuevo clúster de hardware

Cambiar el tamaño de una máquina virtual a un tamaño no está disponible en el clúster de hardware la máquina virtual de la nube de hospedaje deben reconstruirse servicio y todas las máquinas virtuales en el servicio de nube. Cada servicio de nube está hospedado en un clúster de hardware única para que todas las máquinas virtuales en el servicio de nube deben tener un tamaño que es compatible en un clúster de hardware. Los pasos siguientes describen cómo cambiar el tamaño de una máquina virtual eliminando y, a continuación, volver a crear el servicio de nube.

1. Ejecute el siguiente comando PowerShell para los tamaños VM disponibles en la región de la lista. 

    ```powershell
    Get-AzureLocation | where {$_.Name -eq "<locationName>"}
    ```

2. Tome nota de todos los valores de configuración para cada VM en el servicio de nube que contiene la máquina virtual para cambiar de tamaño. 
3. Eliminar todas las máquinas virtuales en el servicio de nube seleccionando la opción para conservar los discos para cada máquina virtual.
4. Vuelva a crear la máquina virtual para cambiar de tamaño con el tamaño de la máquina virtual deseado.
5. Vuelva a crear todas las demás VM que estaba en el servicio de nube con un tamaño de memoria virtual disponible en el clúster de hardware ahora se hospeda el servicio de nube.

Puede encontrar una secuencia de comandos de ejemplo para eliminar y volver a crear un servicio de nube con un nuevo tamaño VM [aquí](https://github.com/Azure/azure-vm-scripts). 


## <a name="next-steps"></a>Pasos siguientes

- [Cambiar el tamaño de una máquina virtual que creó en el modelo de implementación de administrador de recursos](virtual-machines-windows-resize-vm.md).