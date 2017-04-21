<properties
    pageTitle="Copias de seguridad automatizadas para máquinas virtuales de SQL Server (clásico) | Microsoft Azure"
    description="Explica la característica de copia de seguridad automática de SQL Server se ejecuta en Azure máquinas virtuales de Windows mediante el Administrador de recursos. "
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

# <a name="automated-backup-for-sql-server-in-azure-virtual-machines-classic"></a>Copias de seguridad automatizadas para SQL Server en máquinas virtuales de Azure (clásico)

> [AZURE.SELECTOR]
- [Administrador de recursos](virtual-machines-windows-sql-automated-backup.md)
- [Clásico](virtual-machines-windows-classic-sql-automated-backup.md)

Copias de seguridad automatizadas configurará automáticamente [Administrado copia de seguridad a Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) para todas las bases de datos nuevos y existentes en una máquina virtual de Azure ejecuta SQL Server 2014 Standard o Enterprise. Esto le permite configurar copias de seguridad de base de datos normal que utilizan el almacenamiento de blobs de Windows Azure resistentes. Copias de seguridad automatizadas depende de la [Extensión del agente de IaaS de SQL Server](virtual-machines-windows-classic-sql-server-agent-extension.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Para ver la versión del Administrador de recursos de este artículo, consulte [Copia de seguridad automática para SQL Server en el Administrador de recursos de máquinas virtuales de Windows Azure](virtual-machines-windows-sql-automated-backup.md).

## <a name="prerequisites"></a>Requisitos previos

Para usar la copia de seguridad automática, tenga en cuenta los siguientes requisitos previos:

**Sistema operativo**:

- Windows Server 2012
- Windows Server 2012 R2

**Edición de la versión SQL Server**:

- Estándar SQL Server de 2014
- SQL Server 2014 Enterprise

>[AZURE.NOTE] SQL Server 2016 no es compatible todavía para copia de seguridad automática.

**Configuración de la base de datos**:

- Bases de datos de destino deben utilizar el modelo de recuperación completa.

**Azure PowerShell**:

- [Instalar la última comandos de PowerShell de Azure](../powershell-install-configure.md).

**Extensión de IaaS SQL Server**:

- [Instale la extensión de IaaS SQL Server](virtual-machines-windows-classic-sql-server-agent-extension.md).

## <a name="settings"></a>Configuración

La siguiente tabla describe las opciones que pueden configurar para copia de seguridad automática. Para VM clásicas, debe usar PowerShell para configurar estas opciones.

|Configuración|Rango (predeterminado)|Descripción|
|---|---|---|
|**Copia de seguridad automática**|Habilitar o deshabilitar (deshabilitado)|Habilita o deshabilita la copia de seguridad automática para una máquina virtual de Azure ejecuta SQL Server 2014 Standard o Enterprise.|
|**Período de retención**|1-30 días (30 días)|El número de días que desea conservar una copia de seguridad.|
|**Cuenta de almacenamiento**|Almacenamiento de Azure cuenta (el almacenamiento creado para la máquina virtual especificada)|Una cuenta de almacenamiento de Azure para almacenar archivos de copia de seguridad automática en el almacenamiento de blobs de Windows. Se crea un contenedor en esta ubicación para almacenar todos los archivos de copia de seguridad. La convención de nomenclatura del archivo de copia de seguridad incluye la fecha, la hora y el nombre del equipo.|
|**Cifrado**|Habilitar o deshabilitar (deshabilitado)|Habilita o deshabilita el cifrado. Cuando se habilita el cifrado, los certificados que se utiliza para restaurar la copia de seguridad se encuentran en la cuenta de almacenamiento especificado en el mismo contenedor automaticbackup usando la misma convención de nomenclatura. Si cambia la contraseña, se genera un nuevo certificado con contraseña, pero el certificado antiguo permanece restaurar las copias de seguridad anteriores.|
|**Contraseña**|Texto de contraseña (ninguno)|Una contraseña para claves de cifrado. Esto solo es necesario si se habilita el cifrado. Para restaurar una copia de seguridad cifrado, debe tener la contraseña correcta y el certificado relacionada que usó en el momento en que se realizó la copia de seguridad.|

## <a name="configuration-with-powershell"></a>Configuración con PowerShell

En el siguiente ejemplo de PowerShell, la copia de seguridad automática está configurada para una máquina virtual de SQL Server 2014 existente. El comando **Nuevo AzureVMSqlServerAutoBackupConfig** configura las opciones de copia de seguridad automatizadas para almacenar las copias de seguridad de la cuenta de almacenamiento de Azure especificada por la variable $storageaccount. Estas copias de seguridad se conservarán durante 10 días. El comando **Conjunto AzureVMSqlServerExtension** actualiza la máquina virtual de Azure especificado con esta configuración.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Podría tardar varios minutos para instalar y configurar al agente de IaaS de SQL Server.

Para habilitar el cifrado, modifique la secuencia de comandos anterior para pasar el parámetro EnableEncryption junto con una contraseña (cadena segura) para el parámetro CertificatePassword. La siguiente secuencia de comandos habilita a la configuración de copia de seguridad automática en el ejemplo anterior y agrega el cifrado.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $password = "P@ssw0rd"
    $encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Para deshabilitar la copia de seguridad automática, ejecute el mismo script sin la **-Habilitar** parámetro para el **Nuevo AzureVMSqlServerAutoBackupConfig**. Como ocurre con la instalación, puede tardar varios minutos para deshabilitar la copia de seguridad automática.

>[AZURE.NOTE] Deshabilitar y desinstalar al agente de IaaS de SQL Server no se elimina la configuración de copia de seguridad administrado configurada previamente. Debe deshabilitar automatizado de copia de seguridad antes de deshabilitar o desinstalar al agente de IaaS de SQL Server.

## <a name="next-steps"></a>Pasos siguientes

Copias de seguridad automatizadas configura administra la copia de seguridad en máquinas virtuales de Azure. Así que es importante para [Revisar la documentación de copia de seguridad administradas](https://msdn.microsoft.com/library/dn449496.aspx) a comprender el comportamiento y las implicaciones.

Puede encontrar la copia de seguridad adicional y restaurar pautas para SQL Server en máquinas virtuales de Azure en el tema siguiente: [copia de seguridad y restauración de SQL Server en Azure máquinas virtuales de Windows](virtual-machines-windows-sql-backup-recovery.md).

Para obtener información sobre otras tareas de automatización disponibles, vea [IaaS agente de extensión de SQL Server](virtual-machines-windows-classic-sql-server-agent-extension.md).

Para obtener más información sobre la ejecución de SQL Server en máquinas virtuales de Azure, consulte [SQL Server en máquinas virtuales de Azure overview](virtual-machines-windows-sql-server-iaas-overview.md).
