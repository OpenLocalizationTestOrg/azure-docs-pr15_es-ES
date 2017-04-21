<properties
    pageTitle="Extensión de agente SQL Server para las máquinas virtuales SQL Server (clásico) | Microsoft Azure"
    description="En este tema se describe cómo administrar la extensión de agente de SQL Server, que automatiza tareas específicas de administración de SQL Server. Incluyen copia de seguridad automática, automatizado revisiones y la integración de depósito de claves de Azure. En este tema se utiliza el modo de implementación clásico."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/27/2016"
    ms.author="jroth"/>

# <a name="sql-server-agent-extension-for-sql-server-vms-classic"></a>Extensión de agente SQL Server para las máquinas virtuales SQL Server (clásico)

> [AZURE.SELECTOR]
- [Administrador de recursos](virtual-machines-windows-sql-server-agent-extension.md)
- [Clásico](virtual-machines-windows-classic-sql-server-agent-extension.md)

La extensión del agente de SQL Server IaaS (SQLIaaSAgent) se ejecuta en Azure máquinas virtuales de Windows para automatizar tareas de administración. Este tema proporciona una descripción general de los servicios compatibles con la extensión, así como instrucciones para la instalación, estado y eliminación.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Para ver la versión del Administrador de recursos de este artículo, consulte [Agente de extensión del Administrador de recursos de SQL Server máquinas virtuales de SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="supported-services"></a>Servicios compatibles

La extensión del agente de SQL Server IaaS admite las siguientes tareas de administración:

| Característica de administración | Descripción |
|---------------------|-------------------------------|
| **Copias de seguridad automatizadas de SQL** | Automatiza la programación de copias de seguridad para todas las bases de datos de la instancia predeterminada de SQL Server en la máquina virtual. Para obtener más información, consulte [copias de seguridad para SQL Server en Azure máquinas virtuales de Windows (clásico) automatizadas](virtual-machines-windows-classic-sql-automated-backup.md).|
| **SQL automatizado de revisión** | Configura una ventana de mantenimiento durante el cual las actualizaciones de la máquina virtual tenga lugar, por lo que puede evitar actualizaciones durante las horas de su carga de trabajo. Para obtener más información, vea [automatizado de revisión para SQL Server en Azure máquinas virtuales de Windows (clásico)](virtual-machines-windows-classic-sql-automated-patching.md).|
| **Integración de Azure depósito clave** | Le permite instalar y configurar depósito de clave de Azure en la VM de SQL Server automáticamente. Para obtener más información, vea [Configurar Azure clave depósito de integración de SQL Server en máquinas virtuales de Azure (clásico)](virtual-machines-windows-classic-ps-sql-keyvault.md).|

## <a name="prerequisites"></a>Requisitos previos

Requisitos para usar la extensión del agente de SQL Server IaaS en su máquina virtual:

### <a name="operating-system"></a>Sistema operativo:

- Windows Server 2012
- Windows Server 2012 R2

### <a name="sql-server-versions"></a>Versiones de SQL Server:

- SQL Server 2012
- SQL Server de 2014
- SQL Server 2016

### <a name="azure-powershell"></a>Azure PowerShell:

[Descargar y configurar los comandos de PowerShell de Azure más recientes](../powershell-install-configure.md).

Inicie Windows PowerShell y conectarse a su suscripción de Azure con el comando **Agregar AzureAccount** .

    Add-AzureAccount

Si tiene varias suscripciones, use **Seleccione AzureSubscription** para seleccionar la suscripción que contiene el destino VM clásico.

    Select-AzureSubscription -SubscriptionName <subscriptionname>

En este momento, puede obtener una lista de los equipos virtuales clásicos y sus nombres de servicio asociado con el comando **Get-AzureVM** .

    Get-AzureVM

## <a name="installation"></a>Instalación

Para VM clásicas, debe usar PowerShell para instalar la extensión del agente de SQL Server IaaS y configurar sus servicios asociados. Use el cmdlet de PowerShell **Conjunto AzureVMSqlServerExtension** para instalar la extensión. Por ejemplo, el siguiente comando instala la extensión en una máquina virtual de servidor de Windows (clásico) y nombres "SQLIaaSExtension".

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -ReferenceName "SQLIaasExtension" -Version "1.2" | Update-AzureVM

Si actualiza a la última versión de la extensión del Agente SQL IaaS, debe reiniciar la máquina virtual después de actualizar la extensión.

>[AZURE.NOTE] Máquinas virtuales de Windows clásico no tiene una opción para instalar y configurar la extensión del Agente SQL IaaS a través del portal.

## <a name="status"></a>Estado

Es una manera de comprobar que está instalada la extensión ver el estado de un agente en el Portal de Azure. Seleccione **todas las opciones** en el módulo de máquina virtual y, a continuación, haga clic en **extensiones**. Ahora debe aparecer en la lista la extensión **SQLIaaSAgent** .

![Extensión de IaaS Agente SQL Server en el Portal de Azure](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-portal.png)

También puede usar el cmdlet de Powershell de Azure **Get-AzureVMSqlServerExtension** .

    Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## <a name="removal"></a>Eliminación   

En el Portal de Azure, puede desinstalar la extensión haciendo clic en los puntos suspensivos en el módulo de **extensiones** de las propiedades de la máquina virtual. A continuación, haga clic en **Eliminar**.

![Desinstale la extensión de IaaS Agente SQL Server en el Portal de Azure](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-uninstall.png)

También puede usar el cmdlet de Powershell **Quitar AzureVMSqlServerExtension** .

    Get-AzureVM –ServiceName "service" –Name "vmname" | Remove-AzureVMSqlServerExtension | Update-AzureVM

## <a name="next-steps"></a>Pasos siguientes

Empezar a usar uno de los servicios compatibles con la extensión. Para obtener más detalles, vea los temas que se hace referencia en la sección [compatibles con servicios](#supported-services) de este artículo.

Para obtener más información acerca de cómo ejecutar SQL Server en Azure máquinas virtuales de Windows, vea [SQL Server en máquinas virtuales de Azure overview](virtual-machines-windows-sql-server-iaas-overview.md).
