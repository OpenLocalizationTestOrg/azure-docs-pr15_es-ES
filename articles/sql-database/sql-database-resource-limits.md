<properties
    pageTitle="Límites de recursos de base de datos SQL Azure"
    description="Esta página describe algunos límites de recursos comunes de base de datos de SQL Azure."
    services="sql-database"
    documentationCenter="na"
    authors="CarlRabeler"
    manager="jhubbard"
    editor="monicar" />


<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-management"
    ms.date="10/13/2016"
    ms.author="carlrab" />


# <a name="azure-sql-database-resource-limits"></a>Límites de recursos de base de datos de SQL Azure

## <a name="overview"></a>Información general

Base de datos de SQL Azure administra los recursos disponibles para una base de datos con dos mecanismos diferentes: **Gobernanza de recursos** y **Cumplimiento de los límites**. En este tema se explica estas dos áreas principales de administración de recursos.

## <a name="resource-governance"></a>Gobernanza de recursos
Uno de los objetivos de diseño de los niveles de servicio básico, estándar y Premium es para que la base de datos de SQL de Azure se comporte como si la base de datos se ejecuta en su propio equipo, completamente aislado de otras bases de datos. Gobernanza de recursos emula este comportamiento. Si el uso de recursos agregado alcanza el máximo de CPU disponible, memoria, i/OS de registro y datos i/OS recursos asignados a la base de datos, gobernanza de recursos se cola consultas en ejecución y asignar recursos a las consultas en cola mientras liberar.

Como en un equipo dedicado, utilizando todos los recursos disponibles dará como resultado una ejecución más larga de consultas, lo que pueden dar lugar a tiempos de espera de comando en el cliente está ejecutando actualmente. Aplicaciones con lógica de reintento agresiva y que ejecutar consultas en la base de datos con una alta frecuencia puede encontrar mensajes de error al intentar ejecutar las consultas nuevas cuando se ha alcanzado el límite de solicitudes simultáneas.

### <a name="recommendations"></a>Recomendaciones:
Supervisar el uso de recursos, así como los tiempos de respuesta promedio de consultas cuando casi la utilización máxima de una base de datos. Cuando se produzcan mayor latencia de consulta, en general, tiene tres opciones:

1.  Reducir la cantidad de las solicitudes entrantes para evitar el tiempo de espera y la pila de solicitudes de la base de datos.

2.  Asignar un nivel de rendimiento superior a la base de datos.

3.  Optimizar las consultas para reducir el uso de recursos de cada consulta. Para obtener más información, vea la sección consulta ajuste/Hinting en el artículo de la Guía de rendimiento de base de datos de SQL Azure.

## <a name="enforcement-of-limits"></a>Aplicación de límites
Recursos distintos de CPU, memoria, i/OS de registro y datos i/OS se aplican al denegar nuevas solicitudes cuando se alcance el límite. Los clientes recibirán un [mensaje de error](sql-database-develop-error-messages.md) según el límite que se ha alcanzado.

Por ejemplo, el número de conexiones a una base de datos SQL, así como el número de solicitudes simultáneas que puede procesar está restringido. Base de datos SQL permite al número de conexiones a la base de datos sea mayor que el número de solicitudes simultáneas para admitir la agrupación de conexiones. Mientras la aplicación fácilmente puede controlar la cantidad de conexiones que están disponibles, la cantidad de solicitudes paralelas suele veces más difíciles estimar y controlar. Especialmente durante las cargas máximas cuando la aplicación ya sea envía muchas solicitudes o la base de datos alcanza su recurso limita y comienza acumulando subprocesos debido a las consultas de ejecución más largas, pueden encontrarse errores.

## <a name="service-tiers-and-performance-levels"></a>Niveles de servicio y los niveles de rendimiento

Existen niveles de servicio y los niveles de rendimiento para ambos grupos de base de datos y elástica independiente.

### <a name="standalone-databases"></a>Bases de datos independiente

Para una base de datos independiente, los límites de una base de datos están definidos por el nivel de rendimiento y los niveles de servicio de base de datos. La siguiente tabla describe las características de básico, estándar y Premium bases de datos en distintos niveles de rendimiento.

[AZURE.INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

### <a name="elastic-pools"></a>Grupos de elásticos

[Grupos de elásticos](sql-database-elastic-pool.md) compartir recursos entre bases de datos en el grupo. La siguiente tabla describe las características de grupos de elásticos de la base de datos básico, estándar y Premium.

[AZURE.INCLUDE [SQL DB service tiers table for elastic databases](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

Para obtener una definición expandida de cada recurso que se muestran en las tablas anteriores, consulte las descripciones de [los límites y las capacidades de nivel de servicio](sql-database-performance-guidance.md#service-tier-capabilities-and-limits). Para obtener información general de los niveles de servicio, vea [niveles de rendimiento y niveles de servicio de base de datos de SQL Azure](sql-database-service-tiers.md).

## <a name="other-sql-database-limits"></a>Otros límites de base de datos SQL

| Área | Límite | Descripción |
|---|---|---|
| Bases de datos mediante exportación automatizada por suscripción | 10 | Exportar automatizado le permite crear un calendario personalizado para la copia de seguridad de las bases de datos SQL. Para obtener más información, vea [bases de datos SQL: soporte técnico para las exportaciones de base de datos SQL de automatizado](http://weblogs.asp.net/scottgu/windows-azure-july-updates-sql-database-traffic-manager-autoscale-virtual-machines).|
| Base de datos por servidor | Hasta 5.000 | Hasta 5000 bases de datos permitidos por cada servidor en servidores de V12. |  
| DTUs por servidor | 45000 | 45000 DTUs están disponibles por cada servidor en servidores de V12 de aprovisionamiento de bases de datos, grupos elásticas y datawarehouses. |



## <a name="resources"></a>Recursos

[Suscripción de Azure y los límites del servicio, cuotas y restricciones](../azure-subscription-service-limits.md)

[Niveles de servicio de base de datos SQL Azure y niveles de rendimiento](sql-database-service-tiers.md)

[Mensajes de error de los programas de cliente de base de datos SQL](sql-database-develop-error-messages.md)
