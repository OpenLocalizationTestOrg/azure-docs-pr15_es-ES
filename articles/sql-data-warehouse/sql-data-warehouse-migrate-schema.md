<properties
   pageTitle="Migrar su esquema al almacén de datos de SQL | Microsoft Azure"
   description="Sugerencias para migrar su esquema al almacén de datos de SQL Azure para desarrollar soluciones."
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
   ms.date="08/25/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="migrate-your-schema-to-sql-data-warehouse"></a>Migrar su esquema al almacén de datos de SQL#

La siguiente se resumen ayudarle a entender las diferencias entre SQL Server y almacenamiento de datos de SQL para ayudarle a migrar la base de datos.

## <a name="table-migration"></a>Migración de tabla

Durante la migración de las tablas, desea familiarizarse con las características de tabla de las tablas del almacén de datos de SQL.  [Información general de la tabla][] es un excelente lugar para empezar.  Este artículo presenta las consideraciones más importantes al crear una tabla como estadísticas de la tabla, distribución, partición e indización.  También trata sobre algunas [características de tabla no compatibles][] y alternativas.

Almacén de datos SQL es compatible con los tipos de datos empresariales comunes.  Consulte el artículo de [tipos de datos][] para una lista de compatibles y los [tipos de datos no admitidos][].  [Tipos de datos][] también contiene una consulta para identificar [los tipos de datos no admitidos][].  Al convertir a los tipos de datos, asegúrese de buscar en el [tipo de datos de los procedimientos recomendados][].

## <a name="next-steps"></a>Pasos siguientes
Una vez que se han migrado correctamente el esquema de la base de datos al almacén de datos de SQL, continúe con uno de los siguientes artículos:

- [Migrar los datos][]
- [Migrar el código][]

Para obtener más información sobre procedimientos recomendados de almacén de datos de SQL, vea el artículo de [los procedimientos recomendados][] .

<!--Image references-->

<!--Article references-->
[Migrar el código]: ./sql-data-warehouse-migrate-code.md
[Migrar los datos]: ./sql-data-warehouse-migrate-data.md
[prácticas recomendadas]: ./sql-data-warehouse-best-practices.md
[información general de la tabla]: ./sql-data-warehouse-tables-overview.md
[características de tabla no compatibles]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[tipos de datos]: ./sql-data-warehouse-tables-data-types.md
[tipos de datos no admitidos]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types
[prácticas recomendadas de tipo de datos]: ./sql-data-warehouse-tables-data-types.md#data-type-best-practices

<!--MSDN references-->


<!--Other Web references-->
