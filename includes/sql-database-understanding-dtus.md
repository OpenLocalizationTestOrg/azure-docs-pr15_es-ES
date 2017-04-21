Unidad de transacción de base de datos (DTU) es la unidad de medida de base de datos de SQL que representa la potencia relativa de bases de datos según una medida reales: la transacción de base de datos. Se realizó un conjunto de operaciones que están típico para una transacción en línea procesamiento de solicitud (OLTP) y, a continuación, mide cuántas transacciones se pueden completar por segundo en totalmente cargado condiciones (que es la versión abreviada, puede leer los detalles de la [información general de referencia](../articles/sql-database/sql-database-benchmark-overview.md)). 

Por ejemplo, una base de datos P11 Premium con 1750 DTUs proporciona potencia 350 x DTU más de una base de datos básico con 5 DTUs. 

![Introducción a la base de datos SQL: solo DTUs de base de datos por nivel y nivel.](./media/sql-database-understanding-dtus/single_db_dtus.png)

>[AZURE.NOTE] Si va a migrar una base de datos de SQL Server, puede usar una herramienta de terceros, [La calculadora de DTU de base de datos de SQL Azure](http://dtucalculator.azurewebsites.net/), para obtener una estimación del nivel de rendimiento y puede requerir la base de datos en la base de datos de SQL Azure de nivel de servicio.

### <a name="dtu-vs-edtu"></a>DTU frente a eDTU

La DTU de bases de datos se traduce directamente en el eDTU para elásticos bases de datos. Por ejemplo, una base de datos en un grupo de base de datos elástico básica ofrece un máximo de 5 eDTUs. Es el mismo rendimiento que una base de datos básico. La diferencia es que la base de datos elástico no consume cualquier eDTUs del grupo hasta que tenga a. 

![Introducción a la base de datos SQL: grupos elásticos por nivel.](./media/sql-database-understanding-dtus/sqldb_elastic_pools.png)

Ayuda un ejemplo sencillo. Llevar un grupo de base de datos elástico básica con 1000 DTUs y colocar 800 bases de datos en él. Siempre que se utilizan sólo 200 de las bases de datos de 800 en cualquier punto de tiempo (5 DTU X 200 = 1000), no acertar capacidad del grupo de y no degrada el rendimiento de la base de datos. En este ejemplo se ha simplificado para claridad. La expresión matemática real es más complicada. El portal hace las matemáticas por usted y hace una recomendación basada en el uso de la base de datos histórica. Consulte [Consideraciones de precio y rendimiento para un grupo de la base de datos elástica](../articles/sql-database/sql-database-elastic-pool-guidance.md) para aprender cómo funcionan las recomendaciones, o para realizar los cálculos usted mismo. 
