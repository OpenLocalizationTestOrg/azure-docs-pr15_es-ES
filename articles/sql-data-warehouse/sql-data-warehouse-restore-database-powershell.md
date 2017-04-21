<properties
   pageTitle="Restaurar un almacén de datos SQL Azure (PowerShell) | Microsoft Azure"
   description="Tareas de PowerShell para restaurar un almacén de datos de SQL Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="Lakshmi1812"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/21/2016"
   ms.author="lakshmir;barbkess;sonyama"/>

# <a name="restore-an-azure-sql-data-warehouse-powershell"></a>Restaurar un almacén de datos SQL Azure (PowerShell)

> [AZURE.SELECTOR]
- [Información general][]
- [Portal][]
- [PowerShell][]
- [REST][]

En este artículo aprenderá cómo restaurar un almacén de datos de SQL Azure con PowerShell.

## <a name="before-you-begin"></a>Antes de empezar

**Comprobar la capacidad DTU.** Cada almacén de datos de SQL está alojado en un servidor de SQL server (por ejemplo, myserver.database.windows.net) que tiene una cuota DTU predeterminada.  Antes de poder restaurar un almacén de datos de SQL, compruebe que el servidor SQL server tiene suficiente cuota DTU restante que se está restaurando la base de datos. Para obtener información sobre cómo calcular DTU es necesario o solicitar más DTU, consulte [Solicitar un cambio de cuota DTU][].

### <a name="install-powershell"></a>Instale PowerShell

Para usar PowerShell Azure con el almacén de datos de SQL, debe instalar Azure PowerShell versión 1.0 o superior.  Puede comprobar la versión ejecutando **módulo Get - ListAvailable-AzureRM nombre**.  Desde el [Instalador de plataforma Web de Microsoft][], se puede instalar la versión más reciente.  Para obtener más información sobre cómo instalar la última versión, consulte [cómo instalar y configurar Azure PowerShell][].

## <a name="restore-an-active-or-paused-database"></a>Restaurar una base de datos activa o en pausa

Para restaurar una base de datos desde una instantánea usar el cmdlet de PowerShell [AzureRmSqlDatabase restaurar][] .

1. Abrir Windows PowerShell.
2. Conectarse a su cuenta de Azure y todas las suscripciones asociadas a su cuenta de la lista.
3. Seleccione la suscripción que contiene la base de datos pueden restaurarse.
4. Lista de los puntos de restauración de la base de datos.
5. Seleccione el punto de restauración deseado mediante el RestorePointCreationDate.
6. Restaurar la base de datos hasta el momento que quiera restaurar.
7. Compruebe que la base de datos está en línea.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# List the last 10 database restore points
((Get-AzureRMSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName ($DatabaseName).RestorePointCreationDate)[-10 .. -1]

# Or list all restore points
Get-AzureRmSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.$ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

>[AZURE.NOTE] Una vez se haya completado, puede configurar la base de datos recuperado por siguientes [Configurar la base de datos después de recuperación][].


## <a name="restore-a-deleted-database"></a>Restaurar una base de datos eliminado

Para restaurar una base de datos eliminado, use el cmdlet [AzureRmSqlDatabase restaurar][] .

1. Abrir Windows PowerShell.
2. Conectarse a su cuenta de Azure y todas las suscripciones asociadas a su cuenta de la lista.
3. Seleccione la suscripción que contiene la base de datos eliminado pueden restaurarse.
4. Obtener la base de datos eliminado.
5. Restaurar la base de datos eliminado.
6. Compruebe que la base de datos está en línea.

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# Get the deleted database to restore
$DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupNam -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status
```

>[AZURE.NOTE] Una vez se haya completado, puede configurar la base de datos recuperado por siguientes [Configurar la base de datos después de recuperación][].


## <a name="restore-from-an-azure-geographical-region"></a>Restaurar a partir de una región geográfica Azure

Para recuperar una base de datos, use el cmdlet [AzureRmSqlDatabase restaurar][] .

1. Abrir Windows PowerShell.
2. Conectarse a su cuenta de Azure y todas las suscripciones asociadas a su cuenta de la lista.
3. Seleccione la suscripción que contiene la base de datos pueden restaurarse.
4. Obtener la base de datos que desea recuperar.
5. Crear la solicitud de recuperación de la base de datos.
6. Comprobar el estado de la base de datos restaurado geo.

```Powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID

# Verify that the geo-restored database is online
$GeoRestoredDatabase.status
```

>[AZURE.NOTE] Para configurar la base de datos después de que se haya completado, vea [Configurar la base de datos después de recuperación][]. 


La base de datos recuperado estará habilitado TDE si la base de datos está habilitada para TDE.


## <a name="next-steps"></a>Pasos siguientes
Para obtener información sobre las características de continuidad empresarial de ediciones de base de datos de SQL Azure, lea la [Introducción de continuidad empresarial de base de datos de SQL Azure][].

<!--Image references-->

<!--Article references-->
[Información general de continuidad de negocio base de datos de SQL Azure]: sql-database-business-continuity.md
[Solicitar un cambio de cuota DTU]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change
[Configurar la base de datos después de la recuperación]: ./sql-database-disaster-recovery.md#configure-your-database-after-recovery
[Cómo instalar y configurar PowerShell de Azure]: powershell-install-configure.md
[Información general]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configurar la base de datos después de la recuperación]: ./sql-database-disaster-recovery.md#configure-your-database-after-recovery

<!--MSDN references-->
[Restaurar AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
[Instalador de plataforma Web de Microsoft]: https://aka.ms/webpi-azps
