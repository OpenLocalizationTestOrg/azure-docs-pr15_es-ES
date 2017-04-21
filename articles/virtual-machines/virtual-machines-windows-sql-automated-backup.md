<properties
    pageTitle="Copias de seguridad automatizadas para máquinas virtuales de SQL Server (Administrador de recursos) | Microsoft Azure"
    description="Explica la característica de copia de seguridad automática de SQL Server se ejecuta en Azure máquinas virtuales de Windows mediante el Administrador de recursos. "
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
    ms.date="07/14/2016"
    ms.author="jroth" />

# <a name="automated-backup-for-sql-server-in-azure-virtual-machines-resource-manager"></a>Copias de seguridad automatizadas para SQL Server en máquinas virtuales de Azure (jefe de recursos)

> [AZURE.SELECTOR]
- [Administrador de recursos](virtual-machines-windows-sql-automated-backup.md)
- [Clásico](virtual-machines-windows-classic-sql-automated-backup.md)

Copias de seguridad automatizadas configurará automáticamente [Administrado copia de seguridad a Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) para todas las bases de datos nuevos y existentes en una máquina virtual de Azure ejecuta SQL Server 2014 Standard o Enterprise. Esto le permite configurar copias de seguridad de base de datos normal que utilizan el almacenamiento de blobs de Windows Azure resistentes. Copias de seguridad automatizadas depende de la [Extensión del agente de IaaS de SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásico. Para ver la versión básica de este artículo, consulte [Copia de seguridad automática de SQL Server en máquinas virtuales de Windows clásico de Azure](virtual-machines-windows-classic-sql-automated-backup.md).

## <a name="prerequisites"></a>Requisitos previos

Para usar la copia de seguridad automática, tenga en cuenta los siguientes requisitos previos:

**Sistema operativo**:

- Windows Server 2012
- Windows Server 2012 R2

**Edición de la versión SQL Server**:

- Estándar SQL Server de 2014
- SQL Server 2014 Enterprise

**Configuración de la base de datos**:

- Bases de datos de destino deben utilizar el modelo de recuperación completa

**Azure PowerShell**:

- [Instalar la última comandos de PowerShell de Azure](../powershell-install-configure.md) si va a configurar copias de seguridad automatizadas con PowerShell.

>[AZURE.NOTE] Copias de seguridad automatizadas se basa en la extensión del agente de SQL Server IaaS. Imágenes de la Galería de máquina virtual SQL actuales agregar esta extensión predeterminada. Para obtener más información, vea [IaaS agente de extensión de SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="settings"></a>Configuración

La siguiente tabla describe las opciones que pueden configurar para copia de seguridad automática. Los pasos de configuración real varían dependiendo de si utiliza el portal de Azure o los comandos de PowerShell de Windows Azure.

|Configuración|Rango (predeterminado)|Descripción|
|---|---|---|
|**Copia de seguridad automática**|Habilitar o deshabilitar (deshabilitado)|Habilita o deshabilita la copia de seguridad automática para una máquina virtual de Azure ejecuta SQL Server 2014 Standard o Enterprise.|
|**Período de retención**|1-30 días (30 días)|El número de días que desea conservar una copia de seguridad.|
|**Cuenta de almacenamiento**|Almacenamiento de Azure cuenta (el almacenamiento creado para la máquina virtual especificada)|Una cuenta de almacenamiento de Azure para almacenar archivos de copia de seguridad automática en el almacenamiento de blobs de Windows. Se crea un contenedor en esta ubicación para almacenar todos los archivos de copia de seguridad. La convención de nomenclatura del archivo de copia de seguridad incluye la fecha, la hora y el nombre del equipo.|
|**Cifrado**|Habilitar o deshabilitar (deshabilitado)|Habilita o deshabilita el cifrado. Cuando se habilita el cifrado, los certificados que se utiliza para restaurar la copia de seguridad se encuentran en la cuenta de almacenamiento especificado en el mismo contenedor automaticbackup usando la misma convención de nomenclatura. Si cambia la contraseña, se genera un nuevo certificado con contraseña, pero el certificado antiguo permanece restaurar las copias de seguridad anteriores.|
|**Contraseña**|Texto de contraseña (ninguno)|Una contraseña para claves de cifrado. Esto solo es necesario si se habilita el cifrado. Para restaurar una copia de seguridad cifrado, debe tener la contraseña correcta y el certificado relacionada que usó en el momento en que se realizó la copia de seguridad.|

## <a name="configuration-in-the-portal"></a>Configuración en el Portal
Puede usar el Portal de Azure Configurar copia de seguridad automática durante aprovisionamiento o de máquinas virtuales existentes.

### <a name="new-vms"></a>Nuevas máquinas virtuales
Usar el Portal de Azure Configurar copia de seguridad automática cuando se crea una nueva máquina Virtual de SQL Server 2014 en el modelo de implementación de administrador de recursos.

En el módulo de **configuración de SQL Server** , seleccione **copias de seguridad automatizadas**. La siguiente captura de pantalla de portal Azure muestra el módulo de **Copia de seguridad automática de SQL** .

![Configuración de copia de seguridad automática de SQL en el portal de Azure](./media/virtual-machines-windows-sql-automated-backup/azure-sql-arm-autobackup.png)

Para el contexto, vea el tema completo en [una máquina virtual de SQL Server en Azure de aprovisionamiento](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>Máquinas virtuales existentes
Existentes máquinas virtuales de SQL Server, seleccione la máquina virtual de SQL Server. A continuación, seleccione la sección **configuración de SQL Server** del módulo de **configuración** .

![SQL copia de seguridad automática para máquinas virtuales existentes](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-existing-vms.png)

En el módulo de **configuración de SQL Server** , haga clic en el botón **Editar** en la sección de copia de seguridad automática.

![Configurar copias de seguridad automatizadas de SQL para máquinas virtuales existentes](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-configuration.png)

Cuando haya terminado, haga clic en el botón **Aceptar** en la parte inferior de la hoja de **configuración de SQL Server** para guardar los cambios.

Si va a habilitar la copia de seguridad automática por primera vez, Azure configura al agente de SQL Server IaaS en segundo plano. Durante este período, el portal de Azure no puede mostrar que está configurada la copia de seguridad automática. Espere unos minutos para que el agente se pueden instalar, configurar. Después de que el portal de Azure reflejará la nueva configuración.

>[AZURE.NOTE] También puede configurar copias de seguridad automatizadas usando una plantilla. Para obtener más información, vea [Tutorial rápido Azure plantilla para copia de seguridad automática](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autobackup-update).

## <a name="configuration-with-powershell"></a>Configuración con PowerShell

Si ya dispone de la VM SQL, usar PowerShell para configurar copias de seguridad automatizadas.

En el siguiente ejemplo de PowerShell, la copia de seguridad automática está configurada para una máquina virtual de SQL Server 2014 existente. El comando **AzureRM.Compute\New AzureVMSqlServerAutoBackupConfig** configura las opciones de copia de seguridad automatizadas para almacenar las copias de seguridad de la cuenta de almacenamiento de Azure asociada con la máquina virtual. Estas copias de seguridad se conservarán durante 10 días. El comando **Conjunto AzureRmVMSqlServerExtension** actualiza la máquina virtual de Azure especificado con esta configuración.

    $vmname = "vmname"
    $resourcegroupname = "resourcegroupname"
    $autobackupconfig = AzureRM.Compute\New-AzureVMSqlServerAutoBackupConfig -Enable -RetentionPeriodInDays 10 -ResourceGroupName $resourcegroupname

    Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig -VMName $vmname -ResourceGroupName $resourcegroupname

Podría tardar varios minutos para instalar y configurar al agente de IaaS de SQL Server.

Para habilitar el cifrado, modifique la secuencia de comandos anterior para pasar el parámetro **EnableEncryption** junto con una contraseña (cadena segura) para el parámetro **CertificatePassword** . La siguiente secuencia de comandos habilita a la configuración de copia de seguridad automática en el ejemplo anterior y agrega el cifrado.

    $vmname = "vmname"
    $resourcegroupname = "resourcegroupname"
    $password = "P@ssw0rd"
    $encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
    $autobackupconfig = AzureRM.Compute\New-AzureVMSqlServerAutoBackupConfig -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword -ResourceGroupName $resourcegroupname

    Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig -VMName $vmname -ResourceGroupName $resourcegroupname

Para deshabilitar la copia de seguridad automática, ejecute el mismo script sin la **-Habilitar** parámetro con el comando **AzureRM.Compute\New AzureVMSqlServerAutoBackupConfig** . Ausencia de la **-Habilitar** parámetro indica el comando para deshabilitar la característica. Como ocurre con la instalación, puede tardar varios minutos para deshabilitar la copia de seguridad automática.

>[AZURE.NOTE] Quitar al agente de IaaS de SQL Server no se quita la configuración de copia de seguridad automatizadas configurada previamente. Debe deshabilitar automatizado de copia de seguridad antes de deshabilitar o desinstalar al agente de IaaS de SQL Server.

## <a name="next-steps"></a>Pasos siguientes

Copias de seguridad automatizadas configura administra la copia de seguridad en máquinas virtuales de Azure. Así que es importante para [Revisar la documentación de copia de seguridad administradas](https://msdn.microsoft.com/library/dn449496.aspx) a comprender el comportamiento y las implicaciones.

Puede encontrar la copia de seguridad adicional y restaurar pautas para SQL Server en máquinas virtuales de Azure en el tema siguiente: [copia de seguridad y restauración de SQL Server en Azure máquinas virtuales de Windows](virtual-machines-windows-sql-backup-recovery.md).

Para obtener información sobre otras tareas de automatización disponibles, vea [IaaS agente de extensión de SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

Para obtener más información sobre la ejecución de SQL Server en máquinas virtuales de Azure, consulte [SQL Server en máquinas virtuales de Azure overview](virtual-machines-windows-sql-server-iaas-overview.md).
