<properties
   pageTitle="Administrar la potencia de la informática en el almacén de datos de SQL Azure (REST) | Microsoft Azure"
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
   ms.date="08/08/2016"
   ms.author="barbkess;sonyama"/>

# <a name="manage-compute-power-in-azure-sql-data-warehouse-rest"></a>Administrar la potencia de la informática en el almacén de datos de SQL Azure (REST)

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

<a name="scale-performance-bk"></a>
<a name="scale-compute-bk"></a>

## <a name="scale-compute-power"></a>Potencia informática de escala

[AZURE.INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Para cambiar la DWUs, use la API de REST de [crear o actualizar base de datos][] . En el ejemplo siguiente se establece el objetivo de nivel de servicio a DW1000 para la base de datos MySQLDW que se hospeda en el servidor miservidor. El servidor está en un grupo de recursos de Azure denominado ResourceGroup1.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/ResourceGroup1/providers/Microsoft.Sql/servers/MyServer/databases/MySQLDW?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>Cálculo de pausa

[AZURE.INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

Para pausar una base de datos, utilice la API de REST de [Pausar la base de datos][] . En el ejemplo siguiente se detiene la base de datos denominada Database02 alojado en un servidor denominado Server01. El servidor está en un grupo de recursos de Azure denominado ResourceGroup1.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/ResourceGroup1/providers/Microsoft.Sql/servers/Server01/databases/Database02/pause?api-version=2014-04-01-preview HTTP/1.1
```

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>Cálculo de currículo

[AZURE.INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]

Para iniciar una base de datos, use la API de REST de [Base de datos][] . En el ejemplo siguiente se inicia una base de datos denominada Database02 alojado en un servidor denominado Server01. El servidor está en un grupo de recursos de Azure denominado ResourceGroup1. 

```
POST https://management.azure.com/subscriptions{subscription-id}/resourceGroups/ResourceGroup1/providers/Microsoft.Sql/servers/Server01/databases/Database02/resume?api-version=2014-04-01-preview HTTP/1.1
```

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Pasos siguientes

Para otras tareas de administración, vea [información general de administración][].

<!--Image references-->

<!--Article references-->
[Información general de administración]: ./sql-data-warehouse-overview-manage.md
[Administrar información general de cálculo]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->
[Base de datos de pausa]: https://msdn.microsoft.com/library/azure/mt718817.aspx
[Base de datos de currículo]: https://msdn.microsoft.com/library/azure/mt718820.aspx
[Crear o actualizar la base de datos]: https://msdn.microsoft.com/library/azure/mt163685.aspx

<!--Other Web references-->

[Azure portal]: http://portal.azure.com/
