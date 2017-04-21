<properties 
    pageTitle="Iniciar un error planificado o para la base de datos de Azure SQL con PowerShell | Microsoft Azure" 
    description="Iniciar un error planificado o para la base de datos de SQL Azure con PowerShell" 
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
    ms.workload="data-management" 
    ms.date="08/29/2016"
    ms.author="sstein"/>

# <a name="initiate-a-planned-or-unplanned-failover-for-azure-sql-database-with-powershell"></a>Iniciar un error planificado o para la base de datos de Azure SQL con PowerShell



> [AZURE.SELECTOR]
- [Portal de Azure](sql-database-geo-replication-failover-portal.md)
- [PowerShell](sql-database-geo-replication-failover-powershell.md)
- [T-SQL](sql-database-geo-replication-failover-transact-sql.md)


Este artículo le muestra cómo iniciar un error planificado o para la base de datos de SQL con PowerShell. Para configurar la replicación de Geo, vea [Configurar Geo-replicación de base de datos de SQL Azure](sql-database-geo-replication-powershell.md).



## <a name="initiate-a-planned-failover"></a>Iniciar un error planeado

Use el cmdlet **Set-AzureRmSqlDatabaseSecondary** con el parámetro **Failover** para promover una base de datos secundaria para convertirse en la nueva base de datos principal degradar principal existente para convertirse en un secundario. Esta funcionalidad está diseñada para una migración tras error planeada, como durante la recuperación de desastres y requiere que la base de datos principal esté disponible.

El comando realiza el flujo de trabajo siguiente:

1. Cambiar temporalmente la replicación a modo sincrónico. Esto hará que todas las transacciones pendientes vaciado a la secundaria.

2. Cambiar los roles de las dos bases de datos en la asociación de replicación Geo.  

Esta secuencia garantiza que se sincronizan las dos bases de datos antes de cambiar los roles y, por tanto, no se producirá pérdida de datos. Hay un corto período durante el cual ambas bases de datos no están disponibles (del orden de 0 a 25 segundos) mientras se cambian los roles. Toda la operación tendrá menos de un minuto en completarse en circunstancias normales. Para obtener más información, consulte [Set-AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt619393.aspx).




Este cmdlet devolverá cuando se complete el proceso de cambiar la base de datos secundaria a principal.

El siguiente comando cambia los roles de la base de datos mydb a "" en el servidor "srv2" en el grupo de recursos "rg2" a principal. La principal original a la que estaba conectado "db2" cambiará a secundario después de las dos bases de datos se sincronizan completamente.

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" –ResourceGroupName "rg2” –ServerName "srv2”
    $database | Set-AzureRmSqlDatabaseSecondary -Failover


> [AZURE.NOTE] En algunos casos es posible que no puede completar la operación y que puede aparecer no responde. En este caso, el usuario puede llamar al comando de migración tras error forzar (migración tras error planificado) y acepte la pérdida de datos.


## <a name="initiate-an-unplanned-failover-from-the-primary-database-to-the-secondary-database"></a>Iniciar un planificado migración tras error de la base de datos principal a la base de datos secundario


Puede usar el cmdlet **Set-AzureRmSqlDatabaseSecondary** con parámetros **: Migración** y **- AllowDataLoss** para promover una base de datos secundaria para convertirse en la nueva base de datos principal de un modo no planeado, forzar la degradación del equipo principal existente para convertirse en un secundario a la vez cuando la base de datos principal ya no está disponible.

Esta funcionalidad está diseñada para recuperación cuando restaurar la disponibilidad de la base de datos es fundamental y algunas pérdidas de datos es aceptable. Cuando se invoca forzado migración tras error, la base de datos secundario especificado inmediatamente se convierte en la base de datos principal y comienza a aceptar las transacciones de escritura. Tan pronto como la base de datos principal original se pueda volver a conectar con esta base de datos principal nueva después de la operación de forzado migración tras error, se realiza una copia de seguridad incremental en la base de datos principal original y la base de datos principal se convierte en una base de datos secundaria para la nueva base de datos principal; posteriormente, es simplemente una réplica de nuevo principal.

Pero porque no se admite la restauración de punto de tiempo en bases de datos secundarios, si desea comprometidos a la base de datos principal antiguo que no tenía ha replicar la nueva base de datos principal de datos de recuperación, debe comunicarse con CSS para restaurar una base de datos en la copia de seguridad conocidos.

> [AZURE.NOTE] Si el comando se emite cuando primario y secundario estén en línea se convertirá en el principal anterior la nueva secundaria inmediatamente sin sincronización de datos. Si es el principal pueden producirse confirmar las transacciones cuando se ejecuta el comando algunas pérdidas de datos.


Si la base de datos principal tiene varios secundarios el comando parcialmente se realizará correctamente. El secundario en el que se ejecuta el comando estará principal. El principal anterior sin embargo permanecerán principal, es decir, los dos primarios fin en estado incoherente y conectan por un vínculo de réplica suspendido. El usuario tendrá que reparar manualmente esta configuración con una API "quitar secundario" en cualquiera de estas bases de datos principales.


El siguiente comando cambia los roles de la base de datos mydb "" en primaria cuando el primario no está disponible. La principal original a la que estaba conectado "mydb" cambiará a secundario después de volver a conectarse. En ese momento la sincronización puede provocar pérdida de datos.

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" –ResourceGroupName "rg2” –ServerName "srv2”
    $database | Set-AzureRmSqlDatabaseSecondary –Failover -AllowDataLoss




## <a name="next-steps"></a>Pasos siguientes   

- Después de la migración, asegúrese de que se han configurado los requisitos de autenticación de su servidor y la base de datos en el nuevo principal. Para obtener más información, vea [seguridad de base de datos SQL después de recuperación](sql-database-geo-replication-security-config.md).
- Para obtener la recuperación después de un desastre utilizando replicación Geo activa, incluyendo pre y recuperación pasos posteriores y realizar un ejercicio de recuperación de desastres, consulte [Recuperación de desastres](sql-database-disaster-recovery.md)
- Para una entrada de blog sobre Active Geo replicación de Sasha Nosov, vea [el primer plano de nuevas capacidades de replicación Geo](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)
- Para obtener información acerca del diseño de aplicaciones de nube para usar la replicación de Geo Active, vea [Diseño de aplicaciones de nube para continuidad empresarial con replicación Geo](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- Para obtener información sobre el uso de la replicación de Geo Active con agrupaciones elásticos de la base de datos, vea [grupo elástico estrategias](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
- Para obtener información general de continurity de empresa, vea [Información general de continuidad empresarial](sql-database-business-continuity.md)
