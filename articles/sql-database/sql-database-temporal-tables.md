<properties
   pageTitle="Introducción a las tablas temporales de base de datos SQL Azure | Microsoft Azure"
   description="Obtenga información sobre cómo empezar a usar tablas Temporal en la base de datos de SQL Azure."
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sql-database"
   ms.date="08/29/2016"
   ms.author="carlrab"/>

#<a name="getting-started-with-temporal-tables-in-azure-sql-database"></a>Introducción a las tablas temporales de base de datos SQL Azure

Las tablas temporales son una nueva característica de programación de base de datos SQL Azure que permite realizar un seguimiento y analizar el historial completo de los cambios de los datos, sin necesidad de codificación personalizada. Tablas temporales mantienen datos relacionados con el contexto de tiempo para que se pueden interpretar hechos almacenados como válido solo dentro del período específico. Esta propiedad de tablas Temporal permite eficaz análisis de tiempo y obtener perspectivas de evolución de datos.

##<a name="temporal-scenario"></a>Escenario temporal

En este artículo se muestra los pasos para utilizar tablas temporales en un escenario de aplicación. Suponga que desea realizar un seguimiento de actividad de usuario en un sitio Web nuevo desarrollado desde cero o en un sitio Web existente que desea ampliar con análisis de actividad de usuario. En este ejemplo simplificado, se supone que el número de páginas web que visita durante un período de tiempo es un indicador que necesita para capturar y supervisar la base de datos del sitio Web alojado en la base de datos de SQL Azure. El objetivo del análisis histórico de actividad de usuario es obtener entradas a diseñar el sitio Web y ofrecer la mejor experiencia para los visitantes.

El modelo de base de datos de este escenario es muy sencillo: métrica de actividad de usuario se representa con un campo de número entero, **PageVisited**y se captura junto con información básica sobre el perfil de usuario. Además, para realizar análisis de tiempo según debería mantener una serie de filas para cada usuario, donde cada fila representa el número de páginas de un usuario concreto visitado dentro de un período de tiempo específico.

![Esquema](./media/sql-database-temporal-tables/AzureTemporal1.png)

Afortunadamente, no es necesario colocar cualquier esfuerzo en su aplicación para mantener la información de la actividad. Con tablas temporales, este proceso es automático - ofreciendo flexibilidad completa durante el diseño de sitio Web y más tiempo para centrarse en el análisis de datos propio. Lo único que tiene que hacer es asegurarse de que la tabla **WebSiteInfo** está configurado como [temporal sistema versión](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_0). A continuación, se describen los pasos exactos para utilizar tablas temporales en este escenario.

##<a name="step-1-configure-tables-as-temporal"></a>Paso 1: Configurar tablas como temporal

Dependiendo de si va a iniciar el desarrollo de nuevos o actualizar una aplicación existente, se crear tablas temporales o modifique los existentes mediante la adición de atributos temporales. En general caso, el escenario puede ser una combinación de estas dos opciones. Realizar estas acción con [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) (SSMS), [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) (SSDT) o cualquier otra herramienta de desarrollo de Transact-SQL.


> [AZURE.IMPORTANT] Se recomienda usar siempre la versión más reciente de Management Studio permanezca sincronizado con las actualizaciones de Microsoft Azure y base de datos de SQL. [Actualizar SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


###<a name="create-new-table"></a>Crear tabla

Use el elemento del menú contextual "Nueva versión del sistema de tabla" SSMS Explorador de objetos para abrir el editor de consultas con una secuencia de comandos de plantilla de tabla temporal y, a continuación, usar "Especificar valores de parámetros de plantilla" (Ctrl + Mayús + M) para rellenar la plantilla:

![SSMSNewTable](./media/sql-database-temporal-tables/AzureTemporal2.png)

En SSDT, elige la plantilla de "Tabla Temporal (sistema de control de versiones)" al agregar nuevos elementos a la base de datos proyecto. Que se abra el Diseñador de tablas y permiten especificar fácilmente el diseño de tabla:

![SSDTNewTable](./media/sql-database-temporal-tables/AzureTemporal3.png)

También puede crear una una tabla temporal especificando las instrucciones Transact-SQL directamente, tal como se muestra en el ejemplo siguiente. Observe que los elementos de cada tabla temporal obligatorio la definición del período y la cláusula SYSTEM_VERSIONING con una referencia a otra tabla de usuario que se guardará versiones históricas fila:

````
CREATE TABLE WebsiteUserInfo 
(  
    [UserID] int NOT NULL PRIMARY KEY CLUSTERED 
  , [UserName] nvarchar(100) NOT NULL
  , [PagesVisited] int NOT NULL 
  , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
  , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
  , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
 )  
 WITH (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.WebsiteUserInfoHistory));
````

Cuando se crea una versión sistema tabla temporal, se crea automáticamente la tabla de historial que con la configuración predeterminada. La tabla de historial predeterminado contiene un índice de árbol B agrupado en las columnas de periodo (final, inicio) habilitada la compresión de página. Esta configuración es óptima para la mayoría de los casos en los que se usan tablas temporales, especialmente para [datos de auditoría](https://msdn.microsoft.com/library/mt631669.aspx#Anchor_0). 

En este caso particular, queremos realizar análisis de tendencias de tiempo por un historial de datos más largo y con conjuntos de datos más grandes, por lo que la opción de almacenamiento para la tabla de historial es un índice agrupado columnstore. Un columnstore agrupado proporciona muy buena compresión y rendimiento de las consultas analíticas. Tablas temporales proporcionan la flexibilidad para configurar los índices en las tablas actuales y temporales completamente por separado. 

**Nota**: índices Columnstore solo están disponibles en el nivel de servicio premium.

La siguiente secuencia de comandos muestra cómo se puede cambiar el índice de forma predeterminada en la tabla de historial para la agrupadas columnstore:

````
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON); 
````

Tablas temporales se representan en el Explorador de objetos con el icono específico para facilitar su identificación, mientras que la tabla de historial se muestra como un nodo secundario.

![AlterTable](./media/sql-database-temporal-tables/AzureTemporal4.png)

###<a name="alter-existing-table-to-temporal"></a>Modificar una tabla existente a temporal

Vamos a cubra el escenario alternativo en el que la tabla WebsiteUserInfo ya existe, pero no se ha diseñado para mantener un historial de cambios. En este caso, simplemente puede ampliar la tabla existente para convertirse en temporales, tal como se muestra en el ejemplo siguiente:

````
ALTER TABLE WebsiteUserInfo 
ADD 
    ValidFrom datetime2 (0) GENERATED ALWAYS AS ROW START HIDDEN  
        constraint DF_ValidFrom DEFAULT DATEADD(SECOND, -1, SYSUTCDATETIME())
    , ValidTo datetime2 (0)  GENERATED ALWAYS AS ROW END HIDDEN   
        constraint DF_ValidTo DEFAULT '9999.12.31 23:59:59.99'
    , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo); 

ALTER TABLE WebsiteUserInfo  
SET (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.WebsiteUserInfoHistory));
GO

CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON); 
````

##<a name="step-2-run-your-workload-regularly"></a>Paso 2: Ejecutar la carga de trabajo con regularidad

La ventaja principal de tablas temporales es que no es necesario cambiar o ajustar su sitio Web de ninguna forma para realizar el seguimiento de cambios. Una vez creada, tablas temporales transparente conservar las versiones anteriores de fila cada vez que realice modificaciones en los datos. 

Para poder aprovechar el cambio automático de seguimiento para esta situación particular, vamos a solo actualizar columna **PagesVisited** cada vez cuando el usuario termina el sesión en el sitio Web:

````
UPDATE WebsiteUserInfo  SET [PagesVisited] = 5 
WHERE [UserID] = 1;
````

Es importante tener en cuenta que la consulta de actualización no es necesario saber el tiempo exacto cuando se produjo la operación real ni cómo se conservarán los datos históricos para futuros análisis. Ambos aspectos se administran automáticamente la base de datos de SQL Azure. El siguiente diagrama muestra cómo se generan datos de historial en cada actualización.

![TemporalArchitecture](./media/sql-database-temporal-tables/AzureTemporal5.png)

##<a name="step-3-perform-historical-data-analysis"></a>Paso 3: Realizar análisis de datos históricos

Ahora cuando se habilita el control de versiones de sistema temporal, análisis de datos históricos es una consulta lejos de usted. En este artículo, le ofrecemos algunos ejemplos esa dirección análisis escenarios - obtener todos los detalles, explorar diversas opciones presentadas con la cláusula [FOR SYSTEM_TIME](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_3) .

Para ver los 10 usuarios principales ordenados por el número de páginas web que visita a partir de hace una hora, ejecute esta consulta:

````
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
SELECT TOP 10 * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME AS OF @hourAgo
ORDER BY PagesVisited DESC
````

Puede modificar fácilmente esta consulta para analizar las visitas de sitio a partir de un día hace, hace un mes o en cualquier punto en el pasado que desee.

Para realizar análisis estadísticos básicos para el día anterior, utilice el siguiente ejemplo:

````
DECLARE @twoDaysAgo datetime2 = DATEADD(DAY, -2, SYSUTCDATETIME());
DECLARE @aDayAgo datetime2 = DATEADD(DAY, -1, SYSUTCDATETIME());

SELECT UserID, SUM (PagesVisited) as TotalVisitedPages, AVG (PagesVisited) as AverageVisitedPages,
MAX (PagesVisited) AS MaxVisitedPages, MIN (PagesVisited) AS MinVisitedPages,
STDEV (PagesVisited) as StDevViistedPages
FROM dbo.WebsiteUserInfo 
FOR SYSTEM_TIME BETWEEN @twoDaysAgo AND @aDayAgo
GROUP BY UserId
````

Para buscar actividades de un usuario específico, dentro de un período de tiempo, utilice la cláusula contenidos en:

````
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
DECLARE @twoHoursAgo datetime2 = DATEADD(HOUR, -2, SYSUTCDATETIME());
SELECT * FROM dbo.WebsiteUserInfo 
FOR SYSTEM_TIME CONTAINED IN (@twoHoursAgo, @hourAgo)
WHERE [UserID] = 1;
````

Visualización gráfico es especialmente útil para consultas temporales, tal como puede mostrar tendencias y patrones de uso en una intuitiva forma muy fácilmente:

![TemporalGraph](./media/sql-database-temporal-tables/AzureTemporal6.png)

##<a name="evolving-table-schema"></a>Esquema de la tabla en evolución

Normalmente, debe cambiar el esquema de la tabla temporal mientras realiza el desarrollo de aplicaciones. Para ello, simplemente ejecute regular ALTER TABLE instrucciones y base de datos de SQL Azure se adecuadamente propagar cambios a la tabla del historial. La siguiente secuencia de comandos muestra cómo puede agregar atributos adicionales para el seguimiento de:

````
/*Add new column for tracking source IP address*/
ALTER TABLE dbo.WebsiteUserInfo 
ADD  [IPAddress] varchar(128) NOT NULL CONSTRAINT DF_Address DEFAULT 'N/A';
````

Del mismo modo, puede cambiar la definición de columna mientras esté activa la carga de trabajo:

````
/*Increase the length of name column*/
ALTER TABLE dbo.WebsiteUserInfo 
    ALTER COLUMN  UserName nvarchar(256) NOT NULL;
````

Por último, puede quitar una columna que ya no necesite.

````
/*Drop unnecessary column */
ALTER TABLE dbo.WebsiteUserInfo 
    DROP COLUMN TemporaryColumn; 
````
    
Como alternativa, use última [SSDT](https://msdn.microsoft.com/library/mt204009.aspx) para cambiar el esquema de la tabla temporal mientras se está conectado a la base de datos (modo en línea) o como parte del proyecto de base de datos (modo sin conexión).

##<a name="controlling-retention-of-historical-data"></a>Control de retención de datos de historial

Con una versión sistema tablas temporales, la tabla del historial puede aumentar el tamaño de la base de datos más tablas normales. Una tabla de historial grande y creciente puede convertirse en un problema ambos debido a los costos de almacenamiento puros así como imponer un rendimiento impuestos en consultas temporal. Por lo tanto, desarrollar una directiva de retención de datos de administración de datos en la tabla Historial es un aspecto importante de planear y administrar el ciclo de vida de cada tabla temporal. Con la base de datos de SQL Azure, tiene los siguientes enfoques para administrar los datos históricos en la tabla temporal:

- [Partición de tablas](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_2)
- [Script de limpieza personalizado](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_3)

##<a name="next-steps"></a>Pasos siguientes

Para obtener información detallada sobre tablas temporales, consulte [la documentación de MSDN](https://msdn.microsoft.com/library/dn935015.aspx).
Visite el canal 9 para oír una [historia de éxito de implementación temporal reales de clientes](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) y ver una [demostración temporal de live](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016).
