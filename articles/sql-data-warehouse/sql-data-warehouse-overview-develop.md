<properties
   pageTitle="Diseñar las decisiones y técnicas de codificación para el desarrollo de almacén de datos SQL | Microsoft Azure"
   description="Conceptos de desarrollo, las decisiones de diseño, recomendaciones y técnicas de codificación para el almacén de datos de SQL."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/16/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="design-decisions-and-coding-techniques-for-sql-data-warehouse"></a>Decisiones de diseño y técnicas de codificación para el almacén de datos de SQL

Eche un vistazo a través de estos artículos de desarrollo a comprender mejor las decisiones de diseño clave, recomendaciones y técnicas de codificación para el almacén de datos de SQL.

## <a name="key-design-decisions"></a>Decisiones clave de diseño
Los siguientes artículos resaltan algunos de los conceptos clave y las decisiones de diseño que debe conocer para el desarrollo de su almacén de datos distribuidos con el almacén de datos SQL:

- [conexiones][]
- [simultaneidad][]
- [transacciones][]
- [esquemas definidos por el usuario][]
- [distribución de tablas][]
- [índices de tabla][]
- [particiones de tabla][]
- [CTAS][]
- [estadísticas][]

## <a name="development-recommendations-and-coding-techniques"></a>Recomendaciones de desarrollo y técnicas de codificación
Estos artículos resaltar técnicas de codificación específicas, sugerencias y recomendaciones para desarrollar el almacén de datos de SQL:

- [procedimientos almacenados][]
- [etiquetas][]
- [Vistas][]
- [tablas temporales][]
- [SQL dinámico][]
- [bucle][]
- [Opciones de agrupar por][]
- [asignación de variables][]

## <a name="next-steps"></a>Pasos siguientes
Una vez que ha pasado por los artículos de desarrollo Eche un vistazo a través de la página de [referencia de Transact-SQL][] para obtener más detalles sobre la sintaxis compatible para el almacén de datos de SQL.

<!--Image references-->

<!--Article references-->
[simultaneidad]: ./sql-data-warehouse-develop-concurrency.md
[conexiones]: ./sql-data-warehouse-connect-overview.md
[CTAS]: ./sql-data-warehouse-develop-ctas.md
[SQL dinámico]: ./sql-data-warehouse-develop-dynamic-sql.md
[Opciones de agrupar por]: ./sql-data-warehouse-develop-group-by-options.md
[etiquetas]: ./sql-data-warehouse-develop-label.md
[bucle]: ./sql-data-warehouse-develop-loops.md
[estadísticas]: ./sql-data-warehouse-tables-statistics.md
[procedimientos almacenados]: ./sql-data-warehouse-develop-stored-procedures.md
[distribución de tablas]: ./sql-data-warehouse-tables-distribute.md
[índices de tabla]: ./sql-data-warehouse-tables-index.md
[particiones de tabla]: ./sql-data-warehouse-tables-partition.md
[tablas temporales]: ./sql-data-warehouse-tables-temporary.md
[transacciones]: ./sql-data-warehouse-develop-transactions.md
[esquemas definidos por el usuario]: ./sql-data-warehouse-develop-user-defined-schemas.md
[asignación de variables]: ./sql-data-warehouse-develop-variable-assignment.md
[Vistas]: ./sql-data-warehouse-develop-views.md
[Referencia de Transact-SQL]: ./sql-data-warehouse-overview-reference.md

<!--MSDN references-->
[renaming objects]: https://msdn.microsoft.com/library/mt631611.aspx

<!--Other Web references-->
