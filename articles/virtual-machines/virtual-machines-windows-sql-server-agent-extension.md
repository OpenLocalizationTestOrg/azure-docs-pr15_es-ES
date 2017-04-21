<properties
    pageTitle="Extensión de agente SQL Server para las máquinas virtuales SQL Server (Administrador de recursos) | Microsoft Azure"
    description="En este tema se describe cómo administrar la extensión de agente de SQL Server, que automatiza tareas específicas de administración de SQL Server. Incluyen copia de seguridad automática, automatizado revisiones y la integración de depósito de claves de Azure. En este tema se utiliza el modo de implementación de administrador de recursos."
    services="virtual-machines-windows"
    documentationCenter=""
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
    ms.date="10/27/2016"
    ms.author="jroth"/>

# <a name="sql-server-agent-extension-for-sql-server-vms-resource-manager"></a>Extensión de agente SQL Server para las máquinas virtuales SQL Server (Administrador de recursos)

> [AZURE.SELECTOR]
- [Administrador de recursos](virtual-machines-windows-sql-server-agent-extension.md)
- [Clásico](virtual-machines-windows-classic-sql-server-agent-extension.md)

La extensión del agente de SQL Server IaaS (SQLIaaSExtension) se ejecuta en Azure máquinas virtuales de Windows para automatizar tareas de administración. Este tema proporciona una descripción general de los servicios compatibles con la extensión, así como instrucciones para la instalación, estado y eliminación.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásico. Para ver la versión básica de este artículo, vea [Extensión de agente de SQL Server para clásico de máquinas virtuales de SQL Server](virtual-machines-windows-classic-sql-server-agent-extension.md).

## <a name="supported-services"></a>Servicios compatibles

La extensión del agente de SQL Server IaaS admite las siguientes tareas de administración:

| Característica de administración | Descripción |
|---------------------|-------------------------------|
| **Copias de seguridad automatizadas de SQL** | Automatiza la programación de copias de seguridad para todas las bases de datos de la instancia predeterminada de SQL Server en la máquina virtual. Para obtener más información, vea [copia de seguridad automática para SQL Server en Azure máquinas virtuales de Windows (Administrador de recursos)](virtual-machines-windows-sql-automated-backup.md).|
| **SQL automatizado de revisión** | Configura una ventana de mantenimiento durante el cual las actualizaciones de la máquina virtual tenga lugar, por lo que puede evitar actualizaciones durante las horas de su carga de trabajo. Para obtener más información, vea [revisiones automatizada para SQL Server en Azure máquinas virtuales de Windows (Administrador de recursos)](virtual-machines-windows-sql-automated-patching.md).|
| **Integración de Azure depósito clave** | Le permite instalar y configurar depósito de clave de Azure en la VM de SQL Server automáticamente. Para obtener más información, vea [Configurar Azure clave depósito de integración de SQL Server en máquinas virtuales de Azure (jefe de recursos)](virtual-machines-windows-ps-sql-keyvault.md).|

## <a name="prerequisites"></a>Requisitos previos

Requisitos para usar la extensión del agente de SQL Server IaaS en su máquina virtual:

**Sistema operativo**:

- Windows Server 2012
- Windows Server 2012 R2

**Versiones de SQL Server**:

- SQL Server 2012
- SQL Server de 2014
- SQL Server 2016

**Azure PowerShell**:

- [Descargar y configurar los comandos de PowerShell de Azure más recientes](../powershell-install-configure.md)

## <a name="installation"></a>Instalación

La extensión del agente de SQL Server IaaS se instala automáticamente cuando se aprovisiona una de las imágenes de galería de máquina virtual de SQL Server.

Si crea una máquina virtual de sólo sistema operativo Windows Server, puede instalar la extensión manualmente mediante el cmdlet de PowerShell **Conjunto AzureVMSqlServerExtension** . Por ejemplo, el siguiente comando instala la extensión en una VM de servidor de Windows solo OS y nombres "SQLIaaSExtension".

    Set-AzureRmVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SQLIaasExtension" -Version "1.2"

Si actualiza a la última versión de la extensión del Agente SQL IaaS, debe reiniciar la máquina virtual después de actualizar la extensión.

>[AZURE.NOTE] Si instala la extensión del agente de SQL Server IaaS manualmente en una máquina virtual de servidor de Windows, debe usar y administrar sus características con los comandos de PowerShell. La interfaz del portal sólo está disponible para imágenes de la Galería de SQL Server.

## <a name="status"></a>Estado

Es una manera de comprobar que está instalada la extensión ver el estado de un agente en el Portal de Azure. Seleccione **todas las opciones** en el módulo de máquina virtual y, a continuación, haga clic en **extensiones**. Ahora debe aparecer en la lista la extensión **SQLIaaSExtension** .

![Extensión de IaaS Agente SQL Server en el Portal de Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

También puede usar el cmdlet de Powershell de Azure **Get-AzureVMSqlServerExtension** .

    Get-AzureRmVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"

El comando anterior confirma el agente está instalado y proporciona información general de estado. También puede obtener información de estado específicas sobre copia de seguridad automática y revisión con los siguientes comandos.

    $sqlext = Get-AzureRmVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings

## <a name="removal"></a>Eliminación   

En el Portal de Azure, puede desinstalar la extensión haciendo clic en los puntos suspensivos en el módulo de **extensiones** de las propiedades de la máquina virtual. A continuación, haga clic en **Eliminar**.

![Desinstale la extensión de IaaS Agente SQL Server en el Portal de Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

También puede usar el cmdlet de Powershell **Quitar AzureRmVMSqlServerExtension** .

    Remove-AzureRmVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SQLIaasExtension"

## <a name="next-steps"></a>Pasos siguientes

Empezar a usar uno de los servicios compatibles con la extensión. Para obtener más detalles, vea los temas que se hace referencia en la sección [compatibles con servicios](#supported-services) de este artículo.

Para obtener más información acerca de cómo ejecutar SQL Server en Azure máquinas virtuales de Windows, vea [SQL Server en máquinas virtuales de Azure overview](virtual-machines-windows-sql-server-iaas-overview.md).
