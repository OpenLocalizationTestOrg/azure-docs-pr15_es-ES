<properties
    pageTitle="OLTP en memoria mejora el rendimiento SQL txn | Microsoft Azure"
    description="En la memoria de adoptar OLTP para mejorar el rendimiento de transacciones en una base de datos SQL existente."
    services="sql-database"
    documentationCenter=""
    authors="jodebrui"
    manager="jhubbard"
    editor="MightyPen"/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="jodebrui"/>


# <a name="use-in-memory-oltp-preview-to-improve-your-application-performance-in-sql-database"></a>Uso de memoria OLTP (preview) para mejorar el rendimiento de la aplicación de base de datos de SQL

[En la memoria OLTP](sql-database-in-memory.md) puede usarse para mejorar el rendimiento de carga de trabajo OLTP en bases de datos de SQL de [Premium](sql-database-service-tiers.md) Azure sin aumentar el nivel de rendimiento.

Siga estos pasos para adoptar en memoria OLTP en la base de datos existente.

## <a name="step-1-ensure-your-premium-database-supports-in-memory-oltp"></a>Paso 1: Asegúrese de que la base de datos Premium admite OLTP en memoria

Bases de datos de Premium creadas en noviembre de 2015 o posterior son compatibles con la característica de memoria. Puede determinar si la base de datos Premium admite la característica en memoria, ejecute la siguiente instrucción Transact-SQL. Es compatible en memoria si el resultado devuelto es 1 (no 0):

```
SELECT DatabasePropertyEx(Db_Name(), 'IsXTPSupported');
```

*XTP* significa *Extremas procesamiento de transacciones*

Si es necesario mover la base de datos existente a una nueva base de datos de V12 Premium, puede usar las técnicas siguientes para exportar e importar los datos.

#### <a name="export-steps"></a>Pasos de exportación

Exportar la base de datos de producción a una mochila usando:

- La funcionalidad [Exportar](sql-database-export.md) en el [portal](https://portal.azure.com/).

- La funcionalidad de la **aplicación de exportación de nivel de datos** en un [SSMS.exe actualizada](http://msdn.microsoft.com/library/mt238290.aspx) (SQL Server Management Studio).
 1. En el **Explorador de objetos**, expanda el nodo de **bases de datos** .
 2. Haga clic en el nodo de base de datos.
 3. Haga clic en **tareas** > **exportar la aplicación de nivel de datos**.
 4. Trabajar con la ventana del asistente que se muestra.


#### <a name="import-steps"></a>Pasos de importación

Importar la mochila en una nueva base de datos de Premium.

1. En el [portal](https://portal.azure.com/)de Azure,
 - Desplácese hasta el servidor.
 - Seleccione la opción de [Base de datos de importación](sql-database-import.md) .
 - Seleccione un nivel de precios de Premium.

2. Utilice SSMS para importar la mochila:
 - En el **Explorador de objetos**, haga clic en el nodo de **bases de datos** .
 - Haga clic en **aplicación del nivel de datos de importación**.
 - Trabajar con la ventana del asistente que se muestra.


## <a name="step-2-identify-objects-to-migrate-to-in-memory-oltp"></a>Paso 2: Identificar los objetos para migrar a OLTP en memoria

SSMS incluye un informe de **Información general de análisis de rendimiento de transacción** que se puede ejecutar en una base de datos con una carga de trabajo activo. El informe identifica tablas y procedimientos almacenados que son candidatos para la migración a OLTP en memoria.

En SSMS, para generar el informe:
- En el **Explorador de objetos**, haga clic en el nodo de base de datos.
- Haga clic en **informes** > **informes estándar** > **información general de análisis de rendimiento de transacción**.

Para obtener más información, vea [determinar si una tabla o almacenado procedimiento debe migrar a OLTP en memoria](http://msdn.microsoft.com/library/dn205133.aspx).


## <a name="step-3-create-a-comparable-test-database"></a>Paso 3: Crear una base de datos de prueba comparables

Supongamos que el informe indica que la base de datos tiene una tabla que podría beneficiarse de convertirlo en una tabla de memoria optimizada. Le recomendamos que pruebe primero para confirmar la indicación probando.

Necesita una copia de prueba de la base de datos de producción. La base de datos de prueba debe estar en el mismo nivel de nivel de servicio que la base de datos de producción.

Para reducir la prueba, ajustar la base de datos de prueba como sigue:

1. Conectarse a la base de datos de prueba con SSMS.

2. Para evitar la necesidad de la opción con (instantánea) en las consultas, establezca la opción de base de datos, como se muestra en la siguiente instrucción SQL T:
```
ALTER DATABASE CURRENT
    SET
        MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT = ON;
```


## <a name="step-4-migrate-tables"></a>Paso 4: Migrar tablas

Debe crear y rellenar una copia de memoria optimizada de la tabla que desea probar. Puede crear usando:

- El Asistente de optimización de memoria útil en SSMS.
- Manual T-SQL.


#### <a name="memory-optimization-wizard-in-ssms"></a>Asistente para la optimización de memoria de SSMS

Para usar esta opción de migración:

1. Conectarse a la base de datos de prueba con SSMS.

2. En el **Explorador de objetos**, haga clic en la tabla y, a continuación, haga clic en **Asesor de optimización de memoria**.
 - Se abre el Asistente de **Asesor de optimizador de memoria de tabla** .

3. En el asistente, haga clic en **validación de migración** (o en el botón **siguiente** ) para ver si la tabla tiene alguna característica no admitida que no es compatibles en las tablas de memoria optimizada. Para obtener más información, consulte:
 - La *lista de comprobación de optimización de memoria* en [Asesor de optimización de memoria](http://msdn.microsoft.com/library/dn284308.aspx).
 - [Transact-SQL construcciones no compatibles con OLTP en memoria](http://msdn.microsoft.com/library/dn246937.aspx).
 - [Migrar a OLTP en memoria](http://msdn.microsoft.com/library/dn247639.aspx).

4. Si la tabla no tiene características no compatibles, el Asesor puede realizar la migración de datos y esquema real.


#### <a name="manual-t-sql"></a>Manual de T-SQL

Para usar esta opción de migración:

1. Conectarse a la base de datos de prueba con SSMS (o una utilidad similar).

2. Obtener la secuencia de comandos T SQL completa de la tabla y sus índices.
 - En SSMS, haga clic en el nodo de tabla.
 - Haga clic en **tabla de secuencia de comandos como** > **crear para** > **nueva ventana de consulta**.

3. En la ventana de la secuencia de comandos, agregue WITH (MEMORY_OPTIMIZED = ON) a la instrucción CREATE TABLE.

4. Si hay un índice agrupado, cambiar NONCLUSTERED.

5. Cambiar el nombre de la tabla existente mediante SP_RENAME.

6. Crear la nueva copia de la tabla optimizar memoria ejecutando el script de crear tabla modificada.

7. Copiar los datos a la tabla optimizar memoria mediante Insertar... SELECCIONE * EN:
    
```
INSERT INTO <new_memory_optimized_table>
        SELECT * FROM <old_disk_based_table>;
```


## <a name="step-5-optional-migrate-stored-procedures"></a>Paso 5 (opcional): migrar procedimientos almacenados

La característica de memoria también puede modificar un procedimiento almacenado para mejorar el rendimiento.


### <a name="considerations-with-natively-compiled-stored-procedures"></a>Consideraciones de forma nativa compiladas procedimientos almacenados

Un procedimiento almacenado de forma nativa compilado debe tener en su cláusula SQL T con las siguientes opciones:

- NATIVE_COMPILATION

- SCHEMABINDING: lo que significa que las tablas que el procedimiento almacenado no puede tener sus definiciones de columna cambia de alguna forma que afecte al procedimiento almacenado, a menos que se quita el procedimiento almacenado.


Un módulo nativo debe utilizar una gran [bloques ATÓMICOS](http://msdn.microsoft.com/library/dn452281.aspx) para la administración. No hay ningún papel para una transacción explícita comenzar o para deshacer la transacción. Si el código detecta una infracción de una regla de negocio, pueda terminar el bloque con una instrucción [producir](http://msdn.microsoft.com/library/ee677615.aspx) atómico.


### <a name="typical-create-procedure-for-natively-compiled"></a>CREATE PROCEDURE típico para compilados de forma nativa

La T-SQL para crear un procedimiento almacenado de forma nativa compilado suele ser similar a la siguiente plantilla:

```
CREATE PROCEDURE schemaname.procedurename
    @param1 type1, …
    WITH NATIVE_COMPILATION, SCHEMABINDING
    AS
        BEGIN ATOMIC WITH
            (TRANSACTION ISOLATION LEVEL = SNAPSHOT,
            LANGUAGE = N'your_language__see_sys.languages'
            )
        …
        END;
```

- Para el TRANSACTION_ISOLATION_LEVEL instantánea es el valor más común para el procedimiento almacenado de forma nativa. Sin embargo, también se admiten un subconjunto de los otros valores:
 - LECTURA REPRODUCIBLE
 - SERIALIZABLE


- El valor de idioma debe estar presente en la vista sys.languages.


### <a name="how-to-migrate-a-stored-procedure"></a>Cómo migrar un procedimiento almacenado

Los pasos de migración son:


1. Obtener la secuencia de comandos CREATE PROCEDURE procedimiento almacenado interpretar normal.

2. Vuelva a escribir su encabezado para que coincida con la plantilla anterior.

3. Determinar si el código SQL de T de procedimiento almacenado utiliza todas las características que no son compatibles para procedimientos almacenados compilados de forma nativa. Implementar soluciones si es necesario.
 - Para obtener más información, vea [Problemas de migración de forma nativa compilada procedimientos almacenados](http://msdn.microsoft.com/library/dn296678.aspx).

4. Cambiar el nombre del procedimiento almacenado antiguo mediante SP_RENAME. O simplemente COLOCARLO.

5. Ejecute la secuencia de comandos de procedimiento crear T-SQL editado.


## <a name="step-6-run-your-workload-in-test"></a>Paso 6: Ejecutar la carga de trabajo de prueba

Ejecutar una carga de trabajo en la base de datos de prueba es similar a la carga de trabajo que se ejecuta en la base de datos de producción. Esto debe revelar la ganancia de rendimiento que se logra mediante el uso de la característica en memoria de tablas y procedimientos almacenados.

Atributos principales de la carga de trabajo son:

- Número de conexiones simultáneas.

- Relación de lectura y escritura.


Para personalizar y ejecutar la carga de trabajo de prueba, considere la posibilidad de mediante la herramienta ostress.exe útiles, que se muestra en [aquí](sql-database-in-memory.md).


Para reducir la latencia de red, ejecute la prueba en la misma región geográfica Azure donde se encuentra la base de datos.


## <a name="step-7-post-implementation-monitoring"></a>Paso 7: Supervisión de postimplementación

Considere la supervisión de los efectos de rendimiento de las implementaciones en memoria en producción:

- [Almacenamiento en memoria de monitor](sql-database-in-memory-oltp-monitoring.md).

- [Supervisión con vistas dinámicas de administración de base de datos SQL Azure](sql-database-monitoring-with-dmvs.md)


## <a name="related-links"></a>Vínculos relacionados

- [OLTP (optimización en memoria) en la memoria](http://msdn.microsoft.com/library/dn133186.aspx)

- [Introducción a los procedimientos almacenados compilados de forma nativa](http://msdn.microsoft.com/library/dn133184.aspx)

- [Asesor de optimización de memoria](http://msdn.microsoft.com/library/dn284308.aspx)

