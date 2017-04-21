<properties
    pageTitle="Sugerencias de optimización del rendimiento de base de datos SQL | Microsoft Azure"
    description="Sugerencias para la optimización de base de datos de SQL Azure a través de evaluación y mejora del rendimiento."
    services="sql-database"
    documentationCenter=""
    authors="v-shysun"
    manager="felixwu"
    editor=""
    keywords="SQL ajuste del rendimiento, base de datos ajuste del rendimiento, sugerencias de optimización del rendimiento de sql ajustar el rendimiento de base de datos sql"/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="v-shysun"/>

# <a name="sql-database-performance-tuning-tips"></a>Sugerencias de ajuste de rendimiento de base de datos SQL
Puede cambiar el [nivel de servicio](sql-database-service-tiers.md) de una base de datos o aumentar el eDTUs de un grupo de la base de datos elásticos en cualquier momento para mejorar el rendimiento, pero puede que desee identificar oportunidades para mejorar y optimizar el rendimiento de la consulta en primer lugar. Índices perdidos y consultas mal optimizadas son los motivos habituales para obtener un rendimiento deficiente de la base de datos. Este artículo proporcionan pautas para la optimización del rendimiento en base de datos de SQL.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="steps-to-evaluate-and-tune-database-performance"></a>Pasos para evaluar y ajustar el rendimiento de base de datos
1.  En el [Portal de Azure](https://portal.azure.com), haga clic en **bases de datos SQL**, seleccione la base de datos y, a continuación, usar el gráfico de supervisión busque recursos acercando a su máximo. Consumo de DTU se muestra de forma predeterminada. Haga clic en **Editar** para cambiar el intervalo de tiempo y los valores que se muestran.
2.  Usar [Una perspectiva de rendimiento de consulta](sql-database-query-performance.md) para evaluar las consultas con DTUs y use [Asesor de base de datos de SQL](sql-database-advisor.md) para ver recomendaciones para crear y quitar índices, parametrizar consultas y solución de problemas de esquema.
3.  Puede usar vistas de administración dinámica (DMV), eventos extendido (Xevents) y el almacenamiento de la consulta en SSMS para obtener los parámetros de rendimiento en tiempo real. Vea el [tema de la Guía de rendimiento](sql-database-performance-guidance.md) para supervisar y sugerencias de ajuste detallada.


    > [AZURE.IMPORTANT] Se recomienda usar siempre la versión más reciente de Management Studio permanezca sincronizado con las actualizaciones de Microsoft Azure y base de datos de SQL. [Actualizar SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


## <a name="steps-to-improve-database-performance-with-more-resources"></a>Pasos para mejorar el rendimiento de la base de datos con más recursos
1.  Para bases de datos, puede [cambiar los niveles de servicio](sql-database-scale-up.md) a petición para mejorar el rendimiento de la base de datos.
2.  Varias bases de datos, considere el uso de [grupos de base de datos elástico](sql-database-elastic-pool-guidance.md) escalar recursos automáticamente.
