<properties
    pageTitle="Restaurar una base de datos de SQL Azure a un punto anterior en el tiempo (PowerShell) | Microsoft Azure"
    description="Restaurar una base de datos de SQL Azure a un punto anterior en el tiempo"
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
    ms.workload="NA"
    ms.date="07/17/2016"
    ms.author="sstein"/>

# <a name="restore-an-azure-sql-database-to-a-previous-point-in-time-with-powershell"></a>Restaurar una base de datos de SQL Azure a un punto anterior en el tiempo con PowerShell

> [AZURE.SELECTOR]
- [Información general](sql-database-recovery-using-backups.md)
- [Restauración de punto en el tiempo: Portal de Azure](sql-database-point-in-time-restore-portal.md)

Este artículo le muestra cómo restaurar la base de datos en un momento anterior en el tiempo de [base de datos SQL había automatizado copias de seguridad](sql-database-automated-backups.md). Puede hacerlo con PowerShell.

[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="restore-your-database-to-a-point-in-time-as-a-standalone-database"></a>Restaurar la base de datos a un punto en el tiempo que una base de datos independiente

1. Obtener la base de datos que desea restaurar mediante la [Get-AzureRmSqlDatabase] (https://msdn.microsoft.com/library/azure/mt603648(v=azure.300\).aspx) cmdlet.

        $Database = Get-AzureRmSqlDatabase -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. Restaurar la base de datos a un punto en el tiempo mediante la [restaurar-AzureRmSqlDatabase] (https://msdn.microsoft.com/library/azure/mt693390(v=azure.300\).aspx) cmdlet.

        Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime UTCDateTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $Database.ResourceID -Edition "Standard" -ServiceObjectiveName "S2"


## <a name="restore-your-database-to-a-point-in-time-into-an-elastic-database-pool"></a>Restaurar la base de datos a un punto en el tiempo en un grupo de elásticos de la base de datos

1. Obtener la base de datos que desea restaurar mediante la [Get-AzureRmSqlDatabase] (https://msdn.microsoft.com/library/azure/mt603648(v=azure.300\).aspx) cmdlet.

        $Database = Get-AzureRmSqlDatabase -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. Restaurar la base de datos a un punto en el tiempo mediante la [restaurar-AzureRmSqlDatabase] (https://msdn.microsoft.com/library/azure/mt693390(v=azure.300\).aspx) cmdlet.

        Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime UTCDateTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $Database.ResourceID –ElasticPoolName "elasticpool01"


## <a name="next-steps"></a>Pasos siguientes

- Para obtener una descripción de la continuidad empresarial y escenarios, vea [información general de continuidad empresarial](sql-database-business-continuity.md)
- Para obtener información sobre las copias de seguridad automatizadas de base de datos SQL Azure, consulte [copias de seguridad de automatizado de base de datos SQL](sql-database-automated-backups.md)
- Para obtener información sobre el uso de copias de seguridad automatizadas de recuperación, consulte [restaurar una base de datos de las copias de seguridad iniciado por el servicio](sql-database-recovery-using-backups.md)
- Para obtener información sobre las opciones de recuperación más rápidas, vea [Replicación de Geo activo](sql-database-geo-replication-overview.md)  
- Para obtener información sobre el uso de copias de seguridad automatizadas de archivado, vea [Copiar de base de datos](sql-database-copy.md)
