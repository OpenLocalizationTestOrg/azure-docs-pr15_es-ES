<properties
   pageTitle="Asignar variables en el almacén de datos de SQL | Microsoft Azure"
   description="Sugerencias para asignar variables de Transact-SQL en el almacén de datos de SQL Azure para desarrollar soluciones."
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

# <a name="assign-variables-in-sql-data-warehouse"></a>Asignar variables en el almacén de datos de SQL
Se establecen las variables en el almacén de datos de SQL con la `DECLARE` instrucción o la `SET` instrucción.

Los siguientes son formas perfectamente válida para establecer el valor de una variable:

## <a name="setting-variables-with-declare"></a>Configuración de variables con DECLARE

Inicialización de variables con DECLARE es una de las maneras más flexibles para establecer el valor de una variable en el almacén de datos de SQL.

```sql
DECLARE @v  int = 0
;
```

También puede usar DECLARE para establecer más de una variable a la vez. No puede usar `SELECT` o `UPDATE` para hacer esto:

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

No se puede iniciar y usar una variable en la instrucción DECLARE misma. **Para ilustrar el punto en el ejemplo siguiente se permite como** @p1 se tanto inicializado y se usa en la misma instrucción DECLARE. Esto provocará un error.

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="setting-values-with-set"></a>Valores de configuración con conjunto
Conjunto es un método muy común para establecer una única variable.

Todos los ejemplos siguientes son formas válidas de establecer una variable con el conjunto:

```sql
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

Solo puede establecer una variable a la vez con el conjunto. Sin embargo, como puede verse encima de operadores compuestos son permisible.

## <a name="limitations"></a>Limitaciones
No puede usar SELECT o UPDATE de asignación variable.


## <a name="next-steps"></a>Pasos siguientes
Para obtener más sugerencias de desarrollo, vea [Introducción al desarrollo][].

<!--Image references-->

<!--Article references-->
[Introducción al desarrollo]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->
