<properties 
    pageTitle="Crear o mover una base de datos de SQL Azure a un grupo de elástico utilizando T SQL | Microsoft Azure" 
    description="Use T SQL para crear una base de datos de SQL Azure en un grupo de elástico. O T SQL para mover la datbase dentro y fuera de los grupos." 
    services="sql-database" 
    documentationCenter="" 
    authors="srinia" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-management" 
    ms.date="05/27/2016"
    ms.author="srinia"/>

# <a name="monitor-and-manage-an-elastic-database-pool-with-transact-sql"></a>Supervisar y administrar un grupo de elásticos de la base de datos con Transact-SQL  

> [AZURE.SELECTOR]
- [Portal de Azure](sql-database-elastic-pool-manage-portal.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)
- [T-SQL](sql-database-elastic-pool-manage-tsql.md)

Usar los comandos [Crear base de datos (base de datos de SQL Azure)](https://msdn.microsoft.com/library/dn268335.aspx) y [Modificar Database(Azure SQL Database)](https://msdn.microsoft.com/library/mt574871.aspx) para crear y mover bases de datos dentro y fuera de grupos elásticos. El grupo elástico debe existir antes de poder usar estos comandos. Estos comandos afectan solo las bases de datos. Creación de nuevos grupos y la configuración de propiedades del grupo (por ejemplo, Mín y Máx eDTUs) no se puede cambiar con comandos T SQL.

## <a name="create-a-new-database-in-an-elastic-pool"></a>Crear una nueva base de datos en un grupo de elástico
Utilice el comando Crear base de datos con la opción SERVICE_OBJECTIVE.   

    CREATE DATABASE db1 ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [S3M100] ));
    -- Create a database named db1 in a pool named S3M100.

Todas las bases de datos en un grupo de elástico heredan el nivel de servicio del grupo elástico (Basic, estándar, Premium). 


## <a name="move-a-database-between-elastic-pools"></a>Mover una base de datos entre grupos elásticas
Utilice el comando ALTER DATABASE con la modificación y configurar el servicio\_opción objetivo como ELÁSTICA\_grupo; Establezca el nombre en el nombre del grupo de destino.

    ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [PM125] ));
    -- Move the database named db1 to a pool named P1M125  

## <a name="move-a-database-into-an-elastic-pool"></a>Mover una base de datos a un grupo de elástico 
Utilice el comando ALTER DATABASE con la modificación y configurar el servicio\_opción objetivo como ELASTIC_POOL; Establezca el nombre en el nombre del grupo de destino.

    ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [S3100] ));
    -- Move the database named db1 to a pool named S3100.

## <a name="move-a-database-out-of-an-elastic-pool"></a>Mover una base de datos fuera de un grupo de elástico
Utilice el comando Modificar base de datos y establezca la SERVICE_OBJECTIVE a uno de los niveles de rendimiento (S0, S1, etcetera).

    ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = 'S1');
    -- Changes the database into a stand-alone database with the service objective S1.

## <a name="list-databases-in-an-elastic-pool"></a>Bases de datos de lista en un grupo de elástico
Usar la [sys.database\_service \_objetivos vista](https://msdn.microsoft.com/library/mt712619) para obtener una lista de todas las bases de datos en un grupo de elástico. Inicie sesión en el patrón de base de datos a la vista de la consulta.

    SELECT d.name, slo.*  
    FROM sys.databases d 
    JOIN sys.database_service_objectives slo  
    ON d.database_id = slo.database_id
    WHERE elastic_pool_name = 'MyElasticPool'; 

## <a name="get-resource-usage-data-for-a-pool"></a>Obtener datos de uso de recursos para un grupo

Usar la [sys.elastic\_grupo \_recursos \_estadísticas vista](https://msdn.microsoft.com/library/mt280062.aspx) para examinar las estadísticas de uso de recursos de un grupo de elástico en un servidor lógico. Inicie sesión en el patrón de base de datos a la vista de la consulta.

    SELECT * FROM sys.elastic_pool_resource_stats 
    WHERE elastic_pool_name = 'MyElasticPool'
    ORDER BY end_time DESC;

## <a name="get-resource-usage-for-an-elastic-database"></a>Obtener el uso de recursos para una base de datos elástica

Usar la [sys.dm\_ db\_ recursos\_estadísticas vista](https://msdn.microsoft.com/library/dn800981.aspx) o [sys.resource \_ver estadísticas](https://msdn.microsoft.com/library/dn269979.aspx) para examinar las estadísticas de uso de recursos de una base de datos en un grupo de elástico. Este proceso es similar a las consultas de uso de recursos para cualquier base de datos única.

## <a name="next-steps"></a>Pasos siguientes

Después de crear un grupo de elásticos de la base de datos, puede administrar elásticos bases de datos en el grupo mediante la creación de trabajos elásticos. Trabajos elásticos facilitan la ejecución de scripts de SQL T frente a cualquier número de bases de datos en el grupo. Para obtener más información, vea [información general de los trabajos de elásticos de la base de datos](sql-database-elastic-jobs-overview.md). 

Consulte [escala fuera con la base de datos de SQL Azure](sql-database-elastic-scale-introduction.md): use herramientas de base de datos elástica para escalado, mover los datos, la consulta o crear transacciones.
