<properties
    pageTitle="Seleccionar filas para migrar empleando una función de filtro (base de datos de estirar) | Microsoft Azure"
    description="Obtenga información sobre cómo seleccionar filas para migrar mediante una función de filtro."
    services="sql-server-stretch-database"
    documentationCenter=""
    authors="douglaslMS"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-server-stretch-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/28/2016"
    ms.author="douglasl"/>

# <a name="select-rows-to-migrate-by-using-a-filter-function-stretch-database"></a>Seleccionar filas para migrar empleando una función de filtro (base de datos de estirar)

Si los datos y se almacenan en una tabla independiente, puede configurar estiramiento de base de datos para migrar toda la tabla. Por otro lado, si la tabla contiene datos frías y caliente, puede especificar una función de filtro para seleccionar las filas que se van a migrar. El predicado de filtro es una tabla en línea\-con valores de función. Este tema describe cómo escribir una tabla en línea\-con valores de función para seleccionar filas para migrar.

>   [AZURE.NOTE] Si desea proporcionar una función de filtro que realiza mal, migración de datos realiza mal. Ajuste de la base de datos aplica la función de filtro a la tabla mediante el operador CROSS APPLY.

Si no especifica una función de filtro, se migra toda la tabla.

Cuando se ejecuta la base de datos de habilitar para estirar asistente, puede migrar una tabla completa o puede especificar una función de filtro simple en el asistente. Si desea utilizar otro tipo de función de filtro para seleccionar filas para migrar, siga uno de los siguientes procedimientos.

-   Salir del asistente y ejecute la instrucción ALTER TABLE para habilitar estirar para la tabla y especificar una función de filtro.

-   Ejecute la instrucción ALTER TABLE para especificar una función de filtro después de salir del asistente.

La sintaxis de ALTER TABLE para agregar una función se describe más adelante en este tema.

## <a name="basic-requirements-for-the-filter-function"></a>Requisitos básicos para la función de filtro
La tabla en línea\-función con valores necesario para un predicado de filtro de base de datos de estirar es similar en el siguiente ejemplo.

```tsql
CREATE FUNCTION dbo.fn_stretchpredicate(@column1 datatype1, @column2 datatype2 [, ...n])
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN  SELECT 1 AS is_eligible
        WHERE <predicate>
```
Los parámetros de la función tienen que ser identificadores para las columnas de la tabla.

Enlace de esquema es necesaria para evitar que las columnas que usan la función filter se eliminan o modificado.

### <a name="return-value"></a>Valor devuelto
Si la función devuelve una no\-resultado vacío, la fila es elegible para migrar. En caso contrario, \- , es decir, si la función no devuelve un resultado \- la fila no es elegible para migrar.

### <a name="conditions"></a>Condiciones
La &lt; *predicado* &gt; pueden constar de una condición, o de varias condiciones combinadas con operador lógico AND.

```
<predicate> ::= <condition> [ AND <condition> ] [ ...n ]
```
Cada condición por separado puede constar de una condición simple o de varias condiciones simples unidas con el operador lógico OR.

```
<condition> ::= <primitive_condition> [ OR <primitive_condition> ] [ ...n ]
```

### <a name="primitive-conditions"></a>Condiciones simples
Una condición simple puede realizar una de las siguientes comparaciones.

```
<primitive_condition> ::=
{
<function_parameter> <comparison_operator> constant
| <function_parameter> { IS NULL | IS NOT NULL }
| <function_parameter> IN ( constant [ ,...n ] )
}
```

-   Comparar un parámetro de la función de una expresión constante. Por ejemplo, `@column1 < 1000`.

    Aquí tenemos un ejemplo que comprueba si el valor de una columna de *fecha* es &lt; 1/1/2016.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate(@column1 datetime)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
    GO

    ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
        FILTER_PREDICATE = dbo.fn_stretchpredicate(date),
        MIGRATION_STATE = OUTBOUND
    ) )
    ```

-   Aplicar el operador IS NULL o no es NULL a un parámetro de la función.

-   Usar el operador IN para comparar un parámetro de la función a una lista de valores constantes.

    Aquí tenemos un ejemplo que comprueba si el valor de un *envío\_estado* columna es `IN (N'Completed', N'Returned', N'Cancelled')`.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate(@column1 nvarchar(15))
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 IN (N'Completed', N'Returned', N'Cancelled')
    GO

    ALTER TABLE table1 SET ( REMOTE_DATA_ARCHIVE = ON (
        FILTER_PREDICATE = dbo.fn_stretchpredicate(shipment_status),
        MIGRATION_STATE = OUTBOUND
    ) )
    ```

### <a name="comparison-operators"></a>Operadores de comparación
Se admiten los siguientes operadores de comparación.

`<, <=, >, >=, =, <>, !=, !<, !>`

```
<comparison_operator> ::= { < | <= | > | >= | = | <> | != | !< | !> }
```

### <a name="constant-expressions"></a>Expresiones constantes
Las constantes que usar en una función de filtro pueden ser cualquier expresión determinista que puede evaluarse al definir la función. Expresiones constantes pueden contener lo siguiente.

-   Literales. Por ejemplo, `N’abc’, 123`.

-   Expresiones de álgebra. Por ejemplo, `123 + 456`.

-   Funciones deterministas. Por ejemplo, `SQRT(900)`.

-   Conversiones deterministas que utilizan conversión o convertir. Por ejemplo, `CONVERT(datetime, '1/1/2016', 101)`.

### <a name="other-expressions"></a>Otras expresiones
Puede usar la BETWEEN y no los operadores entre si la función resultante se ajusta a las reglas que se describen aquí después de reemplazar la BETWEEN y no está entre los operadores con las expresiones equivalente y y o.

No puede usar subconsultas o no\-funciones deterministas como RAND o GETDATE().

## <a name="add-a-filter-function-to-a-table"></a>Agregar una función de filtro a una tabla
Agregar una función de filtro a una tabla, ejecute la instrucción **ALTER TABLE** y especifique una tabla en línea existente\-con valores de función como el valor de la **filtro\_predicado** parámetro. Por ejemplo:

```tsql
ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
    FILTER_PREDICATE = dbo.fn_stretchpredicate(column1, column2),
    MIGRATION_STATE = <desired_migration_state>
) )
```
Después de la función se enlaza a la tabla como predicado, lo siguiente es verdadero.

-   Se produce la migración de datos de tiempo siguiente, solo las filas para que la función devuelve una no\-se migran valor vacío.

-   Las columnas de la función son enlazada de esquema. No se puede modificar estas columnas como una tabla esté usando la función como su predicado de filtro.

No se puede quitar la tabla en línea\-con valores de función como una tabla esté usando la función como su predicado de filtro.

>   [AZURE.NOTE] Para mejorar el rendimiento de la función filter, crear un índice en las columnas de la función.

### <a name="passing-column-names-to-the-filter-function"></a>Nombres de columna de pasar a la función de filtro
Cuando se asigna una función de filtro a una tabla, especifique los nombres de columna se pasan a la función de filtro con un nombre de una parte. Si especifica un nombre de tres partes cuando se pasa la columna nombres, las siguientes consultas en el estiramiento\-tabla habilitada se producirá un error.

Por ejemplo, si se especifica un nombre de columna de tres partes, tal como se muestra en el ejemplo siguiente, la instrucción se ejecutará correctamente, pero se producirá un error en las siguientes consultas en la tabla.

```tsql
ALTER TABLE SensorTelemetry
  SET ( REMOTE_DATA_ARCHIVE = ON (
    FILTER_PREDICATE=dbo.fn_stretchpredicate(dbo.SensorTelemetry.ScanDate),
    MIGRATION_STATE = OUTBOUND )
  )
```

En su lugar, especifique la función de filtro con un nombre de columna de una parte tal como se muestra en el ejemplo siguiente.

```tsql
ALTER TABLE SensorTelemetry
  SET ( REMOTE_DATA_ARCHIVE = ON  (
    FILTER_PREDICATE=dbo.fn_stretchpredicate(ScanDate),
    MIGRATION_STATE = OUTBOUND )
  )
```

## <a name="addafterwiz"></a>Agregar una función de filtro después de ejecutar el Asistente  

Si desea usar una función que se puede crear en el Asistente para **Habilitar la base de datos para expandir** , puede ejecutar la instrucción ALTER TABLE para especificar una función después de salir del asistente. Antes de aplicar una función, sin embargo, debe detener la migración de datos que ya está en curso y ponerlas datos migrados. (Para obtener más información sobre por qué es necesario, consulte [reemplazar una función de filtro existente](#replacePredicate).  

1. Invertir la dirección de migración y recuperar que los datos ya se migran. No se puede cancelar esta operación después de que se inicia. También incurre en costos en Azure para las transferencias de datos de salida \(salida\). Para obtener más información, consulte [cómo Azure precios funciona](https://azure.microsoft.com/pricing/details/data-transfers/).  

    ```tsql  
    ALTER TABLE <table name>  
         SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = INBOUND ) ) ;   
    ```  

2. Espere a que la migración Finalizar. Puede comprobar el estado de **Estirar Monitor de base de datos** de SQL Server Management Studio, o puede consultar la vista **sys.dm_db_rda_migration_status** . Para obtener más información, vea [Monitor y solucionar problemas de migración de datos](sql-server-stretch-database-monitor.md) o [sys.dm_db_rda_migration_status](https://msdn.microsoft.com/library/dn935017.aspx).  

3. Cree la función de filtro que desea aplicar a la tabla.  

4. Agregar la función a la tabla y reinicie la migración de datos de Azure.  

    ```tsql  
    ALTER TABLE <table name>  
        SET ( REMOTE_DATA_ARCHIVE  
            (           
                FILTER_PREDICATE = <predicate>,  
                MIGRATION_STATE = OUTBOUND  
            )  
        );   
    ```  

## <a name="filter-rows-by-date"></a>Filtrar filas por fecha
En el ejemplo siguiente se migra filas donde la columna **fecha** contiene un valor anteriores al 1 de enero de 2016.

```tsql
-- Filter by date
--
CREATE FUNCTION dbo.fn_stretch_by_date(@date datetime2)
RETURNS TABLE
WITH SCHEMABINDING
AS
       RETURN SELECT 1 AS is_eligible WHERE @date < CONVERT(datetime2, '1/1/2016', 101)
GO
```

## <a name="filter-rows-by-the-value-in-a-status-column"></a>Filtrar filas por el valor de una columna de estado
En el ejemplo siguiente se migra filas donde la columna de **estado** contiene uno de los valores especificados.

```tsql
-- Filter by status column
--
CREATE FUNCTION dbo.fn_stretch_by_status(@status nvarchar(128))
RETURNS TABLE
WITH SCHEMABINDING
AS
       RETURN SELECT 1 AS is_eligible WHERE @status IN (N'Completed', N'Returned', N'Cancelled')
GO
```

## <a name="filter-rows-by-using-a-sliding-window"></a>Filtrar filas mediante una ventana deslizante
Para filtrar filas mediante una ventana deslizante, tenga en cuenta los siguientes requisitos para la función de filtro.

-   La función tiene que ser determinista. Por lo tanto, no puede crear una función que actualiza automáticamente la ventana deslizante transcurso del tiempo.

-   La función usa el enlace de esquema. Por lo tanto, no puede simplemente actualizar la función "en lugar de" todos los días llamando **ALTER FUNCTION** para mover la ventana deslizante.

Comience con una función de filtro como el ejemplo siguiente, que migra filas donde la columna **systemEndTime** contiene un valor anteriores al 1 de enero de 2016.

```tsql
CREATE FUNCTION dbo.fn_StretchBySystemEndTime20160101(@systemEndTime datetime2)
RETURNS TABLE
WITH SCHEMABINDING  
AS  
RETURN SELECT 1 AS is_eligible
  WHERE @systemEndTime < CONVERT(datetime2, '2016-01-01T00:00:00', 101) ;
```

La función filter se aplican a la tabla.

```tsql
ALTER TABLE <table name>
SET (
        REMOTE_DATA_ARCHIVE = ON
                (
                        FILTER_PREDICATE = dbo.fn_StretchBySystemEndTime20160101 (SysEndTime)
                                , MIGRATION_STATE = OUTBOUND
                )
        )
;
```

Si desea actualizar la ventana deslizante, realice lo siguiente.

1.  Crear una nueva función que especifica la nueva ventana deslizante. En el ejemplo siguiente se selecciona las fechas anteriores al 2 de enero de 2016, en lugar del 1 de enero de 2016.

2.  Reemplazar la función filter anterior con la nueva llamada **ALTER TABLE**, tal como se muestra en el ejemplo siguiente.

3. Si lo desea, quite la función filter anterior que ya no está usando llamada **DROP FUNCTION**. (Este paso no se muestra en el ejemplo).

```tsql
BEGIN TRAN
GO
        /*(1) Create new predicate function definition */
        CREATE FUNCTION dbo.fn_StretchBySystemEndTime20160102(@systemEndTime datetime2)
        RETURNS TABLE
        WITH SCHEMABINDING
        AS
        RETURN SELECT 1 AS is_eligible
               WHERE @systemEndTime < CONVERT(datetime2,'2016-01-02T00:00:00', 101)
        GO

        /*(2) Set the new function as the filter predicate */
        ALTER TABLE <table name>
        SET
        (
               REMOTE_DATA_ARCHIVE = ON
               (
                       FILTER_PREDICATE = dbo.fn_StretchBySystemEndTime20160102(SysEndTime),
                       MIGRATION_STATE = OUTBOUND
               )
        )
COMMIT ;
```

## <a name="more-examples-of-valid-filter-functions"></a>Más ejemplos de funciones de filtro válido

-   En el ejemplo siguiente se combinan dos condiciones simples mediante el operador lógico y.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate((@column1 datetime, @column2 nvarchar(15))
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
      WHERE @column1 < N'20150101' AND @column2 IN (N'Completed', N'Returned', N'Cancelled')
    GO

    ALTER TABLE table1 SET ( REMOTE_DATA_ARCHIVE = ON (
        FILTER_PREDICATE = dbo.fn_stretchpredicate(date, shipment_status),
        MIGRATION_STATE = OUTBOUND
    ) )
    ```

-   En el ejemplo siguiente se utiliza una conversión determinista y varias condiciones con convertir.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate_example1(@column1 datetime, @column2 int, @column3 nvarchar)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '1/1/2015', 101)AND (@column2 < -100 OR @column2 > 100 OR @column2 IS NULL)AND @column3 IN (N'Completed', N'Returned', N'Cancelled')
    GO
    ```

-   El ejemplo siguiente utiliza los operadores matemáticos y funciones.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate_example2(@column1 float)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 < SQRT(400) + 10
    GO
    ```

-   En el ejemplo siguiente se utiliza la BETWEEN y no los operadores entre. Este uso es válido porque la función resultante se ajusta a las reglas que se describen aquí después de reemplazar la BETWEEN y no está entre los operadores con las expresiones equivalente y y o.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate_example3(@column1 int, @column2 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 BETWEEN 0 AND 100
                AND (@column2 NOT BETWEEN 200 AND 300 OR @column1 = 50)
    GO
    ```
    La función anterior es equivalente a la siguiente función después de reemplazar la BETWEEN y no los operadores entre con las expresiones equivalente y y o.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate_example4(@column1 int, @column2 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 >= 0 AND @column1 <= 100AND (@column2 < 200 OR @column2 > 300 OR @column1 = 50)
    GO
    ```

## <a name="examples-of-filter-functions-that-arent-valid"></a>Ejemplos de funciones de filtro que no son válidos

-   La siguiente función no es válida porque contiene un no\-conversión determinista.

    ```tsql
    CREATE FUNCTION dbo.fn_example5(@column1 datetime)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 < CONVERT(datetime, '1/1/2016')
    GO
    ```

-   La siguiente función no es válida porque contiene un no\-llamada de función determinista.

    ```tsql
    CREATE FUNCTION dbo.fn_example6(@column1 datetime)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 < DATEADD(day, -60, GETDATE())
    GO
    ```

-   La siguiente función no es válida porque contiene una subconsulta.

    ```tsql
    CREATE FUNCTION dbo.fn_example7(@column1 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 IN (SELECT SupplierID FROM Supplier WHERE Status = 'Defunct'))
    GO
    ```

-   Las siguientes funciones no son válidas porque las expresiones que utilizan operadores álgebra o integrado\-en funciones se debe evaluar como una constante al definir la función. No puede incluir referencias de columna en expresiones álgebra o llamadas de función.

    ```tsql
    CREATE FUNCTION dbo.fn_example8(@column1 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 % 2 =  0
    GO

    CREATE FUNCTION dbo.fn_example9(@column1 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE SQRT(@column1) = 30
    GO
    ```

-   La siguiente función no es válida porque infringe las reglas que se describen aquí después de reemplazar el operador entre con la expresión equivalente de y.

    ```tsql
    CREATE FUNCTION dbo.fn_example10(@column1 int, @column2 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE (@column1 BETWEEN 1 AND 200 OR @column1 = 300) AND @column2 > 1000
    GO
    ```
    La función anterior es equivalente a la siguiente función después de reemplazar el operador entre con la expresión equivalente de y. Esta función no es válida porque condiciones simples solo pueden usar el operador lógico OR.

    ```tsql
    CREATE FUNCTION dbo.fn_example11(@column1 int, @column2 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE (@column1 >= 1 AND @column1 <= 200 OR @column1 = 300) AND @column2 > 1000
    GO
    ```

## <a name="how-stretch-database-applies-the-filter-function"></a>¿Cómo expandir base de datos se aplica a la función de filtro
Ajuste de la base de datos aplica la función de filtro a la tabla y determina elegibles filas mediante el operador CROSS APPLY. Por ejemplo:

```tsql
SELECT * FROM stretch_table_name CROSS APPLY fn_stretchpredicate(column1, column2)
```
Si la función devuelve una no\-resultado vacío de la fila, la fila es elegible para migrar.

## <a name="replacePredicate"></a>Reemplazar una función de filtro existente
Puede reemplazar una función de filtro especificada anteriormente, ejecute la instrucción **ALTER TABLE** nuevo y especifique un nuevo valor para el **filtro\_predicado** parámetro. Por ejemplo:

```tsql
ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
    FILTER_PREDICATE = dbo.fn_stretchpredicate2(column1, column2),
    MIGRATION_STATE = <desired_migration_state>
```
La nueva tabla en línea\-función con valores tiene los siguientes requisitos.

-   La nueva función tiene que ser menores que la función anterior.

-   Todos los operadores que existieron en la antigua función deben existir en la nueva función.

-   La nueva función no puede contener operadores que no existen en la función antigua.

-   No puede cambiar el orden de los argumentos de operador.

-   Solo los valores constantes que forman parte de un `<, <=, >, >=` comparación puede cambiarse de manera que hace que la función menos restrictivo.

### <a name="example-of-a-valid-replacement"></a>Ejemplo de reemplazo válido
Supongamos que la siguiente función es el predicado de filtro actual.

```tsql
CREATE FUNCTION dbo.fn_stretchpredicate_old (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN  SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
            AND (@column2 < -100 OR @column2 > 100)
GO
```
La siguiente función es un reemplazo válido porque la constante nueva fecha (que especifica una fecha de corte más adelante) hace que la función menos restrictiva.

```tsql
CREATE FUNCTION dbo.fn_stretchpredicate_new (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN  SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '2/1/2016', 101)
            AND (@column2 < -50 OR @column2 > 50)
GO
```

### <a name="examples-of-replacements-that-arent-valid"></a>Ejemplos de sustituciones que no son válidos
La siguiente función no reemplaza válida porque la constante nueva fecha (que especifica una fecha límite anterior) no hace que la función menos restrictiva.

```tsql
CREATE FUNCTION dbo.fn_notvalidreplacement_1 (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN  SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '1/1/2015', 101)
            AND (@column2 < -100 OR @column2 > 100)
GO
```
La siguiente función no reemplaza válida porque se ha quitado uno de los operadores de comparación.

```tsql
CREATE FUNCTION dbo.fn_notvalidreplacement_2 (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN  SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
            AND (@column2 < -50)
GO
```
La siguiente función no reemplaza válida porque se ha agregado una nueva condición con operador lógico AND.

```tsql
CREATE FUNCTION dbo.fn_notvalidreplacement_3 (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN  SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
            AND (@column2 < -100 OR @column2 > 100)
            AND (@column2 <> 0)
GO
```

## <a name="remove-a-filter-function-from-a-table"></a>Quitar una función de filtro de una tabla
Para migrar toda la tabla en lugar de las filas seleccionadas, quite la función existente estableciendo **filtro\_predicado** null. Por ejemplo:

```tsql
ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
    FILTER_PREDICATE = NULL,
    MIGRATION_STATE = <desired_migration_state>
) )
```
Después de quitar la función filter, todas las filas de la tabla son elegibles para la migración. Por tanto, no puede especificar una función de filtro para la misma tabla más adelante a menos que ponerlas todos los datos remotos para la tabla de Azure en primer lugar. Esta restricción existe para evitar la situación donde filas que no son elegibles para la migración al proporcionar una nueva función de filtro ya se han migrado a Azure.

## <a name="check-the-filter-function-applied-to-a-table"></a>Compruebe la función de filtro aplicada a una tabla
Para comprobar la función de filtro aplicada a una tabla, abra la vista de catálogo **sys.remote\_datos\_archivo\_tablas** y compruebe el valor de la **filtro\_predicado** columna. Si el valor es null, toda la tabla es elegible para archivado. Para obtener más información, vea [sys.remote_data_archive_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx).

## <a name="security-notes-for-filter-functions"></a>Notas de seguridad para funciones de filtro  
Una cuenta con privilegios db_owner comprometida puede hacer lo siguiente.  

-   Crear y aplicar una función con valores de tabla que consume gran cantidad de recursos de servidor o que espera un período de una denegación de servicio.  

-   Crear y aplicar una función con valores de tabla que hace que sea posible deducir el contenido de una tabla para que el usuario tiene denegado explícitamente acceso de lectura.  

## <a name="see-also"></a>Vea también

[Modificar tabla (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)
