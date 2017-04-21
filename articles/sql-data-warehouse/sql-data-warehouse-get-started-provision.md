<properties
   pageTitle="Crear un almacén de datos de SQL en el portal de Azure | Microsoft Azure"
   description="Aprenda a crear un almacén de datos de SQL Azure en el portal de Azure"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="jhubbard"
   editor=""
   tags="azure-sql-data-warehouse"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/25/2016"
   ms.author="barbkess;lodipalm;sonyama"/>

# <a name="create-an-azure-sql-data-warehouse"></a>Crear un almacén de datos SQL Azure

> [AZURE.SELECTOR]
- [Portal de Azure](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

Este tutorial utiliza el portal de Azure para crear un almacén de datos de SQL que contiene una base de datos de ejemplo AdventureWorksDW.


## <a name="prerequisites"></a>Requisitos previos

Para empezar, debe:

- **Cuenta de Azure**: visite [Prueba gratuita de Azure][] o [Créditos de Azure de MSDN][] para crear una cuenta.
- **Servidor de SQL Azure**: consulte [crear un servidor de base de datos de SQL Azure lógico con el portal de Azure][] para obtener más detalles.

> [AZURE.NOTE] Crear un almacén de datos de SQL podría provocar un nuevo servicio facturable.  Para obtener más detalles, consulte [precios de almacén de datos de SQL][] .

## <a name="create-a-sql-data-warehouse"></a>Crear un almacén de datos SQL

1. Inicie sesión en el [portal de Azure](https://portal.azure.com).

2. Haga clic en **+ nuevo** > **datos + almacenamiento** > **almacén de datos SQL**.

    ![Crear](./media/sql-data-warehouse-get-started-provision/create-sample.gif)

3. En el módulo de **Almacén de datos de SQL** , rellene la información necesaria y presione 'Create' para crear.

    ![Crear base de datos](./media/sql-data-warehouse-get-started-provision/create-database.png)

    - **Servidor**: recomendamos seleccionar primero el servidor.  

    - **Nombre de la base de datos**: el nombre que se usa para hacer referencia al almacén de datos de SQL.  Debe ser único en el servidor.
    
    - **Rendimiento**: se recomienda comenzando con 400 [DWUs][DWU]. Puede mover el control deslizante hacia la izquierda o derecha para ajustar el rendimiento de su almacén de datos o escalar hacia arriba o abajo después de la creación.  Para obtener más información sobre DWUs, consulte la documentación en [escala](./sql-data-warehouse-manage-compute-overview.md) o nuestra [página de precios][precios de almacén de datos de SQL]. 

    - **Suscripción**: seleccione la [suscripción] que se facturación este almacén de datos de SQL.

    - **Grupo de recursos**: [grupos de recursos] [ Resource group] son contenedores diseñados para ayudarle a administrar una colección de recursos de Azure. Más información acerca de los [grupos de recursos](../azure-resource-manager/resource-group-overview.md).

    - **Seleccione la fuente**: haga clic en **Seleccionar origen** > **muestra**. Azure rellena automáticamente la opción **Seleccionar ejemplo** con AdventureWorksDW.

> [AZURE.NOTE] La intercalación predeterminada para un almacén de datos de SQL es SQL_Latin1_General_CP1_CI_AS. Si se necesita una intercalación diferente, [T-SQL][] puede utilizarse para crear la base de datos con una intercalación diferente.

4. Haga clic en **crear** para crear su almacén de datos de SQL.

5. Espere unos minutos. Cuando el almacén de datos esté listo, deben devolver en el [portal de Azure](https://portal.azure.com). Puede encontrar el almacén de datos de SQL en el panel, que aparece debajo de las bases de datos de SQL, o en el grupo de recursos que usó para crearlo. 

    ![vista del portal](./media/sql-data-warehouse-get-started-provision/database-portal-view.png)

[AZURE.INCLUDE [SQL Database create server](../../includes/sql-database-create-new-server-firewall-portal.md)] 

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha creado un almacén de datos de SQL, está listo para [Conectar](./sql-data-warehouse-connect-overview.md) y empezar a consultar.

Para cargar datos en el almacén de datos de SQL, vea la [información general de carga](./sql-data-warehouse-overview-load.md).

Si está intentando migrar una base de datos al almacén de datos de SQL, vea la [información general de migración](./sql-data-warehouse-overview-migrate.md) o use [La herramienta de migración](./sql-data-warehouse-migrate-migration-utility.md).

También se pueden configurar reglas de Firewall mediante Transact-SQL. Para obtener más información, consulte [sp_set_firewall_rule][] y [sp_set_database_firewall_rule][].

También es una buena idea que mirar las [prácticas recomendadas][].

<!--Article references-->
[Crear un servidor de base de datos de SQL Azure lógico con el portal de Azure]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Create an Azure SQL Database logical server with PowerShell]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[resource groups]: ../resource-group-template-deploy-portal.md
[Prácticas recomendadas]: sql-data-warehouse-best-practices.md
[DWU]: sql-data-warehouse-overview-what-is.md#data-warehouse-units
[suscripción]: ../azure-glossary-cloud-terminology.md#subscription
[resource group]: ../azure-glossary-cloud-terminology.md#resource-group
[T-SQL]: ./sql-data-warehouse-get-started-create-database-tsql.md
 
<!--MSDN references-->
[sp_set_firewall_rule]: https://msdn.microsoft.com/library/dn270017.aspx
[sp_set_database_firewall_rule]: https://msdn.microsoft.com/library/dn270010.aspx

<!--Other Web references-->
[Precios de almacén de datos SQL]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Prueba gratuita de Azure]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN créditos de Azure]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F

