<properties 
    pageTitle="Iniciar un error planificado o para la base de datos de Azure SQL con Transact-SQL | Microsoft Azure" 
    description="Iniciar un error planificado o para la base de datos de SQL Azure con Transact-SQL" 
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
    ms.workload="data-management"
    ms.date="08/29/2016"
    ms.author="carlrab"/>

# <a name="initiate-a-planned-or-unplanned-failover-for-azure-sql-database-with-transact-sql"></a>Iniciar un error planificado o para la base de datos de Azure SQL con Transact-SQL


> [AZURE.SELECTOR]
- [Portal de Azure](sql-database-geo-replication-failover-portal.md)
- [PowerShell](sql-database-geo-replication-failover-powershell.md)
- [T-SQL](sql-database-geo-replication-failover-transact-sql.md)


Este artículo muestra cómo iniciar migración tras error a una base de datos de SQL secundaria mediante Transact-SQL. Para configurar la replicación de Geo, vea [Configurar Geo-replicación de base de datos de SQL Azure](sql-database-geo-replication-transact-sql.md).



Para iniciar la migración tras error, se necesita lo siguiente:

- Un inicio de sesión es DBManager en el principal tiene db_ownership de la base de datos local, podrá replicar geo, y ser DBManager en los servidores de asociado a la que va a configurar replicación Geo.
- SQL Server Management Studio (SSMS)


> [AZURE.IMPORTANT] Se recomienda usar siempre la versión más reciente de Management Studio permanezca sincronizado con las actualizaciones de Microsoft Azure y base de datos de SQL. [Actualizar SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).




## <a name="initiate-a-planned-failover-promoting-a-secondary-database-to-become-the-new-primary"></a>Iniciar un error planeado promover una base de datos secundaria para convertirse en el nuevo principal

Puede usar la instrucción **ALTER DATABASE** para aumentar el nivel de una base de datos secundaria para convertirse en la nueva base de datos principal de forma planeada, degradar principal existente para convertirse en un secundario. Esta declaración se ejecuta en la base de datos en el servidor lógico de base de datos de SQL Azure en la que reside el geo secundario base de datos replicada que se promociona. Esta funcionalidad está diseñada para failover planificado, como durante el aumenta el DR y requiere que la base de datos principal esté disponible.

El comando realiza el flujo de trabajo siguiente:

1. Cambia temporalmente la replicación en el modo sincrónico, todas las transacciones pendientes a la secundaria vaciado la causa y el bloqueo de todas las transacciones nuevas;

2. Cambia las funciones de las dos bases de datos en la asociación de replicación Geo.  

Esta secuencia garantiza que se sincronizan las dos bases de datos antes de cambiar los roles y, por tanto, no se producirá pérdida de datos. Hay un corto período durante el cual ambas bases de datos no están disponibles (del orden de 0 a 25 segundos) mientras se cambian los roles. Si la base de datos principal tiene varias bases de datos secundarios, el comando automáticamente a configurar los otros secundarios para conectar con el nuevo principal.  Toda la operación tendrá menos de un minuto en completarse en circunstancias normales. Para obtener más información, vea [Modificar base de datos (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) y [Niveles de servicio](sql-database-service-tiers.md).


Siga estos pasos para iniciar un error planeado.

1. En Management Studio, conectar con el servidor de base de datos de SQL Azure lógico en el que reside una base de datos secundaria replicar geo.

2. Abra la carpeta de bases de datos, expanda la carpeta de **Bases de datos de sistema** , haga clic en **patrón**y, a continuación, haga clic en **Nueva consulta**.

3. Use la siguiente instrucción **ALTER DATABASE** para cambiar la base de datos secundaria a la función principal.

        ALTER DATABASE <MyDB> FAILOVER;

4. Haga clic en **Ejecutar** para ejecutar la consulta.

>[AZURE.NOTE] En algunas ocasiones, es posible que no puede completar la operación y que puede aparecer atascada. En este caso, el usuario puede ejecutar el comando de migración tras error forzar y aceptar la pérdida de datos.


## <a name="initiate-an-unplanned-failover-from-the-primary-database-to-the-secondary-database"></a>Iniciar un planificado migración tras error de la base de datos principal a la base de datos secundario

Puede usar la instrucción **ALTER DATABASE** para aumentar el nivel de una base de datos secundaria para convertirse en la nueva base de datos principal de un modo no planeado, forzar la degradación del equipo principal existente para convertirse en un secundario a la vez cuando la base de datos principal ya no está disponible. Esta declaración se ejecuta en la base de datos en el servidor lógico de base de datos de SQL Azure en la que reside el geo secundario base de datos replicada que se promociona.

Esta funcionalidad está diseñada para recuperación cuando restaurar la disponibilidad de la base de datos es fundamental y algunas pérdidas de datos es aceptable. Cuando se invoca forzado migración tras error, la base de datos secundario especificado inmediatamente se convierte en la base de datos principal y comienza a aceptar las transacciones de escritura. Tan pronto como la base de datos principal original se pueda volver a conectar con esta nueva base de datos principal, se realiza una copia de seguridad incremental en la base de datos principal original y la base de datos principal se convierte en una base de datos secundaria para la nueva base de datos principal; posteriormente, es simplemente una réplica de sincronización de nuevo principal.

Sin embargo, porque no se admite la restauración de punto de tiempo en las bases de datos secundarios, si el usuario desea recuperar datos comprometidos a la base de datos principal antiguo que no ha tenido replicar a la nueva base de datos principal antes de la migración tras error forzada, el usuario necesita integrarse al soporte técnico para recuperar esta pérdida de datos.

Si la base de datos principal tiene varias bases de datos secundarios, el comando automáticamente a configurar los otros secundarios para conectar con el nuevo principal.

Realice los pasos siguientes para iniciar una migración tras error no planeado.

1. En Management Studio, conectar con el servidor de base de datos de SQL Azure lógico en el que reside una base de datos secundaria replicar geo.

2. Abra la carpeta de bases de datos, expanda la carpeta de **Bases de datos de sistema** , haga clic en **patrón**y, a continuación, haga clic en **Nueva consulta**.

3. Use la siguiente instrucción **ALTER DATABASE** para cambiar la base de datos secundaria a la función principal.

        ALTER DATABASE <MyDB>   FORCE_FAILOVER_ALLOW_DATA_LOSS;

4. Haga clic en **Ejecutar** para ejecutar la consulta.

>[AZURE.NOTE] Si el comando se emite cuando primario y secundario estén en línea se convertirá en el principal anterior la nueva secundaria inmediatamente sin sincronización de datos. Si es el principal pueden producirse confirmar las transacciones cuando se ejecuta el comando algunas pérdidas de datos.



## <a name="next-steps"></a>Pasos siguientes   

- Después de la migración, asegúrese de que se han configurado los requisitos de autenticación de su servidor y la base de datos en el nuevo principal. Para obtener más información, vea [seguridad de base de datos SQL después de recuperación](sql-database-geo-replication-security-config.md).
- Para obtener la recuperación después de un desastre utilizando replicación Geo activo, incluidas las anteriores y recuperación pasos posteriores y realizar un ejercicio de recuperación de desastres, consulte [Recuperación](sql-database-disaster-recovery.md)
- Para una entrada de blog sobre Active Geo replicación de Sasha Nosov, vea [el primer plano de nuevas capacidades de replicación Geo](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)
- Para obtener información acerca del diseño de aplicaciones de nube para usar la replicación de Geo Active, vea [Diseño de aplicaciones de nube para continuidad empresarial con replicación Geo](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- Para obtener información sobre el uso de la replicación de Geo Active con agrupaciones elásticos de la base de datos, vea [grupo elástico estrategias](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
- Para obtener información general de continurity de empresa, vea [Información general de continuidad empresarial](sql-database-business-continuity.md)
