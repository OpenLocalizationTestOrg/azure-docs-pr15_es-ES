<properties
   pageTitle="Administrar la potencia de la informática en el almacén de datos de SQL Azure (REST) | Microsoft Azure"
   description="Tareas de Transact-SQL (T-SQL) para el rendimiento de escalado ajustando DWUs. Ahorrar costos ajustando durante la horas no pico."
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

# <a name="manage-compute-power-in-azure-sql-data-warehouse-t-sql"></a>Administrar la potencia de la informática en el almacén de datos de SQL Azure (T-SQL)

> [AZURE.SELECTOR]
- [Información general](sql-data-warehouse-manage-compute-overview.md)
- [Portal](sql-data-warehouse-manage-compute-portal.md)
- [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
- [REST](sql-data-warehouse-manage-compute-rest-api.md)
- [TSQL](sql-data-warehouse-manage-compute-tsql.md)


Ajustar el rendimiento del escalado calcular recursos y la memoria para satisfacer las necesidades cambiantes de su carga de trabajo. Ahorrar costos de recursos atrás escalado durante las horas no punta o pausar cálculo totalmente. 

Esta colección de tareas usa T-SQL para:

- Configuración de DWU la vista actual
- Recursos de cálculo cambiar ajustando DWUs

Para pausar o reanudar una base de datos, elija una de las otras opciones de la plataforma en la parte superior de este artículo.

Para obtener información sobre esto, vea [Administrar calcular información general de energía][].

<a name="current-dwu-bk"></a>

## <a name="view-current-dwu-settings"></a>Configuración de DWU la vista actual

Para ver la configuración actual de DWU para las bases de datos:

1. Abra el Explorador de objetos SQL Server en Visual Studio de 2015.
2. Conectarse a la base de datos asociado con el servidor de base de datos SQL lógico.
2. Seleccione en la vista de administración dinámica sys.database_service_objectives. Aquí tenemos un ejemplo: 

```
SELECT
 db.name [Database],
 ds.edition [Edition],
 ds.service_objective [Service Objective]
FROM
 sys.database_service_objectives ds
 JOIN sys.databases db ON ds.database_id = db.database_id
```

<a name="scale-dwu-bk"></a>
<a name="scale-compute-bk"></a>

## <a name="scale-compute"></a>Cálculo de escala

[AZURE.INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Para cambiar la DWUs:


1. Conectarse a la base de datos asociado con el servidor de base de datos SQL lógico.
2. Utilice la instrucción de TSQL [Modificar base de datos][] . En el ejemplo siguiente se establece el objetivo de nivel de servicio a DW1000 para la base de datos MySQLDW. 

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Pasos siguientes

Para otras tareas de administración, vea [información general de administración][].

<!--Image references-->

<!--Article references-->
[Service capacity limits]: ./sql-data-warehouse-service-capacity-limits.md
[Información general de administración]: ./sql-data-warehouse-overview-manage.md
[Administrar información general de energía de cálculo]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->

[MODIFICAR BASE DE DATOS]: https://msdn.microsoft.com/library/mt204042.aspx


<!--Other Web references-->

[Azure portal]: http://portal.azure.com/
