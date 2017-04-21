<properties
   pageTitle="Transacciones en el almacén de datos SQL | Microsoft Azure"
   description="Sugerencias para implementar transacciones en el almacén de datos de SQL Azure para desarrollar soluciones."
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
   ms.date="07/31/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="transactions-in-sql-data-warehouse"></a>Transacciones en el almacén de datos SQL

Como cabría esperar, el almacén de datos SQL admite transacciones como parte de la carga de trabajo del almacén de datos. Sin embargo, para asegurarse de que se mantiene el rendimiento del almacén de datos SQL en escala algunas características están limitadas en comparación con SQL Server. En este artículo se resalta las diferencias y listas de los demás. 

## <a name="transaction-isolation-levels"></a>Niveles de aislamiento
Almacén de datos SQL implementa transacciones ACID. Sin embargo, el aislamiento de la compatibilidad de transacciones se limita a `READ UNCOMMITTED` y esto no se puede cambiar. Puede implementar un número de codificación de métodos para evitar dirty lecturas de datos si se trata de un problema para usted. Los métodos más populares aprovechen CTAS y cambio de partición de tabla (a menudo conocido como el modelo de la ventana deslizante) para impedir que los usuarios consultar datos que aún se está preparando. Las vistas que previamente filtran los datos también es un enfoque más popular.  

## <a name="transaction-size"></a>Tamaño de la transacción
Una transacción de modificación de datos tiene un tamaño limitada. El límite se aplica hoy "por distribución". Por lo tanto, se puede calcular la asignación total, multiplique el límite por el recuento de distribución. Para aproximada el número máximo de filas de la transacción divida el capital de distribución el tamaño total de cada fila. Para las columnas de longitud variable puede tomar una longitud media de columna en lugar de con el tamaño máximo.

Se han realizado en la tabla siguiente lo siguiente:

* Se ha producido una distribución uniforme de datos 
* La longitud de fila promedio es de 250 bytes

| [DWU][]    | Tapa por distribución (GiB) | Número de distribuciones | Tamaño máximo de transacción (GiB) | # Filas por distribución | Número máximo de filas por transacción |
| ------ | -------------------------- | ----------------------- | -------------------------- | ----------------------- | ------------------------ |
| DW100  |  1                         | 60                      |   60                       |   4,000,000             |    240,000,000           |
| DW200  |  1,5                       | 60                      |   90                       |   6,000,000             |    360,000,000           |
| DW300  |  2,25                      | 60                      |  135                       |   9,000,000             |    540,000,000           |
| DW400  |  3                         | 60                      |  180                       |  12,000,000             |    720,000,000           |
| DW500  |  3,75                      | 60                      |  225                       |  15,000,000             |    900,000,000           |
| DW600  |  4.5                       | 60                      |  270                       |  18,000,000             |  1,080,000,000           |
| DW1000 |  7.5                       | 60                      |  450                       |  30.000.000             |  1.800.000.000           |
| DW1200 |  9                         | 60                      |  540                       |  36,000,000             |  2,160,000,000           |
| DW1500 | 11,25                      | 60                      |  675                       |  45,000,000             |  2,700,000,000           |
| DW2000 | 15                         | 60                      |  900                       |  60.000.000             |  3,600,000,000           |
| DW3000 | 22.5                       | 60                      |  1.350                     |  90,000,000             |  5,400,000,000           |
| DW6000 | 45                         | 60                      |  2.700                     | 180,000,000             | 10,800,000,000           |

Se aplica el límite de tamaño de la transacción por transacción o la operación. No se aplica en todas las transacciones simultáneas. Por lo tanto, cada transacción puede escribir esta cantidad de datos en el registro. 

Optimizar y reducir la cantidad de datos que se escriben en el registro, consulte el artículo [transacciones procedimientos recomendados][] .

> [AZURE.WARNING] El tamaño máximo de la transacción solo puede conseguirse para HASH o incluso de tablas ROUND_ROBIN distribuido donde es la distribución de los datos. Si la transacción está escribiendo datos en forma sesgada a las distribuciones el límite es probable que se alcanzó antes el tamaño máximo de la transacción.
<!--REPLICATED_TABLE-->

## <a name="transaction-state"></a>Estado de la transacción
Almacén de datos SQL usa la función XACT_STATE() para informar de una transacción de error al usar el valor -2. Esto significa que la transacción ha fallado y está marcada para deshacer sólo

> [AZURE.NOTE] El uso de -2 por la función XACT_STATE para indicar una error transacción representa un comportamiento diferente a SQL Server. SQL Server usa el valor -1 para representar una transacción confirmable anular. SQL Server puede tolerar algunos errores dentro de una transacción sin que tenga que desea marcar como no confirmable. Por ejemplo `SELECT 1/0` podría producir un error pero no forzar una transacción en un estado no confirmable. SQL Server también permite lecturas de la transacción confirmable anular. Sin embargo, almacén de datos SQL no le permiten hacer esto. Si se produce un error dentro de una transacción de almacén de datos SQL introduce automáticamente el estado de-2 y no podrá realizar cualquiera Seleccionar instrucciones hasta que se ha deshecho la declaración. Por lo tanto, es importante comprobar que el código de la aplicación para ver si usa XACT_STATE() como necesiten hacer modificaciones en el código.

Por ejemplo, en SQL Server, es posible que vea una transacción que es similar a esta:

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;
        
        IF @@TRANCOUNT > 0
        BEGIN
            PRINT 'ROLLBACK';
            ROLLBACK TRAN;
        END

    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

Si deja el código tal como se encuentra encima obtendrá el siguiente mensaje de error:

Msj 111233, nivel 16, estado 1, línea 1 111233; Se ha cancelado la transacción actual y, a continuación, se han deshecho los cambios pendientes. Causa: Deshace una transacción en un estado de deshacer no se explícitamente antes de un DDL, DML o instrucción SELECT.

También no obtendrá el resultado de las funciones ERROR_ *.

En el almacén de datos de SQL debe ser modificado ligeramente el código:

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();
        
        IF @@TRANCOUNT > 0
        BEGIN
            PRINT 'ROLLBACK';
            ROLLBACK TRAN;
        END

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;
    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

Ahora se observa el comportamiento esperado. Se administra el error en la transacción y las funciones ERROR_ * proporcionan valores según lo esperado.

Todo lo que ha cambiado es que el `ROLLBACK` de la transacción tenía que suceda antes de la lectura de la información de error en la `CATCH` bloque.

## <a name="errorline-function"></a>Función Error_Line()
También es importante recordar que almacén de datos SQL no implementa o compatible con la función ERROR_LINE(). Si tiene esto en el código que se debe quitar para que sea compatible con el almacén de datos de SQL. Utilizar etiquetas de consulta en el código para implementar funcionalidad equivalente. Consulte el artículo de la [etiqueta][] para obtener más detalles acerca de esta característica.

## <a name="using-throw-and-raiserror"></a>Usar produzca y RAISERROR
INICIA la implementación más moderna para iniciar excepciones en el almacén de datos de SQL pero RAISERROR también es compatible. Existen algunas diferencias que vale la pena que preste atención a no obstante.

- Mensajes de error números no pueden estar en el rango de 100,000 150.000 produzca definidas por el usuario
- Mensajes de error RAISERROR se corrigen en 50.000
- No se admite el uso de sys.messages

## <a name="limitiations"></a>Limitiations
Almacén de datos de SQL tiene algunas otras restricciones relacionadas con transacciones.

Son las siguientes:

- Sin transacciones distribuidas
- Sin transacciones anidadas permitidas
- No guardar puntos permitido
- No hay transacciones con nombre
- Sin transacciones marcadas
- No es compatible con DDL como `CREATE TABLE` dentro de un usuario transacción definida

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información sobre la optimización de transacciones, consulte [prácticas recomendadas de transacciones][].  Para obtener información sobre otros procedimientos recomendados de almacén de datos de SQL, consulte [prácticas recomendadas de almacén de datos de SQL][].

<!--Image references-->

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[development overview]: ./sql-data-warehouse-overview-develop.md
[Prácticas recomendadas de transacciones]: ./sql-data-warehouse-develop-best-practices-transactions.md
[Prácticas recomendadas de almacén de datos SQL]: ./sql-data-warehouse-best-practices.md
[ETIQUETA]: ./sql-data-warehouse-develop-label.md

<!--MSDN references-->

<!--Other Web references-->
