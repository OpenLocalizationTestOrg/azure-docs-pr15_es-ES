<properties
   pageTitle="Procedimientos almacenados en el almacén de datos de SQL | Microsoft Azure"
   description="Sugerencias para implementar procedimientos almacenados en el almacén de datos de SQL Azure para desarrollar soluciones."
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
   ms.date="06/30/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="stored-procedures-in-sql-data-warehouse"></a>Procedimientos almacenados en el almacén de datos de SQL

Almacén de datos SQL es compatible con muchas de las características de Transact-SQL que se encuentra en SQL Server. Más importante aún hay escalado características específicas que queremos aprovechar para maximizar el rendimiento de la solución.

Sin embargo, para mantener la escala y el rendimiento del almacén de datos de SQL hay también están algunas características y funcionalidades que tienen diferencias en el comportamiento y otras personas que no son compatibles.

En este artículo se explica cómo implementar procedimientos almacenados en el almacén de datos de SQL.

## <a name="introducing-stored-procedures"></a>Introducción a los procedimientos almacenados
Procedimientos almacenados son excelentes para encapsular el código SQL; almacenamiento cerca de los datos en el almacén de datos. Mediante encapsulado el código en unidades manejables procedimientos almacenados ayudar a los desarrolladores dividir sus soluciones; facilitar la mayor reutilización de código. Cada procedimiento almacenado también puede aceptar parámetros para que sean más flexibles.

Almacén de datos SQL proporciona una implementación de procedimiento almacenado simplificado y optimizado. La diferencia en comparación con SQL Server es que el procedimiento almacenado no es código compilado previamente. En el almacén de datos le preocupa generalmente menos con el tiempo de compilación. Es más importante que el código de procedimiento almacenado está optimizado correctamente cuando se trabaja con grandes volúmenes de datos. El objetivo es guardar horas, minutos y segundos no milisegundos. Por lo tanto, es más útil pensar en procedimientos almacenados como contenedores de lógica SQL.     

Cuando se ejecuta el procedimiento de almacenamiento de datos de SQL se analizan las instrucciones SQL, traducir y optimizado en tiempo de ejecución. Durante el proceso de cada instrucción se convierte en consultas distribuidas. El código SQL que realmente se ejecuta en los datos es diferente a la consulta que se ha enviado.

## <a name="nesting-stored-procedures"></a>Procedimientos almacenado de anidamiento
Cuando los procedimientos almacenados llamar a otros procedimientos almacenados o ejecutar sql dinámico la llamada a procedimiento o código almacenada interna se dice que se pueden anidar.

Almacén de datos SQL admite un máximo de niveles de anidamiento 8. Esto es ligeramente diferente a SQL Server. El nivel de anidamiento en SQL Server es 32.

La llamada a procedimiento almacenado de nivel superior equivale para anidar nivel 1

```sql
EXEC prc_nesting
```
Si el procedimiento almacenado también realiza otra ejecutivo llamar, Esto aumentará el nivel de anidamiento a 2
```sql
CREATE PROCEDURE prc_nesting
AS
EXEC prc_nesting_2  -- This call is nest level 2
GO
EXEC prc_nesting
```
Si el segundo procedimiento ejecuta algunas sql dinámico, Esto aumentará el nivel de anidamiento a 3
```sql
CREATE PROCEDURE prc_nesting_2
AS
EXEC sp_executesql 'SELECT 'another nest level'  -- This call is nest level 2
GO
EXEC prc_nesting
```

Almacén de datos de SQL de nota no se admite actualmente @@NESTLEVEL. Debe mantener un seguimiento de su nivel de anidamiento usted mismo. Es probable que se visita el límite de nivel de 8 anidamiento pero lo necesite volver a su código de trabajo y "acoplar" para que quepa dentro de este límite.

## <a name="insertexecute"></a>INSERTAR... EJECUTAR
Almacén de datos SQL no permite utilizar el conjunto de resultados de un procedimiento almacenado con una instrucción INSERT. Sin embargo, hay una alternativa que puede usar.

Consulte el siguiente artículo en [tablas temporales] un ejemplo de cómo hacerlo.

## <a name="limitations"></a>Limitaciones

Hay algunos aspectos de los procedimientos almacenados de Transact-SQL que no se ha implementado en el almacén de datos de SQL.

Son:

- procedimientos almacenados temporales
- procedimientos almacenados numerados
- procedimientos almacenados extendidos
- Procedimientos almacenados CLR
- opción de cifrado
- opción de replicación
- parámetros con valores de tabla
- parámetros de solo lectura
- parámetros predeterminados
- contextos de ejecución
- instrucción return

## <a name="next-steps"></a>Pasos siguientes
Para obtener más sugerencias de desarrollo, vea [Introducción al desarrollo][].

<!--Image references-->

<!--Article references-->
[tablas temporales]: ./sql-data-warehouse-tables-temporary.md#modularizing-code
[Introducción al desarrollo]: ./sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[nest level]: https://msdn.microsoft.com/library/ms187371.aspx

<!--Other Web references-->
