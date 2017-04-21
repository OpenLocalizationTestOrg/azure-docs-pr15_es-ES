<properties
   pageTitle="Tipos de datos de tablas en el almacén de datos de SQL | Microsoft Azure"
   description="Introducción a los tipos de datos de tablas de almacén de datos de SQL Azure."
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
   ms.date="06/29/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="data-types-for-tables-in-sql-data-warehouse"></a>Tipos de datos de tablas en el almacén de datos de SQL

> [AZURE.SELECTOR]
- [Información general][]
- [Tipos de datos][]
- [Distribuir][]
- [Índice][]
- [Partición][]
- [Estadísticas][]
- [Temporal][]

Almacén de datos SQL es compatible con los más usados tipos de datos.  A continuación se muestra una lista de los tipos de datos admitidos por el almacén de datos de SQL.  Para obtener más información sobre el tipo de datos de soporte técnico, consulte [Crear tabla][].

|**Tipos de datos compatibles**|||
|---|---|---|
[bigint][]|[decimal][]|[smallint][]|
[binario.][]|[flotante][]|[smallmoney][]|
[bit][]|[int][]|[sysname][]|
[Char][]|[dinero][]|[hora][]|
[fecha][]|[nchar][]|[tinyint][]|
[fecha y hora][]|[nvarchar][]|[uniqueidentifier][]|
[datetime2][]|[real][]|[varbinary][]|
[DateTimeOffset][]|[smalldatetime][]|[varchar][]|


## <a name="data-type-best-practices"></a>Prácticas recomendadas de tipo de datos

 Cuando define los tipos de columna, utilizando el tipo de datos más pequeño que ayude a los datos mejorará el rendimiento de la consulta. Esto es especialmente importante para las columnas CHAR y VARCHAR. Si el valor más largo de una columna es de 25 caracteres, a continuación, defina la columna como VARCHAR(25). Evite definir todas las columnas de carácter a una longitud de gran tamaño predeterminado. Además, debe definir columnas como VARCHAR cuando eso es todo lo que es necesario, en lugar de usar [NVARCHAR][].  Utilice nvarchar (4000) o VARCHAR(8000) cuando sea posible en lugar de nvarchar (max), varchar (max).

## <a name="polybase-limitation"></a>Limitación de Polybase

Si está utilizando Polybase para cargar las tablas, defina las tablas para que el tamaño máximo posible de fila, incluida la longitud total de columnas de longitud variable, no supere 32.767 bytes.  Puede definir una fila con datos de longitud variable que pueden superar este ancho y cargar las filas con BCP, no podrá usar Polybase para cargar estos datos.  Compatibilidad con Polybase amplia filas se agregará pronto.

## <a name="unsupported-data-types"></a>Tipos de datos no admitidos

Si va a migrar la base de datos desde otra plataforma SQL como base de datos de SQL Azure, migrar, puede encontrar algunos tipos de datos que no son compatibles en el almacén de datos de SQL.  A continuación se muestran los tipos de datos no compatibles, así como algunas alternativas que puede usar en lugar de los tipos de datos no admitidos.

|Tipo de datos|Solución alternativa|
|---|---|
|[geometría][]|[varbinary][]|
|[geografía][]|[varbinary][]|
|[hierarchyid][]|[nvarchar][] (4000)|
|[imagen][ntext,text,image]|[varbinary][]|
|[texto][ntext,text,image]|[varchar][]|
|[ntext][ntext,text,image]|[nvarchar][]|
|[sql_variant][]|División de columna en varias columnas inflexible.|
|[tabla][]|Convertir en tablas temporales.|
|[marca de tiempo][]|Modificar código para usar [datetime2][] y `CURRENT_TIMESTAMP` función.  Constantes solo se admiten como valores predeterminados, por lo tanto, current_timestamp no se puede definir como una restricción predeterminada. Si necesita migrar los valores de la versión de fila de una columna con tipo de marca de hora, a continuación, use [binario][](8) o [VARBINARY][binario](8) para los valores de la versión de fila no NULL o NULL.|
|[XML][]|[varchar][]|
|[tipos definidos por el usuario][]|convertir en sus tipos nativos siempre que sea posible|
|valores predeterminados|valores predeterminados de soporte técnico literales y constantes solo.  Las expresiones no deterministas o funciones, como por ejemplo `GETDATE()` o `CURRENT_TIMESTAMP`, no son compatibles.|

La debajo de SQL se pueden ejecutar en la actual base de datos SQL para identificar las columnas que no son sea compatible con el almacén de datos de SQL Azure:

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','timestamp','xml')
 AND  y.[is_user_defined] = 1;
```

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información, consulte los artículos[información general]de [Información general de la tabla], [distribuir una tabla][distribuir], [indización de una tabla]y de[índice], [partición de una tabla]de[partición], [Estadísticas de la tabla de mantenimiento de][las estadísticas de] [Tablas temporales][temporal].  Para obtener más información sobre procedimientos recomendados, consulte [Prácticas recomendadas de almacén de datos de SQL][].

<!--Image references-->

<!--Article references-->
[Información general]: ./sql-data-warehouse-tables-overview.md
[Tipos de datos]: ./sql-data-warehouse-tables-data-types.md
[Distribuir]: ./sql-data-warehouse-tables-distribute.md
[Índice]: ./sql-data-warehouse-tables-index.md
[Partición]: ./sql-data-warehouse-tables-partition.md
[Estadísticas]: ./sql-data-warehouse-tables-statistics.md
[Temporal]: ./sql-data-warehouse-tables-temporary.md
[Prácticas recomendadas de almacén de datos SQL]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->

<!--Other Web references-->
[Crear tabla]: https://msdn.microsoft.com/library/mt203953.aspx
[bigint]: https://msdn.microsoft.com/library/ms187745.aspx
[binario.]: https://msdn.microsoft.com/library/ms188362.aspx
[bit]: https://msdn.microsoft.com/library/ms177603.aspx
[Char]: https://msdn.microsoft.com/library/ms176089.aspx
[fecha]: https://msdn.microsoft.com/library/bb630352.aspx
[fecha y hora]: https://msdn.microsoft.com/library/ms187819.aspx
[datetime2]: https://msdn.microsoft.com/library/bb677335.aspx
[DateTimeOffset]: https://msdn.microsoft.com/library/bb630289.aspx
[decimal]: https://msdn.microsoft.com/library/ms187746.aspx
[flotante]: https://msdn.microsoft.com/library/ms173773.aspx
[geometría]: https://msdn.microsoft.com/library/cc280487.aspx
[geografía]: https://msdn.microsoft.com/library/cc280766.aspx
[hierarchyid]: https://msdn.microsoft.com/library/bb677290.aspx
[int]: https://msdn.microsoft.com/library/ms187745.aspx
[dinero]: https://msdn.microsoft.com/library/ms179882.aspx
[nchar]: https://msdn.microsoft.com/library/ms186939.aspx
[nvarchar]: https://msdn.microsoft.com/library/ms186939.aspx
[ntext,text,image]: https://msdn.microsoft.com/library/ms187993.aspx
[real]: https://msdn.microsoft.com/library/ms173773.aspx
[smalldatetime]: https://msdn.microsoft.com/library/ms182418.aspx
[smallint]: https://msdn.microsoft.com/library/ms187745.aspx
[smallmoney]: https://msdn.microsoft.com/library/ms179882.aspx
[sql_variant]: https://msdn.microsoft.com/library/ms173829.aspx
[sysname]: https://msdn.microsoft.com/library/ms186939.aspx
[tabla]: https://msdn.microsoft.com/library/ms175010.aspx
[hora]: https://msdn.microsoft.com/library/bb677243.aspx
[marca de tiempo]: https://msdn.microsoft.com/library/ms182776.aspx
[tinyint]: https://msdn.microsoft.com/library/ms187745.aspx
[uniqueidentifier]: https://msdn.microsoft.com/library/ms187942.aspx
[varbinary]: https://msdn.microsoft.com/library/ms188362.aspx
[varchar]: https://msdn.microsoft.com/library/ms186939.aspx
[XML]: https://msdn.microsoft.com/library/ms187339.aspx
[tipos definidos por el usuario]: https://msdn.microsoft.com/library/ms131694.aspx
