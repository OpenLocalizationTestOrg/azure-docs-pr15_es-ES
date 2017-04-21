<properties
    pageTitle="Migrar a la ventana del Administrador de recursos con PowerShell | Microsoft Azure"
    description="En este artículo se recorre la migración compatibles con la plataforma de IaaS recursos de clásico para el Administrador de recursos de Azure con los comandos de PowerShell de Azure"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="cynthn"/>

# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-powershell"></a>Migrar recursos de IaaS de clásico para el Administrador de recursos de Azure con PowerShell de Azure

Estos pasos le muestran cómo utilizar los comandos de PowerShell de Azure para migrar infraestructura como un recursos de servicio (IaaS) del modelo de implementación clásico para el modelo de implementación de administrador de recursos de Azure. 

Si lo desea, también puede migrar recursos utilizando la [interfaz de línea de comandos de Azure (Azure CLI)](virtual-machines-linux-cli-migration-classic-resource-manager.md).

- Para información sobre escenarios de migración admitidos, vea [migración compatibles con la plataforma de IaaS recursos clásico para el Administrador de recursos de Azure](virtual-machines-windows-migration-classic-resource-manager.md). 
- Para obtener instrucciones detalladas y un tutorial de migración, vea [análisis técnico detallado en compatibles con la plataforma de migración de clásico para el Administrador de recursos de Azure](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md).

## <a name="step-1-plan-for-migration"></a>Paso 1: Planear la migración

Estas son algunas prácticas recomendadas que se recomienda evaluar los recursos IaaS migrar desde clásico para el Administrador de recursos:

- Lea [las configuraciones y características compatibles y no compatibles](virtual-machines-windows-migration-classic-resource-manager.md). Si tiene máquinas virtuales que usan configuraciones no admitidas o características, le recomendamos que espere a que la compatibilidad de configuración o característica a anunciará. Como alternativa, si se adapte a sus necesidades, quitar dicha característica o mover fuera de que la configuración para permitir la migración.
-   Si automatizada de secuencias de comandos que implementación la infraestructura y las aplicaciones de hoy, intente crear un programa de instalación de prueba similar mediante esas secuencias de comandos para la migración. Como alternativa, puede configurar los entornos de ejemplo mediante el portal de Azure.

>[AZURE.IMPORTANT]Las puertas de enlace de red virtual (a sitios, Azure ExpressRoute, puerta de enlace de aplicación, un sitio de punto) no se admiten actualmente para la migración de clásico para el Administrador de recursos. Para migrar una red virtual clásica con una puerta de enlace, quite la puerta de enlace antes de ejecutar una operación de confirmación para mover la red (puede ejecutar el paso Preparar sin eliminar la puerta de enlace). Una vez completada la migración, vuelva a conectar la puerta de enlace en el Administrador de recursos de Azure.

## <a name="step-2-install-the-latest-version-of-azure-powershell"></a>Paso 2: Instalar la última versión de PowerShell de Azure

Existen dos opciones principales para instalar Azure PowerShell: [Galería de PowerShell](https://www.powershellgallery.com/profiles/azure-sdk/) o [Instalador de plataforma Web (WebPI)](http://aka.ms/webpi-azps). WebPI recibe actualizaciones mensuales. Galería de PowerShell recibe las actualizaciones de forma continua. En este artículo se basa en Azure PowerShell versión 2.1.0.

Para obtener instrucciones de instalación, consulte [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md).

<br>
## <a name="step-3-set-your-subscription-and-sign-up-for-migration"></a>Paso 3: Configurar la suscripción y registrarse para la migración

En primer lugar, inicie un símbolo del sistema de PowerShell. Para la migración, debe configurar el entorno para ambos clásico y el Administrador de recursos.

Inicie sesión en su cuenta para el modelo de administrador de recursos.

```powershell
    Login-AzureRmAccount
```

Obtener las suscripciones disponibles mediante el siguiente comando:

```powershell
    Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName
```

Establezca su suscripción de Azure para la sesión actual. En este ejemplo se establece el nombre de suscripción predeterminado en **Mi suscripción de Azure**. Reemplace el nombre de suscripción de ejemplo con su propio. 

```powershell
    Select-AzureRmSubscription –SubscriptionName "My Azure Subscription"
```

>[AZURE.NOTE] Registro es un paso de una sola vez, pero debe hacer una vez antes de intentar la migración. Sin registrar, verá el siguiente mensaje de error: 

>   *BadRequest: Suscripción no está registrada para la migración.* 

Registrar con el proveedor de recursos de migración, con el siguiente comando:

```powershell
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Espere cinco minutos para el registro Finalizar. Puede comprobar el estado de la aprobación mediante el comando siguiente:

```powershell
    Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Asegúrese de que es RegistrationState `Registered` antes de continuar. 

Ahora inicie sesión en su cuenta para el modelo clásico.

```powershell
    Add-AzureAccount
```

Obtener las suscripciones disponibles mediante el siguiente comando:

```powershell
    Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
```

Establezca su suscripción de Azure para la sesión actual. En este ejemplo se establece la suscripción predeterminada para **Mi suscripción de Azure**. Reemplace el nombre de suscripción de ejemplo con su propio. 

```powershell
    Select-AzureSubscription –SubscriptionName "My Azure Subscription"
```

<br>

## <a name="step-4-make-sure-you-have-enough-azure-resource-manager-virtual-machine-cores-in-the-azure-region-of-your-current-deployment-or-vnet"></a>Paso 4: Asegúrese de que tiene suficiente núcleos de máquina Virtual de administrador de recursos de Azure en la región de Azure de su implementación actual o VNET

Puede usar el siguiente comando PowerShell para comprobar el número de núcleos que tiene en el Administrador de recursos de Azure actual. Para obtener más información acerca de las cuotas de núcleo, consulte [el Administrador de recursos de Azure y límites](../azure-subscription-service-limits.md#limits-and-the-azure-resource-manager). 

Este ejemplo comprueba la disponibilidad de la región **Oeste de Estados Unidos** . Reemplace el nombre de la región de ejemplo con su propio. 

```powershell
Get-AzureRmVMUsage -Location "West US"
```

## <a name="step-5-run-commands-to-migrate-your-iaas-resources"></a>Paso 5: Ejecutar comandos para migrar los recursos de IaaS

>[AZURE.NOTE] Todas las operaciones que se describen aquí son idempotentes. Si tiene un problema distinto de una característica no admitida o un error de configuración, le recomendamos que vuelva a intentar preparar, anular o confirmar la operación. La plataforma, trata de la acción de nuevo.

### <a name="migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network"></a>Migrar máquinas virtuales de un servicio de nube (no en una red virtual)

Obtener la lista de servicios de nube mediante el comando siguiente y, a continuación, seleccione el servicio de nube que desea migrar. Si las máquinas virtuales en el servicio de nube que se encuentran en una red virtual o tienen funciones web o de trabajo, el comando devuelve un mensaje de error.

```powershell
    Get-AzureService | ft Servicename
```

Obtener el nombre de la implementación del servicio de nube. En este ejemplo, el nombre del servicio es **Mi servicio**. Reemplace el nombre de servicio de ejemplo con su propio nombre de servicio. 

```powershell
    $serviceName = "My Service"
    $deployment = Get-AzureDeployment -ServiceName $serviceName
    $deploymentName = $deployment.DeploymentName
```

Preparar las máquinas virtuales en el servicio de nube para la migración. Tiene dos opciones para elegir.

* **Opción 1. Migrar las máquinas virtuales a una red virtual creado plataforma**

    Validar en primer lugar, si puede migrar el servicio de nube mediante los siguientes comandos:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    $validate.ValidationMessages
    ```

    El comando anterior muestra las advertencias y errores que bloquear la migración. Si la validación es correcta, puede mover con el paso de **preparación** :

    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    ```

* **Opción 2. Migrar a una red virtual existente en el modelo de implementación de administrador de recursos**

    En este ejemplo se establece el nombre del grupo de recursos en **myResourceGroup**, el nombre de red virtual para **myVirtualNetwork** y el nombre de subred a **mySubNet**. Reemplace los nombres en el ejemplo por los nombres de los recursos propios.

    ```powershell
    $existingVnetRGName = "myResourceGroup"
    $vnetName = "myVirtualNetwork"
    $subnetName = "mySubNet"
    ```

    Validar en primer lugar, si puede migrar el servicio de nube mediante el siguiente comando:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName
    $validate.ValidationMessages
    ```

    El comando anterior muestra las advertencias y errores que bloquear la migración. Si la validación es correcta, puede continuar con el siguiente paso de preparación:

    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName `
        -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName `
        -VirtualNetworkName $vnetName -SubnetName $subnetName
    ```
    

Cuando se complete correctamente la operación de preparación con cualquiera de las opciones anteriores, el estado de la migración de las máquinas virtuales de consulta. Asegúrese de que están en la `Prepared` estado.

En este ejemplo se establece el nombre de la máquina virtual para **myVM**. Reemplace el nombre de ejemplo con su propio nombre de la máquina virtual.

    ```powershell
    $vmName = "myVM"
    $vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
    $vm.VM.MigrationState
    ```

Compruebe la configuración de los recursos preparados con PowerShell o en el portal de Azure. Si no está listo para la migración y desea volver al estado anterior, utilice el siguiente comando:

```powershell
    Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName
```

Si la configuración preparados es satisfactorio, puede desplazarse hacia delante y confirmar los recursos con el siguiente comando:

```powershell
    Move-AzureService -Commit -ServiceName $serviceName -DeploymentName $deploymentName
```

### <a name="migrate-virtual-machines-in-a-virtual-network"></a>Migrar máquinas virtuales de una red virtual

Para migrar máquinas virtuales de una red virtual, migrar la red. Los equipos virtuales migrar automáticamente con la red. Elija la red virtual que desea migrar. 

En este ejemplo se establece el nombre de red virtual a **myVnet**. Reemplace el nombre de red virtual de ejemplo con su propio. 

```powershell
    $vnetName = "myVnet"
```

>[AZURE.NOTE] Si la red virtual contiene web o funciones de trabajo o máquinas virtuales con configuraciones no compatibles, recibe un mensaje de error de validación.

Validar en primer lugar, si puede migrar una red virtual mediante el comando siguiente:

```powershell
    Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

El comando anterior muestra las advertencias y errores que bloquear la migración. Si la validación es correcta, puede continuar con el siguiente paso de preparación:

```powershell
    Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

Compruebe la configuración de los equipos virtuales preparados mediante PowerShell Azure o en el portal de Azure. Si no está listo para la migración y desea volver al estado anterior, utilice el siguiente comando:

```powershell
    Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

Si la configuración preparados es satisfactorio, puede desplazarse hacia delante y confirmar los recursos con el siguiente comando:

```powershell
    Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```

### <a name="migrate-a-storage-account"></a>Migrar una cuenta de almacenamiento

Cuando haya terminado de migrar los equipos virtuales, se recomienda migrar las cuentas de almacenamiento.

Prepare cada cuenta de almacenamiento para la migración mediante el siguiente comando. En este ejemplo, el nombre de la cuenta de almacenamiento es **myStorageAccount**. Reemplace el nombre de ejemplo con el nombre de su cuenta de almacenamiento. 

```powershell
    $storageAccountName = "myStorageAccount"
    Move-AzureStorageAccount -Prepare -StorageAccountName $storageAccountName
```

Compruebe la configuración de la cuenta de almacenamiento preparado mediante PowerShell Azure o en el portal de Azure. Si no está listo para la migración y desea volver al estado anterior, utilice el siguiente comando:

```powershell
    Move-AzureStorageAccount -Abort -StorageAccountName $storageAccountName
```

Si la configuración preparados es satisfactorio, puede desplazarse hacia delante y confirmar los recursos con el siguiente comando:

```powershell
    Move-AzureStorageAccount -Commit -StorageAccountName $storageAccountName
```

## <a name="next-steps"></a>Pasos siguientes

- Para obtener más información sobre la migración, consulte [migración compatibles con la plataforma de IaaS recursos clásico para el Administrador de recursos de Azure](virtual-machines-windows-migration-classic-resource-manager.md).
- Para migrar otros recursos de red para el Administrador de recursos con PowerShell de Azure, siga los mismos pasos con [AzureNetworkSecurityGroup mover](https://msdn.microsoft.com/library/mt786729.aspx), [Mover AzureReservedIP](https://msdn.microsoft.com/library/mt786752.aspx)y [Mover AzureRouteTable](https://msdn.microsoft.com/library/mt786718.aspx).
- Secuencias de comandos de código abierto, que puede usar para migrar recursos Azure de clásico para el Administrador de recursos, vea [Herramientas de comunidad para la migración a la migración del Administrador de recursos de Azure](virtual-machines-windows-migration-scripts.md)
