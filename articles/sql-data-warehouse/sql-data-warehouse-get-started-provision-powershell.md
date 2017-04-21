<properties
   pageTitle="Crear almacén de datos SQL con PowerShell | Microsoft Azure"
   description="Crear almacén de datos SQL con PowerShell"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/25/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# <a name="create-sql-data-warehouse-using-powershell"></a>Crear almacén de datos de SQL con PowerShell

> [AZURE.SELECTOR]
- [Portal de Azure](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

Este artículo le muestra cómo crear un almacén de datos de SQL con PowerShell.

## <a name="prerequisites"></a>Requisitos previos

Para empezar, debe:

- **Cuenta de Azure**: visite [Prueba gratuita de Azure][] o [Créditos de Azure de MSDN][] para crear una cuenta.
- **Servidor de SQL Azure**: consulte [crear un servidor de base de datos de SQL Azure lógico con el Portal de Azure][] o [crear un servidor de base de datos de SQL Azure lógico con PowerShell][] para obtener más detalles.
- **Grupo de recursos**: usar el mismo grupo de recursos que el servidor de SQL Azure o cómo [crear un grupo de recursos][].
- **PowerShell versión 1.0.3 o mayor**: puede comprobar la versión ejecutando **módulo Get - ListAvailable-Azure nombre**.  Desde el [Instalador de plataforma Web de Microsoft][], se puede instalar la versión más reciente.  Para obtener más información sobre cómo instalar la última versión, consulte [cómo instalar y configurar Azure PowerShell][].

> [AZURE.NOTE] Crear un almacén de datos de SQL puede traducirse en un nuevo servicio facturable.  Para obtener más detalles sobre los precios, consulte [precios de almacén de datos de SQL][] .

## <a name="create-a-sql-data-warehouse"></a>Crear un almacén de datos SQL

1. Abrir Windows PowerShell.
2. Ejecutar este cmdlet para inicio de sesión para el Administrador de recursos de Azure.

    ```Powershell
    Login-AzureRmAccount
    ```
    
3. Seleccione la suscripción que desea usar para la sesión actual.

    ```Powershell
    Get-AzureRmSubscription -SubscriptionName "MySubscription" | Select-AzureRmSubscription
    ```

4.  Crear la base de datos. En este ejemplo se crea una base de datos denominada "mynewsqldw", con el nivel de objetivo de servicio "DW400" en el servidor llamado "sqldwserver1", que se encuentra en el grupo de recursos denominado "mywesteuroperesgp1".

    ```Powershell
    New-AzureRmSqlDatabase -RequestedServiceObjectiveName "DW400" -DatabaseName "mynewsqldw" -ServerName "sqldwserver1" -ResourceGroupName "mywesteuroperesgp1" -Edition "DataWarehouse" -CollationName "SQL_Latin1_General_CP1_CI_AS" -MaxSizeBytes 10995116277760
    ```

Parámetros requeridos son:

- **RequestedServiceObjectiveName**: la cantidad de [DWU][] que solicita.  Los valores admitidos son: DW100, DW200, DW300, DW400, DW500, DW600, DW1000, DW1200, DW1500, DW2000, DW3000 y DW6000.
- **DatabaseName**: el nombre del almacén de datos de SQL que está creando.
- **Nombre de servidor**: el nombre del servidor que está utilizando para la creación (debe ser V12).
- **ResourceGroupName**: grupo de recursos que está utilizando.  Para buscar recursos disponibles grupos en su suscripción use Get-AzureResource.
- **Edición**: debe ser "Almacenamiento de datos" para crear un almacén de datos de SQL.

Los parámetros opcionales son:

- **CollationName**: la intercalación predeterminada si no se especifica es SQL_Latin1_General_CP1_CI_AS.  No se puede cambiar la intercalación en una base de datos.
- **MaxSizeBytes**: el tamaño máximo predeterminado de una base de datos es 10 GB.


Para obtener más detalles sobre las opciones de parámetros, vea [AzureRmSqlDatabase de nuevo][] y [Crear base de datos (almacén de datos de SQL Azure)][].

## <a name="next-steps"></a>Pasos siguientes

Cuando termine el almacén de datos de SQL de aprovisionamiento desea a intentar [cargar los datos de ejemplo][] o consulte cómo [desarrollar][], [cargar][]o [migrar][].

Si está interesado en obtener más información sobre cómo administrar el almacén de datos SQL mediante programación, consulte nuestro artículo sobre cómo usar [cmdlets de PowerShell y las API de REST][].

<!--Image references-->

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[migrar]: ./sql-data-warehouse-overview-migrate.md
[desarrollar]: ./sql-data-warehouse-overview-develop.md
[cargar]: ./sql-data-warehouse-load-with-bcp.md
[cargar datos de ejemplo]: ./sql-data-warehouse-load-sample-databases.md
[Cmdlets de PowerShell y las API de REST]: ./sql-data-warehouse-reference-powershell-cmdlets.md
[firewall rules]: ../sql-database-configure-firewall-settings.md

[Cómo instalar y configurar PowerShell de Azure]: ../powershell/powershell-install-configure.md
[how to create a SQL Data Warehouse from the Azure Portal]: ./sql-data-warehouse-get-started-provision.md
[Crear un servidor de base de datos de SQL Azure lógico con el Portal de Azure]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Crear un servidor de base de datos de SQL Azure lógico con PowerShell]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[cómo crear un grupo de recursos]: ../resource-group-template-deploy-portal.md#create-resource-group

<!--MSDN references--> 
[MSDN]: https://msdn.microsoft.com/library/azure/dn546722.aspx
[Nueva AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Crear base de datos (almacén de datos SQL Azure)]: https://msdn.microsoft.com/library/mt204021.aspx

<!--Other Web references-->
[Instalador de plataforma Web de Microsoft]: https://aka.ms/webpi-azps
[Precios de almacén de datos SQL]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Prueba gratuita de Azure]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN créditos de Azure]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F
