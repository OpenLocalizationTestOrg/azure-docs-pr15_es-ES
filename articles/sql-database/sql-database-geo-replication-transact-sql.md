<properties
    pageTitle="Configurar la replicación de Geo para base de datos SQL Azure con Transact-SQL | Microsoft Azure"
    description="Configurar la replicación de Geo para la base de datos de SQL Azure con Transact-SQL"
    services="sql-database"
    documentationCenter=""
    authors="CarlRabeler"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="NA"
    ms.date="10/13/2016"
    ms.author="carlrab"/>

# <a name="configure-geo-replication-for-azure-sql-database-with-transact-sql"></a>Configurar la replicación de Geo para base de datos SQL Azure con Transact-SQL

> [AZURE.SELECTOR]
- [Información general](sql-database-geo-replication-overview.md)
- [Portal de Azure](sql-database-geo-replication-portal.md)
- [PowerShell](sql-database-geo-replication-powershell.md)
- [T-SQL](sql-database-geo-replication-transact-sql.md)

Este artículo le muestra cómo configurar la replicación de Geo Active para una base de datos de SQL Azure con Transact-SQL.

Para iniciar la migración tras error mediante Transact-SQL, vea [iniciar una migración tras error planificada o para la base de datos de Azure SQL con Transact-SQL](sql-database-geo-replication-failover-transact-sql.md).

>[AZURE.NOTE] Replicación de Geo Active (secundarios legibles) ahora está disponible para todas las bases de datos en todos los niveles de servicio. En abril de 2017 se retirará el tipo secundario no se puede leer y bases de datos no se puede leer existentes se actualizarán automáticamente a secundarios legibles.

Para configurar la replicación de Geo Active mediante Transact-SQL, necesita lo siguiente:

- Una suscripción de Azure.
- Un servidor de base de datos de SQL Azure lógico <MyLocalServer> y una base de datos SQL <MyDB> -la base de datos principal que desea duplicar.
- Una o más bases de datos de SQL Azure servidores lógicos < MySecondaryServer(n) > - los servidores lógicos que se van a los servidores de partner en la que va a crear bases de datos secundarios.
- Un inicio de sesión es DBManager en el principal tiene db_ownership de la base de datos local, podrá replicar geo, y ser DBManager en los servidores de asociado a la que va a configurar replicación Geo.
- SQL Server Management Studio (SSMS)

> [AZURE.IMPORTANT] Se recomienda usar siempre la versión más reciente de Management Studio permanezca sincronizado con las actualizaciones de Microsoft Azure y base de datos de SQL. [Actualizar SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


## <a name="add-secondary-database"></a>Agregar base de datos secundaria

Puede usar la instrucción **ALTER DATABASE** para crear una base de datos secundaria replicar geo en un servidor de partner. Ejecute esta instrucción en la base de datos del servidor que contiene la base de datos se pueden replicar. La base de datos replicada geo (la "base de datos principal") tendrá el mismo nombre que la base de datos que se replicada y, de forma predeterminada, tienen el mismo nivel de servicio que la base de datos principal. La base de datos secundario puede ser legible o no se puede leer y puede ser una base de datos o un databbase elástico. Para obtener más información, vea [Modificar base de datos (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) y [Niveles de servicio](sql-database-service-tiers.md).
Después de crear la base de datos secundario y visible, datos, comenzará la replicación de forma asincrónica de la base de datos principal. Los pasos siguientes describen cómo configurar la replicación de Geo con Management Studio. Se proporcionan pasos para crear secundarios no se puede leer y legibles con una base de datos o una base de datos elástico.

> [AZURE.NOTE] Si existe una base de datos en el servidor especificado asociado con el mismo nombre que la base de datos principal se producirá un error en el comando.


### <a name="add-non-readable-secondary-single-database"></a>Agregar secundario no se puede leer (base de datos único)

Use los siguientes pasos para crear un secundario no se puede leer como una base de datos.

1. Con la versión 13.0.600.65 o posterior de SQL Server Management Studio.

     > [AZURE.IMPORTANT] Descargue la [última](https://msdn.microsoft.com/library/mt238290.aspx) versión de SQL Server Management Studio. Se recomienda usar siempre la versión más reciente de Management Studio permanezca sincronizado con actualizaciones en el portal de Azure.


2. Abra la carpeta de bases de datos, expanda la carpeta de **Bases de datos de sistema** , haga clic en **patrón**y, a continuación, haga clic en **Nueva consulta**.

3. Use la siguiente instrucción **ALTER DATABASE** para convertir una base de datos local en una réplica de Geo principal con una base de datos secundario no se puede leer en MySecondaryServer1 donde MySecondaryServer1 es el nombre del servidor descriptivo.

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer1> WITH (ALLOW_CONNECTIONS = NO);

4. Haga clic en **Ejecutar** para ejecutar la consulta.


### <a name="add-readable-secondary-single-database"></a>Agregar secundario legible (base de datos único)
Use los siguientes pasos para crear un secundario legible como una base de datos.

1. En Management Studio, conéctese a su servidor de base de datos de SQL Azure lógico.

2. Abra la carpeta de bases de datos, expanda la carpeta de **Bases de datos de sistema** , haga clic en **patrón**y, a continuación, haga clic en **Nueva consulta**.

3. Use la siguiente instrucción **ALTER DATABASE** para convertir una base de datos local en una réplica de Geo principal con una base de datos secundaria legible en un servidor secundario.

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer2> WITH (ALLOW_CONNECTIONS = ALL);

4. Haga clic en **Ejecutar** para ejecutar la consulta.



### <a name="add-non-readable-secondary-elastic-database"></a>Agregar secundario no se puede leer (base de datos elástica)

Use los siguientes pasos para crear un secundario no se puede leer como una base de datos elástico.

1. En Management Studio, conéctese a su servidor de base de datos de SQL Azure lógico.

2. Abra la carpeta de bases de datos, expanda la carpeta de **Bases de datos de sistema** , haga clic en **patrón**y, a continuación, haga clic en **Nueva consulta**.

3. Use la siguiente instrucción **ALTER DATABASE** para convertir una base de datos local en una réplica de Geo principal con una base de datos secundario no se puede leer en un servidor secundario en un grupo de elástico.

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer3> WITH (ALLOW_CONNECTIONS = NO
           , SERVICE_OBJECTIVE = ELASTIC_POOL (name = MyElasticPool1));

4. Haga clic en **Ejecutar** para ejecutar la consulta.



### <a name="add-readable-secondary-elastic-database"></a>Agregar secundario legible (base de datos elástica)
Use los siguientes pasos para crear un secundario legible como una base de datos elástico.

1. En Management Studio, conéctese a su servidor de base de datos de SQL Azure lógico.

2. Abra la carpeta de bases de datos, expanda la carpeta de **Bases de datos de sistema** , haga clic en **patrón**y, a continuación, haga clic en **Nueva consulta**.

3. Use la siguiente instrucción **ALTER DATABASE** para convertir una base de datos local en una réplica de Geo principal con una base de datos secundario legible en un servidor secundario en un grupo de elástico.

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer4> WITH (ALLOW_CONNECTIONS = ALL
           , SERVICE_OBJECTIVE = ELASTIC_POOL (name = MyElasticPool2));

4. Haga clic en **Ejecutar** para ejecutar la consulta.



## <a name="remove-secondary-database"></a>Quitar la base de datos secundaria

Puede usar la instrucción **ALTER DATABASE** para terminar permanentemente la asociación de replicación entre una base de datos secundaria y su principal. Esta declaración se ejecuta en la base de datos en el que reside la base de datos principal. Después de la finalización de la relación, la base de datos secundaria se convierte en una base de datos de lectura y escritura normal. Si la conectividad de base de datos secundaria se rompe el comando se realiza correctamente pero el secundario se convertirá en lectura y escritura después de que se restaura la conectividad. Para obtener más información, vea [Modificar base de datos (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) y [Niveles de servicio](sql-database-service-tiers.md).

Use los siguientes pasos para quitar replicar geo secundario de una asociación de replicación Geo.

1. En Management Studio, conéctese a su servidor de base de datos de SQL Azure lógico.

2. Abra la carpeta de bases de datos, expanda la carpeta de **Bases de datos de sistema** , haga clic en **patrón**y, a continuación, haga clic en **Nueva consulta**.

3. Use la siguiente instrucción **ALTER DATABASE** para quitar un secundario replicar geo.

        ALTER DATABASE <MyDB>
           REMOVE SECONDARY ON SERVER <MySecondaryServer1>;

4. Haga clic en **Ejecutar** para ejecutar la consulta.

## <a name="monitor-geo-replication-configuration-and-health"></a>Configuración de replicación Geo monitor y el estado

Tareas de supervisión incluyen la supervisión de la configuración de replicación Geo y supervisar el estado de la replicación de datos.  Puede usar la vista de administración dinámica **sys.dm_geo_replication_links** en la base de datos para devolver información sobre todos los vínculos de replicación existente para cada base de datos en el servidor de base de datos de SQL Azure lógico. Esta vista contiene una fila para cada uno de los vínculos de replicación entre las bases de datos principales y secundarios. Puede usar la vista de administración dinámica **sys.dm_replication_link_status** para devolver una fila para cada base de datos de SQL Azure que actualmente está ocupado en un vínculo de réplica de replicación. Esto incluye bases de datos principales y secundarias. Si hay más de un vínculo de replicación continua para cada base de datos principal, esta tabla contiene una fila para cada una de las relaciones. Se crea la vista en todas las bases de datos, incluidos al patrón lógico. Sin embargo, consultar esta vista en el patrón de lógico, devuelve un conjunto vacío. Puede usar la vista de administración dinámica **sys.dm_operation_status** para mostrar el estado de todas las operaciones de base de datos, incluido el estado de los vínculos de replicación. Para obtener más información, vea [sys.geo_replication_links (base de datos de SQL Azure)](https://msdn.microsoft.com/library/mt575501.aspx), [sys.dm_geo_replication_link_status (base de datos de SQL Azure)](https://msdn.microsoft.com/library/mt575504.aspx)y [sys.dm_operation_status (base de datos de SQL Azure)](https://msdn.microsoft.com/library/dn270022.aspx).

Siga los pasos siguientes para supervisar una asociación Geo replicación.

1. En Management Studio, conéctese a su servidor de base de datos de SQL Azure lógico.

2. Abra la carpeta de bases de datos, expanda la carpeta de **Bases de datos de sistema** , haga clic en **patrón**y, a continuación, haga clic en **Nueva consulta**.

3. Use la siguiente instrucción para mostrar todas las bases de datos con vínculos de replicación Geo.

        SELECT database_id, start_date, modify_date, partner_server, partner_database, replication_state_desc, role, secondary_allow_connections_desc FROM [sys].geo_replication_links;

4. Haga clic en **Ejecutar** para ejecutar la consulta.
5. Abra la carpeta de bases de datos, expanda la carpeta de **Bases de datos de sistema** , haga clic en **MyDB**y, a continuación, haga clic en **Nueva consulta**.
6. Utilice la siguiente instrucción para mostrar la última replicación e intervalos de replicación tiempo de las bases de datos secundarios de MyDB.

        SELECT link_guid, partner_server, last_replication, replication_lag_sec FROM sys.dm_geo_replication_link_status

7. Haga clic en **Ejecutar** para ejecutar la consulta.
8. Use la siguiente instrucción para mostrar las operaciones de replicación geo más recientes asociadas a la base de datos MyDB.

        SELECT * FROM sys.dm_operation_status where major_resource_id = 'MyDB'
        ORDER BY start_time DESC

9. Haga clic en **Ejecutar** para ejecutar la consulta.

## <a name="upgrade-a-non-readable-secondary-to-readable"></a>Actualizar un secundario no se puede leer a legible

En abril de 2017 se retirará el tipo secundario no se puede leer y bases de datos no se puede leer existentes se actualizarán automáticamente a secundarios legibles. Si utiliza actualmente secundarios no se puede leer y desea actualizarlos para que sea legible, puede usar los siguientes pasos simples para cada secundario.

> [AZURE.IMPORTANT] No hay ningún método de autoservicio de la actualización en contexto de un secundario no se puede leer a legible. Si coloca el solo secundario, la base de datos principal se permanecerán desprotegido hasta que la nueva secundaria esté plenamente sincronizada. Si SLA de la aplicación requiere que el principal siempre está protegido, considere la posibilidad de crear un paralelo secundario en otro servidor antes de aplicar la actualización pasos anteriores. Cada primario puede tener hasta 4 bases de datos secundarios.


1. En primer lugar, conectarse al servidor *secundario* y anular la base de datos no se puede leer secundario:  
        
        DROP DATABASE <MyNonReadableSecondaryDB>;

2. Conectar con el servidor *principal* ahora y agregar una nueva legible secundaria

        ALTER DATABASE <MyDB>
            ADD SECONDARY ON SERVER <MySecondaryServer> WITH (ALLOW_CONNECTIONS = ALL);




## <a name="next-steps"></a>Pasos siguientes

- Para obtener más información sobre Active Geo replicación, vea - [Replicación de Geo Active](sql-database-geo-replication-overview.md)
- Para obtener una descripción de la continuidad empresarial y escenarios, vea [información general de continuidad empresarial](sql-database-business-continuity.md)
