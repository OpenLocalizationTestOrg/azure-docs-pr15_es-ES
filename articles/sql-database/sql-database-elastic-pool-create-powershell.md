<properties
    pageTitle="Crear un nuevo grupo de base de datos elástico con PowerShell | Microsoft Azure"
    description="Obtenga información sobre cómo usar PowerShell para la base de datos de SQL Azure escalado recursos mediante la creación de un grupo de base de datos elástico scalable para administrar varias bases de datos."
    services="sql-database"
    documentationCenter=""
    authors="srinia"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management"
    ms.date="05/27/2016"
    ms.author="srinia"/>

# <a name="create-a-new-elastic-database-pool-with-powershell"></a>Crear un nuevo grupo de base de datos elástico con PowerShell

> [AZURE.SELECTOR]
- [Portal de Azure](sql-database-elastic-pool-create-portal.md)
- [PowerShell](sql-database-elastic-pool-create-powershell.md)
- [C#](sql-database-elastic-pool-create-csharp.md)


Obtenga información sobre cómo crear un [grupo elásticos de la base de datos](sql-database-elastic-pool.md) de uso de cmdlets de PowerShell. 

Códigos de error comunes, vea [códigos de error SQL para las aplicaciones cliente de base de datos de SQL: error de conexión y otros problemas de la base de datos](sql-database-develop-error-messages.md).

> [AZURE.NOTE] Grupos de elásticos son disponible de forma general (GA) en todas las regiones Azure excepto Norte Central de EE y occidental India donde está actualmente en la vista previa.  Se proporcionará GA de grupos de elásticos en estas regiones tan pronto como sea posible. Además, grupos elásticos no admiten actualmente las bases de datos con [OLTP en memoria o análisis en memoria](sql-database-in-memory.md).


Debe estar ejecutándose Azure PowerShell 1.0 o superior. Para obtener información detallada, vea [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md).

## <a name="create-a-new-pool"></a>Crear un nuevo grupo

El cmdlet [New-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt619378.aspx) crea un nuevo grupo. Los valores de eDTU por grupo, min y max Dtus están limitados por el valor de nivel de servicio (básico, estándar o premium). Consulte [eDTU y almacenamiento de los límites de los grupos elásticas y elásticos bases de datos](sql-database-elastic-pool.md#eDTU-and-storage-limits-for-elastic-pools-and-elastic-databases).

    New-AzureRmSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100


## <a name="create-a-new-elastic-database-in-a-pool"></a>Crear una nueva base de datos elástico en un grupo

Usar el cmdlet [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339.aspx) y establezca el parámetro **ElasticPoolName** al grupo de destino. Para mover una base de datos existente en un grupo, vea [mover una base de datos en un grupo de elástico](sql-database-elastic-pool-manage-powershell.md#Move-a-database-into-an-elastic-pool).

    New-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## <a name="create-a-pool-and-populate-it-with-multiple-new-databases"></a>Crear un grupo y rellénela con varias bases de datos nuevas 

Creación de un gran número de bases de datos en un grupo puede tardar tiempo cuando terminado con el portal o los cmdlets de PowerShell que crear una sola base de datos en un momento. Para automatizar la creación de un grupo nuevo, consulte [CreateOrUpdateElasticPoolAndPopulate ](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae).   

## <a name="example-create-a-pool-using-powershell"></a>Ejemplo: crear un fondo con PowerShell 

Esta secuencia de comandos crea un nuevo grupo de recursos de Azure y un servidor de nuevo. Cuando se le solicite, proporcione un nombre de usuario administrador y la contraseña para el nuevo servidor (no las credenciales de Azure).

    $subscriptionId = '<your Azure subscription id>'
    $resourceGroupName = '<resource group name>'
    $location = '<datacenter location>'
    $serverName = '<server name>'
    $poolName = '<pool name>'
    $databaseName = '<database name>'

    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId

    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $serverName -Location $location -ServerVersion "12.0"
    New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $serverName -FirewallRuleName "rule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

    New-AzureRmSqlElasticPool -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100

    New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -ElasticPoolName $poolName -MaxSizeBytes 10GB



## <a name="next-steps"></a>Pasos siguientes

- [Administrar el grupo de servidores](sql-database-elastic-pool-manage-powershell.md)
- [Crear elásticos trabajos](sql-database-elastic-jobs-overview.md) Trabajos elásticos le permiten ejecutar secuencias de comandos T SQL en cualquier número de bases de datos en el grupo.
- [Escalar con la base de datos de SQL Azure](sql-database-elastic-scale-introduction.md): usar herramientas de elásticos de la base de datos a escalado.

