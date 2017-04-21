<properties
   pageTitle="Usar etiquetas a las consultas de instrumento en el almacén de datos de SQL | Microsoft Azure"
   description="Sugerencias para usar las etiquetas a las consultas de instrumento en el almacén de datos de SQL Azure para desarrollar soluciones."
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

# <a name="use-labels-to-instrument-queries-in-sql-data-warehouse"></a>Usar etiquetas a las consultas de instrumento en el almacén de datos de SQL
Almacén de datos SQL es compatible con el concepto de etiquetas de la consulta. Antes de entrar en cualquier profundidad vamos a ver un ejemplo de uno:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

Esta última línea etiquetas la cadena 'Mi consulta etiqueta' a la consulta. Esto es especialmente útil que pueda para la consulta mediante la DMV la etiqueta. Nos proporciona un mecanismo para realizar un seguimiento de las consultas del problema y también para ayudarle a identificar el progreso a través de una ejecución ETL.

Una buena convención de nomenclatura realmente ayuda aquí. Por ejemplo, algo como ' PROJECT: procedimiento: instrucción: comentario ' le ayudarán a identificar la consulta en entre todo el código de control de código fuente.

Puede usar la siguiente consulta que usa las vistas dinámicas de administración para buscar por etiqueta:

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [AZURE.NOTE] Es fundamental ajustar corchetes o comillas dobles alrededor de la etiqueta de word al consultar. Etiqueta es una palabra reservada y le ha producido un error si no lo ha delimitada.


## <a name="next-steps"></a>Pasos siguientes
Para obtener más sugerencias de desarrollo, vea [Introducción al desarrollo][].

<!--Image references-->

<!--Article references-->
[Introducción al desarrollo]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->
