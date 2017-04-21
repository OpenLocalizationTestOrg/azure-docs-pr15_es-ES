<properties
   pageTitle="Crear un almacén de datos SQL con TSQL | Microsoft Azure"
   description="Obtenga información sobre cómo crear un almacén de datos de SQL Azure con TSQL"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""
   tags="azure-sql-data-warehouse"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/24/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# <a name="create-a-sql-data-warehouse-database-by-using-transact-sql-tsql"></a>Crear una base de datos de almacén de datos SQL mediante Transact-SQL (TSQL)

> [AZURE.SELECTOR]
- [Portal de Azure](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

En este artículo se muestra cómo crear un almacén de datos de SQL mediante T SQL.

## <a name="prerequisites"></a>Requisitos previos

Para empezar, debe: 

- **Cuenta de Azure**: visite [Prueba gratuita de Azure][] o [Créditos de Azure de MSDN][] para crear una cuenta.
- **Servidor de SQL Azure**: consulte [crear un servidor de base de datos de SQL Azure lógico con el Portal de Azure][] o [crear un servidor de base de datos de SQL Azure lógico con PowerShell][] para obtener más detalles.
- **Grupo de recursos**: usar el mismo grupo de recursos que el servidor de SQL Azure o cómo [crear un grupo de recursos][].
- **Entorno para ejecutar SQL T**: puede usar [Visual Studio][Installing Visual Studio and SSDT], [sqlcmd][]o [SSMS][] ejecutar T SQL.

> [AZURE.NOTE] Crear un almacén de datos de SQL puede traducirse en un nuevo servicio facturable.  Para obtener más detalles sobre los precios, consulte [precios de almacén de datos de SQL][] .

## <a name="create-a-database-with-visual-studio"></a>Crear una base de datos con Visual Studio

Si es nuevo en Visual Studio, vea el artículo de [Almacén de datos de SQL de Azure de consulta (Visual Studio)][].  Para empezar, abra el Explorador de objetos de SQL Server en Visual Studio y conectarse al servidor que hospedará la base de datos de almacén de datos de SQL.  Una vez conectado, puede crear un almacén de datos de SQL, ejecute el siguiente comando SQL contra **la base de datos** .  Este comando crea la base de datos MySqlDwDb con un objetivo de DW400 de servicio y permitir que la base de datos alcanzar un tamaño máximo de 10 TB.

```sql
CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB);
```

## <a name="create-a-database-with-sqlcmd"></a>Crear una base de datos con sqlcmd

Como alternativa, puede ejecutar el mismo comando con sqlcmd ejecutando lo siguiente en un símbolo del sistema.

```sql
sqlcmd -S <Server Name>.database.windows.net -I -U <User> -P <Password> -Q "CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB)"
```

La intercalación predeterminada cuando no se especifica es SQL_Latin1_General_CP1_CI_AS intercalar.  La `MAXSIZE` puede ser entre 250 GB y 240 TB.  La `SERVICE_OBJECTIVE` entre DW100 y DW2000 [DWU][].  Para obtener una lista de todos los valores válidos, consulte la documentación de MSDN para [Crear la base de datos][].  El tamaño máximo y el SERVICE_OBJECTIVE pueden cambiarse con un comando de SQL T [Modificar base de datos][] .  No se puede cambiar la intercalación de una base de datos después de la creación.   Debe tener precaución cuando cambio la SERVICE_OBJECTIVE cambiando DWU provoca un reinicio de servicios, que cancela todas las consultas de vuelo.  Cambiar el tamaño máximo no reinicia servicios como una operación de metadatos simple.

## <a name="next-steps"></a>Pasos siguientes

Cuando termine el almacén de datos de SQL de aprovisionamiento puede [cargar los datos de ejemplo][] o consulte cómo [desarrollar][], [cargar][]o [migrar][].

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[how to create a SQL Data Warehouse from the Azure portal]: sql-data-warehouse-get-started-provision.md
[Almacén de datos SQL Azure de consulta (Visual Studio)]: sql-data-warehouse-query-visual-studio.md
[migrar]: sql-data-warehouse-overview-migrate.md
[desarrollar]: sql-data-warehouse-overview-develop.md
[cargar]: sql-data-warehouse-overview-load.md
[cargar datos de ejemplo]: sql-data-warehouse-load-sample-databases.md
[Crear un servidor de base de datos de SQL Azure lógico con el Portal de Azure]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Crear un servidor de base de datos de SQL Azure lógico con PowerShell]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[cómo crear un grupo de recursos]: ../resource-group-template-deploy-portal.md#create-resource-group
[Installing Visual Studio and SSDT]: sql-data-warehouse-install-visual-studio.md
[Sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--MSDN references--> 
[CREAR BASE DE DATOS]: https://msdn.microsoft.com/library/mt204021.aspx
[MODIFICAR BASE DE DATOS]: https://msdn.microsoft.com/library/mt204042.aspx
[SSMS]: https://msdn.microsoft.com/library/mt238290.aspx

<!--Other Web references-->
[Precios de almacén de datos SQL]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Prueba gratuita de Azure]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN créditos de Azure]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F
