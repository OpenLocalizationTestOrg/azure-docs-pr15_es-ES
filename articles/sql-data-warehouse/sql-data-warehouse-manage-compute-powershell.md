<properties
   pageTitle="Administrar la potencia de la informática en el almacén de datos de SQL Azure (PowerShell) | Microsoft Azure"
   description="Tareas de PowerShell para administrar potencia. Recursos de cálculo escala ajustando DWUs. O bien, pausar y reanudar calculan recursos para ahorrar costos."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/13/2016"
   ms.author="barbkess;sonyama"/>

# <a name="manage-compute-power-in-azure-sql-data-warehouse-powershell"></a>Administrar la potencia de la informática en el almacén de datos de SQL Azure (PowerShell)

> [AZURE.SELECTOR]
- [Información general](sql-data-warehouse-manage-compute-overview.md)
- [Portal](sql-data-warehouse-manage-compute-portal.md)
- [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
- [REST](sql-data-warehouse-manage-compute-rest-api.md)
- [TSQL](sql-data-warehouse-manage-compute-tsql.md)


Ajustar el rendimiento del escalado calcular recursos y la memoria para satisfacer las necesidades cambiantes de su carga de trabajo. Ahorrar costos de recursos atrás escalado durante las horas no punta o pausar cálculo totalmente. 

Esta colección de tareas usa el portal de Azure para:

- Cálculo de escala
- Cálculo de pausa
- Cálculo de currículo

Para obtener información sobre esto, vea [Administrar calcular información general][].


## <a name="before-you-begin"></a>Antes de empezar

### <a name="install-the-latest-version-of-azure-powershell"></a>Instalar la última versión de PowerShell de Azure

> [AZURE.NOTE]  Para usar PowerShell Azure con el almacén de datos de SQL, debe Azure PowerShell versión 1.0.3 o posterior.  Para comprobar su versión actual, ejecute el comando **módulo Get - ListAvailable-Azure nombre**. Puede instalar la última versión del [Instalador de plataforma Web de Microsoft][].  Para obtener más información, vea [cómo instalar y configurar Azure PowerShell][].

### <a name="get-started-with-azure-powershell-cmdlets"></a>Introducción a los cmdlets de PowerShell de Azure

Introducción:

1. Abra el PowerShell Azure. 
2. En el símbolo del sistema de PowerShell, ejecute los comandos para iniciar sesión en el Administrador de recursos de Azure y seleccione la suscripción.

    ```PowerShell
    Login-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

<a name="scale-performance-bk"></a>
<a name="scale-compute-bk"></a>

## <a name="scale-compute-power"></a>Potencia informática de escala

[AZURE.INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Para cambiar la DWUs, use el cmdlet de PowerShell [Conjunto AzureRmSqlDatabase][] . En el ejemplo siguiente se establece el objetivo de nivel de servicio a DW1000 para la base de datos MySQLDW que se hospeda en el servidor miservidor. 

```Powershell
Set-AzureRmSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000"
```

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>Cálculo de pausa

[AZURE.INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

Para pausar una base de datos, use el cmdlet [Suspender AzureRmSqlDatabase][] . En el ejemplo siguiente se detiene la base de datos denominada Database02 alojado en un servidor denominado Server01. El servidor está en un grupo de recursos de Azure denominado ResourceGroup1. 

> [AZURE.NOTE] Tenga en cuenta que si el servidor es foo.database.windows.net, usar "foo" como nombre de servidor - en los cmdlets de PowerShell.

```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
```
Una variación, en el ejemplo siguiente se recupera la base de datos en el objeto $database. A continuación, canalizaciones que [Suspender AzureRmSqlDatabase][]el objeto. Los resultados se almacenan en el objeto resultDatabase. El comando final muestra los resultados.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>Cálculo de currículo

[AZURE.INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]

Para iniciar una base de datos, use el cmdlet [Resume AzureRmSqlDatabase][] . En el ejemplo siguiente se inicia una base de datos denominada Database02 alojado en un servidor denominado Server01. El servidor está en un grupo de recursos de Azure denominado ResourceGroup1. 

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" -DatabaseName "Database02"
```

Una variación, en el ejemplo siguiente se recupera la base de datos en el objeto $database. A continuación, canalizaciones que [Resume AzureRmSqlDatabase][] el objeto y almacena los resultados en $resultDatabase. El comando final muestra los resultados.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
$resultDatabase
```

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Pasos siguientes

Para otras tareas de administración, vea [información general de administración][].

<!--Image references-->

<!--Article references-->
[Service capacity limits]: ./sql-data-warehouse-service-capacity-limits.md
[Información general de administración]: ./sql-data-warehouse-overview-manage.md
[Cómo instalar y configurar PowerShell de Azure]: ./powershell-install-configure.md
[Administrar información general de cálculo]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->
[Reanudar AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619347.aspx
[AzureRmSqlDatabase suspender]: https://msdn.microsoft.com/library/mt619337.aspx
[Establecer AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619433.aspx

<!--Other Web references-->
[Instalador de plataforma Web de Microsoft]: https://aka.ms/webpi-azps
[Azure portal]: http://portal.azure.com/
