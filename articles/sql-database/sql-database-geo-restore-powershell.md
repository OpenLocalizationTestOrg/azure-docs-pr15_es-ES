<properties
    pageTitle="Restaurar una base de datos de SQL Azure desde una copia de seguridad geo redundantes (PowerShell) | Microsoft Azure"
    description="Restaurar una base de datos de SQL Azure en un nuevo servidor desde una copia de seguridad geo redundantes"
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

# <a name="restore-an-azure-sql-database-from-a-geo-redundant-backup-by-using-powershell"></a>Restaurar una base de datos de SQL Azure desde una copia de seguridad geo redundantes con PowerShell


> [AZURE.SELECTOR]
- [Información general](sql-database-recovery-using-backups.md)
- [Geo-restaurar: Portal de Azure](sql-database-geo-restore-portal.md)

En este artículo le muestra cómo restaurar la base de datos en un servidor nuevo mediante geo restaurar. Esto puede hacerse a través de PowerShell.

[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="geo-restore-your-database-into-a-standalone-database"></a>Geo-restaurar la base de datos en una base de datos independiente

1. Obtener la copia de seguridad geo redundantes de la base de datos que desea restaurar mediante la [Get-AzureRmSqlDatabaseGeoBackup] (https://msdn.microsoft.com/library/azure/mt693388(v=azure.300\).aspx) cmdlet.

        $GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. Iniciar la restauración de la copia de seguridad geo redundantes mediante la [restaurar-AzureRmSqlDatabase] (https://msdn.microsoft.com/library/azure/mt693390(v=azure.300\).aspx) cmdlet.

        Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "TargetResourceGroup" -ServerName "TargetServer" -TargetDatabaseName "RestoredDatabase" –ResourceId $GeoBackup.ResourceID -Edition "Standard" -RequestedServiceObjectiveName "S2"


## <a name="geo-restore-your-database-into-an-elastic-database-pool"></a>Geo-restaurar la base de datos en un grupo de elásticos de la base de datos

1. Obtener la copia de seguridad geo redundantes de la base de datos que desea restaurar mediante la [Get-AzureRmSqlDatabaseGeoBackup] (https://msdn.microsoft.com/library/azure/mt693388(v=azure.300\).aspx) cmdlet.

        $GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. Iniciar la restauración de la copia de seguridad geo redundantes mediante la [restaurar-AzureRmSqlDatabase] (https://msdn.microsoft.com/library/azure/mt693390(v=azure.300\).aspx) cmdlet. Especifique el nombre del grupo que desea restaurar la base de datos en.

        Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "TargetResourceGroup" -ServerName "TargetServer" -TargetDatabaseName "RestoredDatabase" –ResourceId $GeoBackup.ResourceID –ElasticPoolName "elasticpool01"  


## <a name="next-steps"></a>Pasos siguientes

- Para obtener una descripción de la continuidad empresarial y escenarios, vea [información general de continuidad empresarial](sql-database-business-continuity.md).
- Para obtener información sobre las copias de seguridad automatizadas de base de datos SQL Azure, consulte la [base de datos SQL había automatizado copias de seguridad](sql-database-automated-backups.md).
- Para obtener información sobre el uso de copias de seguridad automatizadas de recuperación, consulte [restaurar una base de datos de las copias de seguridad iniciado por el servicio](sql-database-recovery-using-backups.md).
- Para obtener información sobre las opciones de recuperación más rápidas, vea [Replicación de Geo activo](sql-database-geo-replication-overview.md).  
- Para obtener información sobre el uso de copias de seguridad automatizadas para archivar, consulte [copia de la base de datos](sql-database-copy.md).
