<properties
    pageTitle="Base de datos SQL: ¿Qué es una DTU? | Microsoft Azure"
    description="Comprender qué una base de datos de SQL de Azure es de unidad de transacción."
    keywords="Opciones de base de datos, el rendimiento de la base de datos"
    services="sql-database"
    documentationCenter=""
    authors="CarlRabeler"
    manager="jhubbard"
    editor="CarlRabeler"/>

<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="NA"
    ms.date="09/06/2016"
    ms.author="carlrab"/>

# <a name="explaining-database-transaction-units-dtus-and-elastic-database-transaction-units-edtus"></a>Explicación de unidades de transacción de base de datos (DTUs) y elástico unidades de transacción de base de datos (eDTUs)

Este artículo explica las unidades de la transacción de base de datos (DTUs) y elástico unidades de transacción de base de datos (eDTUs), y qué ocurre cuando visita el máximo DTUs o eDTUs.  

## <a name="what-are-database-transaction-units-dtus"></a>¿Qué unidades de transacción de base de datos (DTUs)

Una DTU es una unidad de medida de los recursos que se garantiza que esté disponible para una base de datos de SQL Azure independiente en un nivel de rendimiento específico dentro de un [nivel de servicio de base de datos independiente](sql-database-service-tiers.md#standalone-database-service-tiers-and-performance-levels). Una DTU es una medida combinada de CPU, memoria y datos i/OS e i/OS de registro de transacciones en una relación determinada por una carga de trabajo OLTP comparativas diseñada para ser típico de cargas de trabajo OLTP reales. Duplicar el DTUs aumentando el nivel de rendimiento de una base de datos equivale a duplicar el conjunto de recursos disponibles para dicha base de datos. Por ejemplo, una base de datos P11 Premium con 1750 DTUs proporciona potencia 350 x DTU más de una base de datos básico con 5 DTUs. Para conocer la metodología de la carga de trabajo OLTP comparativa que se utiliza para determinar la mezcla DTU, vea [información general de referencia de base de datos de SQL](sql-database-benchmark-overview.md).

![Introducción a la base de datos SQL: solo DTUs de base de datos por nivel y nivel](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

Puede [cambiar los niveles de servicio](sql-database-scale-up.md) en cualquier momento con el tiempo de inactividad mínimo a la aplicación (generalmente calcular el promedio de menos de cuatro segundos). Para muchas empresas y aplicaciones, poder crear bases de datos y rendimiento de la base de datos única de marcado hacia arriba o hacia abajo a petición es suficiente, especialmente si los patrones de uso son relativamente predecibles. Pero si tiene patrones de uso imprevistos, que puede será difícil de administrar costos y su modelo de negocios. En este escenario, utiliza un grupo elástico con un número determinado de eDTUs.

## <a name="what-are-elastic-database-transaction-units-edtus"></a>¿Qué son elástico unidades de transacción de base de datos (eDTUs)

Una eDTU es una unidad de medida del conjunto de recursos (DTUs) que pueden compartirse entre un conjunto de bases de datos en un servidor de SQL Azure - denominada un [grupo elástico](sql-database-elastic-pool.png). Agrupaciones elásticos proporcionan una solución rentable simple para administrar los objetivos de rendimiento para varias bases de datos que tienen muy distintos y patrones de uso imprevistos. Consulte [elásticos grupos y niveles de servicio](sql-database-service-tiers.md#elastic-pool-service-tiers-and-performance-in-edtus) para obtener más información.

![Introducción a la base de datos SQL: eDTUs por nivel y nivel](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

Un grupo se expresa un determinado número de eDTUs por un precio establecido. En el grupo, bases de datos individuales reciben la flexibilidad a Autoescala dentro de los parámetros de conjunto. Una gran carga, una base de datos puede consumir más eDTUs para satisfacer la demanda. Bases de datos en cargas ligeras consuman menor y bases de datos sin carga no consuman eDTUs. Recursos para todo el grupo en lugar de bases de datos de aprovisionamiento simplifica las tareas de administración. Además, tiene un presupuesto para el grupo predecible.

EDTUs adicionales se pueden agregar a un grupo existente con ningún tiempo de inactividad de la base de datos o ningún impacto en las bases de datos en el grupo elástico. Del mismo modo, si ya no se necesitan eDTUs adicionales puede quitarse de un grupo existente en cualquier punto en el tiempo. Puede sumar o restar bases de datos al grupo. Si una base de datos es predecible en utiliza recursos, moverlo.

## <a name="how-can-i-determine-the-number-of-dtus-needed-by-my-workload"></a>¿Cómo puedo determinar el número de DTUs es necesario por la carga de trabajo?

Si desea para migrar una existente local o carga de trabajo de máquina virtual de SQL Server a la base de datos de SQL Azure, puede usar la [Calculadora DTU](http://dtucalculator.azurewebsites.net/) para aproximar el número de DTUs que sea necesario. Para una carga de trabajo existente de base de datos de SQL Azure, puede usar [Una perspectiva rendimiento de consulta de base de datos de SQL](sql-database-query-performance.md) para comprender su consumo de recursos de base de datos (DTUs) para obtener más información sobre cómo optimizar la carga de trabajo. También puede usar el [resource_stats de sys.dm_db_](https://msdn.microsoft.com/library/dn800981.aspx) DMV para obtener la información de consumo de recursos de la última hora. Como alternativa, también se puede consultar a la vista de catálogo [sys.resource_stats](http://msdn.microsoft.com/library/dn269979.aspx) para obtener los mismos datos para los últimos 14 días, aunque en una fidelidad inferior de promedios de cinco minutos.

## <a name="how-do-i-know-if-i-could-benefit-from-an-elastic-pool-of-resources"></a>¿Cómo se puede saber si podría beneficiarse de un grupo de recursos elástico?

Grupos son adecuados para un gran número de bases de datos con patrones de uso específico. Base de datos, este patrón se caracteriza por uso promedio bajo con puntas de utilización relativamente poco frecuentes. Base de datos SQL se evalúa el uso de recursos históricos de bases de datos en un servidor de base de datos SQL existente automáticamente y se recomienda la configuración del grupo correspondiente en el portal de Azure. Para obtener más información, vea [cuándo se debe utilizar un grupo de la base de datos elástico?](sql-database-elastic-pool-guidance.md)

## <a name="what-happens-when-i-hit-my-maximum-dtus"></a>¿Qué ocurre cuando visita Mi DTUs máximos

Niveles de rendimiento son calibre y regulados para proporcionar los recursos necesarios para ejecutar la carga de trabajo de la base de datos hasta los límites máximos permitido para el nivel / performance de nivel de servicio seleccionado. Si la carga de trabajo alcance los límites de uno de los límites de CPU/datos IO/registro IO, sigue apareciendo los recursos en el nivel máximo, pero es probable que vea una mayor latencia de las consultas. Estos límites no producen los errores, pero en su lugar un retraso en la carga de trabajo, a menos que la disminución pasa a ser tan grave que consultas empiezan intervalos. Si se ha alcanzado el límite máximo permitido simultáneas sesiones/de peticiones de usuario (subprocesos), consulte errores explícitos. Vea i/OS de registro de transacciones y [los límites de recursos de base de datos de SQL Azure](sql-database-resource-limits.md) para obtener información sobre el límite de recursos distintos de CPU, memoria, i/OS de datos.

## <a name="next-steps"></a>Pasos siguientes

- Para obtener información sobre la DTUs y eDTUs disponible para las bases de datos independiente y agrupaciones elásticos, vea [niveles de servicio](sql-database-service-tiers.md) .
- Vea i/OS de registro de transacciones y [los límites de recursos de base de datos de SQL Azure](sql-database-resource-limits.md) para obtener información sobre el límite de recursos distintos de CPU, memoria, i/OS de datos.
- Ver [Información de Performance de consulta de base de datos de SQL](sql-database-query-performance.md) para comprender su consumo (DTUs).
- Vea [información general de referencia de base de datos de SQL](sql-database-benchmark-overview.md) para conocer la metodología de la carga de trabajo OLTP comparativa que se utiliza para determinar la mezcla DTU.