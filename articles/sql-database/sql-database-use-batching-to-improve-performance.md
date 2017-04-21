 <properties
    pageTitle="Cómo usar el procesamiento por lotes para mejorar el rendimiento de la aplicación de base de datos de SQL Azure"
    description="El tema proporciona una prueba que operaciones de base de datos por lotes en gran medida imroves la velocidad y escalabilidad de las aplicaciones de base de datos de SQL Azure. Aunque estas técnicas por lotes funcionan para cualquier base de datos de SQL Server, el foco del artículo está en Azure."
    services="sql-database"
    documentationCenter="na"
    authors="annemill"
    manager="jhubbard"
    editor="" />


<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-management"
    ms.date="07/12/2016"
    ms.author="annemill" />

# <a name="how-to-use-batching-to-improve-sql-database-application-performance"></a>Cómo usar el procesamiento por lotes para mejorar el rendimiento de la aplicación de base de datos SQL

Procesamiento por lotes de las operaciones de base de datos de SQL Azure significativamente mejora el rendimiento y escalabilidad de las aplicaciones. Para conocer las ventajas, la primera parte de este artículo trata sobre los resultados de ejemplo que comparan solicitudes secuenciales y por lotes a una base de datos de SQL. El resto de este artículo muestra las técnicas, escenarios y consideraciones para ayudarle a usar lotes correctamente en las aplicaciones de Azure.

**Autores**: empresa Jason, Silvano Coriani, Trent Swanson (escala completa 180 Inc.)

**Revisores**: Conor Cunningham, Michael Thomassy

## <a name="why-is-batching-important-for-sql-database"></a>¿Por qué es procesar por lotes importantes de base de datos SQL?
Procesamiento por lotes llamadas a un servicio remoto es una estrategia conocida para aumentar el nivel de rendimiento y escalabilidad. Allí se corrigen los costos de procesamiento de las interacciones con un servicio remoto, como serialización, transferencia de red y deserialización. Empaquetar cuántas transacciones independientes en un único lote minimiza estos costos.

En este artículo, desea examinar varias bases de datos de SQL por lotes estrategias y escenarios. Aunque estas estrategias también son importantes para las aplicaciones locales que utilizan SQL Server, hay varios motivos para resaltar el uso de procesar por lotes de base de datos SQL:

- Hay potencialmente mayor latencia de la red de acceso a la base de datos de SQL, especialmente si tiene acceso a base de datos SQL fuera del mismo centro de datos de Microsoft Azure.
- Las características de base de datos SQL multiempresa significa que la eficacia de la corresponde de nivel de acceso de datos con la escalabilidad general de la base de datos. Base de datos SQL debe impedir que cualquier usuario o inquilino único monopolicen los recursos de la base de datos en detrimento de otros inquilinos. En respuesta a uso superiores a las cuotas de predefinido, base de datos SQL puede reducir el rendimiento o responder con limitación excepciones. El rendimiento, como por lotes, le permite trabajar más en la base de datos SQL antes de llegar a estos límites. 
- Procesamiento por lotes también entra en vigor para arquitecturas que usan varias bases de datos (sharding). La eficacia de la interacción con cada unidad de base de datos sigue siendo un factor clave en su escalabilidad general. 

Una de las ventajas de usar la base de datos SQL es que no tiene que administrar los servidores que alojan la base de datos. Sin embargo, esta infraestructura administrada también significa que tiene que pensar optimizaciones de base de datos diferente. Ya no puede buscar para mejorar la infraestructura de red o de hardware de la base de datos. Microsoft Azure controla los entornos. El área principal que puede controlar es cómo interactúa la aplicación con la base de datos de SQL. Procesamiento por lotes es una de estas optimizaciones. 

La primera parte del papel examina varias técnicas por lotes para aplicaciones .NET que utilizan la base de datos de SQL. Las últimas dos secciones cubren por lotes instrucciones y escenarios.

## <a name="batching-strategies"></a>Estrategias por lotes

### <a name="note-about-timing-results-in-this-topic"></a>Tenga en cuenta sobre los resultados de intervalos en este tema
>[AZURE.NOTE] Resultados no son comparativas pero están diseñados para mostrar el **rendimiento relativa**. Intervalos se basan en un promedio de pruebas al menos 10. Las operaciones son inserciones en una tabla vacía. Estas pruebas ha sido pre-V12 medido, y no necesariamente corresponden a rendimiento que surjan en una base de datos de V12 con los [niveles de servicio](sql-database-service-tiers.md)de nuevo. La ventaja relativa de la técnica por lotes debería ser similar.

### <a name="transactions"></a>Transacciones
Parece extraño para comenzar una revisión por lotes analizando las transacciones. Pero el uso de las transacciones del cliente tiene un efecto de servidor por lotes sutil que mejora el rendimiento. Y se pueden agregar transacciones con solo unas pocas líneas de código, por lo que proporcionan una manera rápida para mejorar el rendimiento de operaciones secuenciales.

Considere el siguiente código de C# que contiene una secuencia de insertar y actualizar las operaciones en una tabla simple.

    List<string> dbOperations = new List<string>();
    dbOperations.Add("update MyTable set mytext = 'updated text' where id = 1");
    dbOperations.Add("update MyTable set mytext = 'updated text' where id = 2");
    dbOperations.Add("update MyTable set mytext = 'updated text' where id = 3");
    dbOperations.Add("insert MyTable values ('new value',1)");
    dbOperations.Add("insert MyTable values ('new value',2)");
    dbOperations.Add("insert MyTable values ('new value',3)");

El siguiente código ADO.NET secuencialmente realiza estas operaciones.

    using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
    {
        conn.Open();
    
        foreach(string commandString in dbOperations)
        {
            SqlCommand cmd = new SqlCommand(commandString, conn);
            cmd.ExecuteNonQuery();                   
        }
    }

La mejor manera de optimizar este código es implementar algún tipo de cliente por lotes de estas llamadas. Pero hay una forma sencilla para aumentar el rendimiento de este código simplemente ajustando la secuencia de llamadas en una transacción. Aquí es el mismo código que usa una transacción.

    using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
    {
        conn.Open();
        SqlTransaction transaction = conn.BeginTransaction();
    
        foreach (string commandString in dbOperations)
        {
            SqlCommand cmd = new SqlCommand(commandString, conn, transaction);
            cmd.ExecuteNonQuery();
        }
    
        transaction.Commit();
    }

Transacciones realmente se utilizan en ambos ejemplos. En el primer ejemplo, cada llamada individual es una transacción implícita. En el segundo ejemplo, una transacción explícita se ajusta a todas las llamadas. Por la documentación de la [escritura anticipada registro de transacciones](https://msdn.microsoft.com/library/ms186259.aspx), los registros se vacían en el disco cuando se confirma la transacción. Por lo tanto, incluyendo más llamadas en una transacción, la escritura en el registro de transacciones puede retrasar hasta que se confirma la transacción. De hecho, está habilitando procesar por lotes de la escritura en el registro de transacciones del servidor.

La siguiente tabla muestra algunos resultados de pruebas ad hoc. Las pruebas realizan las inserciones secuenciales mismas con y sin transacciones. Para obtener más perspectiva, el primer conjunto de pruebas ejecutó remotamente desde un portátil a la base de datos de Microsoft Azure. El segundo conjunto de pruebas ejecutó desde una base de datos que se encontraba en el mismo centro de datos de Microsoft Azure (oeste Estados Unidos) y el servicio de nube. La siguiente tabla muestra la duración en milisegundos de inserciones secuenciales con y sin transacciones.

**Local a Azure**:

| Operaciones | No hay ninguna transacción (ms) | Transacción (ms) |
|---|---|---|
| 1 | 130 | 402 |
| 10 | 1208 | 1226 |
| 100 | 12662 | 10395 |
| 1000 | 128852 | 102917 |


**Azure a Azure (mismo centro de datos)**:

| Operaciones | No hay ninguna transacción (ms) | Transacción (ms) |
|---|---|---|
| 1 | 21 | 26 |
| 10 | 220 | 56 |
| 100 | número 2145 | 341 |
| 1000 | 21479 | 2756 |

>[AZURE.NOTE] Los resultados no son pruebas de referencia. Vea la [Nota sobre los resultados de intervalos en este tema](#note-about-timing-results-in-this-topic).

En función de los resultados de la prueba anterior, realmente ajuste una única operación en una transacción disminuye el rendimiento. Pero a medida que aumente el número de operaciones en una sola transacción, la mejora del rendimiento más esté marcada. La diferencia de rendimiento también es más perceptible cuando todas las operaciones que se producen en el centro de datos de Microsoft Azure. La mayor latencia de uso de base de datos SQL fuera del centro de datos de Microsoft Azure oculta la mejora del rendimiento de uso de las transacciones.

Aunque el uso de transacciones puede mejorar el rendimiento, seguirán [observar prácticas recomendadas para las transacciones y las conexiones](https://msdn.microsoft.com/library/ms187484.aspx). Mantener la mayor brevedad posible transacción y a continuación, cierre la conexión de base de datos después de que finalice el trabajo. La instrucción using en el ejemplo anterior garantiza que se cierra la conexión cuando finaliza el bloque de código de las siguientes.

En el ejemplo anterior se muestra que puede agregar una transacción local a cualquier código ADO.NET con dos líneas. Las transacciones ofrecen una forma rápida de mejorar el rendimiento de código que permite insertar secuencial, actualizar y eliminar operaciones. Sin embargo, para mejorar el rendimiento, considere la posibilidad de cambiar el código más para aprovechar las ventajas de por lotes del cliente, como los parámetros con valores de tabla.

Para obtener más información sobre las transacciones de ADO.NET, vea [Las transacciones locales en ADO.NET](https://msdn.microsoft.com/library/vstudio/2k2hy99x.aspx).

### <a name="table-valued-parameters"></a>Parámetros con valores de tabla
Parámetros con valores de tabla admiten tipos de tabla definidos por el usuario como parámetros en las instrucciones Transact-SQL, procedimientos almacenados y funciones. Esta técnica por lotes de cliente le permite enviar varias filas de datos dentro de los parámetros con valores de tabla. Para usar parámetros con valores de tabla, en primer lugar defina un tipo de tabla. La siguiente instrucción Transact-SQL crea un tipo de tabla denominado **MyTableType**.

    CREATE TYPE MyTableType AS TABLE 
    ( mytext TEXT,
      num INT );
 

En el código, se crea una **tabla de datos** con los mismos nombres exactos y tipos del tipo de tabla. Pasar esta **tabla de datos** de un parámetro en una consulta de texto o llamada a procedimiento almacenado. En el ejemplo siguiente se muestra esta técnica:

    using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
    {
        connection.Open();
    
        DataTable table = new DataTable();
        // Add columns and rows. The following is a simple example.
        table.Columns.Add("mytext", typeof(string));
        table.Columns.Add("num", typeof(int));    
        for (var i = 0; i < 10; i++)
        {
            table.Rows.Add(DateTime.Now.ToString(), DateTime.Now.Millisecond);
        }
    
        SqlCommand cmd = new SqlCommand(
            "INSERT INTO MyTable(mytext, num) SELECT mytext, num FROM @TestTvp",
            connection);
                    
        cmd.Parameters.Add(
            new SqlParameter()
            {
                ParameterName = "@TestTvp",
                SqlDbType = SqlDbType.Structured,
                TypeName = "MyTableType",
                Value = table,
            });
    
        cmd.ExecuteNonQuery();
    }

En el ejemplo anterior, el objeto **SqlCommand** inserta filas de un parámetro con valores de tabla, **@TestTvp**. El objeto de **tabla de datos** creado anteriormente se asigna a este parámetro con el método **SqlCommand.Parameters.Add** . Lotes de forma significativa las inserciones en una llamada aumentan el rendimiento sobre inserciones secuenciales.

Para mejorar aún más el ejemplo anterior, utilice un procedimiento almacenado en lugar de un comando de texto. El siguiente comando Transact-SQL crea un procedimiento almacenado que toma el parámetro de **SimpleTestTableType** con valores de tabla.

    CREATE PROCEDURE [dbo].[sp_InsertRows] 
    @TestTvp as MyTableType READONLY
    AS
    BEGIN
    INSERT INTO MyTable(mytext, num) 
    SELECT mytext, num FROM @TestTvp
    END
    GO

A continuación, cambie la declaración del objeto **SqlCommand** en el ejemplo anterior a la siguiente.

    SqlCommand cmd = new SqlCommand("sp_InsertRows", connection);
    cmd.CommandType = CommandType.StoredProcedure;

En la mayoría de los casos, parámetros con valores de tabla tienen equivalente y mejor rendimiento que otras técnicas por lotes. Parámetros con valores de tabla suelen ser preferibles, ya que son más flexibles que otras opciones. Por ejemplo, otras técnicas, como copia masiva SQL, solo permiten la inserción de nuevas filas. Pero con parámetros con valores de tabla, puede usar lógica en el procedimiento almacenado para determinar qué filas son actualizaciones y que están inserta. El tipo de tabla también puede modificarse para que contenga una columna de "Operación" que indica si la fila especificada debe ser insertada, actualizada o eliminada.

La siguiente tabla muestra los resultados de la prueba de ad hoc para el uso de parámetros con valores de tabla en milisegundos.

| Operaciones | Local a Azure (ms)  | Azure mismo el centro de datos (ms) |
|---|---|---|
| 1 | 124 | 32 |
| 10 | 131 | 25 |
| 100 | 338 | 51 |
| 1000 | 2615 | 382 |
| 10000 | 23830 | 3586 |

>[AZURE.NOTE] Los resultados no son pruebas de referencia. Vea la [Nota sobre los resultados de intervalos en este tema](#note-about-timing-results-in-this-topic).

La mejora del rendimiento de procesamiento por lotes es inmediatamente evidente. En la prueba anterior secuencial, operaciones de 1000 tardó 129 segundos fuera del centro de datos y 21 segundos desde dentro del centro de datos. Pero con parámetros con valores de tabla, operaciones de 1000 solo 2.6 segundos fuera del centro de datos y 0,4 segundos en el centro de datos.

Para obtener más información acerca de los parámetros con valores de tabla, vea [Parámetros con valores de tabla](https://msdn.microsoft.com/library/bb510489.aspx).

### <a name="sql-bulk-copy"></a>Copia masiva SQL
Copia masiva SQL es otra forma de insertar grandes cantidades de datos en una base de datos de destino. Aplicaciones .NET pueden usar la clase **SqlBulkCopy** para realizar operaciones de inserción masiva. **SqlBulkCopy** función es similar a la herramienta de línea de comandos, **Bcp.exe**o la instrucción de Transact-SQL, **INSERCIÓN masiva**. En el ejemplo siguiente se muestra cómo copiar de forma masiva las filas en la **tabla de datos**, tabla, tabla de destino en SQL Server, MyTable de origen.

    using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
    {
        connection.Open();
    
        using (SqlBulkCopy bulkCopy = new SqlBulkCopy(connection))
        {
            bulkCopy.DestinationTableName = "MyTable";
            bulkCopy.ColumnMappings.Add("mytext", "mytext");
            bulkCopy.ColumnMappings.Add("num", "num");
            bulkCopy.WriteToServer(table);
        }
    }

Hay algunos casos donde se prefiere copia masiva sobre parámetros con valores de tabla. Consulte la tabla de comparación de parámetros con valores de tabla frente a las operaciones de INSERCIÓN masiva en el tema [Parámetros con valores de tabla](https://msdn.microsoft.com/library/bb510489.aspx).

Los resultados de pruebas de ad-hoc siguientes muestran el rendimiento de procesamiento por lotes con **SqlBulkCopy** en milisegundos.

| Operaciones | Local a Azure (ms)  | Azure mismo el centro de datos (ms) |
|---|---|---|
| 1 | 433 | 57 |
| 10 | 441 | 32 |
| 100 | 636 | 53 |
| 1000 | 2535 | 341 |
| 10000 | 21605 | 2737 |

>[AZURE.NOTE] Los resultados no son pruebas de referencia. Vea la [Nota sobre los resultados de intervalos en este tema](#note-about-timing-results-in-this-topic).

En lotes más pequeños, los parámetros con valores de tabla de uso superó a la clase **SqlBulkCopy** . Sin embargo, **SqlBulkCopy** realizadas 12-31% más rápido que los parámetros con valores de tabla en las pruebas de 1.000 y 10.000 filas. Como parámetros con valores de tabla, **SqlBulkCopy** es una buena opción para inserciones por lotes, especialmente cuando se comparan con el rendimiento de operaciones sin lotes.

Para obtener más información sobre la copia masiva en ADO.NET, vea [Operaciones de copia masiva en SQL Server](https://msdn.microsoft.com/library/7ek5da1a.aspx).

### <a name="multiple-row-parameterized-insert-statements"></a>Instrucciones de parametrizadas insertar varias filas
Es una alternativa para pequeños lotes construir una instrucción INSERT parametrizada grande que inserta varias filas. En el ejemplo siguiente se muestra esta técnica.

    using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
    {
        connection.Open();
    
        string insertCommand = "INSERT INTO [MyTable] ( mytext, num ) " +
            "VALUES (@p1, @p2), (@p3, @p4), (@p5, @p6), (@p7, @p8), (@p9, @p10)";
    
        SqlCommand cmd = new SqlCommand(insertCommand, connection);
    
        for (int i = 1; i <= 10; i += 2)
        {
            cmd.Parameters.Add(new SqlParameter("@p" + i.ToString(), "test"));
            cmd.Parameters.Add(new SqlParameter("@p" + (i+1).ToString(), i));
        }
    
        cmd.ExecuteNonQuery();
    }
 

En este ejemplo se usa para mostrar el concepto básico. Un escenario más realista ¿recorrer las entidades necesarias para construir la cadena de consulta y los parámetros de comando simultáneamente. Está limitado a un total 2100 de parámetros de consulta, lo que se limita el número total de filas que pueden procesarse de esta manera.

Los resultados de pruebas de ad-hoc siguientes muestran el rendimiento de este tipo de instrucción insert en milisegundos.

| Operaciones | Parámetros con valores de tabla (ms) | Única instrucción INSERT (ms) |
|---|---|---|
| 1 | 32 | 20 |
| 10 | 30 | 25 |
| 100 | 33 | 51 |

>[AZURE.NOTE] Los resultados no son pruebas de referencia. Vea la [Nota sobre los resultados de intervalos en este tema](#note-about-timing-results-in-this-topic).

Este enfoque puede ser un poco más rápido para lotes de menos de 100 filas. Aunque la mejora es pequeña, esta técnica es otra opción que podría funcionar bien en su situación de aplicación específica.

### <a name="dataadapter"></a>DataAdapter
La clase **DataAdapter** le permite modificar un objeto de **conjunto de datos** y, a continuación, envíe los cambios como insertar, actualizar y eliminar operaciones. Si está utilizando **DataAdapter** de esta manera, es importante que tenga en cuenta que se realizan llamadas independientes para cada operación distinta. Para mejorar el rendimiento, use la propiedad **UpdateBatchSize** al número de operaciones que deben procesarse en un momento. Para obtener más información, vea [Realizar operaciones de lote usando DataAdapters](https://msdn.microsoft.com/library/aadf8fk2.aspx).

### <a name="entity-framework"></a>Marco de la entidad
Marco de entidad no se admite actualmente por lotes. Distintos desarrolladores de la Comunidad han intentado demostrar alternativas, como invalidar el método **SaveChanges** . Pero las soluciones normalmente son complejos y personalizada a la aplicación y el modelo de datos. El proyecto de codeplex entidad Framework tiene una página de discusión en esta solicitud de característica. Para ver este tema, consulte [Notas de la reunión de diseño: 2 de agosto de 2012](http://entityframework.codeplex.com/wikipage?title=Design%20Meeting%20Notes%20-%20August%202%2c%202012).

### <a name="xml"></a>XML
Conclusión, creemos que es importante hablar sobre XML como una estrategia de procesamiento por lotes. Sin embargo, el uso de XML tiene sin ventajas sobre otros métodos y desventajas. El enfoque es similar a parámetros con valores de tabla, pero un archivo XML o una cadena que se pasa a un procedimiento almacenado en lugar de una tabla definida por el usuario. El procedimiento almacenado analiza los comandos en el procedimiento almacenado.

Hay varias desventajas de este enfoque:

- Trabajar con XML puede ser pesado llevar a errores.
- Analizar los datos XML en la base de datos puede consumir CPU.
- En la mayoría de los casos, este método es más lento que parámetros con valores de tabla.

Para ello, no se recomienda el uso de XML en consultas por lotes.

## <a name="batching-considerations"></a>Consideraciones por lotes
Las siguientes secciones proporcionan más instrucciones para el uso de procesamiento por lotes en aplicaciones de base de datos de SQL.

### <a name="tradeoffs"></a>Compensaciones
Función de la arquitectura, por lotes pueden implicar un equilibrio entre el rendimiento y resistencia. Por ejemplo, considere el escenario donde su rol inesperadamente deja de funcionar. Si pierde una fila de datos, el impacto es menor que el impacto de perder un lote de gran tamaño de filas no enviados. Hay un mayor riesgo cuando búfer filas antes de enviarlo a la base de datos en una ventana de tiempo especificado.

Debido a este equilibrio, evaluar el tipo de las operaciones de dicho lote. Lote más agresiva (lotes más grandes y ventanas de tiempo más largo) con los datos que es menos críticos.

### <a name="batch-size"></a>Tamaño del lote
En nuestras pruebas, normalmente no era ninguna ventaja dividir lotes grandes en partes más pequeñas. De hecho, esta subdivisión a menudo da como resultado un rendimiento menor que enviar un único lote grande. Por ejemplo, considere la posibilidad de un escenario donde desea insertar filas de 1000. La siguiente tabla muestra cuánto tiempo tarda para usar parámetros con valores de tabla para insertar filas de 1000 cuando se divide en lotes más pequeños.

| Tamaño del lote | Iteraciones | Parámetros con valores de tabla (ms) |
| -------- | --- | --- |
| 1000 | 1 | 347 |
| 500 | 2 | 355 |
| 100 | 10 | 465 |
| 50 | 20 | 630 |

>[AZURE.NOTE] Los resultados no son pruebas de referencia. Vea la [Nota sobre los resultados de intervalos en este tema](#note-about-timing-results-in-this-topic).

Puede ver que es el mejor rendimiento de 1000 filas enviarlos a la vez. En otras pruebas (que no se muestra aquí) se ha producido un aumento del rendimiento pequeña para dividir un lote de 10000 fila en dos lotes de 5000. Pero el esquema de la tabla para estas pruebas es relativamente sencillo, por lo que debe realizar pruebas sobre los datos específicos y los tamaños de lote para comprobar estos descubrimientos.

Otro factor a considerar es que si el total del lote se hace demasiado grande, base de datos SQL podría limitar y rechazar el lote de confirmación. Para obtener los mejores resultados, pruebe su situación específica para determinar si hay un tamaño de lote ideal. Hacer que el tamaño del lote configurable en tiempo de ejecución para habilitar ajustes rápidos según el rendimiento o errores.

Por último, saldo el tamaño del lote y los riesgos asociados con el procesamiento por lotes. Si hay errores transitorios o se produce un error en la función, tenga en cuenta las consecuencias de volver a intentar la operación o de perder los datos en el lote.

### <a name="parallel-processing"></a>Procesamiento en paralelo
¿Qué sucede si realizó el enfoque de reducir el tamaño del lote pero utiliza varios subprocesos para ejecutar el trabajo? De nuevo, nuestras pruebas mostraron que varias secciones más pequeñas multiproceso normalmente realizan peor que un único lote más grande. La siguiente prueba intenta insertar filas de 1000 en una o varias secciones paralelas. Esta prueba muestra cómo más lotes simultáneos realmente disminución del rendimiento.

| Tamaño del lote [iteraciones] | Dos subprocesos (ms) | Cuatro subprocesos (ms) | Seis subprocesos (ms) |
| -------- | --- | --- | --- |
| 1000 [1] | 277 | 315 | 266 |
| 500 [2] | 548 | 278 | 256 |
| 250 [4] | 405 | 329 | 265 |
| 100 [10] | 488 | 439 | 391 |

>[AZURE.NOTE] Los resultados no son pruebas de referencia. Vea la [Nota sobre los resultados de intervalos en este tema](#note-about-timing-results-in-this-topic).

Existen varios motivos posibles para la degradación del rendimiento debido a paralelismo:

- Existen varias llamadas simultáneas de red en lugar de uno.
- Varias operaciones en una sola tabla pueden traducirse en conflicto y bloqueo.
- Existen gastos asociados con multithreading.
- El gasto de abrir varias conexiones supera el beneficio de procesamiento en paralelo.

Si elige diferentes tablas o bases de datos, es posible ver algunas rendimiento obtener con esta estrategia. Sharding de base de datos o federaciones sería un escenario para este enfoque. Sharding usa varias bases de datos y envía los datos diferentes para cada base de datos. Si va a una base de datos diferente cada lote pequeño, pueden ser más eficaz operaciones en paralelo. Sin embargo, no es lo suficientemente significativa para usar como base para tomar una decisión para utilizar sharding de base de datos en la solución la mejora del rendimiento.

En algunos diseños, ejecución en paralelo de lotes más pequeños puede dar lugar a mejorar el rendimiento de las solicitudes en un sistema de carga. En este caso, aunque es más rápido procesar un único lote más grande, procesamiento de varios lotes en paralelo puede resultar más eficaz.

Si utiliza la ejecución en paralelo, considere la posibilidad de controlar el número máximo de subprocesos de trabajo. Un número menor puede ocasionar menos conflictos y un tiempo de ejecución más rápido. Además, tenga en cuenta la carga adicional que se coloca en la base de datos de destino en las conexiones y las transacciones.

### <a name="related-performance-factors"></a>Factores de rendimiento relacionados
Orientación típico en el rendimiento de la base de datos también afecta a por lotes. Por ejemplo, insertar el rendimiento se reduce para tablas que tienen una clave principal grande o muchos índices no agrupados.

Si los parámetros con valores de tabla, usa un procedimiento almacenado, puede usar el comando **Activar NOCOUNT** al principio del procedimiento. Esta declaración suprime la devolución del recuento de las filas afectadas del procedimiento. Sin embargo, en nuestras pruebas, el uso de **Activar NOCOUNT** no tenía ningún efecto o disminución del rendimiento. El procedimiento almacenado era sencillo con una sola **Insertar** comando del parámetro con valores de tabla. Es posible que más complejos procedimientos almacenados podrían beneficiarse de esta declaración. Pero no se supone que agrega **SET NOCOUNT ON** al procedimiento almacenado automáticamente mejora el rendimiento. Para conocer el efecto, pruebe el procedimiento almacenado con y sin la instrucción **SET NOCOUNT ON** .

## <a name="batching-scenarios"></a>Escenarios por lotes
Las secciones siguientes describen cómo usar parámetros con valores de tabla en tres escenarios de aplicación. El primer escenario muestra cómo pueden funcionar juntos búfer y por lotes. El segundo escenario mejora el rendimiento al realizar operaciones de maestro y detalles en una llamada a procedimiento almacenado único. El último escenario muestra cómo usar parámetros con valores de tabla en una operación de "UPSERT".

### <a name="buffering"></a>El búfer
Aunque hay algunas situaciones que son candidatos obvios por lotes, hay muchos escenarios que podrían aprovechar las ventajas de procesamiento por lotes de procesamiento de retraso. Sin embargo, procesamiento retrasada también incluye un mayor riesgo de que los datos se pierden si se produce un error inesperado. Es importante comprender el riesgo y tener en cuenta las consecuencias.

Por ejemplo, considere la posibilidad de una aplicación web que registra el historial de exploración de cada usuario. En cada solicitud de página, la aplicación puede crear una base de datos a la vista del usuario página de registro de llamadas. Pero mayor rendimiento y escalabilidad se consigue búfer actividades de navegación de los usuarios y, a continuación, enviar los datos a la base de datos en lotes. Puede activar la actualización de la base de datos por el tiempo transcurrido o el tamaño del búfer. Por ejemplo, una regla puede especificar que el lote debe procesarse después de 20 segundos o cuando el búfer alcance 1000 elementos.

En el ejemplo siguiente se usa [Extensiones reactiva - recepción](https://msdn.microsoft.com/data/gg577609) para procesar búfer eventos generados por una clase supervisión. Cuando rellena el búfer o un alcanza el tiempo de espera, se envía el lote de datos de usuario a la base de datos con un parámetro con valores de tabla.

La siguiente clase NavHistoryData modelos los detalles de navegación del usuario. Contiene información básica como el identificador de usuario, la dirección URL de acceso y el tiempo de acceso.

    public class NavHistoryData
    {
        public NavHistoryData(int userId, string url, DateTime accessTime)
        { UserId = userId; URL = url; AccessTime = accessTime; }
        public int UserId { get; set; }
        public string URL { get; set; }
        public DateTime AccessTime { get; set; }
    }

La clase NavHistoryDataMonitor es responsable de búfer los datos de navegación de usuario a la base de datos. Contiene un método, RecordUserNavigationEntry, que responde provocando un evento **OnAdded** . El código siguiente muestra la lógica de constructor que utiliza recepción para crear una colección visible basada en el evento. A continuación, se suscribe a esta colección visible con el método de búfer. La sobrecarga especifica que se debe enviar el búfer de cada 20 segundos o las entradas de 1000.

    public NavHistoryDataMonitor()
    {
        var observableData =
            Observable.FromEventPattern<NavHistoryDataEventArgs>(this, "OnAdded");
    
        observableData.Buffer(TimeSpan.FromSeconds(20), 1000).Subscribe(Handler);           
    }

El controlador convierte todos los elementos de búfer en un tipo de valores de tabla y, a continuación, pasa a un procedimiento almacenado que procesa el lote de este tipo. El código siguiente muestra la definición completa de la NavHistoryDataEventArgs y las clases NavHistoryDataMonitor.

    public class NavHistoryDataEventArgs : System.EventArgs
    {
        public NavHistoryDataEventArgs(NavHistoryData data) { Data = data; }
        public NavHistoryData Data { get; set; }
    }
    
    public class NavHistoryDataMonitor
    {
        public event EventHandler<NavHistoryDataEventArgs> OnAdded;
    
        public NavHistoryDataMonitor()
        {
            var observableData =
                Observable.FromEventPattern<NavHistoryDataEventArgs>(this, "OnAdded");
    
            observableData.Buffer(TimeSpan.FromSeconds(20), 1000).Subscribe(Handler);           
        }
    
        public void RecordUserNavigationEntry(NavHistoryData data)
        {    
            if (OnAdded != null)
                OnAdded(this, new NavHistoryDataEventArgs(data));
        }
    
        protected void Handler(IList<EventPattern<NavHistoryDataEventArgs>> items)
        {
            DataTable navHistoryBatch = new DataTable("NavigationHistoryBatch");
            navHistoryBatch.Columns.Add("UserId", typeof(int));
            navHistoryBatch.Columns.Add("URL", typeof(string));
            navHistoryBatch.Columns.Add("AccessTime", typeof(DateTime));
            foreach (EventPattern<NavHistoryDataEventArgs> item in items)
            {
                NavHistoryData data = item.EventArgs.Data;
                navHistoryBatch.Rows.Add(data.UserId, data.URL, data.AccessTime);
            }
    
            using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
            {
                connection.Open();
    
                SqlCommand cmd = new SqlCommand("sp_RecordUserNavigation", connection);
                cmd.CommandType = CommandType.StoredProcedure;
    
                cmd.Parameters.Add(
                    new SqlParameter()
                    {
                        ParameterName = "@NavHistoryBatch",
                        SqlDbType = SqlDbType.Structured,
                        TypeName = "NavigationHistoryTableType",
                        Value = navHistoryBatch,
                    });
    
                cmd.ExecuteNonQuery();
            }
        }
    }

Para usar esta clase búfer, la aplicación crea un objeto NavHistoryDataMonitor estático. Cada vez que un usuario tiene acceso a una página, la aplicación llama al método de NavHistoryDataMonitor.RecordUserNavigationEntry. La lógica de búfer continúa ocuparse de enviar estas entradas a la base de datos en lotes.

### <a name="master-detail"></a>Detalle principal
Parámetros con valores de tabla son útiles para escenarios sencillos de insertar. Sin embargo, puede ser más difícil de inserciones lote que implican más de una tabla. El escenario de "maestro y detalles" es un buen ejemplo. La tabla principal identifica la entidad principal. Una o más tablas de detalle almacenan más datos acerca de la entidad. En este escenario, relaciones de clave externa exigir la relación de detalles de una entidad de patrón única. Considere la posibilidad de una versión simplificada de una tabla de PurchaseOrder y su tabla OrderDetail asociada. Transact-SQL crea la tabla PurchaseOrder con cuatro columnas: IdPedido, FechaPedido, CustomerID y estado.

    CREATE TABLE [dbo].[PurchaseOrder](
    [OrderID] [int] IDENTITY(1,1) NOT NULL,
    [OrderDate] [datetime] NOT NULL,
    [CustomerID] [int] NOT NULL,
    [Status] [nvarchar](50) NOT NULL,
     CONSTRAINT [PrimaryKey_PurchaseOrder] 
    PRIMARY KEY CLUSTERED ( [OrderID] ASC ))

Cada pedido contiene uno o más compras de productos. Esta información se genera en la tabla PurchaseOrderDetail. Transact-SQL crea la tabla PurchaseOrderDetail con cinco columnas: IdPedido, OrderDetailID, IdProducto, PrecioUnidad y OrderQty.

    CREATE TABLE [dbo].[PurchaseOrderDetail](
    [OrderID] [int] NOT NULL,
    [OrderDetailID] [int] IDENTITY(1,1) NOT NULL,
    [ProductID] [int] NOT NULL,
    [UnitPrice] [money] NULL,
    [OrderQty] [smallint] NULL,
     CONSTRAINT [PrimaryKey_PurchaseOrderDetail] PRIMARY KEY CLUSTERED 
    ( [OrderID] ASC, [OrderDetailID] ASC ))

La columna IdPedido de la tabla PurchaseOrderDetail debe hacer referencia a un pedido de la tabla de PurchaseOrder. La siguiente definición de una clave externa exige esta limitación.

    ALTER TABLE [dbo].[PurchaseOrderDetail]  WITH CHECK ADD 
    CONSTRAINT [FK_OrderID_PurchaseOrder] FOREIGN KEY([OrderID])
    REFERENCES [dbo].[PurchaseOrder] ([OrderID])

Para usar parámetros con valores de tabla, debe tener un tipo de tabla definida por el usuario para cada tabla de destino.

    CREATE TYPE PurchaseOrderTableType AS TABLE 
    ( OrderID INT,
      OrderDate DATETIME,
      CustomerID INT,
      Status NVARCHAR(50) );
    GO
    
    CREATE TYPE PurchaseOrderDetailTableType AS TABLE 
    ( OrderID INT,
      ProductID INT,
      UnitPrice MONEY,
      OrderQty SMALLINT );
    GO

A continuación, defina un procedimiento almacenado que acepta tablas de estos tipos. Este procedimiento permite una aplicación localmente por lotes un conjunto de pedidos y detalles de pedido en una sola llamada. Transact-SQL proporciona la declaración de procedimiento almacenado completo en este ejemplo de orden de compra.

    CREATE PROCEDURE sp_InsertOrdersBatch (
    @orders as PurchaseOrderTableType READONLY,
    @details as PurchaseOrderDetailTableType READONLY )
    AS
    SET NOCOUNT ON;
    
    -- Table that connects the order identifiers in the @orders
    -- table with the actual order identifiers in the PurchaseOrder table
    DECLARE @IdentityLink AS TABLE ( 
    SubmittedKey int, 
    ActualKey int, 
    RowNumber int identity(1,1)
    );
     
          -- Add new orders to the PurchaseOrder table, storing the actual
    -- order identifiers in the @IdentityLink table   
    INSERT INTO PurchaseOrder ([OrderDate], [CustomerID], [Status])
    OUTPUT inserted.OrderID INTO @IdentityLink (ActualKey)
    SELECT [OrderDate], [CustomerID], [Status] FROM @orders ORDER BY OrderID;
    
    -- Match the passed-in order identifiers with the actual identifiers
    -- and complete the @IdentityLink table for use with inserting the details
    WITH OrderedRows As (
    SELECT OrderID, ROW_NUMBER () OVER (ORDER BY OrderID) As RowNumber 
    FROM @orders
    )
    UPDATE @IdentityLink SET SubmittedKey = M.OrderID
    FROM @IdentityLink L JOIN OrderedRows M ON L.RowNumber = M.RowNumber;
    
    -- Insert the order details into the PurchaseOrderDetail table, 
          -- using the actual order identifiers of the master table, PurchaseOrder
    INSERT INTO PurchaseOrderDetail (
    [OrderID],
    [ProductID],
    [UnitPrice],
    [OrderQty] )
    SELECT L.ActualKey, D.ProductID, D.UnitPrice, D.OrderQty
    FROM @details D
    JOIN @IdentityLink L ON L.SubmittedKey = D.OrderID;
    GO

En este ejemplo, definida localmente @IdentityLink tabla almacena los valores de IdPedido reales de las filas recién insertados. Estos identificadores de orden son diferentes de los valores de IdPedido temporales de la @orders y @details parámetros con valores de tabla. Por este motivo, el @IdentityLink tabla luego conecta a los valores de IdPedido de la @orders parámetro los valores reales de IdPedido para las nuevas filas en la tabla de PurchaseOrder. Después de este paso, el @IdentityLink tabla puede facilitar la inserción de los detalles del pedido con los encabezados de IdPedido real que satisface la restricción de clave externa.

Este procedimiento almacenado puede utilizarse desde el código o desde otras llamadas Transact-SQL. Consulte la sección de parámetros con valores de tabla de este artículo para obtener un ejemplo de código. Transact-SQL siguiente muestra cómo llamar a la sp_InsertOrdersBatch.

    declare @orders as PurchaseOrderTableType
    declare @details as PurchaseOrderDetailTableType
    
    INSERT @orders 
    ([OrderID], [OrderDate], [CustomerID], [Status])
    VALUES(1, '1/1/2013', 1125, 'Complete'),
    (2, '1/13/2013', 348, 'Processing'),
    (3, '1/12/2013', 2504, 'Shipped')
    
    INSERT @details
    ([OrderID], [ProductID], [UnitPrice], [OrderQty])
    VALUES(1, 10, $11.50, 1),
    (1, 12, $1.58, 1),
    (2, 23, $2.57, 2),
    (3, 4, $10.00, 1)
    
    exec sp_InsertOrdersBatch @orders, @details

Esta solución permite cada lote usar un conjunto de valores de IdPedido que comienzan en 1. Estos valores IdPedido temporales describen las relaciones en el lote, pero se determinan los valores reales de IdPedido en el momento de la operación de inserción. Puede ejecutar repetidamente las mismas instrucciones en el ejemplo anterior y generar pedidos únicos en la base de datos. Por este motivo, considere la posibilidad de agregar lógica de código o base de datos más que impide pedidos duplicados al usar esta técnica de lotes.

Este ejemplo muestra que aún más complejas operaciones de base de datos, como las operaciones de maestro y detalles, pueden procesar por lotes con parámetros con valores de tabla.

### <a name="upsert"></a>UPSERT
Otro escenario por lotes implica la actualización simultáneamente insertar nuevas filas y las filas existentes. Esta operación también se conoce como una operación de "UPSERT" (actualización + Insertar). En lugar de realizar llamadas independientes para insertar y actualizar, la instrucción de combinación es más adecuada para esta tarea. La instrucción de combinación puede realizar las opciones Insertar y actualizar las operaciones en una sola llamada.

Parámetros con valores de tabla pueden utilizarse con la instrucción de combinación para realizar actualizaciones e inserciones. Por ejemplo, considere la posibilidad de una tabla de empleados simplificada que contiene las siguientes columnas: IdEmpleado, nombre, apellido, NúmeroSeguridadSocial:

    CREATE TABLE [dbo].[Employee](
    [EmployeeID] [int] IDENTITY(1,1) NOT NULL,
    [FirstName] [nvarchar](50) NOT NULL,
    [LastName] [nvarchar](50) NOT NULL,
    [SocialSecurityNumber] [nvarchar](50) NOT NULL,
     CONSTRAINT [PrimaryKey_Employee] PRIMARY KEY CLUSTERED 
    ([EmployeeID] ASC ))
 
En este ejemplo, puede usar el hecho de que la NúmeroSeguridadSocial es único para realizar una combinación de varios empleados. Primero, cree el tipo de tabla definida por el usuario:

    CREATE TYPE EmployeeTableType AS TABLE 
    ( Employee_ID INT,
      FirstName NVARCHAR(50),
      LastName NVARCHAR(50),
      SocialSecurityNumber NVARCHAR(50) );
    GO

A continuación, cree un procedimiento almacenado o escribir el código que utiliza la instrucción de combinación para realizar la actualización e insertar. En el ejemplo siguiente se utiliza la instrucción de combinación de un parámetro con valores de tabla, @employees, de tipo EmployeeTableType. El contenido de la @employees tabla no se muestra aquí.

    MERGE Employee AS target
    USING (SELECT [FirstName], [LastName], [SocialSecurityNumber] FROM @employees) 
    AS source ([FirstName], [LastName], [SocialSecurityNumber])
    ON (target.[SocialSecurityNumber] = source.[SocialSecurityNumber])
    WHEN MATCHED THEN 
    UPDATE SET
    target.FirstName = source.FirstName, 
    target.LastName = source.LastName
    WHEN NOT MATCHED THEN
       INSERT ([FirstName], [LastName], [SocialSecurityNumber])
       VALUES (source.[FirstName], source.[LastName], source.[SocialSecurityNumber]);

Para obtener más información, consulte la documentación y ejemplos para la instrucción de combinación. Aunque se pudo realizar el trabajo mismo en varios pasos almacenado llamada a procedimiento con separar insertar y las operaciones de actualización, la instrucción de combinación es más eficaz. Código de base de datos también puede crear llamadas de Transact-SQL que utilizan la instrucción de combinación directamente sin necesidad de dos llamadas de base de datos para insertar y actualizar.

## <a name="recommendation-summary"></a>Resumen de recomendaciones

La siguiente lista proporciona un resumen de las recomendaciones por lotes analizados en este tema:

- Usar el búfer y procesamiento por lotes para aumentar el rendimiento y escalabilidad de aplicaciones de base de datos de SQL.
- Comprender las compensaciones entre lotes de búfer y resistencia. Durante un corte de rol, el riesgo de perder un lote de datos empresariales importantes sin procesar puede superar el beneficio de rendimiento por lotes.
- Intentar mantener todas las llamadas a la base de datos dentro de un centro de datos solo para reducir la latencia.
- Si elige una sola técnica por lotes, parámetros con valores de tabla ofrecen el mejor rendimiento y flexibilidad.
- Para obtener un rendimiento insertar más rápido, siga estas instrucciones generales pero el escenario de prueba:
    - Para las filas de < 100, use un comando Insertar parametrizado.
    - Para las filas de < 1000, usar parámetros con valores de tabla.
    - Para > = 1000 filas, use SqlBulkCopy.
- Para actualizar y eliminar operaciones, usar parámetros con valores de tabla con lógica de procedimiento almacenado que determina el funcionamiento correcto en cada fila en el parámetro de la tabla.
- Directrices de tamaño del lote:
    - Utilice los tamaños de lote más grandes que tengan sentido para la aplicación y los requisitos empresariales.
    - Equilibrar la mejora del rendimiento de lotes grandes con los riesgos de errores temporales o graves. ¿Qué es la consecuencia de reintentos o pérdida de datos en el lote? 
    - Probar el tamaño del lote mayor para comprobar que la base de datos de SQL no rechazarlo.
    - Crear configuración dicho control por lotes, como el tamaño del lote o la ventana de tiempo de búfer. Estas opciones proporcionan flexibilidad. Puede cambiar el comportamiento de procesamiento por lotes en producción sin volver a implementar el servicio de nube.
- Evitar la ejecución de lotes que funcionan en una sola tabla en una base de datos en paralelo. Si decide dividir un único lote entre varios subprocesos, ejecute pruebas para determinar el número ideal de subprocesos. Después de un umbral especificado, más subprocesos se disminuir el rendimiento en lugar de aumentar.
- Considere la posibilidad de búfer en tamaño y la hora como una manera de implementar por lotes para escenarios más.

## <a name="next-steps"></a>Pasos siguientes

Este artículo se centra en cómo el diseño de base de datos y técnicas relacionadas con lotes de codificación pueden mejorar el rendimiento de la aplicación y escalabilidad. Pero se trata de un solo factor en su estrategia. Para que otras formas de mejorar el rendimiento y escalabilidad, consulte [pautas de rendimiento de base de datos de SQL Azure para bases de datos](sql-database-performance-guidance.md) y [Consideraciones de precio y rendimiento para un grupo de elásticos de la base de datos](sql-database-elastic-pool-guidance.md).
