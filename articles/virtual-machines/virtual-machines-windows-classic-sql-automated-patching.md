<properties
    pageTitle="Automatizado de revisión para las máquinas virtuales SQL Server (clásico) | Microsoft Azure"
    description="Explica la característica automatizado revisiones para SQL Server máquinas virtuales se ejecutan en Azure utilizando el modo de implementación clásico."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-service-management" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/26/2016"
    ms.author="jroth" />

# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-classic"></a>Automatizado de revisión para SQL Server en máquinas virtuales de Azure (clásico)

> [AZURE.SELECTOR]
- [Administrador de recursos](virtual-machines-windows-sql-automated-patching.md)
- [Clásico](virtual-machines-windows-classic-sql-automated-patching.md)

Revisión automatizado establece una ventana de mantenimiento de una máquina Virtual de Azure ejecuta SQL Server. Solo se pueden instalar actualizaciones automáticas durante esta ventana de mantenimiento. Para SQL Server, esto garantiza que se produce actualizaciones del sistema y cualquier reinicio del asociado en el mejor momento para la base de datos posible. Revisión automatizado depende de la [Extensión del agente de IaaS de SQL Server](virtual-machines-windows-classic-sql-server-agent-extension.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Para ver la versión del Administrador de recursos de este artículo, consulte [Automatizado revisiones para SQL Server en el Administrador de recursos de máquinas virtuales de Windows Azure](virtual-machines-windows-sql-automated-patching.md).

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

- [Instalar la última comandos de PowerShell de Azure](../powershell-install-configure.md).

**Extensión de IaaS SQL Server**:

- [Instale la extensión de IaaS SQL Server](virtual-machines-windows-classic-sql-server-agent-extension.md).

## <a name="settings"></a>Configuración

La siguiente tabla describe las opciones que se pueden configurar para la revisión de automatizada. Para VM clásicas, debe usar PowerShell para configurar estas opciones.

|Configuración|Valores posibles|Descripción|
|---|---|---|
|**Automatizado de revisión**|Habilitar o deshabilitar (deshabilitado)|Habilita o deshabilita automatizado revisiones para una máquina virtual Azure.|
|**Programación de mantenimiento**|Todos los días, el lunes, el martes, el miércoles, el jueves, el viernes, el sábado, domingo|La programación para descargar e instalar actualizaciones de Windows, SQL Server y Microsoft de su máquina virtual.|
|**Hora de inicio de mantenimiento**|24 de 0|La hora de inicio local para actualizar la máquina virtual.|
|**Duración de la ventana de mantenimiento**|30-180|El número de minutos permitido para completar la descarga e instalación de actualizaciones.|
|**Categoría de revisión**|Importante|La categoría de actualizaciones de descargar e instalar.|

## <a name="configuration-with-powershell"></a>Configuración con PowerShell

En el ejemplo siguiente, PowerShell se usa para configurar automatizado revisiones en una máquina virtual existente SQL Server. El comando **Nuevo AzureVMSqlServerAutoPatchingConfig** configura una nueva ventana de mantenimiento de actualizaciones automáticas.

    $aps = New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoPatchingSettings $aps | Update-AzureVM

Basándose en este ejemplo, la siguiente tabla describe el efecto en el destino VM Azure práctico:

|Parámetro|Efecto|
|---|---|
|**DayOfWeek**|Revisiones instalan cada jueves.|
|**MaintenanceWindowStartingHour**|Actualizaciones de comenzar a 11:00 a.m..|
|**MaintenanceWindowsDuration**|Deben estar instaladas revisiones en 120 minutos. En función de la hora de inicio, debe completar 1:00 p.m..|
|**PatchCategory**|La configuración solo posible para este parámetro es "Importante".|

Podría tardar varios minutos para instalar y configurar al agente de IaaS de SQL Server.

Para deshabilitar automatizado revisión, ejecute el mismo sin-parámetro habilitar la AzureVMSqlServerAutoPatchingConfig de nuevo la secuencia de comandos. Como ocurre con la instalación, puede tardar varios minutos para deshabilitar automatizado revisiones.

## <a name="next-steps"></a>Pasos siguientes

Para obtener información sobre otras tareas de automatización disponibles, vea [IaaS agente de extensión de SQL Server](virtual-machines-windows-classic-sql-server-agent-extension.md).

Para obtener más información sobre la ejecución de SQL Server en máquinas virtuales de Azure, consulte [SQL Server en máquinas virtuales de Azure overview](virtual-machines-windows-sql-server-iaas-overview.md).
