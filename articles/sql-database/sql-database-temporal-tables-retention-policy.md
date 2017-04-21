<properties
   pageTitle="Administrar datos históricos en tablas Temporal con la directiva de retención | Microsoft Azure"
   description="Obtenga información sobre cómo usar la directiva de retención temporal para mantener los datos históricos en el control."
   services="sql-database"
   documentationCenter=""
   authors="bonova"
   manager="drasumic"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sql-database"
   ms.date="10/12/2016"
   ms.author="bonova"/>

#<a name="manage-historical-data-in-temporal-tables-with-retention-policy"></a>Administrar datos históricos en tablas Temporal con la directiva de retención

Tablas temporales pueden aumentar el tamaño de la base de datos más de las tablas normales, especialmente si conservar datos históricos de un período de tiempo. Por lo tanto, la directiva de retención de datos históricos es un aspecto importante de planear y administrar el ciclo de vida de cada tabla temporal. Tablas temporales en la base de datos de SQL Azure vienen con mecanismo de retención de fácil de usar que le ayuda a realizar esta tarea.

Retención de historial temporal puede ser configurado en el nivel de tabla individuales, que permite a los usuarios crear antigüedad flexible directivas. Aplicar retención temporal es sencillo: requiere un solo parámetro se almacenen durante el cambio de esquema o creación de tabla.

Después de definir la directiva de retención, base de datos de SQL Azure empezará a comprobar periódicamente si hay filas históricos que son elegibles para la limpieza de datos automática. Identificación de filas coincidentes y su eliminación de la tabla Historial de forma transparente, en la tarea en segundo plano programada y ejecute el sistema. Condición de edad de las filas de tabla del historial está activado en función de la columna que representa el final del período SYSTEM_TIME. Si el período de retención, por ejemplo, se establece en seis meses, elegible para la limpieza de las filas de tabla cumplen la condición siguiente:

````
ValidTo < DATEADD (MONTH, -6, SYSUTCDATETIME())
````

En el ejemplo anterior, se supone que **ValidTo** columna corresponde al final del período SYSTEM_TIME.

##<a name="how-to-configure-retention-policy"></a>¿Cómo puedo configurar la directiva de retención?

Antes de configurar la directiva de retención para una tabla temporal, active primero si retención histórico temporal está habilitado *en el nivel de base de datos*.

````
SELECT is_temporal_history_retention_enabled, name
FROM sys.databases
````

Marca de base de datos **is_temporal_history_retention_enabled** se establece en activado de forma predeterminada, pero los usuarios pueden cambiar con la instrucción ALTER DATABASE. Se establecen automáticamente OFF después de la operación de [punto de restauración de tiempo](sql-database-point-in-time-restore-portal.md) . Para habilitar la limpieza de retención de historial temporal para la base de datos, ejecute la instrucción siguiente:

````
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
````

> [AZURE.IMPORTANT] Puede configurar la retención para tablas temporales incluso si está desactivada de forma **is_temporal_history_retention_enabled** , pero en ese caso no se activará la limpieza automática para las filas por antigüedad.


Directiva de retención se configura durante la creación de tabla que especifica el valor del parámetro HISTORY_RETENTION_PERIOD:

````
CREATE TABLE dbo.WebsiteUserInfo
(  
    [UserID] int NOT NULL PRIMARY KEY CLUSTERED
  , [UserName] nvarchar(100) NOT NULL
  , [PagesVisited] int NOT NULL
  , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
  , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
  , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
 )  
 WITH
 (
     SYSTEM_VERSIONING = ON
     (
        HISTORY_TABLE = dbo.WebsiteUserInfoHistory,
        HISTORY_RETENTION_PERIOD = 6 MONTHS
     )
 );
````

Base de datos de SQL Azure le permite especificar el período de retención con las unidades de tiempo diferente: días, semanas, meses y años. Si se omite HISTORY_RETENTION_PERIOD, se supone retención infinito. También puede usar palabras clave infinito explícitamente.

En algunos casos, desea configurar la retención después de la creación de tabla o para cambiar previamente valor configurado. En ese caso utilice instrucción ALTER TABLE:

````
ALTER TABLE dbo.WebsiteUserInfo
SET (SYSTEM_VERSIONING = ON (HISTORY_RETENTION_PERIOD = 9 MONTHS));
````

> [AZURE.IMPORTANT]  Establecer SYSTEM_VERSIONING en desactivado *no conserva* el valor período de retención. Establecer SYSTEM_VERSIONING on sin HISTORY_RETENTION_PERIOD especifica explícitamente resultados en el período de retención infinito.

Para revisar el estado actual de la directiva de retención, utilice la siguiente consulta que combine la marca de la habilitación de retención temporal en el nivel de base de datos con períodos de retención para tablas individuales:

````
SELECT DB.is_temporal_history_retention_enabled,
SCHEMA_NAME(T1.schema_id) AS TemporalTableSchema,
T1.name as TemporalTableName,  SCHEMA_NAME(T2.schema_id) AS HistoryTableSchema,
T2.name as HistoryTableName,T1.history_retention_period,
T1.history_retention_period_unit_desc
FROM sys.tables T1  
OUTER APPLY (select is_temporal_history_retention_enabled from sys.databases
where name = DB_NAME()) AS DB
LEFT JOIN sys.tables T2   
ON T1.history_table_id = T2.object_id WHERE T1.temporal_type = 2
````


##<a name="how-sql-database-deletes-aged-rows"></a>¿Cómo se elimina la base de datos de SQL por antigüedad filas?

El proceso de limpieza depende del diseño de índice de la tabla del historial. Es importante tener en cuenta que *solo historial tablas con un índice agrupado (árbol B o columnstore) pueden tener directivas de retención finita configurado*. Se crea una tarea en segundo plano para limpiar los datos antiguos en todas las tablas temporales con el período de retención finita.
La lógica de limpieza para el índice agrupado rowstore (árbol B) elimina una fila antigua en fragmentos más pequeños (hasta 10 K) minimizar presión en el registro de la base de datos y subsistema de i/OS. Aunque la lógica de limpieza utiliza requiere índice de árbol B, orden de eliminaciones para las filas más antiguas de período de retención no se puede garantizar bien. Por lo tanto, *no lleve a cabo cualquier dependencia en el orden de Liberador de espacio en las aplicaciones*.

La tarea de limpieza de la agrupadas columnstore quita toda [grupos de filas](https://msdn.microsoft.com/library/gg492088.aspx) a la vez (normalmente contienen 1 millón de filas cada), que es muy eficaz, especialmente cuando se generan datos históricos a alta velocidad.

![Retención columnstore agrupadas](./media/sql-database-temporal-tables-retention-policy/cciretention.png)


Compresión de datos excelente y retención eficiente limpieza hace columnstore índice agrupado elección perfecta para escenarios cuando la carga de trabajo genera rápidamente gran cantidad de datos históricos. Ese modelo es típico para intensiva [cargas de trabajo de procesamiento de transacciones que usan tablas temporales](https://msdn.microsoft.com/library/mt631669.aspx) para el seguimiento de cambios y auditoría, análisis de tendencias o IoT recopilación de datos.

##<a name="index-considerations"></a>Consideraciones de índice

La tarea de limpieza de tablas con índice agrupado de rowstore requiere índice para empezar con la columna correspondiente al final del período SYSTEM_TIME. Si no existe ese índice, no podrá configurar el período de retención finita:

*Msj 13765, nivel 16, estado 1 <br> </br> establecer el período de retención finita error en la tabla temporal con versión sistema 'temporalstagetestdb.dbo.WebsiteUserInfo' porque la tabla de historial 'temporalstagetestdb.dbo.WebsiteUserInfoHistory' no contiene índice agrupado necesario. Considere la posibilidad de crear un columnstore agrupado o un índice de árbol B empezando por la columna que coincida con el fin de SYSTEM_TIME período en la tabla de historial.*

Es importante tener en cuenta que la tabla de historial predeterminado creada por base de datos de SQL Azure ya ha agrupado índice, que es compatible con para la directiva de retención. Si intenta quitar ese índice en una tabla con el período de retención finita, se produce un error en la operación con el siguiente error:

*Msj 13766, nivel 16, estado 1 <br> </br> no se puede quitar el índice agrupado 'WebsiteUserInfoHistory.IX_WebsiteUserInfoHistory' usarlo para la limpieza automática de los datos antiguos. Considere la posibilidad de configurar HISTORY_RETENTION_PERIOD infinito en la tabla temporal con versión sistema correspondiente si necesita eliminar este índice.*

Liberador de espacio en el índice agrupado columnstore funcionará de manera óptima si se insertan filas históricos en el orden ascendente (ordenado por el final de la columna período), que siempre es el caso cuando se rellena la tabla de historial exclusivamente por el mecanismo SYSTEM_VERSIONIOING. Si las filas de la tabla de historial no se ordenan por el final de la columna período (que puede ocurrir si se migran los datos históricos existentes), debe volver a crear índice de columnstore agrupadas encima de índice de rowstore de árbol B se ordena correctamente, para obtener un rendimiento óptimo.

Evite regeneración de índice columnstore agrupado en la tabla de historial con el período de retención finita, porque puede cambiar el orden de los grupos de fila naturalmente impuestos por la operación de control de versiones de sistema. Si necesita volver a generar índice columnstore agrupado en la tabla del historial, haga lo vuelva a crearlo en la parte superior compatible con índice de árbol B, perder la ordenación en el rowgroups necesarios para la limpieza de datos normal. El mismo enfoque debe realizarse si crear tabla temporal con la tabla de historial existente que ha agrupado índice de columna sin orden garantiza datos:

````
/*Create B-tree ordered by the end of period column*/
CREATE CLUSTERED INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory (ValidTo)
WITH (DROP_EXISTING = ON);
GO
/*Re-create clustered columnstore index*/
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON);
````

Cuando el período de retención finita está configurada para la tabla de historial con el índice agrupado columnstore, no puede crear índices de árbol B no agrupados adicionales en dicha tabla:

````
CREATE NONCLUSTERED INDEX IX_WebHistNCI ON WebsiteUserInfoHistory ([UserName])
````

Un intento de ejecutar por encima de la instrucción provocarán el siguiente error:

*Msj 13772, nivel 16, estado 1 <br> </br> no se puede crear índice no agrupado en una tabla de historial temporal 'WebsiteUserInfoHistory' ya que tiene período de retención finita y definido el índice agrupado columnstore.*

##<a name="querying-tables-with-retention-policy"></a>Consultar tablas con la directiva de retención

Todas las consultas en la tabla temporal filtran automáticamente históricas filas coincidentes directiva de retención finita, para evitar resultados imprevistos e incoherentes, puesto que se pueden eliminar filas por antigüedad por la tarea de limpieza, *en cualquier punto en el tiempo y en orden arbitrario*.

La siguiente imagen muestra el plan de consulta para una consulta simple:

````
SELECT * FROM dbo.WebsiteUserInfo FROM SYSTEM_TIME ALL;
````

El plan de consulta incluye adicional filtro aplicado al final de la columna de período (ValidTo) en el operador "Exploración de índice agrupado" en la tabla de historial (resaltado). En este ejemplo se supone que ese período de retención de 1 mes se estableció en tabla WebsiteUserInfo.

![Filtro de consulta de retención](./media/sql-database-temporal-tables-retention-policy/queryexecplanwithretention.png)

Sin embargo, si consulta la tabla Historial directamente, puede ver las filas que sean anteriores a retención especificado período, pero sin ninguna garantía de resultados de la consulta puede repetir. La imagen siguiente muestra el plan de ejecución de consulta para la consulta en la tabla Historial sin filtros adicionales aplicados:

![Consultar el historial sin filtro de retención](./media/sql-database-temporal-tables-retention-policy/queryexecplanhistorytable.png)

No dependen de la lógica empresarial leer tabla Historial más allá del período de retención que puede obtener resultados inesperados o incoherentes. Se recomienda usar consultas temporales con cláusula FOR SYSTEM_TIME para analizar datos en tablas temporales.

##<a name="point-in-time-restore-considerations"></a>Elija en consideraciones de restauración de tiempo

Cuando se crea la nueva base de datos mediante la [restauración de una base de datos existente a un punto específico en el tiempo](sql-database-point-in-time-restore-portal.md), tiene retención temporal deshabilitada en el nivel de base de datos. (marca**is_temporal_history_retention_enabled** establecida en OFF). Esta funcionalidad le permite examinar todas las filas históricas tras la restauración, sin tener en cuenta que se quitan las filas por antigüedad antes de llegar a la consulta de ellos. Puede usarlo para *Inspeccionar datos históricos fuera del período de retención configurada*.

Supongamos que una tabla temporal tiene el periodo de retención de un mes. Si la base de datos se creó en el nivel de servicio Premium, podrá crear una copia de base de datos con el estado de la base de datos hacia arriba a 35 días en el pasado. Que eficaz le permitirá analizar históricos filas hasta 65 días de antigüedad consultando directamente en la tabla de historial.

Si desea activar limpieza de retención temporal, ejecute la siguiente instrucción de Transact-SQL después de punto de restauración de tiempo:

````
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
````

##<a name="next-steps"></a>Pasos siguientes

Para obtener información sobre cómo usar tablas temporales en las aplicaciones consulte [Introducción a las tablas temporales de base de datos de SQL Azure](sql-database-temporal-tables.md).

Visite el canal 9 para oír una [historia de éxito de implementación temporal reales de clientes](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) y ver una [demostración temporal de live](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016).

Para obtener información detallada acerca de las tablas temporales, revise la [documentación de MSDN](https://msdn.microsoft.com/library/dn935015.aspx).
