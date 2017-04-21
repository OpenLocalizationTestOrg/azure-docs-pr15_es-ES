<properties
    pageTitle="& Opciones de rendimiento de la base de datos SQL: niveles de servicio | Microsoft Azure"
    description="Comparar las características de continuidad de base de datos SQL de rendimiento y empresa de los niveles de servicio para equilibrar costo y capacidad de medida que escala."
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
    ms.workload="data-management"
    ms.date="08/10/2016"
    ms.author="carlrab"/>

# <a name="sql-database-options-and-performance-understand-whats-available-in-each-service-tier"></a>Opciones de la base de datos SQL y rendimiento: comprender qué está disponible en cada nivel de servicio

[Base de datos de SQL Azure](sql-database-technical-overview.md) ofrece tres niveles de servicio con varios niveles de rendimiento para controlar diferentes cargas de trabajo. Cada nivel de rendimiento proporciona un conjunto creciente de recursos diseñados para ofrecer un rendimiento cada vez superior. Puede administrar sus propios [niveles de servicio](sql-database-service-tiers.md#standalone-database-service-tiers-and-performance-levels) con su propio nivel de rendimiento de cada base de datos. También puede administrar varias bases de datos en un [grupo elástico](sql-database-service-tiers.md#elastic-pool-service-tiers-and-performance-in-edtus) con un conjunto de recursos compartido. Los recursos disponibles para las bases de datos independiente se expresan en términos de unidades de transacción de base de datos (DTUs) y para los grupos de elásticos en términos elástico DTUs o eDTUs. Para obtener más información sobre DTUs y eDTUs, consulte [¿Qué es una DTU](sql-database-what-is-a-dtu.md). 

En ambos casos, los niveles de servicio incluyen **básico**, **estándar**y **Premium**. Opciones de base de datos en estos niveles son similares para las bases de datos independiente y elásticos grupos, pero hay otras consideraciones para los grupos de elásticos. Este artículo proporciona detalles de los niveles de servicio para las bases de datos independiente y agrupaciones elásticos.

## <a name="service-tiers-and-database-options"></a>Niveles de servicio y opciones de la base de datos
Básico, estándar y niveles de servicio Premium todos tienen un tiempo de actividad SLA del 99,99% y ofrecen performance predecible, opciones de continuidad empresarial flexible, características de seguridad y facturación cada hora. La siguiente tabla proporciona ejemplos de los niveles que mejor adecuados para las cargas de trabajo de otra aplicación.

| Nivel de servicio | Cargas de trabajo de destino |
|---|---|
| **Básico** | Ideal para una base de datos pequeña auxiliares normalmente una única operación activa en un momento dado. Ejemplos de bases de datos que se usan para desarrollo o pruebas o pequeños poco utilizan aplicaciones. |
| **Estándar** | La opción Ir a las aplicaciones de nube auxiliares varias consultas simultáneas. Ejemplos de aplicaciones web o grupo de trabajo. |
| **Premium** | Diseñado para un gran volumen de transacciones, compatible con muchos usuarios simultáneos y que requieren el máximo nivel de capacidades de continuidad empresarial. Algunos ejemplos son bases de datos de aplicaciones importantes. |

>[AZURE.NOTE] Se retiren ediciones Web y empresa. Lea las [Preguntas más frecuentes de puesta de sol](https://azure.microsoft.com/pricing/details/sql-database/web-business/) si desea seguir utilizando ediciones Web y empresa.

## <a name="standalone-database-service-tiers-and-performance-levels"></a>Niveles de rendimiento y niveles de servicio de base de datos independiente
Bases de datos independiente, hay varios niveles de rendimiento en cada nivel de servicio. Tiene la flexibilidad para elegir el nivel que mejor se adapte a las peticiones de su carga de trabajo. Si necesita escalar hacia arriba o hacia abajo, puede cambiar fácilmente los niveles de la base de datos. Para obtener más información, vea [cambiar niveles de servicio de base de datos y niveles de rendimiento](sql-database-scale-up.md) .

Características de rendimiento que se muestran aquí se aplican a las bases de datos creados con [V12 de base de datos de SQL](sql-database-v12-whats-new.md). Independientemente del número de bases de datos hospedado, la base de datos obtiene un conjunto de garantía de recursos y las características de rendimiento esperado de la base de datos no se ven afectadas.

[AZURE.INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

>[AZURE.NOTE] Para obtener una explicación detallada de las demás filas en esta tabla de niveles de servicio, vea [los límites y las capacidades de nivel de servicio](sql-database-performance-guidance.md#service-tier-capabilities-and-limits).

## <a name="elastic-pool-service-tiers-and-performance-in-edtus"></a>Niveles de servicio de grupo elásticas y rendimiento en eDTUs
Además de crear y aplicar una base de datos independiente, también tiene la opción de la administración de varias bases de datos en un [grupo elástico](sql-database-elastic-pool.md). Todas las bases de datos en un grupo de elástico comparten un conjunto común de recursos. Las características de rendimiento se miden *Elástico unidades de transacción de base de datos* (eDTUs). Como con bases de datos independiente, grupos vienen en tres niveles de servicio: **básico**, **estándar**y **Premium**. Para los grupos, estos tres niveles aún definen los límites de rendimiento general y varias características.

Grupos de permitir que las bases de datos compartir y consumir recursos DTU sin necesidad de asignar un nivel de rendimiento específicos para cada base de datos en el grupo. Por ejemplo, una base de datos independiente en un grupo estándar puede ir desde mediante eDTUs 0 a la eDTU máximo de la base de datos que se configura al configurar el grupo. Grupos de permiten varias bases de datos con diferentes cargas de trabajo a utilizar de forma eficaz eDTU recursos disponibles para todo el grupo. Para obtener más información, vea [Consideraciones de precio y rendimiento para un grupo de elástico](sql-database-elastic-pool-guidance.md) .

La siguiente tabla describe las características de los niveles de servicio de grupo.

[AZURE.INCLUDE [SQL DB service tiers table for elastic pools](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

Cada base de datos dentro de un grupo también se adhiere a las características de base de datos independiente para ese nivel. Por ejemplo, el grupo básico tiene un límite para el número máximo de sesiones por cada grupo de 4800-28800, pero una base de datos individual dentro de un grupo básico tiene un límite de base de datos de sesiones de 300.

## <a name="choosing-a-service-tier"></a>Elegir un nivel de servicio

Para decidir en un nivel de servicio, empiece por determinar si la base de datos debe ser una base de datos independiente o parte de un grupo elástico. 

### <a name="choosing-a-service-tier-for-a-standalone-database"></a>Elegir un nivel de servicio para una base de datos independiente

Para decidir en un nivel de servicio para una base de datos independiente, empiece por determinar las características de base de datos que necesita para elegir la edición de la base de datos SQL:

- Tamaño de la base de datos (2 GB de memoria máxima para básica, máximo de 250 GB para estándar y 500 GB hasta 1 TB máximo de Premium - según el nivel de rendimiento)
- Período de retención de copia de seguridad de base de datos (7 días para Basic, 35 días estándar y 35 días para Premium)

Una vez haya determinado la edición de la base de datos SQL, ya está listo para determinar el nivel de rendimiento de la base de datos (el número de DTUs). Se puede ver y, a continuación [escalar hacia arriba o abajo dinámicamente](sql-database-scale-up.md) en función de la experiencia real. También puede usar la [Calculadora DTU](http://dtucalculator.azurewebsites.net/) para aproximar el número de DTUs que sea necesario. 

### <a name="choosing-a-service-tier-for-an-elastic-database-pool"></a>Elegir un nivel de servicio para un grupo de elásticos de la base de datos.

Para decidir en el nivel de servicio para un grupo de la base de datos elástico, empiece por determinar las características de base de datos que necesita para elegir el nivel de servicio para la agrupación.

- Tamaño de la base de datos (2 GB para Basic, estándar de 250 GB y 500 GB para Premium)
- Período de retención de copia de seguridad de base de datos (7 días para Basic, 35 días estándar y 35 días para Premium)
- Número de bases de datos por grupo (400 Basic, 400 estándar y 50 Premium)
- Almacenamiento máximo por grupo (117 GB para Basic, 1200 estándar y 750 Premium)

Una vez haya determinado el nivel de servicio para la agrupación, ya está listo para determinar el nivel de rendimiento para el grupo (eDTUs). Se puede ver y, a continuación, [ampliar o reducir dinámicamente](sql-database-elastic-pool-manage-portal.md#change-performance-settings-of-a-pool) según experiencia real. Puede usar la [Calculadora DTU](http://dtucalculator.azurewebsites.net/) para aproximar el número de DTUs necesarias para cada base de datos en un grupo para ayudar a determinar el límite superior para el grupo.

## <a name="next-steps"></a>Pasos siguientes
- Obtenga más información sobre los precios de estos niveles de [Precios de base de datos de SQL](https://azure.microsoft.com/pricing/details/sql-database/).
- Obtenga información sobre los detalles de [grupos elásticas](sql-database-elastic-pool-guidance.md) y [Consideraciones de precio y rendimiento para grupos de elásticos](sql-database-elastic-pool-guidance.md).
- Obtenga información sobre cómo [supervisar, administrar y cambiar el tamaño de los grupos elásticos](sql-database-elastic-pool-manage-portal.md) y [supervisar el rendimiento de bases de datos independiente](sql-database-single-database-monitor.md).
- Ahora que sabe acerca de los niveles de base de datos de SQL, pruebe con una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial/) y aprenda [a crear la primera base de datos SQL](sql-database-get-started.md).

## <a name="additional-resources"></a>Recursos adicionales

- [Patrones de diseño para las aplicaciones de múltiples arrendatarios SaaS con la base de datos de SQL Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md)
- [Curso de vídeo de Microsoft Virtual Academy capacidades elástica de la base de datos en la base de datos de SQL Azure](https://mva.microsoft.com/en-US/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)
