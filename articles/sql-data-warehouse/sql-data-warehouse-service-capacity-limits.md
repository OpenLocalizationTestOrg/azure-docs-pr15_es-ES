<properties
   pageTitle="Límites de capacidad de almacenamiento de datos SQL | Microsoft Azure"
   description="Valores máximos de conexiones, bases de datos, tablas y consultas para el almacén de datos de SQL."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/01/2016"
   ms.author="sonyama;barbkess;jrj"/>

# <a name="sql-data-warehouse-capacity-limits"></a>Límites de capacidad de almacenamiento de datos de SQL

Las tablas siguientes contienen los valores máximos permitidos para los distintos componentes de almacén de datos de SQL Azure.


## <a name="workload-management"></a>Administración de carga de trabajo

| Categoría            | Descripción                                       | Valor máximo            |
| :------------------ | :------------------------------------------------ | :----------------- |
| [Unidades de almacén de datos (DWU)][]| Max DWU para un único almacén de datos de SQL | 6000               |
| [Unidades de almacén de datos (DWU)][]| Max DWU para un único servidor SQL         | 6000 predeterminada<br/><br/> De forma predeterminada, cada servidor de SQL server (por ejemplo, myserver.database.windows.net) tiene una cuota DTU de 45.000 que permite hasta DWU 6000. Esta cuota es simplemente un límite de seguridad. Puede aumentar su cuota [creando una incidencia de soporte técnico][] y seleccionar *cuota* como el tipo de solicitud.  Para calcular la DTU necesita, multiplique el 7.5 total que DWU es necesario. Puede ver su consumo DTU actual desde el módulo SQL server en el portal. Bases de datos pausados y anular pausados cuentan en la cuota DTU. |
| Conexión de base de datos | Sesiones abiertas simultáneas                          | 1024<br/><br/>Se admite un máximo de 1024 conexiones activas, cada una de las cuales puede enviar solicitudes para una base de datos de almacén de datos SQL al mismo tiempo. Tenga en cuenta que no hay límites en el número de consultas que realmente se pueden ejecutar simultáneamente. Cuando se supera el límite de simultaneidad, la solicitud entra en una cola interna donde espera que deben procesarse.|
| Conexión de base de datos | Memoria máxima para instrucciones preparadas            | 20 MB              |
| [Administración de carga de trabajo][] | Número máximo de consultas simultáneo                    | 32<br/><br/> De forma predeterminada, el almacén de datos de SQL puede ejecutar un máximo de 32 consultas simultáneas y colas restante consultas.<br/><br/>Cuando los usuarios están asignados a una clase de recursos superior o al almacén de datos de SQL está configurado con DWU baja, puede disminuir el nivel de simultaneidad. Algunas consultas, como las consultas DMV, siempre se pueden ejecutar.|
| [Tempdb][]          | Tamaño máximo de Tempdb                                | 399 GB por DW100. Por lo tanto en Tempdb DWU1000 tamaño 3,99 TB |


## <a name="database-objects"></a>Objetos de base de datos

| Categoría          | Descripción                                  | Valor máximo            |
| :---------------- | :------------------------------------------- | :----------------- |
| Base de datos          | Tamaño máximo                                     | 240 TB comprimidos en disco<br/><br/>Este espacio es independiente del espacio de tempdb o registro y, por tanto, este espacio está dedicado a tablas permanentes.  Compresión de columnstore agrupadas se calcula en 5 X.  Esta compresión permite la base de datos alcanzar aproximadamente 1 PB cuando todas las tablas están agrupada columnstore (el tipo de tabla de forma predeterminada).|
| Tabla             | Tamaño máximo                                     | 60 TB comprimido en disco   |
| Tabla             | Tablas por base de datos                          | 2 millones          |
| Tabla             | Columnas por tabla                            | 1024 columnas       |
| Tabla             | Bytes por columna                             | Depende de la columna [tipo de datos][].  Límite es 8000 para los tipos de datos char, 4000 para nvarchar o 2 GB para tipos de datos MAX.|
| Tabla             | Bytes por fila, tamaño definido                  | 8060 bytes<br/><br/>Se calcula el número de bytes por fila de la misma manera como está para SQL Server con la compresión de la página. Como SQL Server, almacén de datos SQL admite el almacenamiento de desbordamiento de fila que permite **columnas de longitud variable** enviarse desactivar fila. Cuando las filas de longitud variable se insertan desactivar fila, solo 24 bytes raíz se almacena en el registro principal. Para obtener más información, vea el artículo MSDN de [desbordamiento de fila datos superiores a 8 KB][] .|
| Tabla             | Particiones por tabla                    | 15.000<br/><br/>De alto rendimiento, se recomienda minimizando el número de particiones necesita mientras mantiene la compatibilidad con los requisitos empresariales. A medida que crece el número de particiones, la carga de las operaciones de lenguaje de definición de datos (DDL) y lenguaje de manipulación de datos (DML) crece y hace que el rendimiento más lento.|
| Tabla             | Caracteres por valor de límite de partición.| 4000 |
| Índice             | Índices no agrupados por tabla.        | 999<br/><br/>Se aplica a las tablas de rowstore solo.|
| Índice             | Índices agrupados por tabla.            | 1<br><br/>Se aplica a las tablas rowstore y columnstore.|
| Índice             | Tamaño de la clave de índice.                          | 900 bytes.<br/><br/>Se aplica a los índices de rowstore solo.<br/><br/>Si los datos existentes en las columnas no supere 900 bytes cuando se crea el índice, se pueden crear índices en columnas varchar con un tamaño máximo de más de 900 bytes. Sin embargo, insertar más adelante o acciones de actualización en las columnas que provocan el tamaño total supere 900 bytes se producirá un error.|
| Índice             | Columnas de clave por índice.                   | 16<br/><br/>Se aplica a los índices de rowstore solo. Índices agrupados columnstore incluyen todas las columnas.|
| Estadísticas        | Tamaño de los valores de columna combinada.      | 900 bytes.         |
| Estadísticas        | Columnas por objeto estadísticas.           | 32                 |
| Estadísticas        | Estadísticas creadas en columnas por tabla. | 30.000            |
| Procedimientos almacenados | Número máximo de niveles de anidamiento.               | 8                 |
| Vista              | Columnas por vista                         | 1.024             |


## <a name="loads"></a>Cargas

| Categoría          | Descripción                                  | Valor máximo            |
| :---------------- | :------------------------------------------- | :----------------- |
| Carga de Polybase    | Bytes por fila                                | 32.768<br/><br/>Polybase cargas están limitadas a cargar filas ambos menor que 32K y no se pueden cargar VARCHR(MAX), nvarchar (max) o varbinary (max).  Aunque este límite existe hoy, se quitarán bastante pronto.<br/><br/>


## <a name="queries"></a>Consultas

| Categoría          | Descripción                                  | Valor máximo            |
| :---------------- | :------------------------------------------- | :----------------- |
| Consulta             | Consultas en cola en tablas de usuario.               | 1000               |
| Consulta             | Consultas simultáneas en vistas del sistema.          | 100                |
| Consulta             | Consultas en cola en vistas del sistema               | 1000               |
| Consulta             | Parámetros máximos                           | 2098               |
| Proceso por lotes             | Tamaño máximo                                 | 65, 536 * 4096        |
| Resultados de la selección    | Columnas por fila                              | 4096<br/><br/>Nunca pueden tener más de 4096 columnas por fila en el resultado de la selección. No hay ninguna garantía que siempre puede tener 4096. Si el plan de consulta requiere una tabla temporal, pueden aplicar las columnas de 1024 por tabla máximo.|
| SELECCIONE            | Subconsultas anidadas                            | 32<br/><br/>Nunca pueden tener más de 32 subconsultas anidadas en una instrucción SELECT. No hay ninguna garantía que siempre puede tener 32. Por ejemplo, una combinación puede introducir una subconsulta en el plan de consulta. El número de subconsultas también puede estar limitado por la memoria disponible.|
| SELECCIONE            | Columnas por cada combinación                             | 1024 columnas<br/><br/>Nunca pueden tener más de 1024 columnas en la combinación. No hay ninguna garantía que siempre puede tener 1024. Si el plan de combinaciones requiere una tabla temporal con más columnas que el resultado de la combinación, el límite de 1024 se aplica a la tabla temporal. |
| SELECCIONE            | Bytes por grupo por columnas.                  | 8060<br/><br/>Las columnas de la cláusula GROUP BY pueden tener un máximo de 8060 bytes.|
| SELECCIONE            | Bytes por orden por columnas                   | 8060 bytes.<br/><br/>Las columnas de la cláusula ORDER BY pueden tener un máximo de 8060 bytes.|
| Identificadores y constantes por instrucción | Número de identificadores que se hace referencia y constantes. | 65535<br/><br/>Almacén de datos SQL limita el número de identificadores y constantes que pueden estar contenidas en una única expresión de una consulta. Este límite es 65535. Superior a este número Obtiene un error de SQL Server 8632. Para obtener más información, vea [error interno: se ha alcanzado el límite de servicios de expresión][].|


## <a name="metadata"></a>Metadatos

| Vista del sistema                        | Número máximo de filas |
| :--------------------------------- | :------------|
| Sys.dm_pdw_component_health_alerts | 10.000       |
| Sys.dm_pdw_dms_cores               | 100          |
| Sys.dm_pdw_dms_workers             | Número total de los trabajadores DMS para 1000 más reciente solicitudes de SQL. |
| Sys.dm_pdw_errors                  | 10.000       |
| Sys.dm_pdw_exec_requests           | 10.000       |
| Sys.dm_pdw_exec_sessions           | 10.000       |
| Sys.dm_pdw_request_steps           | Número total de pasos para las solicitudes SQL de 1000 más recientes que se almacenan en sys.dm_pdw_exec_requests. |
| Sys.dm_pdw_os_event_logs           | 10.000       |
| Sys.dm_pdw_sql_requests            | Las solicitudes SQL de 1000 más recientes se almacenan en sys.dm_pdw_exec_requests. |


## <a name="next-steps"></a>Pasos siguientes
Para obtener más información de referencia, vea [información general de referencia de almacén de datos de SQL][].

<!--Image references-->

<!--Article references-->
[Unidades de almacén de datos (DWU)]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[Información general de referencia de almacén de datos SQL]: ./sql-data-warehouse-overview-reference.md
[Administración de carga de trabajo]: ./sql-data-warehouse-develop-concurrency.md
[Tempdb]: ./sql-data-warehouse-tables-temporary.md
[tipo de datos]: ./sql-data-warehouse-tables-data-types.md
[crear una incidencia de soporte técnico]: /sql-data-warehouse-get-started-create-support-ticket.md

<!--MSDN references-->
[Datos de desbordamiento de la fila superior a 8 KB]: https://msdn.microsoft.com/library/ms186981.aspx
[Error interno: se ha alcanzado el límite de servicios de expresión]: https://support.microsoft.com/kb/913050
