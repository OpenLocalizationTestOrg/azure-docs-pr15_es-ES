<properties
    pageTitle="Supervisar el rendimiento de la base de datos en la base de datos de SQL Azure | Microsoft Azure"
    description="Obtenga información sobre las opciones para supervisar la base de datos con herramientas de Azure y vistas dinámicas de administración."
    keywords="base de datos de supervisión de rendimiento de la base de datos de nube"
    services="sql-database"
    documentationCenter=""
    authors="CarlRabeler"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="data-management"
    ms.date="09/27/2016"
    ms.author="carlrab"/>

# <a name="monitoring-database-performance-in-azure-sql-database"></a>Supervisar el rendimiento de la base de datos en la base de datos de SQL Azure
Supervisar el rendimiento de una base de datos SQL Azure comienza con supervisar el uso de recursos en relación con el nivel de rendimiento de la base de datos que elija. Supervisión le ayudan a que determinar si la base de datos tiene capacidad adicional o tiene problemas porque se sobrepasa recursos y, a continuación, decida si es el momento de ajustar el nivel de rendimiento y el [nivel de servicio](sql-database-service-tiers.md) de la base de datos. Puede supervisar la base de datos utilizando herramientas gráficas en el [portal de Azure](https://portal.azure.com) o SQL [vistas dinámicas de administración](https://msdn.microsoft.com/library/ms188754.aspx).

## <a name="monitor-databases-using-the-azure-portal"></a>Supervisar las bases de datos con el portal de Azure

En el [portal de Azure](https://portal.azure.com/), puede supervisar la utilización de una base de datos selección de la base de datos y haciendo clic en el gráfico de **supervisión** . Aparece una ventana de **métrica** que puede cambiar haciendo clic en el botón **Editar gráfico** . Agregue las siguientes métricas:

- Porcentaje de CPU
- Porcentaje DTU
- Porcentaje de IO de datos
- Porcentaje de tamaño de la base de datos

Una vez que haya agregado estas métricas, aún puede verlos en el gráfico de **supervisión** con más detalles en la ventana de **métrica** . Todas las cuatro mediciones muestran el porcentaje de uso promedio en relación con la **DTU** de la base de datos. Vea el artículo de [niveles de servicio](sql-database-service-tiers.md) para obtener más información sobre DTUs.

![Nivel de supervisión de rendimiento de la base de datos de servicio.](./media/sql-database-service-tiers/sqldb_service_tier_monitoring.png)

También puede configurar las alertas de la métrica de rendimiento. Haga clic en el botón **Agregar aviso** en la ventana de **métrica** . Siga el Asistente para configurar la alerta. Tiene la opción de alerta si la métrica supere un determinado umbral o si la métrica está por debajo de un determinado umbral.

Por ejemplo, si desea que la carga de trabajo en la base de datos crezca, puede configurar una alerta de correo electrónico cada vez que la base de datos alcanza el 80% en cualquiera de las mediciones de rendimiento. Puede usar como una advertencia para averiguar cuando tenga que cambiar al siguiente nivel superior de rendimiento.

Las mediciones de rendimiento también pueden ayudarle a determinar si puede degradar a un nivel de rendimiento. Se supone está utilizando una base de datos de S2 estándar y todos los indicadores de rendimiento muestran que la base de datos promedio no utilice más de 10% en cualquier momento. Es probable que la base de datos funcionarán correctamente en S1 estándar. Sin embargo, tenga en cuenta de cargas de trabajo que llegue o fluctúan antes de realizar la decisión de migrar a un nivel de rendimiento.

## <a name="monitor-databases-using-dmvs"></a>Bases de datos de monitor mediante DMV

La misma métrica que se expone en el portal también está disponible a través de vistas del sistema: [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) en la base de datos lógica **principal** de su servidor y [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) en la base de datos de usuario. Use **sys.resource_stats** si necesita supervisar los datos menos detallados a través de un período de tiempo. Use **sys.dm_db_resource_stats** si necesita supervisar los datos más granulares dentro de un período de tiempo más pequeño. Para obtener más información, consulte la [Guía de rendimiento de base de datos de SQL Azure](sql-database-performance-guidance.md#monitoring-resource-use-with-sysresourcestats).

>[AZURE.NOTE] **Sys.dm_db_resource_stats** devuelve un resultado vacío establecer cuando se usa en la Web y empresa edition bases de datos, que se retiren.

Para los grupos de elásticos de la base de datos, puede supervisar las bases de datos individuales en el grupo con las técnicas descritas en esta sección. Pero también puede supervisar el grupo como un todo. Para obtener más información, vea [Monitor y administrar un grupo de la base de datos elástico](sql-database-elastic-pool-manage-portal.md).
