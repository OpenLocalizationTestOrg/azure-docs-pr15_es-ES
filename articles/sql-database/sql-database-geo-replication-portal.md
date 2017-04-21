<properties 
    pageTitle="Configurar la replicación de Geo para base de datos de SQL Azure con el portal de Azure | Microsoft Azure" 
    description="Configurar la replicación de Geo para la base de datos de SQL Azure con el portal de Azure" 
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
    ms.workload="NA"
    ms.date="10/18/2016"
    ms.author="sstein"/>

# <a name="configure-geo-replication-for-azure-sql-database-with-the-azure-portal"></a>Configurar la replicación de Geo para base de datos de SQL Azure con el portal de Azure


> [AZURE.SELECTOR]
- [Información general](sql-database-geo-replication-overview.md)
- [Portal de Azure](sql-database-geo-replication-portal.md)
- [PowerShell](sql-database-geo-replication-powershell.md)
- [T-SQL](sql-database-geo-replication-transact-sql.md)

En este artículo se muestra cómo configurar la replicación de Geo Active para la base de datos de SQL con el [portal de Azure](http://portal.azure.com).

Para iniciar la migración tras error con el portal de Azure, vea [iniciar una migración tras error planificada o para la base de datos de Azure SQL con el portal de Azure](sql-database-geo-replication-failover-portal.md).

>[AZURE.NOTE] Replicación de Geo Active (secundarios legibles) ahora está disponible para todas las bases de datos en todos los niveles de servicio. En abril de 2017, se retirará el tipo secundario no se puede leer y bases de datos no se puede leer existentes se actualizarán automáticamente a secundarios legibles.

Para configurar la replicación Geo con el portal de Azure, tiene los siguientes recursos:

- Una base de datos de SQL Azure - la base de datos principal que desea duplicar en una región geográfica distinta.

## <a name="add-secondary-database"></a>Agregar base de datos secundaria

Los pasos siguientes crean una nueva base de datos secundario en una asociación de replicación Geo.  

Para agregar un secundario, debe ser el propietario de la suscripción o copropietario. 

La base de datos secundario tendrá el mismo nombre que la base de datos principal y, de forma predeterminada, tienen el mismo nivel de servicio. La base de datos secundario puede ser una base de datos o una base de datos elástico. Para obtener más información, vea [Niveles de servicio](sql-database-service-tiers.md).
Después de crear y visible el secundario, datos comenzará la replicación de la base de datos principal a la nueva base de datos secundario. 

> [AZURE.NOTE] Si ya existe la base de datos de partner (por ejemplo - como resultado de terminar una relación de replicación Geo anterior) se produce un error en el comando.

### <a name="add-secondary"></a>Agregar secundario

1. En el [portal de Azure](http://portal.azure.com), vaya a la base de datos que desea configurar para la replicación de Geo.
2. En la página de la base de datos SQL, seleccione **Replicación Geo**y, a continuación, seleccione la región para crear la base de datos secundario. Puede seleccionar una región distinta de la región que aloja la base de datos principal, pero se recomienda la [región pareja](../best-practices-availability-paired-regions.md) .

    ![configurar la replicación de Geo](./media/sql-database-geo-replication-portal/configure-geo-replication.png)


4. Seleccione o configurar el servidor y el nivel de precios para la base de datos secundario.

    ![configurar secundario](./media/sql-database-geo-replication-portal/create-secondary.png)

5. Si lo desea, puede agregar una base de datos secundario a un grupo de elásticos de la base de datos:

 Para crear la base de datos secundario en un grupo, haga clic en **grupo elásticos de la base de datos** y seleccione un grupo en el servidor de destino. Un grupo de servidores ya debe existir en el servidor de destino como este flujo de trabajo no crear un grupo.

6. Haga clic en **crear** para agregar el secundario.
 
6. Se crea la base de datos secundario y comienza el proceso de transferencia. 
 
    ![configurar secundario](./media/sql-database-geo-replication-portal/seeding0.png)

7. Una vez completado el proceso de transferencia, la base de datos secundario muestra su estado.

    ![Inicialización completada](./media/sql-database-geo-replication-portal/seeding-complete.png)


## <a name="remove-secondary-database"></a>Quitar la base de datos secundaria

La operación permanentemente finaliza la replicación de la base de datos secundario y cambie la función de la secundaria a una base de datos de lectura y escritura normal. Si se interrumpe la conectividad con la base de datos secundario, el comando se realiza correctamente pero se restaura la secundaria ya no se convierten en después de la conectividad de lectura y escritura.  

1. En el [portal de Azure](http://portal.azure.com), vaya a la base de datos principal en la asociación de replicación Geo.
2. En la página de la base de datos de SQL, seleccione **Replicación Geo**.
3. En la lista **secundarios** , seleccione la base de datos que desea quitar de la asociación de replicación Geo.
4. Haga clic en **Detener la replicación**.

    ![quitar secundario](./media/sql-database-geo-replication-portal/remove-secondary.png)

5. Haga clic en **Detener replicación** abre una ventana de confirmación, haga clic en **Sí** para quitar la base de datos de la asociación de replicación Geo (configurar una base de datos de lectura y escritura no forma parte de la replicación).


## <a name="next-steps"></a>Pasos siguientes

- Para obtener más información sobre Active Geo replicación, vea - [Replicación de Geo Active](sql-database-geo-replication-overview.md)
- Para obtener una descripción de la continuidad empresarial y escenarios, vea [información general de continuidad empresarial](sql-database-business-continuity.md)

