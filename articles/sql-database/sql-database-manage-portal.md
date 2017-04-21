<properties
    pageTitle="Administrar la base de datos de SQL Azure con el Portal de Azure | Microsoft Azure"
    description="Aprenda a usar el Portal de Azure para administrar una base de datos relacional en la nube con el Portal de Azure."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.date="09/19/2016"
    ms.author="sstein"/>


# <a name="managing-azure-sql-databases-using-the-azure-portal"></a>Administrar bases de datos de SQL Azure con el portal de Azure


> [AZURE.SELECTOR]
- [Portal de Azure](sql-database-manage-portal.md)
- [SSMS](sql-database-manage-azure-ssms.md)
- [PowerShell](sql-database-manage-powershell.md)

El [portal de Azure](https://portal.azure.com/) le permite crear, supervisar y administrar los servidores y bases de datos de SQL Azure. Este artículo proporciona una descripción rápida y vínculos a los detalles de las tareas más comunes.

## <a name="view-your-azure-sql-databases-servers-and-pools"></a>Ver sus bases de datos de SQL Azure, servidores y grupos

Para ver los servicios disponibles de la base de datos de SQL, haga clic en **más servicios**y a continuación, escriba **SQL** en el cuadro de búsqueda:

![Base de datos SQL](./media/sql-database-manage-portal/sql-services.png)


## <a name="how-do-i-create-or-view-azure-sql-databases"></a>¿Cómo crear o ver las bases de datos de SQL Azure?

Para abrir el módulo de **bases de datos SQL** , haga clic en **bases de datos SQL**y haga clic en la base de datos que desea trabajar con o bien, haga clic en **+ Agregar** para crear una base de datos SQL. Para obtener información detallada, vea [crear una base de datos SQL en minutos mediante el portal de Azure](sql-database-get-started.md).


![Bases de datos SQL](./media/sql-database-manage-portal/sql-databases.png)


## <a name="how-do-i-create-or-view-azure-sql-servers"></a>¿Cómo crear o ver los servidores de SQL Azure?

Para abrir el módulo **SQL Server** , haga clic en **servidores de SQL Server**y haga clic en el servidor que desea trabajar con o bien, haga clic en **+ Add** para crear un servidor SQL server. Para obtener información detallada, vea [crear una base de datos SQL en minutos mediante el portal de Azure](sql-database-get-started.md).

![Servidores de SQL Server](./media/sql-database-manage-portal/sql-servers.png)


## <a name="how-do-i-create-or-view-sql-elastic-pools"></a>¿Cómo crear o ver grupos elásticos de SQL?

Para abrir el módulo de **grupos elásticos de SQL** , haga clic en **grupos elásticos de SQL**y haga clic en el grupo que desea trabajar o bien, haga clic en **+ Add** para crear un grupo. Para obtener información detallada, vea [crear un grupo de elásticos de la base de datos con el portal de Azure](sql-database-elastic-pool-create-portal.md).

![Grupos elásticos de SQL](./media/sql-database-manage-portal/elastic-pools.png)



## <a name="how-do-i-update-or-view-sql-database-settings"></a>¿Cómo actualizar o ver la configuración de la base de datos SQL

Para ver o actualizar la configuración de la base de datos, haga clic en la opción que desee en el módulo de base de datos SQL:


![Configuración de la base de datos SQL](./media/sql-database-manage-portal/settings.png)


## <a name="how-do-i-find-a-sql-databases-fully-qualified-server-name"></a>¿Cómo puedo encontrar un nombre completo del servidor de bases de datos SQL?

Para ver el nombre de su servidor de bases de datos, haga clic en **Descripción general** en el módulo de la **base de datos SQL** y anote el nombre del servidor:


![Configuración de la base de datos SQL](./media/sql-database-manage-portal/server-name.png)


## <a name="how-do-i-manage-firewall-rules-to-control-access-to-my-sql-server-and-database"></a>¿Cómo puedo administrar las reglas de firewall para controlar el acceso a mi servidor SQL y la base de datos?

Para ver, crear o actualizar las reglas de firewall, haga clic en **Configurar el servidor de seguridad** en el módulo de la **base de datos SQL** . Para obtener información detallada, vea [Configurar una regla de firewall de nivel de servidor de base de datos de SQL Azure con el portal de Azure](sql-database-configure-firewall-settings.md).


![reglas de Firewall](./media/sql-database-manage-portal/sql-database-firewall.png)


## <a name="how-do-i-change-my-sql-database-service-tier-or-performance-level"></a>¿Cómo puedo cambiar el nivel de rendimiento o nivel del servicio de base de datos SQL?


Para actualizar el nivel de rendimiento o nivel de servicio de una base de datos SQL, haga clic en **el nivel de precios (escala DTUs)** en el módulo de la **base de datos SQL** . Para obtener más información, vea [cambiar el nivel y rendimiento de nivel de servicio (nivel de precios) de una base de datos SQL](sql-database-scale-up.md).


![niveles de precios](./media/sql-database-manage-portal/pricing-tier.png)


## <a name="how-do-i-configure-auditing-and-threat-detection-for-a-sql-database"></a>¿Cómo configurar la auditoría y detección de una base de datos SQL de amenazas

Para configurar la detección de auditoría y amenazas para una base de datos SQL, haga clic en **detección de auditoría y amenazas** en el módulo de la **base de datos SQL** . Para obtener información detallada, vea [Introducción a la auditoría de la base de datos SQL](sql-database-auditing-get-started.md)y [empezar a trabajar con detección de amenaza de base de datos de SQL](sql-database-threat-detection-get-started.md).


## <a name="how-do-i-configure-dynamic-data-masking-for-a-sql-database"></a>¿Cómo configuro masking para una base de datos SQL de datos dinámicos?

Para configurar los datos dinámicos masking para una base de datos SQL, haga clic en **datos dinámicos masking** en el módulo de la **base de datos SQL** . Para obtener información detallada, vea [Introducción a Masking datos dinámicos de SQL base de datos](sql-database-dynamic-data-masking-get-started.md).


## <a name="how-do-i-configure-transparent-data-encryption-tde-for-a-sql-database"></a>¿Cómo se configura el cifrado de datos transparente (TDE) para una base de datos SQL?

Para configurar el cifrado de datos transparente para una base de datos SQL, haga clic en **cifrado de datos transparente** en el módulo de la **base de datos SQL** . Para obtener más información, vea [Habilitar TDE en una base de datos con el portal](https://msdn.microsoft.com/library/dn948096#Anchor_1).

## <a name="how-do-i-view-or-change-the-max-size-of-a-sql-database"></a>¿Cómo ver o cambiar el tamaño máximo de una base de datos SQL

Para ver o cambiar el tamaño de una base de datos SQL, haga clic en **tamaño de la base de datos** en el módulo de la **base de datos SQL** . Actualizar el tamaño máximo de una base de datos cambiando el nivel de rendimiento o nivel de servicio. Para obtener más información, vea [cambiar el nivel y rendimiento de nivel de servicio (nivel de precios) de una base de datos SQL](sql-database-scale-up.md).

## <a name="how-do-i-monitor-and-improve-the-performance-of-a-sql-database"></a>¿Cómo supervisar y mejorar el rendimiento de una base de datos SQL

Para supervisar y mejorar las características de rendimiento de una base de datos SQL, haga clic en **información general de rendimiento** en el módulo de la **base de datos SQL** . Para obtener más información, consulte [Información de rendimiento de base de datos de SQL](sql-database-performance.md).


## <a name="how-do-i-configure-geo-replication"></a>¿Cómo se puede configurar la replicación de Geo?

Para configurar la replicación de Geo para una base de datos SQL, haga clic en **Replicación Geo** en el módulo de la **base de datos SQL** . Para obtener información detallada, vea [Configurar Geo-replicación de base de datos de Azure SQL con el portal de Azure](sql-database-geo-replication-portal.md).


## <a name="how-do-i-failover-to-a-geo-replicated-sql-database"></a>¿Cómo migración tras error a una base de datos SQL replicar geo?

Para conmutar por error a un secundario replicar geo, haga clic en **Replicación Geo** en el módulo de la **base de datos SQL** , haga clic en **migración tras error**. Para obtener más información, vea [iniciar una migración tras error planificada o para la base de datos de Azure SQL con el portal de Azure](sql-database-geo-replication-failover-portal.md).


## <a name="how-do-i-copy-a-sql-database"></a>¿Cómo puedo copiar una base de datos SQL?

Para copiar una base de datos SQL, haga clic en **Copiar** en el módulo de la **base de datos SQL** . Para obtener información detallada, vea [Copiar una base de datos de SQL Azure con el portal de Azure](sql-database-copy-portal.md).


![Configuración de la base de datos SQL](./media/sql-database-manage-portal/sql-database-copy.png)

## <a name="how-do-i-archive-an-azure-sql-database-to-a-bacpac-file"></a>¿Cómo se puede archivar una base de datos de SQL Azure a un archivo de MOCHILA?

Para crear una MOCHILA de una base de datos SQL, haga clic en **Exportar** en el módulo de la **base de datos SQL** . Para obtener información detallada, vea [archivar una base de datos de SQL Azure a un archivo de MOCHILA con el portal de Azure](sql-database-export.md).


![Exportación de la base de datos SQL](./media/sql-database-manage-portal/sql-database-export.png)



## <a name="how-do-i-restore-a-sql-database-to-a-previous-point-in-time"></a>¿Cómo puedo restaurar una base de datos SQL a un punto anterior en el tiempo?

Para restaurar una base de datos SQL, haga clic en **Restaurar** en el módulo de la **base de datos SQL** . Para obtener más información, consulte [restaurar una base de datos de SQL Azure a un punto anterior en el tiempo con el portal de Azure](sql-database-point-in-time-restore-portal.md).


![Configuración de la base de datos SQL](./media/sql-database-manage-portal/sql-database-restore.png)


## <a name="how-do-i-create-an-azure-sql-database-from-a-bacpac-file"></a>¿Cómo puedo crear una base de datos de SQL Azure desde un archivo de MOCHILA?

Para crear una base de datos SQL desde un archivo de MOCHILA, haga clic en **Importar base de datos** en el módulo **SQL server** . Para obtener más información, consulte [importar un archivo de MOCHILA para crear una base de datos de SQL Azure](sql-database-import.md).


![Servidor SQL](./media/sql-database-manage-portal/server-commands.png)


## <a name="how-do-i-restore-a-deleted-sql-database"></a>¿Cómo puedo restaurar una base de datos SQL eliminada?

Para restaurar una base de datos SQL eliminado, haga clic en **eliminar bases de datos** en el módulo **SQL server** (servidor SQL que contiene la base de datos que se ha eliminado). Para obtener más información, consulte [restaurar una base de datos de SQL Azure eliminado con el portal de Azure](sql-database-restore-deleted-database-portal.md).

## <a name="how-do-i-delete-a-sql-database"></a>¿Cómo elimino una base de datos SQL?

Para eliminar una base de datos SQL, haga clic en **Eliminar** en el módulo de la **base de datos SQL** . 

![Configuración de la base de datos SQL](./media/sql-database-manage-portal/sql-database-delete.png)



## <a name="additional-resources"></a>Recursos adicionales

- [Base de datos SQL](sql-database-technical-overview.md)
- [Supervisar y administrar un grupo de elásticos de la base de datos con el portal de Azure](sql-database-elastic-pool-manage-portal.md)
