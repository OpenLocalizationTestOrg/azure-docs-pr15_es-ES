<properties
    pageTitle="Importar un archivo de MOCHILA para crear una base de datos de SQL Azure con PowerShell | Microsoft Azure"
    description="Importar un archivo de MOCHILA para crear una base de datos de SQL Azure con PowerShell"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management"
    ms.date="08/31/2016"
    ms.author="sstein"/>

# <a name="import-a-bacpac-file-to-create-an-azure-sql-database-by-using-powershell"></a>Importar un archivo de MOCHILA para crear una base de datos de SQL Azure con PowerShell

**Base de datos única**

> [AZURE.SELECTOR]
- [Portal de Azure](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)
- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)

Este artículo ofrece instrucciones para crear una base de datos de SQL Azure importando un archivo de [MOCHILA](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) con PowerShell.

Se crea la base de datos desde un archivo de MOCHILA (.bacpac) importado desde un contenedor de blobs de Windows Azure almacenamiento. Si no tiene un archivo de MOCHILA en el almacenamiento de Azure, vea [archivar una base de datos de SQL Azure a un archivo de MOCHILA con PowerShell](sql-database-export-powershell.md). Si ya tiene un archivo de MOCHILA que no está en el almacenamiento de Azure, [use AzCopy para cargarlo fácilmente a su cuenta de almacenamiento de Azure](../storage/storage-use-azcopy.md#blob-upload).

> [AZURE.NOTE] Base de datos de SQL Azure se crea automáticamente y se mantiene copias de seguridad para cada base de datos de usuario que puede restaurar. Para obtener más información, consulte [copias de seguridad de había automatizado de base de datos de SQL](sql-database-automated-backups.md).


Para importar una base de datos SQL, se necesita lo siguiente:

- Una suscripción de Azure. Si necesita una suscripción de Azure simplemente haga clic en la **Versión de prueba gratuita** en la parte superior de esta página y, a continuación, vuelva a fin de este artículo.
- Un archivo de MOCHILA de la base de datos que desea importar. La MOCHILA debe estar en un contenedor de blobs de Windows [Azure almacenamiento de cuenta](../storage/storage-create-storage-account.md) .



[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]



## <a name="set-up-the-variables-for-your-environment"></a>Configurar las variables para su entorno

Existen algunas variables dónde debe reemplazar los valores de ejemplo con los valores específicos para la base de datos y su cuenta de almacenamiento.

El nombre del servidor debe ser un servidor que ya existe en la suscripción seleccionada en el paso anterior. Debe ser el servidor que desea que se creen en la base de datos. No se admite la importación de una base de datos directamente en un grupo de elástico. Pero puede importar a una base de datos y, a continuación, mueva la base de datos en un grupo.

El nombre de la base de datos es el nombre que desee para la nueva base de datos.

    $ResourceGroupName = "resource group name"
    $ServerName = "server name"
    $DatabaseName = "database name"


Las variables siguientes son de la cuenta de almacenamiento donde se encuentra su MOCHILA. En el [portal de Azure](https://portal.azure.com), vaya a su cuenta de almacenamiento para obtener acceso a estos valores. Puede encontrar la clave de acceso principal haciendo clic en **toda la configuración** y, a continuación, **las teclas** de módulo de su cuenta de almacenamiento.

El nombre de blob es el nombre de un archivo de MOCHILA existente que desea crear la base de datos. Debe incluir la extensión .bacpac.

    $StorageName = "storageaccountname"
    $StorageKeyType = "StorageAccessKey"
    $StorageUri = "http://$StorageName.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"


Ejecuta el [Get-Credential] (https://msdn.microsoft.com/library/azure/hh849815(v=azure.300\).aspx) cmdlet abre una ventana que se solicita su nombre de usuario y contraseña. Escriba el inicio de sesión de administrador y la contraseña para el servidor de base de datos SQL ($ServerName desde arriba) y no el nombre de usuario y la contraseña de su cuenta de Azure.

    $credential = Get-Credential


## <a name="import-the-database"></a>Importar la base de datos

Este comando envía una solicitud de base de datos de importación para el servicio. Dependiendo del tamaño de la base de datos, la operación de importación puede tardar algún tiempo en completarse.

    $importRequest = New-AzureRmSqlDatabaseImport –ResourceGroupName $ResourceGroupName –ServerName $ServerName –DatabaseName $DatabaseName –StorageKeytype $StorageKeyType –StorageKey $StorageKey -StorageUri $StorageUri –AdministratorLogin $credential.UserName –AdministratorLoginPassword $credential.Password –Edition Standard –ServiceObjectiveName S0 -DatabaseMaxSizeBytes 50000


## <a name="monitor-the-progress-of-the-operation"></a>Supervisar el progreso de la operación

Después de ejecutar [nuevo AzureRmSqlDatabaseImport] (https://msdn.microsoft.com/library/azure/mt707793(v=azure.300\).aspx), puede comprobar el estado de la solicitud ejecutando el [Get-AzureRmSqlDatabaseImportExportStatus] (https://msdn.microsoft.com/library/azure/mt707794(v=azure.300\).aspx).

    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink



## <a name="sql-database-powershell-import-script"></a>Importar script de PowerShell de base de datos de SQL


    $ResourceGroupName = "resourceGroupName"
    $ServerName = "servername"
    $DatabaseName = "databasename"

    $StorageName = "storageaccountname"
    $StorageKeyType = "StorageAccessKey"
    $StorageUri = "http://$StorageName.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"

    $credential = Get-Credential

    $importRequest = New-AzureRmSqlDatabaseImport –ResourceGroupName $ResourceGroupName –ServerName $ServerName –DatabaseName $DatabaseName –StorageKeytype $StorageKeyType –StorageKey $StorageKey -StorageUri $StorageUri –AdministratorLogin $credential.UserName –AdministratorLoginPassword $credential.Password –Edition Standard –ServiceObjectiveName S0 -DatabaseMaxSizeBytes 50000

    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink



## <a name="next-steps"></a>Pasos siguientes

- Para obtener información para conectarse a una base de datos importados de SQL de la consulta, vea [conectarse a la base de datos de SQL con SQL Server Management Studio y realizar una consulta de SQL de T de ejemplo](sql-database-connect-query-ssms.md)
