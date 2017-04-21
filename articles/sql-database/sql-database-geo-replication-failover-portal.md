<properties 
    pageTitle="Iniciar un error planificado o para la base de datos de SQL Azure con el portal de Azure | Microsoft Azure" 
    description="Iniciar un error planificado o para la base de datos de SQL Azure con el portal de Azure" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-management" 
    ms.date="08/29/2016"
    ms.author="sstein"/>

# <a name="initiate-a-planned-or-unplanned-failover-for-azure-sql-database-with-the-azure-portal"></a>Iniciar un error planificado o para la base de datos de SQL Azure con el portal de Azure


> [AZURE.SELECTOR]
- [Portal de Azure](sql-database-geo-replication-failover-portal.md)
- [PowerShell](sql-database-geo-replication-failover-powershell.md)
- [T-SQL](sql-database-geo-replication-failover-transact-sql.md)


Este artículo le muestra cómo iniciar migración tras error a una base de datos de SQL secundario con el [portal de Azure](http://portal.azure.com). Para configurar la replicación de Geo, vea [Configurar Geo-replicación de base de datos de SQL Azure](sql-database-geo-replication-portal.md).


## <a name="initiate-a-failover"></a>Iniciar un error

Puede cambiar la base de datos secundaria para convertirse en el principal.  

1. En el [portal de Azure](http://portal.azure.com) , vaya a la base de datos principal en la asociación de replicación Geo.
2. En el módulo de la base de datos de SQL, seleccione **todas las configuraciones** > **Geo replicación**.
3. En la lista **secundarios** , seleccione la base de datos que desea convertirse en el nuevo principal y haga clic en **migración tras error**.

    ![migración tras error][2]

4. Haga clic en **Sí** para comenzar la migración tras error.

El comando inmediatamente cambiará la base de datos secundario en la función principal. 

Hay un corto período durante el cual ambas bases de datos no están disponibles (del orden de 0 a 25 segundos) mientras se cambian los roles. Si la base de datos principal tiene varias bases de datos secundarios, el comando automáticamente a configurar los otros secundarios para conectar con el nuevo principal. Toda la operación tendrá menos de un minuto en completarse en circunstancias normales. 

>[AZURE.NOTE] Si principal está en línea y pueden ocurrir transacciones de confirmación cuando se ejecuta el comando algunas pérdidas de datos.


## <a name="next-steps"></a>Pasos siguientes   

- Después de la migración, asegúrese de que se han configurado los requisitos de autenticación de su servidor y la base de datos en el nuevo principal. Para obtener más información, vea [seguridad de base de datos SQL después de recuperación](sql-database-geo-replication-security-config.md).
- Para obtener la recuperación después de un desastre utilizando replicación Geo activo, incluidas las anteriores y recuperación pasos posteriores y realizar un ejercicio de recuperación de desastres, consulte [Recuperación de desastres](sql-database-disaster-recovery.md)
- Para una entrada de blog sobre Active Geo replicación de Sasha Nosov, vea [el primer plano de nuevas capacidades de replicación Geo](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)
- Para obtener información acerca del diseño de aplicaciones de nube para usar la replicación de Geo Active, vea [Diseño de aplicaciones de nube para continuidad empresarial con replicación Geo](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- Para obtener información sobre el uso de la replicación de Geo Active con agrupaciones elásticos de la base de datos, vea [grupo elástico estrategias](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
- Para obtener información general de continurity de empresa, vea [Información general de continuidad empresarial](sql-database-business-continuity.md)




<!--Image references-->
[1]: ./media/sql-database-geo-replication-failover-portal/failover.png
[2]: ./media/sql-database-geo-replication-failover-portal/secondaries.png
