<properties
   pageTitle="Bucles en el almacén de datos SQL | Microsoft Azure"
   description="Sugerencias para reemplazar cursores en el almacén de datos de SQL Azure para desarrollar soluciones y bucles Transact-SQL."
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

# <a name="loops-in-sql-data-warehouse"></a>Bucles en el almacén de datos SQL
Almacén de datos SQL es compatible con el bucle [mientras][] para ejecutar varias veces bloques de instrucciones. Seguirá para siempre y cuando las condiciones especificadas son verdaderas o hasta que el código específicamente termina el bucle utilizando la `BREAK` palabra clave. Bucles son particularmente útiles de reemplazo cursores definidos en el código SQL. Afortunadamente, casi todos los cursores que se escriben en código SQL son del avance rápido, lea solo variedad. Por tanto bucles [WHILE] son una excelente alternativa si siente que tener que reemplazar uno.

## <a name="leveraging-loops-and-replacing-cursors-in-sql-data-warehouse"></a>Sacar provecho de bucles y reemplazar cursores en el almacén de datos de SQL
Sin embargo, antes de comenzar en cabeza primero debería hágase la pregunta siguiente: "Este cursor se pudo volver a escribir para utilizar las operaciones de conjunto basado?". En muchos casos la respuesta será Sí y con qué frecuencia es el mejor método. Una operación de conjunto basado a menudo realiza significativamente más rápido que un enfoque iterativo fila por fila.

Avance rápido cursores de sólo lectura pueden sustituirse fácilmente con una construcción de bucle. A continuación es un ejemplo sencillo. Este ejemplo de código actualiza las estadísticas de todas las tablas de la base de datos. Repitiendo las tablas en el bucle somos capaces de ejecutar cada comando en secuencia.

Primero, cree una tabla temporal que contiene un número de fila único utilizado para identificar las instrucciones individuales:

```
CREATE TABLE #tbl
WITH
( DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT  ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS Sequence
,       [name]
,       'UPDATE STATISTICS '+QUOTENAME([name]) AS sql_code
FROM    sys.tables
;
```

En segundo término, inicializar las variables necesarios para realizar el bucle:

```
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

Ahora recorrer instrucciones ejecutar uno a la vez:

```
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

Por último colocar la tabla temporal creada en el primer paso

```
DROP TABLE #tbl;
```


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="next-steps"></a>Pasos siguientes
Para obtener más sugerencias de desarrollo, vea [Introducción al desarrollo][].

<!--Image references-->

<!--Article references-->
[Introducción al desarrollo]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[MIENTRAS]: https://msdn.microsoft.com/library/ms178642.aspx


<!--Other Web references-->
