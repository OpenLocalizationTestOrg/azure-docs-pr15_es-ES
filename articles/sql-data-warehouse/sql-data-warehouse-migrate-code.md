<properties
   pageTitle="Migrar el código SQL al almacén de datos de SQL | Microsoft Azure"
   description="Sugerencias para migrar el código SQL al almacén de datos de SQL Azure para desarrollar soluciones."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/02/2016"
   ms.author="lodipalm;barbkess;sonyama;jrj"/>

# <a name="migrate-your-sql-code-to-sql-data-warehouse"></a>Migrar el código SQL al almacén de datos de SQL

Al migrar su código de otra base de datos al almacén de datos de SQL, es muy probable que debe realizar cambios en la base de código. Algunas características de almacén de datos SQL significativamente pueden mejorar el rendimiento como están diseñados para trabajar de forma distribuida. Sin embargo, para mantener el rendimiento y escala, algunas características también no están disponibles.

## <a name="common-t-sql-limitations"></a>Limitaciones de T SQL comunes

En la lista siguiente se resume la característica más comunes que no son compatibles en el almacén de datos de SQL Azure. Los vínculos le llevan a alternativas para la característica no admitida:

- [Combinaciones de ANSI en actualizaciones][]
- [Combinaciones de ANSI en eliminaciones][]
- [instrucción de combinación][]
- combinaciones entre bases de datos
- [cursores][]
- [SELECCIONE... EN][]
- [INSERTAR... EJECUTIVO][]
- cláusula de salida
- funciones definidas por el usuario de en línea
- funciones de múltiples instrucciones
- [expresiones de tabla comunes](#Common-table-expressions)
- [recursiva expresiones de tabla comunes (CTE)] (#Recursive-common-table-expressions-(CTE)
- Procedimientos y funciones CLR
- función $partition
- variables de tabla
- parámetros de valor de tabla
- transacciones distribuidas
- ejecutar / Deshacer de trabajo
- Guardar transacción
- contextos de ejecución (EXECUTE AS)
- [Agrupar por cláusula con rollup, cubo y conjuntos de opciones de agrupamiento][]
- [niveles de anidamiento más allá de 8][]
- [actualizar a través de vistas][]
- [uso de select de asignación variable][]
- [datos MAX no escriba dinámico cadenas de SQL][]

Afortunadamente la mayoría de estas limitaciones puede solucionar. Explicaciones se proporcionan en los artículos de desarrollo pertinente mencionados anteriormente.

## <a name="supported-cte-features"></a>Características CTE compatibles

Expresiones de tabla comunes (CTE) son parcialmente compatibles en el almacén de datos de SQL.  Actualmente se admiten las siguientes características CTE:

- Puede especificar una CTE en una instrucción SELECT.
- Puede especificar una CTE en una instrucción CREATE VIEW.
- Una CTE se puede especificar en una instrucción Crear tabla AS seleccione (CTAS).
- Una CTE se puede especificar en una instrucción crear remoto tabla AS seleccione (CRTAS).
- Una CTE se puede especificar en una instrucción crear externos tabla AS seleccione (CETAS).
- Puede hacer referencia a una tabla remota de una CTE.
- Puede hacer referencia a una tabla externa desde una CTE.
- Varias definiciones de consulta CTE pueden definirse en una CTE.

## <a name="cte-limitations"></a>Limitaciones de CTE

Expresiones de tabla comunes tienen algunas limitaciones en, incluido el almacén de datos SQL:

- Una CTE debe seguida de una sola instrucción SELECT. Insertar, actualizar, eliminar, y no se admiten instrucciones de combinación de correspondencia.
- Una expresión de tabla común que incluya referencias a sí mismo (una expresión de tabla común de recursiva) no es compatible (consulte debajo de la sección).
- No se permite especificar más de una cláusula en una CTE. Por ejemplo, si un CTE_query_definition contiene una subconsulta, que subconsulta no puede contener anidada con cláusula que define la otra.
- Una cláusula ORDER BY no pueden usarse en la CTE_query_definition, excepto cuando se especifica una cláusula TOP.
- Cuando se usa una CTE en una instrucción que forma parte de un lote, la instrucción antes de que se debe ir seguida de un punto y coma.
- Cuando se utiliza en instrucciones preparados por sp_prepare, CTE comportan del mismo modo que las otras instrucciones SELECT en PDW. Sin embargo, si se usan las CTE como parte de CETAS preparado por sp_prepare, puede aplazar el comportamiento de SQL Server y otras instrucciones PDW debido a la forma enlace se ha implementado para sp_prepare. Si SELECT que referencias que CTE usa una columna incorrecta que no existe en CTE, pasará la sp_prepare sin detectar el error, pero se produjo el error durante sp_execute.

## <a name="recursive-ctes"></a>CTE recursivas

No se admiten las CTE recursiva en el almacén de datos de SQL.  La migraion de CTE recursiva puede ser un poco completa y es el mejor proceso dividir la en varios pasos. Normalmente, puede utilizar un bucle y rellenar una tabla temporal como iteración las consultas de provisionales recursivas. Una vez que se rellena la tabla temporal, a continuación, puede devolver los datos como un único conjunto de resultados. Se ha usado un enfoque similar para resolver `GROUP BY WITH CUBE` en el artículo [cláusula con rollup group by, cubo y conjuntos de opciones de agrupamiento][] .

## <a name="unsupported-system-functions"></a>Funciones de sistema no admitidas

También hay algunas funciones del sistema que no son compatibles. Algunos de los principales que normalmente puede resultarle usados en el almacenamiento de datos son:

- NEWSEQUENTIALID()
- @@NESTLEVEL()
- @@IDENTITY()
- @@ROWCOUNT()
- ROWCOUNT_BIG
- ERROR_LINE()

Algunos de estos problemas pueden solucionar.

## <a name="rowcount-workaround"></a>@@ROWCOUNTsolución alternativa

Para evitar la falta de soporte para @@ROWCOUNT, crear un procedimiento almacenado que recuperará el último número de filas de sys.dm_pdw_request_steps y, a continuación, ejecutar `EXEC LastRowCount` después de una instrucción DML.

```sql
CREATE PROCEDURE LastRowCount AS
WITH LastRequest as 
(   SELECT TOP 1    request_id
    FROM            sys.dm_pdw_exec_requests
    WHERE           session_id = SESSION_ID()
    AND             resource_class IS NOT NULL
    ORDER BY end_time DESC
),
LastRequestRowCounts as
(
    SELECT  step_index, row_count
    FROM    sys.dm_pdw_request_steps
    WHERE   row_count >= 0
    AND     request_id IN (SELECT request_id from LastRequest)
)
SELECT TOP 1 row_count FROM LastRequestRowCounts ORDER BY step_index DESC
;
```

## <a name="next-steps"></a>Pasos siguientes
Para obtener una lista completa de todas las instrucciones SQL T admitidas, vea [los temas de Transact-SQL][].

<!--Image references-->

<!--Article references-->
[Combinaciones de ANSI en actualizaciones]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[Combinaciones de ANSI en eliminaciones]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[instrucción de combinación]: ./sql-data-warehouse-develop-ctas.md#replace-merge-statements
[INSERTAR... EJECUTIVO]: ./sql-data-warehouse-tables-temporary.md#modularizing-code
[Temas de Transact-SQL]: ./sql-data-warehouse-reference-tsql-statements.md

[cursores]: ./sql-data-warehouse-develop-loops.md
[SELECCIONE... EN]: ./sql-data-warehouse-develop-ctas.md#selectinto
[Agrupar por cláusula con rollup, cubo y conjuntos de opciones de agrupamiento]: ./sql-data-warehouse-develop-group-by-options.md
[niveles de anidamiento más allá de 8]: ./sql-data-warehouse-develop-transactions.md
[actualizar a través de vistas]: ./sql-data-warehouse-develop-views.md
[uso de select de asignación variable]: ./sql-data-warehouse-develop-variable-assignment.md
[datos MAX no escriba dinámico cadenas de SQL]: ./sql-data-warehouse-develop-dynamic-sql.md

<!--MSDN references-->

<!--Other Web references-->
