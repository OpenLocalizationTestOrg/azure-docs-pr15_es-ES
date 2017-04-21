<properties
   pageTitle="SQL dinámico en el almacén de datos SQL | Microsoft Azure"
   description="Sugerencias para usar SQL dinámico en el almacén de datos de SQL Azure para desarrollar soluciones."
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
   ms.date="06/14/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="dynamic-sql-in-sql-data-warehouse"></a>SQL dinámico en el almacén de datos SQL
Al desarrollar el código de la aplicación para el almacén de datos de SQL debe utilizar sql dinámico para ayudar a ofrecer soluciones flexibles, genéricas y modulares. Almacén de datos SQL no admite tipos de datos blob en este momento. Esto puede limitar el tamaño de las cadenas como tipos de blob incluyen tipos varchar (max) y nvarchar (max). Si ha utilizado estos tipos de código de aplicación al generar cadenas muy grandes, necesitará dividir el código en fragmentos y utilizar la instrucción de ejecución en su lugar.

Un ejemplo sencillo:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Si la cadena es breve puede utilizar [sp_executesql][] como normal.

> [AZURE.NOTE] Ejecutado instrucciones como SQL dinámico seguirán sujetos a todas las reglas de validación de TSQL.

## <a name="next-steps"></a>Pasos siguientes
Para obtener más sugerencias de desarrollo, vea [Introducción al desarrollo][].

<!--Image references-->

<!--Article references-->
[Introducción al desarrollo]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[sp_executesql]: https://msdn.microsoft.com/library/ms188001.aspx

<!--Other Web references-->
