<properties 
    pageTitle="Copiar una base de datos de SQL Azure con PowerShell | Microsoft Azure" 
    description="Crear copia de una base de datos de SQL Azure con PowerShell" 
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="09/08/2016"
    ms.author="sstein"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="copy-an-azure-sql-database-using-powershell"></a>Copiar una base de datos de SQL Azure con PowerShell


> [AZURE.SELECTOR]
- [Información general](sql-database-copy.md)
- [Portal de Azure](sql-database-copy-portal.md)
- [PowerShell](sql-database-copy-powershell.md)
- [T-SQL](sql-database-copy-transact-sql.md)

Este artículo le muestra cómo copiar una base de datos SQL con PowerShell en el mismo servidor, en un servidor diferente, o copiar una base de datos en un [grupo elásticos de la base de datos](sql-database-elastic-pool.md). La operación de copia de la base de datos utiliza el cmdlet [AzureRmSqlDatabaseCopy de nuevo](https://msdn.microsoft.com/library/mt603644.aspx) . 


Para completar este artículo, se necesita lo siguiente:

- Una base de datos de SQL Azure (una base de datos para copiar). Si no tiene una base de datos SQL, crear una siguiendo los pasos de este artículo: [crear la primera base de datos de SQL Azure](sql-database-get-started.md).
- La última versión de PowerShell de Azure. Para obtener información detallada, vea [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md).


Solo se admiten muchas características nuevas de base de datos SQL cuando se utiliza el [modelo de implementación de administrador de recursos de Azure](../azure-resource-manager/resource-group-overview.md), por lo que ejemplos usan los [cmdlets de PowerShell de base de datos de SQL Azure](https://msdn.microsoft.com/library/azure/mt574084.aspx) para el Administrador de recursos. El modelo de implementación clásico existente [cmdlets (clásico) de base de datos de SQL Azure](https://msdn.microsoft.com/library/azure/dn546723.aspx) son compatibles para la compatibilidad con versiones anteriores, pero se recomienda que usar los cmdlets del Administrador de recursos.


>[AZURE.NOTE] Dependiendo del tamaño de la base de datos, la operación de copia puede tardar algún tiempo en completarse.


## <a name="copy-a-sql-database-to-the-same-server"></a>Copiar una base de datos SQL en el mismo servidor

Para crear una copia en el mismo servidor, omita el `-CopyServerName` parámetro (o establecer en el mismo servidor).

    New-AzureRmSqlDatabaseCopy -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -CopyDatabaseName "database1_copy"

## <a name="copy-a-sql-database-to-a-different-server"></a>Copiar una base de datos SQL en un servidor diferente

Para crear la copia en un servidor distinto, incluya el `-CopyServerName` parámetro y establézcalo en un servidor distinto. Ya debe existir la *copia* del servidor. Si está en otro grupo de recursos, también debe incluir la `-CopyResourceGroupName` parámetro.

    New-AzureRmSqlDatabaseCopy -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -CopyServerName "server2" -CopyDatabaseName "database1_copy"


## <a name="copy-a-sql-database-into-an-elastic-database-pool"></a>Copiar una base de datos SQL en un grupo de elásticos de la base de datos

Para crear una copia de una base de datos SQL en un grupo, establezca la `-ElasticPoolName` parámetro a un grupo existente.

    New-AzureRmSqlDatabaseCopy -ResourceGroupName "resourcegoup1" -ServerName "server1" -DatabaseName "database1" -CopyResourceGroupName "poolResourceGroup" -CopyServerName "poolServer1" -CopyDatabaseName "database1_copy" -ElasticPoolName "poolName"


## <a name="resolve-logins"></a>Resolver inicios de sesión

Para resolver los inicios de sesión después de que finalice la operación de copia, vea [resolver inicios de sesión](sql-database-copy-transact-sql.md#resolve-logins-after-the-copy-operation-completes)


## <a name="example-powershell-script"></a>Secuencia de comandos de PowerShell de ejemplo

La siguiente secuencia de comandos presupone todos los grupos de recursos, servidores, y el grupo ya existe (reemplace los valores de variables con los recursos existentes). Todo lo que debe existir, excepto la copia de la base de datos.

    # Sign in to Azure and set the subscription to work with
    # ------------------------------------------------------
    $SubscriptionId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    Add-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $SubscriptionId
    
    
    # SQL database source (the existing database to copy)
    # ---------------------------------------------------
    $sourceDbName = "db1"
    $sourceDbServerName = "server1"
    $sourceDbResourceGroupName = "rg1"
    
    # SQL database copy (the new db to be created)
    # --------------------------------------------
    $copyDbName = "db1_copy"
    $copyDbServerName = "server2"
    $copyDbResourceGroupName = "rg2"
    
    # Copy a database to the same server
    # ----------------------------------
    New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyDatabaseName $copyDbName
    
    # Copy a database to a different server
    # -------------------------------------
    New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyResourceGroupName $copyDbResourceGroupName -CopyServerName $copyDbServerName -CopyDatabaseName $copyDbName
    
    # Copy a database into an elastic database pool
    # ---------------------------------------------
    $poolName = "pool1"
    
    New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyResourceGroupName $copyDbResourceGroupName -CopyServerName $copyDbServerName -ElasticPoolName $poolName -CopyDatabaseName $copyDbName



    

## <a name="next-steps"></a>Pasos siguientes

- Para obtener información general de la copia de una base de datos de SQL Azure, vea [Copiar una base de datos de SQL Azure](sql-database-copy.md) .
- Consulte [Copiar una base de datos de SQL Azure con el portal de Azure](sql-database-copy-portal.md) para copiar una base de datos con el portal de Azure.
- Consulte [Copiar una base de datos de SQL Azure con SQL T](sql-database-copy-transact-sql.md) para copiar una base de datos mediante Transact-SQL.
- Consulte [cómo administrar la seguridad de la base de datos de SQL Azure tras la recuperación](sql-database-geo-replication-security-config.md) para obtener información acerca de cómo administrar usuarios y los inicios de sesión al copiar una base de datos a otro servidor lógico.


## <a name="additional-resources"></a>Recursos adicionales

- [Nueva AzureRmSqlDatabase](https://msdn.microsoft.com/library/mt603644.aspx)
- [Get-AzureRmSqlDatabaseActivity](https://msdn.microsoft.com/library/mt603687.aspx)
- [Administrar inicios de sesión](sql-database-manage-logins.md)
- [Conectarse a la base de datos de SQL con SQL Server Management Studio y realizar una consulta de SQL de T de ejemplo](sql-database-connect-query-ssms.md)
- [Exportar la base de datos a una MOCHILA](sql-database-export.md)
- [Información general de continuidad empresarial](sql-database-business-continuity.md)
- [Documentación de la base de datos SQL](https://azure.microsoft.com/documentation/services/sql-database/)
- [Referencia de Cmdlet de PowerShell de base de datos SQL Azure](https://msdn.microsoft.com/library/mt574084.aspx)
