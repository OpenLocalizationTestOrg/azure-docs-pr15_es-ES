<properties
    pageTitle="Supervisar y administrar un grupo de base de datos elástico con C# | Microsoft Azure"
    description="Utilizar técnicas de desarrollo de base de datos de C# para administrar un grupo de elásticos de la base de datos de la base de datos de SQL Azure."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="csharp"
    ms.workload="data-management"
    ms.date="10/04/2016"
    ms.author="sstein"/>

# <a name="monitor-and-manage-an-elastic-database-pool-with-cx23"></a>Supervisar y administrar un grupo de base de datos elástico con C & #x 23; 

> [AZURE.SELECTOR]
- [Portal de Azure](sql-database-elastic-pool-manage-portal.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)
- [T-SQL](sql-database-elastic-pool-manage-tsql.md)


Obtenga información sobre cómo administrar un [grupo elásticos de la base de datos](sql-database-elastic-pool.md) con C & #x 23;. 

>[AZURE.NOTE] Solo se admiten muchas características nuevas de base de datos de SQL cuando se utiliza el [modelo de implementación de administrador de recursos de Azure](../azure-resource-manager/resource-group-overview.md), por lo que debe usar siempre la información más reciente **biblioteca de administración de base de datos de Azure SQL para .NET ([documentos](https://msdn.microsoft.com/library/azure/mt349017.aspx) | [Paquete NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql))**. El antiguo [bibliotecas basados en el modelo de implementación clásica](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Sql) son compatibles para la compatibilidad con versiones anteriores, por lo que recomendamos usar las bibliotecas en función de administrador de recursos más recientes.

Para completar los pasos de este artículo, necesita lo siguiente:

- Un grupo elástico (el grupo que desea administrar). Para crear un grupo, vea [crear un grupo de base de datos elástico con C#](sql-database-elastic-pool-create-csharp.md).
- Visual Studio. Para obtener una copia gratuita de Visual Studio, consulte la página de [Descargas de Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs) .


## <a name="move-a-database-into-an-elastic-pool"></a>Mover una base de datos a un grupo de elástico

Puede mover una base de datos independiente dentro o fuera de un grupo.  

    // Retrieve current database properties.

    currentDatabase = sqlClient.Databases.Get("resourcegroup-name", "server-name", "Database1").Database;

    // Configure create or update parameters with existing property values, override those to be changed.
    DatabaseCreateOrUpdateParameters updatePooledDbParameters = new DatabaseCreateOrUpdateParameters()
    {
        Location = currentDatabase.Location,
        Properties = new DatabaseCreateOrUpdateProperties()
        {
            Edition = "Standard",
            RequestedServiceObjectiveName = "ElasticPool",
            ElasticPoolName = "ElasticPool1",
            MaxSizeBytes = currentDatabase.Properties.MaxSizeBytes,
            Collation = currentDatabase.Properties.Collation,
        }
    };

    // Update the database.
    var dbUpdateResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database1", updatePooledDbParameters);

## <a name="list-databases-in-an-elastic-pool"></a>Bases de datos de lista en un grupo de elástico

Para recuperar todas las bases de datos en un grupo, llame al método [ListDatabases](https://msdn.microsoft.com/library/microsoft.azure.management.sql.elasticpooloperationsextensions.listdatabases) .

    //List databases in the elastic pool
    DatabaseListResponse dbListInPool = sqlClient.ElasticPools.ListDatabases("resourcegroup-name", "server-name", "ElasticPool1");
    Console.WriteLine("Databases in Elastic Pool {0}", "server-name.ElasticPool1");
    foreach (Database db in dbListInPool)
    {
        Console.WriteLine("  Database {0}", db.Name);
    }

## <a name="change-performance-settings-of-a-pool"></a>Cambiar la configuración de rendimiento de un grupo

Recuperar una existente de las propiedades del grupo. Modifique los valores y ejecutar el método CreateOrUpdate.

    var currentPool = sqlClient.ElasticPools.Get("resourcegroup-name", "server-name", "ElasticPool1").ElasticPool;

    // Configure create or update parameters with existing property values, override those to be changed.
    ElasticPoolCreateOrUpdateParameters updatePoolParameters = new ElasticPoolCreateOrUpdateParameters()
    {
        Location = currentPool.Location,
        Properties = new ElasticPoolCreateOrUpdateProperties()
        {
            Edition = currentPool.Properties.Edition,
            DatabaseDtuMax = 50, /* Setting DatabaseDtuMax to 50 limits the eDTUs that any one database can consume */
            DatabaseDtuMin = 10, /* Setting DatabaseDtuMin above 0 limits the number of databases that can be stored in the pool */
            Dtu = (int)currentPool.Properties.Dtu,
            StorageMB = currentPool.Properties.StorageMB,  /* For a Standard pool there is 1 GB of storage per eDTU. */
        }
    };

    newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate("resourcegroup-name", "server-name", "ElasticPool1", newPoolParameters);


## <a name="latency-of-elastic-pool-operations"></a>Latencia de las operaciones del fondo elásticas

- Cambiar el eDTUs min por base de datos o max eDTUs por base de datos normalmente completa en cinco minutos o menos.
- Tiempo para cambiar el tamaño de grupo (eDTUs) depende el tamaño combinado de todas las bases de datos en el grupo. Calcular el promedio de cambios 90 minutos o menos por 100 GB. Por ejemplo, si el espacio total de todas las bases de datos en el grupo es 200 GB, a continuación, la latencia esperada para cambiar la eDTU de grupo por grupo es 3 horas o menos.




## <a name="additional-resources"></a>Recursos adicionales

- [Códigos de error SQL para las aplicaciones cliente de base de datos de SQL: error de conexión y otros problemas de la base de datos](sql-database-develop-error-messages.md).
- [Base de datos SQL](https://azure.microsoft.com/documentation/services/sql-database/)
- [API de administración de recursos de Azure](https://msdn.microsoft.com/library/azure/dn948464.aspx)
- [Crear un nuevo grupo de base de datos elástico con C#](sql-database-elastic-pool-create-csharp.md)
- [¿Cuándo se debe utilizar un grupo de la base de datos elástico?](sql-database-elastic-pool-guidance.md)
- Consulte [escala fuera con la base de datos de SQL Azure](sql-database-elastic-scale-introduction.md): use herramientas de base de datos elástica para escalado, mover los datos, la consulta o crear transacciones.

