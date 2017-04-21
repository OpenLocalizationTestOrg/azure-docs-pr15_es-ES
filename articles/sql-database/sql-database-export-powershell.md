<properties
    pageTitle="Archivar una base de datos de SQL Azure a un archivo de MOCHILA con PowerShell"
    description="Archivar una base de datos de SQL Azure a un archivo de MOCHILA con PowerShell"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="08/15/2016"
    ms.author="sstein"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="archive-an-azure-sql-database-to-a-bacpac-file-by-using-powershell"></a>Archivar una base de datos de SQL Azure a un archivo de MOCHILA con PowerShell

> [AZURE.SELECTOR]
- [Portal de Azure](sql-database-export.md)
- [PowerShell](sql-database-export-powershell.md)


Este artículo proporcionan instrucciones para la base de datos de SQL Azure a un archivo de [MOCHILA](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) (almacenado en el almacenamiento de blobs de Windows Azure) archivado con PowerShell.

Cuando necesite crear un archivo de una base de datos de SQL Azure, puede exportar el esquema de la base de datos y los datos a un archivo de MOCHILA. Un archivo de MOCHILA es simplemente un archivo ZIP con una extensión de .bacpac. Un archivo de MOCHILA puede almacenarse más adelante en el almacenamiento de blobs de Windows Azure o en almacenamiento local en una ubicación local. También se pueden importar en base de datos de SQL Azure o en una instalación de SQL Server local.

**Consideraciones**

- Para que un archivo ser coherentes, no debe asegurarse de que ninguna escritura actividad se produce durante la exportación o que va a exportar una [copia coherente](sql-database-copy.md) de la base de datos de SQL Azure.
- El tamaño máximo de un archivo de MOCHILA archivado al almacenamiento de blobs de Windows Azure es 200 GB. Para archivar un archivo MOCHILA mayor al almacenamiento local, use la herramienta de símbolo de [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) . Esta utilidad se incluye con Visual Studio y SQL Server. También puede [Descargar](https://msdn.microsoft.com/library/mt204009.aspx) la última versión de SQL Server Data Tools para obtener esta utilidad.
- No se admite el archivado con el almacenamiento de Azure premium mediante un archivo de MOCHILA.
- Si la operación de exportación supera 20 horas, se puede cancelar. Para aumentar el rendimiento durante la exportación, puede:
 - Aumentar temporalmente el nivel de servicio.
 - Dejen de todos de lectura y escriben actividad durante la exportación.
 - Utilizar un [índice agrupado](https://msdn.microsoft.com/library/ms190457.aspx) con valores no nulos en todas las tablas de gran tamaño. Sin índices agrupados, una exportación puede fallar si tiene más de 6-12 horas. Esto es porque el servicio de exportación debe completar un recorrido de tabla para exportar la tabla completa. Ejecute **DBCC SHOW_STATISTICS** y asegúrese de que el *RANGE_HI_KEY* no es nulo y su valor tiene buena distribución es una buena forma de determinar si las tablas están optimizadas para exportar. Para obtener más información, consulte [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx).

> [AZURE.NOTE] BACPACs no están diseñados para su uso para copia de seguridad y restaurar las operaciones. Base de datos de SQL Azure crea automáticamente las copias de seguridad para cada base de datos de usuario. Para obtener más información, consulte [copias de seguridad de había automatizado de base de datos de SQL](sql-database-automated-backups.md).

Para completar este artículo, se necesita lo siguiente:

- Una suscripción de Azure.
- Una base de datos de SQL Azure.
- Una [cuenta de almacenamiento de Azure estándar](../storage/storage-create-storage-account.md), con un contenedor de blobs para almacenar la MOCHILA de almacenamiento estándar.


[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]




## <a name="export-your-database"></a>Exportar la base de datos

[New-AzureRmSqlDatabaseExport] (https://msdn.microsoft.com/library/azure/mt707796(v=azure.300\).aspx) cmdlet envía una solicitud de base de datos de exportación del servicio. Dependiendo del tamaño de la base de datos, la operación de exportación puede tardar algún tiempo en completarse.

> [AZURE.IMPORTANT] Para garantizar un archivo MOCHILA coherente, debe [crear una copia de la base de datos](sql-database-copy-powershell.md)y, a continuación, exportar la copia de la base de datos.


     $exportRequest = New-AzureRmSqlDatabaseExport –ResourceGroupName $ResourceGroupName –ServerName $ServerName `
       –DatabaseName $DatabaseName –StorageKeytype $StorageKeytype –StorageKey $StorageKey -StorageUri $BacpacUri `
       –AdministratorLogin $creds.UserName –AdministratorLoginPassword $creds.Password


## <a name="monitor-the-progress-of-the-export-operation"></a>Supervisar el progreso de la operación de exportación

Después de ejecutar [nuevo AzureRmSqlDatabaseExport] (https://msdn.microsoft.com/library/azure/mt603644(v=azure.300\).aspx), puede comprobar el estado de la solicitud ejecutando [Get-AzureRmSqlDatabaseImportExportStatus] (https://msdn.microsoft.com/library/azure/mt707794(v=azure.300\).aspx). Ejecutar este inmediatamente después de la solicitud normalmente devuelve **Estado: en curso**. Cuando vea **Estado: se realizó correctamente** la exportación está completa.


    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink



## <a name="export-sql-database-example"></a>Ejemplo de base de datos SQL de exportación

En el ejemplo siguiente se exporta una base de datos SQL existente a una MOCHILA y, a continuación, se muestra cómo comprobar el estado de la operación de exportación.

Para ejecutar el ejemplo, existen algunas variables que debe reemplazar con los valores específicos para su cuenta de almacenamiento y base de datos. En el [portal de Azure](https://portal.azure.com), vaya a su cuenta de almacenamiento para obtener el nombre de la cuenta de almacenamiento, el nombre del contenedor de blob y el valor de clave. Puede encontrar la clave haciendo clic en **las teclas de acceso** en el módulo de su cuenta de almacenamiento.

Reemplazar las siguientes `VARIABLE-VALUES` con valores para los recursos de Azure específicos. El nombre de la base de datos es la base de datos que desea exportar.



    $subscriptionId = "YOUR AZURE SUBSCRIPTION ID"

    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId

    # Database to export
    $DatabaseName = "DATABASE-NAME"
    $ResourceGroupName = "RESOURCE-GROUP-NAME"
    $ServerName = "SERVER-NAME"
    $serverAdmin = "ADMIN-NAME"
    $serverPassword = "ADMIN-PASSWORD" 
    $securePassword = ConvertTo-SecureString –String $serverPassword –AsPlainText -Force
    $creds = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $serverAdmin, $securePassword

    # Generate a unique filename for the BACPAC
    $bacpacFilename = $DatabaseName + (Get-Date).ToString("yyyyMMddHHmm") + ".bacpac"

    # Storage account info for the BACPAC
    $BaseStorageUri = "https://STORAGE-NAME.blob.core.windows.net/BLOB-CONTAINER-NAME/"
    $BacpacUri = $BaseStorageUri + $bacpacFilename
    $StorageKeytype = "StorageAccessKey"
    $StorageKey = "YOUR STORAGE KEY"

    $exportRequest = New-AzureRmSqlDatabaseExport –ResourceGroupName $ResourceGroupName –ServerName $ServerName `
       –DatabaseName $DatabaseName –StorageKeytype $StorageKeytype –StorageKey $StorageKey -StorageUri $BacpacUri `
       –AdministratorLogin $creds.UserName –AdministratorLoginPassword $creds.Password
    $exportRequest

    # Check status of the export
    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink



## <a name="next-steps"></a>Pasos siguientes

- Para obtener información sobre cómo importar una base de datos de SQL Azure con Powershell, vea [importar una MOCHILA con PowerShell](sql-database-import-powershell.md).


## <a name="additional-resources"></a>Recursos adicionales

- [Nuevo-AzureRmSqlDatabaseExport] (https://msdn.microsoft.com/library/azure/mt707796(v=azure.300\).aspx)
- [Get-AzureRmSqlDatabaseImportExportStatus] (https://msdn.microsoft.com/library/azure/mt707794(v=azure.300\).aspx)