<properties
    pageTitle="Automatizado de revisión para las máquinas virtuales SQL Server (Administrador de recursos) | Microsoft Azure"
    description="Explica la característica automatizado revisiones para SQL Server máquinas virtuales se ejecutan en Azure con el Administrador de recursos."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-resource-manager"/>
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="08/19/2016"
    ms.author="jroth" />

# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-resource-manager"></a>Automatizado de revisión para SQL Server en máquinas virtuales de Azure (jefe de recursos)

> [AZURE.SELECTOR]
- [Administrador de recursos](virtual-machines-windows-sql-automated-patching.md)
- [Clásico](virtual-machines-windows-classic-sql-automated-patching.md)

Revisión automatizado establece una ventana de mantenimiento de una máquina Virtual de Azure ejecuta SQL Server. Solo se pueden instalar actualizaciones automáticas durante esta ventana de mantenimiento. Para SQL Server, este rescriction garantiza que se produce actualizaciones del sistema y cualquier reinicio del asociado en el mejor momento para la base de datos posible. Revisión automatizado depende de la [Extensión del agente de IaaS de SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásico. Para ver la versión básica de este artículo, consulte [Automatizado revisiones para SQL Server en máquinas virtuales de Windows clásico de Azure](virtual-machines-windows-classic-sql-automated-patching.md).

## <a name="prerequisites"></a>Requisitos previos

Para usar automatizado revisiones, tenga en cuenta los siguientes requisitos previos:

**Sistema operativo**:

- Windows Server 2012
- Windows Server 2012 R2

**Versión de SQL Server**:

- SQL Server 2012
- SQL Server de 2014
- SQL Server 2016

**Azure PowerShell**:

- [Instalar la última comandos de PowerShell de Azure](../powershell-install-configure.md) si va a configurar automatizado revisiones con PowerShell.

>[AZURE.NOTE] Revisión automatizado se basa en la extensión del agente de SQL Server IaaS. Imágenes de la Galería de máquina virtual SQL actuales agregar esta extensión predeterminada. Para obtener más información, vea [IaaS agente de extensión de SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="settings"></a>Configuración

La siguiente tabla describe las opciones que se pueden configurar para la revisión de automatizada. Los pasos de configuración real varían dependiendo de si utiliza el portal de Azure o los comandos de PowerShell de Windows Azure.

|Configuración|Valores posibles|Descripción|
|---|---|---|
|**Automatizado de revisión**|Habilitar o deshabilitar (deshabilitado)|Habilita o deshabilita automatizado revisiones para una máquina virtual Azure.|
|**Programación de mantenimiento**|Todos los días, el lunes, el martes, el miércoles, el jueves, el viernes, el sábado, domingo|La programación para descargar e instalar actualizaciones de Windows, SQL Server y Microsoft de su máquina virtual.|
|**Hora de inicio de mantenimiento**|24 de 0|La hora de inicio local para actualizar la máquina virtual.|
|**Duración de la ventana de mantenimiento**|30-180|El número de minutos permitido para completar la descarga e instalación de actualizaciones.|
|**Categoría de revisión**|Importante|La categoría de actualizaciones de descargar e instalar.|

## <a name="configuration-in-the-portal"></a>Configuración en el Portal
Puede usar el portal de Azure para configurar automatizado revisiones durante el aprovisionamiento o de máquinas virtuales existentes.

### <a name="new-vms"></a>Nuevas máquinas virtuales
Utilice el portal de Azure para configurar revisiones automatizado cuando se crea una nueva máquina Virtual de SQL Server en el modelo de implementación de administrador de recursos.

En el módulo de **configuración de SQL Server** , seleccione **revisiones automatizada**. La siguiente captura de pantalla de portal Azure muestra el módulo **Automatizado revisiones SQL** .

![SQL automatizado revisiones en el portal de Azure](./media/virtual-machines-windows-sql-automated-patching/azure-sql-arm-patching.png)

Para el contexto, vea el tema completo en [una máquina virtual de SQL Server en Azure de aprovisionamiento](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>Máquinas virtuales existentes
Existentes máquinas virtuales de SQL Server, seleccione la máquina virtual de SQL Server. A continuación, seleccione la sección **configuración de SQL Server** del módulo de **configuración** .

![Revisiones automáticas de SQL para máquinas virtuales existentes](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-existing-vms.png)

En el módulo de **configuración de SQL Server** , haga clic en el botón **Editar** en el automatizado de revisión de la sección.

![Configurar automatizado revisiones SQL para máquinas virtuales existentes](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-configuration.png)

Cuando haya terminado, haga clic en el botón **Aceptar** en la parte inferior de la hoja de **configuración de SQL Server** para guardar los cambios.

Si va a habilitar automatizado revisiones por primera vez, Azure configura al agente de SQL Server IaaS en segundo plano. Durante este período, el portal de Azure no puede mostrar que se ha configurado la revisión automática. Espere unos minutos para que el agente se pueden instalar, configurar. Después de que el portal de Azure refleja la nueva configuración.

>[AZURE.NOTE] También puede configurar automatizado revisiones usando una plantilla. Para obtener más información, vea [Tutorial rápido Azure plantilla para la revisión de automatizada](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autopatching-update).

## <a name="configuration-with-powershell"></a>Configuración con PowerShell

Si ya dispone de la VM SQL, usar PowerShell para configurar revisiones automatizada.

En el ejemplo siguiente, PowerShell se usa para configurar automatizado revisiones en una máquina virtual existente SQL Server. El comando **AzureRM.Compute\New AzureVMSqlServerAutoPatchingConfig** configura una nueva ventana de mantenimiento de actualizaciones automáticas.

    $vmname = "vmname"
    $resourcegroupname = "resourcegroupname"
    $aps = AzureRM.Compute\New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Set-AzureRmVMSqlServerExtension -AutoPatchingSettings $aps -VMName $vmname -ResourceGroupName $resourcegroupname

Basándose en este ejemplo, la siguiente tabla describe el efecto en el destino VM Azure práctico:

|Parámetro|Efecto|
|---|---|
|**DayOfWeek**|Revisiones instalan cada jueves.|
|**MaintenanceWindowStartingHour**|Actualizaciones de comenzar a 11:00 a.m..|
|**MaintenanceWindowsDuration**|Deben estar instaladas revisiones en 120 minutos. En función de la hora de inicio, debe completar 1:00 p.m..|
|**PatchCategory**|La configuración solo posible para este parámetro es **importante**.|

Podría tardar varios minutos para instalar y configurar al agente de IaaS de SQL Server.

Para deshabilitar automatizado revisión, ejecute la misma secuencia de comandos sin la **-Habilitar** parámetro la **AzureRM.Compute\New AzureVMSqlServerAutoPatchingConfig**. Ausencia de la **-Habilitar** parámetro indica el comando para deshabilitar la característica.

## <a name="next-steps"></a>Pasos siguientes

Para obtener información sobre otras tareas de automatización disponibles, vea [IaaS agente de extensión de SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

Para obtener más información sobre la ejecución de SQL Server en máquinas virtuales de Azure, consulte [SQL Server en máquinas virtuales de Azure overview](virtual-machines-windows-sql-server-iaas-overview.md).
