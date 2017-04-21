<properties
   pageTitle="Distribución de tablas en el almacén de datos de SQL | Microsoft Azure"
   description="Introducción a distribuir tablas en el almacén de datos de SQL Azure."
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
   ms.date="08/30/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="distributing-tables-in-sql-data-warehouse"></a>Distribución de tablas en el almacén de datos de SQL

> [AZURE.SELECTOR]
- [Información general][]
- [Tipos de datos][]
- [Distribuir][]
- [Índice][]
- [Partición][]
- [Estadísticas][]
- [Temporal][]

Almacén de datos de SQL es que un procesamiento enormemente paralelo (MPP) distribuido el sistema de base de datos.  Dividiendo los datos y capacidad de procesamiento entre varios nodos, almacén de datos SQL pueden ofrecer gran escalabilidad - más allá de cualquier sistema único.  Decidir cómo distribuir los datos en el almacén de datos de SQL es uno de los factores más importantes para lograr un rendimiento óptimo.   La clave para obtener un rendimiento óptimo minimizar el movimiento de datos y a su vez la clave para minimizar el movimiento de datos es seleccionando la estrategia de distribución adecuada.

## <a name="understanding-data-movement"></a>Descripción de movimiento de datos

En un sistema MPP, los datos de cada tabla se dividen en varias bases de datos subyacentes.  Las consultas más optimizadas en un sistema MPP simplemente se pasa a través de ejecutar en las bases de datos individuales distribuidos sin interacción entre otras bases de datos.  Por ejemplo, supongamos que tiene una base de datos con los datos de ventas que contiene dos tablas, ventas y clientes.  Si tiene una consulta que necesita para unirse a la tabla sales a la tabla de clientes y divide las ventas y la tablas de cliente hacia arriba por número de cliente, colocación de cada cliente en una base de datos independiente, pueden resolver las consultas que unirse de ventas y clientes dentro de cada base de datos sin conocer otras bases de datos.  Por el contrario, si divide los datos de ventas por número de pedido y los datos del cliente por número de cliente, a continuación, cualquier base de datos determinado no tendrán los datos correspondientes de cada cliente y, por tanto, si desea unir los datos de ventas a los datos de clientes, necesarias para obtener los datos de cada cliente desde otras bases de datos.  En este segundo ejemplo, el movimiento de datos necesarias para producir para mover los datos del cliente a los datos de ventas, para que las dos tablas pueden unirse.  

Movimiento de datos no es una mala, a veces es necesario resolver una consulta.  Pero cuando se puede evitar este paso adicional, naturalmente la consulta se ejecutará más rápidamente.  Movimiento de datos con más frecuencia surge cuando se combinan las tablas o las agregaciones se realizan.  A menudo tiene que hacer dos, por lo que aunque es posible que pueda optimizar para un escenario, como una combinación, todavía necesita movimientos de datos para ayudar a resolver para el escenario, como una agregación.  El truco es averiguar cuál es menos trabajo.  En la mayoría de los casos, la distribución de tablas de hechos grandes en una columna combinada con frecuencia es el método más eficaz para reducir el movimiento de datos de la mayoría.  Distribución de datos en columnas de combinación es un método mucho más comunes para reducir el movimiento de datos de distribución de datos en columnas implicadas en una agregación.

## <a name="select-distribution-method"></a>Seleccione el método de distribución

En segundo plano, almacén de datos SQL divide los datos en bases de datos de 60.  Cada base de datos se conoce como una **distribución**.  Cuando se cargan datos en cada tabla, almacén de datos de SQL tiene que saber cómo dividir los datos entre estas 60 distribuciones.  

El método de distribución se define en la tabla y actualmente hay dos opciones:

1. **Turnos** que distribuir uniformemente pero aleatoriamente los datos.
2. **Distribuido hash** que distribuye datos basándose en los valores de una sola columna de hash

De forma predeterminada, cuando no se define un método de distribución de datos, la tabla se distribuye con el método de distribución de **turnos** .  Sin embargo, a medida que vaya más complejas en su implementación, que desee considerar usar tablas **hash distribuido** para reducir el movimiento de datos que a su vez optimizar el rendimiento de la consulta.

### <a name="round-robin-tables"></a>Tablas de turnos

Usar el método de turnos de distribución de datos es muy cómo suena.  Al cargan los datos, cada fila simplemente se envía a la distribución siguiente.  Este método de distribución de los datos siempre aleatoriamente distribuirá los datos muy uniformemente en todas las distribuciones.  Es decir, no hay ninguna ordenación hacerlo durante el proceso de turnos que coloca los datos.  Una distribución de turnos se denomina hash aleatorio por este motivo.  Con una tabla de turnos distribuida no es necesario comprender los datos.  Por este motivo, las tablas de turnos a menudo tengan buena carga destinos.

De forma predeterminada, si no se elige ningún método de distribución, se utilizará el método de distribución de turnos.  Sin embargo, mientras turnos tablas son fáciles de usar, porque los datos se distribuyen al azar en el sistema significa que el sistema no puede garantizar la distribución cada fila es.  Como resultado, el sistema a veces necesita invocar una operación de movimiento de datos para organizar mejor los datos antes de que pueda resolver una consulta.  Este paso adicional puede ralentizar las consultas.

Considere la utilización de distribución de turnos de la tabla en las situaciones siguientes:

- Al comenzar como punto de partida sencillo
- Si no hay ninguna clave de unión obvia
- Si no hay columna buenos candidatos para distribuir la tabla de hash
- Si la tabla no comparte una clave de combinación comunes con otras tablas
- Si la combinación es tan importante otras combinaciones en la consulta
- Cuando la tabla es una tabla de ensayo temporal

Ambos ejemplos crearán una tabla de turnos:

```SQL
-- Round Robin created by default
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
;

-- Explicitly Created Round Robin Table
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = ROUND_ROBIN
)
;
```

> [AZURE.NOTE] Mientras turnos es el tipo de tabla predeterminado sean explícitos en su DDL se considera práctica recomendada para que las intenciones del diseño de tabla están desactivadas a otras personas.

### <a name="hash-distributed-tables"></a>Hash tablas distribuidos

Usar un algoritmo de **tipo Hash distribuido** para distribuir las tablas puede mejorar el rendimiento para muchos escenarios reduciendo el movimiento de datos en tiempo de consulta.  Tablas de hash distribuido son tablas que se dividen entre las bases de datos distribuidas utilizando un algoritmo de hash en una sola columna que seleccionar.  La columna de la distribución es lo que determina cómo se dividen los datos en todas las bases de datos distribuidas.  La función hash utiliza la columna de distribución para asignar filas a las distribuciones.  El algoritmo de hash y distribución resultante es determinista.  Que es el mismo valor con el mismo tipo de datos siempre se tiene la misma distribución.    

En este ejemplo se creará una tabla que se distribuye en id:

```SQL
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,  DISTRIBUTION = HASH([ProductKey])
)
;
```

## <a name="select-distribution-column"></a>Seleccione la columna de distribución.

Al elegir **hash** distribuir una tabla, debe seleccionar una columna única de distribución.  Al seleccionar una columna de distribución, hay tres factores importantes a tener en cuenta.  

Seleccione una sola columna que:

1. No se pueden actualizar
2. Distribuir uniformemente, datos evitar sesgo de datos
3. Reducir el movimiento de datos

### <a name="select-distribution-column-which-will-not-be-updated"></a>Seleccione la columna de distribución que no se actualizarán

Columnas de distribución no actualizables, por lo tanto, seleccione una columna con valores estáticos.  Si una columna se necesita actualizarse, generalmente no es un candidato correctas de distribución.  Si hay un caso donde debe actualizar una columna de distribución, es posible eliminar primero la fila y después insertando una nueva fila.

### <a name="select-distribution-column-which-will-distribute-data-evenly"></a>Seleccionar columna de distribución que distribuirá datos uniformemente

Dado que un sistema distribuido realiza solo tan rápidamente como su distribución más lento, es importante dividir el trabajo uniformemente entre las distribuciones para lograr una ejecución equilibrada en el sistema.  La manera en que se divide el trabajo en un sistema distribuido se basa en donde residen los datos de cada distribución.  Esto hace que sea muy importante para seleccionar la columna derecha de distribución para distribuir los datos para que cada distribución tiene trabajo igual y tendrá el mismo tiempo para completar su parte del trabajo.  Cuando también se divide el trabajo en el sistema, los datos se equilibra entre las distribuciones.  Cuando los datos no está equilibrados uniformemente, llamamos este **sesgo de datos**.  

Para dividir datos uniformemente y evitar el sesgo de datos, considere lo siguiente al seleccionar la columna de distribución:

1. Seleccione una columna que contiene un gran número de valores distintos.
2. Evitar la distribución de datos en columnas con unos valores distintos. 
3. Evitar la distribución de datos en columnas con una alta frecuencia de valores nulos.
4. Evitar la distribución de datos en columnas de fecha.

Dado que cada valor se calcula al 1 de 60 distribuciones, para lograr la distribución par se desea seleccionar una columna que es prácticamente únicos y contiene más de 60 valores únicos.  Para ilustrar, considere la posibilidad de un caso en una columna sólo tiene 40 valores únicos.  Si se ha seleccionado esta columna como la clave de distribución, los datos de esa tabla ' d land en 40 distribuciones como máximo, salir de 20 distribuciones con ningún dato y sin procesamiento.  Por el contrario, las distribuciones de 40 tendría más trabajo hacer si los datos se distribuyen uniformemente distribuciones más de 60.  Este escenario es un ejemplo de datos skew.

En el sistema MPP, cada paso de consulta que espera para que todas las distribuciones completar su parte del trabajo.  Si una distribución hace más trabajo que los demás, a continuación, el recurso de las demás distribuciones se esencialmente pierde esperando en la distribución ocupada.  Cuando el trabajo no se distribuye uniformemente entre todas las distribuciones, llamamos este **sesgo de procesamiento**.  Hará que el sesgo de procesamiento de consultas para que se ejecute más despacio que si la carga de trabajo se puede distribuir uniformemente en las distribuciones.  Sesgo de datos le llevará a procesamiento skew.

Evitar la distribución en la columna nullable altamente según los valores nulos se colocará en la misma distribución. Distribuir en una columna de fecha puede causar el sesgo de procesamiento porque todos los datos para una fecha determinada se colocará en la misma distribución. Si varios usuarios están ejecutando consultas todos los filtros, en la misma fecha, a continuación, sólo 1 de las distribuciones de 60 realizará todo el trabajo desde una fecha determinada sólo estará en una distribución. En este escenario, las consultas es probable que se ejecutarán más lentos que si los datos se extiendan uniformemente sobre todas las distribuciones de 60 veces. 

Cuando no existe ninguna columna correcta debe, considere la posibilidad de usar turnos como el método de distribución.

### <a name="select-distribution-column-which-will-minimize-data-movement"></a>Seleccionar columna de distribución que minimizará movimientos de datos

Minimizar el movimiento de datos, seleccione la columna derecha distribución es una de las estrategias más importantes para optimizar el rendimiento de su almacén de datos de SQL.  Movimiento de datos con más frecuencia surge cuando se combinan las tablas o las agregaciones se realizan.  Las columnas utilizadas en `JOIN`, `GROUP BY`, `DISTINCT`, `OVER` y `HAVING` cláusulas todos realizar para **buena** hash candidatos de distribución. 

Por otro lado, las columnas de la `WHERE` cláusula hacer **realiza** de candidatos de columna de hash buena causando procesamiento inclinar porque limitar qué distribuciones participan en la consulta.  Un buen ejemplo de una columna que podría ser tentador distribuir en, pero a menudo puede provocar este procesamiento sesgo es una columna de fecha.

Por lo general, si tiene dos tablas de hecho de gran tamaño con frecuencia implicadas en una combinación, obtendrá el máximo rendimiento mediante la distribución de ambas tablas en una de las columnas de combinación.  Si tiene una tabla que nunca se ha unido a otra tabla de hechos grandes, a continuación, busque a las columnas que se encuentran con frecuencia en el `GROUP BY` cláusula.

Hay algunos criterios clave que se deben cumplir para evitar el movimiento de datos durante una combinación:

1. Las tablas implicadas en la combinación deben ser hash distribuido en **una** de las columnas que participan en la combinación.
2. Los tipos de datos de las columnas de combinación deben coincidir entre ambas tablas.
3. Las columnas deben combinarse con un operador es igual a.
4. El tipo de combinación no puede ser un `CROSS JOIN`.


## <a name="troubleshooting-data-skew"></a>Solución de problemas de datos skew

Cuando los datos de la tabla se distribuyen con el método de distribución de tipo hash es probable que se pueden sesgadas algunas distribuciones para que haya más datos de otros usuarios. Sesgo de datos excesivo puede afectar al rendimiento de la consulta porque el resultado final de una consulta distribuida debe esperar a que la distribución de ejecución más larga Finalizar. Según el grado de distorsión de datos posible que necesite dirección.

### <a name="identifying-skew"></a>Identificar el sesgo

Una forma sencilla para identificar una tabla como sesgada es usar `DBCC PDW_SHOWSPACEUSED`.  Esta es una forma muy rápida y sencilla para ver el número de filas de tabla que se almacenan en cada una de las distribuciones de 60 de la base de datos.  Recuerde que el rendimiento más equilibrada, las filas de la tabla distribuida se deben distribuir uniformemente en todas las distribuciones.

```sql
-- Find data skew for a distributed table
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Sin embargo, si consulta las vistas dinámicas de administración de almacén de datos de SQL Azure (DMV) puede realizar un análisis más detallado.  Para empezar, cree la vista de [dbo.vTableSizes][] de vista mediante la instrucción SQL del artículo de[Visión general] de la [Información general de la tabla].  Una vez creada la vista, ejecute esta consulta para identificar las tablas que tengan más de 10% datos sesgo.

```sql
select *
from dbo.vTableSizes 
where two_part_name in 
    (
    select two_part_name
    from dbo.vTableSizes
    where row_count > 0
    group by two_part_name
    having min(row_count * 1.000)/max(row_count * 1.000) > .10
    )
order by two_part_name, row_count
;
```

### <a name="resolving-data-skew"></a>Resolver datos skew

No todos los skew es suficiente para garantizar una solución.  En algunos casos, el rendimiento de una tabla en algunas consultas puede superar los daños de datos skew.  Para decidir si debe resolver datos sesgo de una tabla, debería entender tanto como sea posible sobre los volúmenes de datos y las consultas de la carga de trabajo.   Una forma de ver el efecto de distorsión es realice los pasos en el artículo de la [Supervisión de consulta][] para supervisar el efecto de distorsión en el rendimiento de la consulta y específicamente el impacto a las consultas de cuánto tiempo llevar a cabo en las distribuciones individuales.

Distribución de datos se trata de encontrar el equilibrio adecuado entre minimizar el sesgo de datos y minimizar el movimiento de datos. Estos pueden ser opuestas objetivos y, a veces desea mantener datos skew para reducir el movimiento de datos. Por ejemplo, cuando la columna de distribución con frecuencia es la columna compartida de combinaciones y agregaciones, se le pueden minimizar movimientos de datos. La ventaja de disponer el movimiento de datos mínima puede superar el impacto de tener datos inclinar. 

La manera habitual para resolver el sesgo de datos es volver a crear la tabla con una columna de distribución diferente. Puesto que no hay ninguna manera de cambiar la columna de distribución en una tabla existente, la manera de cambiar la distribución de una tabla que volver a crearlo con una [[CTAS]].  Estos son dos ejemplos de cómo resolver sesgo de datos:

### <a name="example-1-re-create-the-table-with-a-new-distribution-column"></a>Ejemplo 1: Volver a crear la tabla con una nueva columna de distribución.

Este ejemplo usa [] [CTAS] para volver a crear una tabla con una columna de la distribución de hash diferente. 

```sql
CREATE TABLE [dbo].[FactInternetSales_CustomerKey] 
WITH (  CLUSTERED COLUMNSTORE INDEX
     ,  DISTRIBUTION =  HASH([CustomerKey])
     ,  PARTITION       ( [OrderDateKey] RANGE RIGHT FOR VALUES (   20000101, 20010101, 20020101, 20030101
                                                                ,   20040101, 20050101, 20060101, 20070101
                                                                ,   20080101, 20090101, 20100101, 20110101
                                                                ,   20120101, 20130101, 20140101, 20150101
                                                                ,   20160101, 20170101, 20180101, 20190101
                                                                ,   20200101, 20210101, 20220101, 20230101
                                                                ,   20240101, 20250101, 20260101, 20270101
                                                                ,   20280101, 20290101
                                                                )
                        )
    )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
OPTION  (LABEL  = 'CTAS : FactInternetSales_CustomerKey')
;

--Create statistics on new table
CREATE STATISTICS [ProductKey] ON [FactInternetSales_CustomerKey] ([ProductKey]);
CREATE STATISTICS [OrderDateKey] ON [FactInternetSales_CustomerKey] ([OrderDateKey]);
CREATE STATISTICS [CustomerKey] ON [FactInternetSales_CustomerKey] ([CustomerKey]);
CREATE STATISTICS [PromotionKey] ON [FactInternetSales_CustomerKey] ([PromotionKey]);
CREATE STATISTICS [SalesOrderNumber] ON [FactInternetSales_CustomerKey] ([SalesOrderNumber]);
CREATE STATISTICS [OrderQuantity] ON [FactInternetSales_CustomerKey] ([OrderQuantity]);
CREATE STATISTICS [UnitPrice] ON [FactInternetSales_CustomerKey] ([UnitPrice]);
CREATE STATISTICS [SalesAmount] ON [FactInternetSales_CustomerKey] ([SalesAmount]);

--Rename the tables
RENAME OBJECT [dbo].[FactInternetSales] TO [FactInternetSales_ProductKey];
RENAME OBJECT [dbo].[FactInternetSales_CustomerKey] TO [FactInternetSales];
```

### <a name="example-2-re-create-the-table-using-round-robin-distribution"></a>Ejemplo 2: Volver a crear la tabla con la distribución de turnos

Este ejemplo usa [] [CTAS] para volver a crear una tabla con turnos en lugar de una distribución de hash. Este cambio devolverá incluso la distribución de datos a costa de movimiento de datos mayor. 

```sql
CREATE TABLE [dbo].[FactInternetSales_ROUND_ROBIN] 
WITH (  CLUSTERED COLUMNSTORE INDEX
     ,  DISTRIBUTION =  ROUND_ROBIN
     ,  PARTITION       ( [OrderDateKey] RANGE RIGHT FOR VALUES (   20000101, 20010101, 20020101, 20030101
                                                                ,   20040101, 20050101, 20060101, 20070101
                                                                ,   20080101, 20090101, 20100101, 20110101
                                                                ,   20120101, 20130101, 20140101, 20150101
                                                                ,   20160101, 20170101, 20180101, 20190101
                                                                ,   20200101, 20210101, 20220101, 20230101
                                                                ,   20240101, 20250101, 20260101, 20270101
                                                                ,   20280101, 20290101
                                                                )
                        )
    )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
OPTION  (LABEL  = 'CTAS : FactInternetSales_ROUND_ROBIN')
;

--Create statistics on new table
CREATE STATISTICS [ProductKey] ON [FactInternetSales_ROUND_ROBIN] ([ProductKey]);
CREATE STATISTICS [OrderDateKey] ON [FactInternetSales_ROUND_ROBIN] ([OrderDateKey]);
CREATE STATISTICS [CustomerKey] ON [FactInternetSales_ROUND_ROBIN] ([CustomerKey]);
CREATE STATISTICS [PromotionKey] ON [FactInternetSales_ROUND_ROBIN] ([PromotionKey]);
CREATE STATISTICS [SalesOrderNumber] ON [FactInternetSales_ROUND_ROBIN] ([SalesOrderNumber]);
CREATE STATISTICS [OrderQuantity] ON [FactInternetSales_ROUND_ROBIN] ([OrderQuantity]);
CREATE STATISTICS [UnitPrice] ON [FactInternetSales_ROUND_ROBIN] ([UnitPrice]);
CREATE STATISTICS [SalesAmount] ON [FactInternetSales_ROUND_ROBIN] ([SalesAmount]);

--Rename the tables
RENAME OBJECT [dbo].[FactInternetSales] TO [FactInternetSales_HASH];
RENAME OBJECT [dbo].[FactInternetSales_ROUND_ROBIN] TO [FactInternetSales];
```

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre el diseño de tabla, vea [distribuir][], [índice][], [partición][], [Tipos de datos][], [estadísticas][] y artículos[temporal] de [Tablas temporales].

Para obtener información general de los procedimientos recomendados, consulte [Prácticas recomendadas de almacén de datos de SQL][].


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
[Supervisión de consulta]: ./sql-data-warehouse-manage-monitor.md
[dbo.vTableSizes]: ./sql-data-warehouse-tables-overview.md#querying-table-sizes

<!--MSDN references-->
[DBCC PDW_SHOWSPACEUSED()]: https://msdn.microsoft.com/library/mt204028.aspx

<!--Other Web references-->
