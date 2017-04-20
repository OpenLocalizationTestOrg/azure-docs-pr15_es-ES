<properties
    pageTitle="Usar PowerShell para realizar copias de seguridad y restauración de aplicaciones de servicio de aplicaciones"
    description="Obtenga información sobre cómo usar PowerShell para realizar copias de seguridad y restauración de una aplicación de servicio de la aplicación de Azure"
    services="app-service"
    documentationCenter=""
    authors="NKing92"
    manager="wpickett"
    editor="" />

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="nicking"/>
# <a name="use-powershell-to-back-up-and-restore-app-service-apps"></a>Usar PowerShell para realizar copias de seguridad y restauración de aplicaciones de servicio de aplicaciones

> [AZURE.SELECTOR]
- [PowerShell](app-service-powershell-backup.md)
- [API DE REST](../app-service-web/websites-csm-backup.md)

Obtenga información sobre cómo usar PowerShell Azure para realizar copias de seguridad y restaurar [aplicaciones de servicio de aplicación](https://azure.microsoft.com/services/app-service/web/). Para obtener más información sobre copias de seguridad de aplicación web, incluidos los requisitos y restricciones, vea [realizar copias de seguridad de una aplicación web de servicio de aplicaciones de Azure](../app-service-web/web-sites-backup.md).

## <a name="prerequisites"></a>Requisitos previos
Para usar PowerShell para administrar las copias de seguridad de la aplicación, se necesita lo siguiente:

- **Una SA URL** que permite acceso de lectura y escritura a un contenedor de almacenamiento de Azure. Para obtener una explicación de direcciones URL de SA, consulte [Descripción general del modelo de SA](../storage/storage-dotnet-shared-access-signature-part-1.md) . Vea [con Azure con el almacenamiento de Azure](../storage/storage-powershell-guide-full.md) para obtener ejemplos de administración del almacenamiento de Azure con PowerShell.
- **Una cadena de conexión de base de datos** si desea hacer copia de seguridad de una base de datos junto con la aplicación web.

### <a name="how-to-generate-a-sas-url-to-use-with-the-web-app-backup-cmdlets"></a>Cómo generar una URL de SA para usarlo con los cmdlets de copia de seguridad de aplicación web
Se pueden generar una URL SA con PowerShell. Aquí es un ejemplo de cómo generar uno que se pueden usar con los cmdlets analizados en este artículo.

        $storageAccountName = "<your storage account's name>"
        $storageAccountRg = "<your storage account's resource group>"

        # This returns an array of keys for your storage account. Be sure to select the appropriate key. Here we select the first key as a default.
        $storageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $storageAccountRg -Name $storageAccountName
        $context = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey[0].Value

        $blobContainerName = "<name of blob container for app backups>"
        $sasUrl = New-AzureStorageContainerSASToken -Name $blobContainerName -Permission rwdl -Context $context -ExpiryTime (Get-Date).AddMonths(1) -FullUri

## <a name="install-azure-powershell-132-or-greater"></a>Instalar Azure PowerShell 1.3.2 o superior

Vea [con Azure con el Administrador de recursos de Azure](../powershell-install-configure.md) para obtener instrucciones sobre la instalación y uso de PowerShell de Azure.

## <a name="create-a-backup"></a>Crear una copia de seguridad

Usar el cmdlet AzureRmWebAppBackup de nuevo para crear una copia de seguridad de una aplicación web.

        $sasUrl = "<your SAS URL>"
        $resourceGroupName = "Default-Web-WestUS"
        $appName = "ContosoApp"

        $backup = New-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -StorageAccountUrl $sasUrl

Esto crea una copia de seguridad con un nombre generado automáticamente. Si desea proporcionar un nombre para la copia de seguridad, use el parámetro opcional nombreCopiaSeguridad.

        $backup = New-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -StorageAccountUrl $sasUrl -BackupName MyBackup

Para incluir una base de datos en la copia de seguridad, crear una configuración de copia de seguridad de base de datos mediante el cmdlet New-AzureRmWebAppDatabaseBackupSetting luego suministrar ese valor en el parámetro de bases de datos del cmdlet AzureRmWebAppBackup de nuevo. El parámetro de bases de datos acepta una matriz de configuración de la base de datos, lo que le permite realizar copias de seguridad de más de una base de datos.

        $dbSetting1 = New-AzureRmWebAppDatabaseBackupSetting -Name DB1 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
        $dbSetting2 = New-AzureRmWebAppDatabaseBackupSetting -Name DB2 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
        $dbBackup = New-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -BackupName MyBackup -StorageAccountUrl $sasUrl -Databases $dbSetting1,$dbSetting2

## <a name="get-backups"></a>Obtener copias de seguridad

El cmdlet Get-AzureRmWebAppBackupList devuelve una matriz de todas las copias de seguridad para una aplicación web. Debe proporcionar el nombre de la aplicación web y su grupo de recursos.

        $resourceGroupName = "Default-Web-WestUS"
        $appName = "ContosoApp"
        $backups = Get-AzureRmWebAppBackupList -Name $appName -ResourceGroupName $resourceGroupName

Para obtener una copia de seguridad específico, use el cmdlet Get-AzureRmWebAppBackup.

        $backup = Get-AzureRmWebAppBackup -Name $appName -ResourceGroupName $resourceGroupName -BackupId 10102

También puede tubería un objeto de la aplicación web en cualquiera de los cmdlets de administración de copia de seguridad para mayor comodidad.

        $app = Get-AzureRmWebApp -Name ContosoApp -ResourceGroupName Default-Web-WestUS
        $backupList = $app | Get-AzureRmWebAppBackupList
        $backup = $app | Get-AzureRmWebAppBackup -BackupId 10102

## <a name="schedule-automatic-backups"></a>Programar copias de seguridad automáticas

Puede programar las copias de seguridad se producen automáticamente en un intervalo especificado. Para configurar una programación de copia de seguridad, use el cmdlet AzureRmWebAppBackupConfiguration de edición. Este cmdlet toma varios parámetros:

- **Nombre** : el nombre de la aplicación web.
- **ResourceGroupName** - el nombre del grupo de recursos que contiene la aplicación web.
- **Franja** : opcional. El nombre de la franja de la aplicación web.
- **StorageAccountUrl** - la dirección URL de SA para el contenedor de almacenamiento de Azure utilizado para almacenar las copias de seguridad.
- **FrequencyInterval** - valor numérico para la frecuencia con que deben realizarse las copias de seguridad. Debe ser un entero positivo.
- **FrequencyUnit** - unidad de tiempo ¿con qué frecuencia se deben realizar las copias de seguridad. Opciones son día y hora.
- **RetentionPeriodInDays** - cuántos días se deben guardar copias de seguridad automáticas antes de eliminarse automáticamente.
- **Hora de inicio** - opcional. La hora cuando deben comenzar las copias de seguridad automáticas. Realizar copias de seguridad comenzar inmediatamente si es null. Debe ser un valor DateTime.
- **Bases de datos** - opcional. Matriz de DatabaseBackupSettings para las bases de datos de copia de seguridad.
- **KeepAtLeastOneBackup** - opcional cambiar parámetros. Proporcionar este si siempre se debe conservar una copia de seguridad en la cuenta de almacenamiento, independientemente de su antigüedad.

A continuación se muestra un ejemplo de cómo usar este cmdlet.

        $resourceGroupName = "Default-Web-WestUS"
        $appName = "ContosoApp"
        $slotName = "StagingSlot"
        $dbSetting1 = New-AzureRmWebAppDatabaseBackupSetting -Name DB1 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
        $dbSetting2 = New-AzureRmWebAppDatabaseBackupSetting -Name DB2 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
        Edit-AzureRmWebAppBackupConfiguration -Name $appName -ResourceGroupName $resourceGroupName -Slot $slotName `
          -StorageAccountUrl "<your SAS URL>" -FrequencyInterval 6 -FrequencyUnit Hour -Databases $dbSetting1,$dbSetting2 `
          -KeepAtLeastOneBackup -StartTime (Get-Date).AddHours(1)

Para obtener la programación de copia de seguridad actual, use el cmdlet Get-AzureRmWebAppBackupConfiguration. Esto puede ser útil para modificar una programación que ya se ha configurado.

        $configuration = Get-AzureRmWebAppBackupConfiguration -Name $appName -ResourceGroupName $resourceGroupName

        # Modify the configuration slightly
        $configuration.FrequencyInterval = 2
        $configuration.FrequencyUnit = "Day"

        # Apply the new configuration by piping it into the Edit-AzureRmWebAppBackupConfiguration cmdlet
        $configuration | Edit-AzureRmWebAppBackupConfiguration

## <a name="restore-a-web-app-from-a-backup"></a>Restaurar una aplicación web de una copia de seguridad

Para restaurar una aplicación web de una copia de seguridad, use el cmdlet AzureRmWebAppBackup restaurar. Es la manera más sencilla de usar este cmdlet canalización de un objeto de copia de seguridad recuperados desde el cmdlet Get-AzureRmWebAppBackup o el cmdlet Get-AzureRmWebAppBackupList.

Una vez que tenga un objeto de copia de seguridad, puede conectarla al cmdlet AzureRmWebAppBackup restaurar. Especifique el parámetro del modificador sobrescribir para indicar que desea sobrescribir el contenido de la aplicación web con el contenido de la copia de seguridad. Si la copia de seguridad contiene bases de datos, se restauran también las bases de datos.

        $backup | Restore-AzureRmWebAppBackup -Overwrite

A continuación se muestra un ejemplo de cómo usar la AzureRmWebAppBackup restaurar especificando todos los parámetros.

        $resourceGroupName = "Default-Web-WestUS"
        $appName = "ContosoApp"
        $slotName = "StagingSlot"
        $blobName = "ContosoBackup.zip"
        $dbSetting1 = New-AzureRmWebAppDatabaseBackupSetting -Name DB1 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
        $dbSetting2 = New-AzureRmWebAppDatabaseBackupSetting -Name DB2 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
        Restore-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -Slot $slotName -StorageAccountUrl "<your SAS URL>" -BlobName $blobName -Databases $dbSetting1,$dbSetting2 -Overwrite

## <a name="delete-a-backup"></a>Eliminar una copia de seguridad

Para eliminar una copia de seguridad, use el cmdlet quitar AzureRmWebAppBackup. Quita la copia de seguridad de su cuenta de almacenamiento. Especifique el nombre de la aplicación, su grupo de recursos y el identificador de la copia de seguridad que desea eliminar.

        $resourceGroupName = "Default-Web-WestUS"
        $appName = "ContosoApp"
        Remove-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -BackupId 10102

También puede tubería un objeto de copia de seguridad al cmdlet AzureRmWebAppBackup quitar para eliminarla.

        $backup = Get-AzureRmWebAppBackup -Name $appName -ResourceGroupName $resourceGroupName -BackupId 10102
        $backup | Remove-AzureRmWebAppBackup -Overwrite
