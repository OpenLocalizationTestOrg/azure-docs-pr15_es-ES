<properties
    pageTitle="Todos los temas de servicio de almacén de datos SQL | Microsoft Azure"
    description="Tabla de todos los temas para el servicio de Azure denominada almacén de datos de SQL que existe en http://azure.microsoft.com/documentation/articles/, el título y la descripción."
    services="sql-data-warehouse"
    documentationCenter=""
    authors="barbkess"
    manager="jhubbard"
    editor="MightyPen"/>

<tags
    ms.service="sql-data-warehouse"
    ms.workload="sql-data-warehouse"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="barbkess"/>


# <a name="all-topics-for-azure-sql-data-warehouse-service"></a>Todos los temas de servicio de almacén de datos de SQL Azure

En este tema se enumeran todos los temas que se aplican directamente al servicio de **Almacén de datos SQL** de Azure. Puede buscar esta página Web para las palabras clave mediante **CTRL+f**, para buscar los temas de interés actual.




## <a name="new"></a>Nuevo

| &nbsp; | Título | Descripción |
| --: | :-- | :-- |
| 1 | [Realizar copias de seguridad del almacén de datos SQL](sql-data-warehouse-backups.md) | Obtenga información sobre las copias de seguridad de base de datos integrados de almacén de datos de SQL que le permiten restaurar un almacén de datos de SQL Azure en un punto de restauración o una región geográfica distinta. |


## <a name="updated-articles-sql-data-warehouse"></a>Artículos actualizados, almacén de datos SQL

En esta sección se enumera los artículos que se han actualizado recientemente, donde la actualización fue grande o significativa. Para cada artículo actualizado, se muestra un fragmento de código aproximada del texto agregado descuento. Los artículos se actualizaron dentro del intervalo de fechas de **2016-08-22** a **2016-10-05**.

| &nbsp; | Artículo | Texto actualizado, fragmento de código | Se actualiza cuando |
| --: | :-- | :-- | :-- |
| 2 | [Cargar datos de almacenamiento de blobs de Windows Azure en el almacén de datos de SQL (PolyBase)](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) | /-Para realizar un seguimiento de archivos y bytes r.command SELECT, s.request_id, r.status, count (distinct input_name) como nbr_files, sumar (s.bytes_processed) / 1024/1024 como gb_processed de sys.dm_pdw_exec_requests r combinación interna sys.dm_pdw_dms_external_work s en r.request_id = s.request_id dónde r. etiqueta = ' CTAS: cso de carga. DimProduct ' r o. etiqueta = ' CTAS: cso de carga. GROUP BY r.command de FactOnlineSales, s.request_id, r.status ORDER BY nbr_files desc, gb_processed desc;  | 2016-09-07 |
| 3 | [Restauración de almacén de datos SQL](sql-data-warehouse-restore-database-overview.md) | **¿Puedo restaurar un almacén de datos en pausa?** Para restaurar un almacén de datos está en pausa, debe primero ponerlo en línea. Una vez que el almacén de datos es volver a conectarse, tiene siete días de puntos de restauración para elegir. **Restaurar a una región geográfica redundantes** Si está utilizando el almacenamiento geo redundantes, puede restaurar el almacén de datos a su centro de datos pareja en una región geográfica distinta. El almacén de datos se restaura desde la última copia de seguridad diaria. **Restaurar la escala de tiempo** Puede restaurar una base de datos en cualquier punto de restauración dentro de los siete últimos días. Instantáneas iniciar cada horas de cuatro a ocho y están disponibles para los siete días. Cuando una instantánea es más de siete días, que expira y su punto de restauración ya no está disponible. **Restaurar los costos** El gasto de almacenamiento para el almacén de datos restaurados se cobra a la tasa de almacenamiento de Azure Premium. Si se detiene un almacén de datos restaurado, se cargan de almacenamiento a la tasa de almacenamiento de Azure Premium. La ventaja de pausar es que no es cargo | 29-09-2016 |





## <a name="get-started"></a>Introducción

| &nbsp; | Título | Descripción |
| --: | :-- | :-- |
| 4 | [Autenticación al almacén de datos SQL Azure](sql-data-warehouse-authentication.md) | Autenticación de Active Directory (AAD) y SQL Server Azure al almacén de datos de SQL Azure. |
| 5 | [Procedimientos recomendados para el almacenamiento de datos de SQL Azure](sql-data-warehouse-best-practices.md) | Recomendaciones y prácticas recomendadas que debe saber que desarrollar soluciones de almacenamiento de datos de SQL Azure. Le ayudará a tener éxito. |
| 6 | [Controladores de almacén de datos SQL Azure](sql-data-warehouse-connection-strings.md) | Cadenas de conexión y los controladores de almacén de datos SQL |
| 7 | [Conectarse al almacén de datos SQL Azure](sql-data-warehouse-connect-overview.md) | Cómo encontrar el servidor de nombre y cadena de conexión para el almacén de datos de SQL Azure |
| 8 | [Analizar los datos con el aprendizaje Azure](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md) | Use el aprendizaje del equipo de Azure para crear una máquina predictiva aprendizaje modelo basado en los datos almacenados en el almacén de datos de SQL Azure. |
| 9 | [Almacén de datos de SQL Azure (sqlcmd) de la consulta](sql-data-warehouse-get-started-connect-sqlcmd.md) | Consultar el almacén de datos de SQL Azure con la herramienta de línea de comandos sqlcmd. |
| 10 | [Crear una base de datos de almacén de datos SQL mediante Transact-SQL (TSQL)](sql-data-warehouse-get-started-create-database-tsql.md) | Obtenga información sobre cómo crear un almacén de datos de SQL Azure con TSQL |
| 11 | [Cómo crear una incidencia de soporte técnico para el almacén de datos SQL](sql-data-warehouse-get-started-create-support-ticket.md) | Cómo crear una incidencia de soporte técnico en el almacén de datos de SQL Azure. |
| 12 | [Cargar los datos con el generador de datos de Azure](sql-data-warehouse-get-started-load-with-azure-data-factory.md) | Más información sobre cómo cargar los datos con el generador de datos de Azure |
| 13 | [Cargar los datos con PolyBase en el almacén de datos de SQL](sql-data-warehouse-get-started-load-with-polybase.md) | Obtenga información sobre qué es PolyBase y cómo usarla para escenarios de almacenamiento de datos. |
| 14 | [Crear un almacén de datos SQL Azure](sql-data-warehouse-get-started-provision.md) | Aprenda a crear un almacén de datos de SQL Azure en el portal de Azure |
| 15 | [Crear almacén de datos de SQL con PowerShell](sql-data-warehouse-get-started-provision-powershell.md) | Crear almacén de datos SQL con PowerShell |
| 16 | [Visualizar datos con Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md) | Visualizar datos de almacén de datos SQL con Power BI |
| 17 | [Almacén de datos SQL Azure de consulta (Visual Studio)](sql-data-warehouse-query-visual-studio.md) | Almacén de datos SQL de consulta con Visual Studio. |



## <a name="develop"></a>Desarrollar

| &nbsp; | Título | Descripción |
| --: | :-- | :-- |
| 18 | [Optimización de transacciones de almacén de datos SQL](sql-data-warehouse-develop-best-practices-transactions.md) | Instrucciones de prácticas recomendadas sobre cómo escribir actualizaciones eficaz de transacciones en el almacén de datos de SQL Azure |
| 19 | [Administración de simultaneidad y la carga de trabajo en el almacén de datos de SQL](sql-data-warehouse-develop-concurrency.md) | Comprender la gestión de simultaneidad y la carga de trabajo en el almacén de datos de SQL Azure para desarrollar soluciones. |
| 20 | [Crear tabla como Select (CTAS) en el almacén de datos SQL](sql-data-warehouse-develop-ctas.md) | Sugerencias para codificar con create table como instrucción select de (CTAS) en el almacén de datos de SQL Azure para desarrollar soluciones. |
| 21 | [SQL dinámico en el almacén de datos SQL](sql-data-warehouse-develop-dynamic-sql.md) | Sugerencias para usar SQL dinámico en el almacén de datos de SQL Azure para desarrollar soluciones. |
| 22 | [Agrupar por opciones en el almacén de datos de SQL](sql-data-warehouse-develop-group-by-options.md) | Sugerencias para implementar el grupo de opciones en el almacén de datos de SQL Azure para desarrollar soluciones. |
| 23 | [Usar etiquetas a las consultas de instrumento en el almacén de datos de SQL](sql-data-warehouse-develop-label.md) | Sugerencias para usar las etiquetas a las consultas de instrumento en el almacén de datos de SQL Azure para desarrollar soluciones. |
| 24 | [Bucles en el almacén de datos SQL](sql-data-warehouse-develop-loops.md) | Sugerencias para reemplazar cursores en el almacén de datos de SQL Azure para desarrollar soluciones y bucles Transact-SQL. |
| 25 | [Procedimientos almacenados en el almacén de datos de SQL](sql-data-warehouse-develop-stored-procedures.md) | Sugerencias para implementar procedimientos almacenados en el almacén de datos de SQL Azure para desarrollar soluciones. |
| 26 | [Transacciones en el almacén de datos SQL](sql-data-warehouse-develop-transactions.md) | Sugerencias para implementar transacciones en el almacén de datos de SQL Azure para desarrollar soluciones. |
| 27 | [Esquemas definidos por el usuario en el almacén de datos de SQL](sql-data-warehouse-develop-user-defined-schemas.md) | Sugerencias para usar los esquemas de Transact-SQL en el almacén de datos de SQL Azure para desarrollar soluciones. |
| 28 | [Asignar variables en el almacén de datos de SQL](sql-data-warehouse-develop-variable-assignment.md) | Sugerencias para asignar variables de Transact-SQL en el almacén de datos de SQL Azure para desarrollar soluciones. |
| 29 | [Vistas en el almacén de datos SQL](sql-data-warehouse-develop-views.md) | Sugerencias para usar vistas de Transact-SQL en el almacén de datos de SQL Azure para desarrollar soluciones. |
| 30 | [Decisiones de diseño y técnicas de codificación para el almacén de datos de SQL](sql-data-warehouse-overview-develop.md) | Conceptos de desarrollo, las decisiones de diseño, recomendaciones y técnicas de codificación para el almacén de datos de SQL. |



## <a name="manage"></a>Administrar

| &nbsp; | Título | Descripción |
| --: | :-- | :-- |
| 31 | [Administrar la potencia de la informática en el almacén de datos de SQL Azure (información general)](sql-data-warehouse-manage-compute-overview.md) | Escala de rendimiento capacidades en el almacén de datos de SQL Azure. Cambiar la escala ajustando DWUs o pausar y reanudar recursos de cálculo para ahorrar costos. |
| 32 | [Administrar la potencia de la informática en el almacén de datos de SQL Azure (portal de Azure)](sql-data-warehouse-manage-compute-portal.md) | Azure tareas del portal administrar potencia. Recursos de cálculo escala ajustando DWUs. O bien, pausar y reanudar calculan recursos para ahorrar costos. |
| 33 | [Administrar la potencia de la informática en el almacén de datos de SQL Azure (PowerShell)](sql-data-warehouse-manage-compute-powershell.md) | Tareas de PowerShell para administrar potencia. Recursos de cálculo escala ajustando DWUs. O bien, pausar y reanudar calculan recursos para ahorrar costos. |
| 34 | [Administrar la potencia de la informática en el almacén de datos de SQL Azure (REST)](sql-data-warehouse-manage-compute-rest-api.md) | Tareas de PowerShell para administrar potencia. Recursos de cálculo escala ajustando DWUs. O bien, pausar y reanudar calculan recursos para ahorrar costos. |
| 35 | [Administrar la potencia de la informática en el almacén de datos de SQL Azure (T-SQL)](sql-data-warehouse-manage-compute-tsql.md) | Tareas de Transact-SQL (T-SQL) para el rendimiento de escalado ajustando DWUs. Ahorrar costos ajustando durante la horas no pico. |
| 36 | [Supervisar la carga de trabajo con DMV](sql-data-warehouse-manage-monitor.md) | Obtenga información sobre cómo supervisar la carga de trabajo con DMV. |
| 37 | [Administrar bases de datos en el almacén de datos de SQL Azure](sql-data-warehouse-overview-manage.md) | Descripción general de la administración de bases de datos de almacén de datos de SQL. Incluye herramientas de administración, DWUs y rendimiento de escalado, solución de problemas de rendimiento de la consulta, establecer directivas de seguridad y restaurar una base de datos de daños en los datos o de una interrupción regional. |
| 38 | [Supervisar las consultas de usuario en el almacén de datos de SQL Azure](sql-data-warehouse-overview-manage-user-queries.md) | Información general sobre las consideraciones, prácticas recomendadas y tareas para supervisar las consultas de usuario en el almacén de datos de SQL Azure |
| 39 | [Restauración de almacén de datos SQL](sql-data-warehouse-restore-database-overview.md) | Descripción general de las opciones de restauración de la base de datos para recuperar una base de datos en el almacén de datos de SQL Azure. |
| 40 | [Restaurar un almacén de datos SQL Azure (Portal)](sql-data-warehouse-restore-database-portal.md) | Azure portal las tareas para restaurar un almacén de datos de SQL Azure. |
| 41 | [Restaurar un almacén de datos SQL Azure (PowerShell)](sql-data-warehouse-restore-database-powershell.md) | Tareas de PowerShell para restaurar un almacén de datos de SQL Azure. |
| 42 | [Restaurar un almacén de datos SQL Azure (API de REST)](sql-data-warehouse-restore-database-rest-api.md) | API de REST las tareas para restaurar un almacén de datos de SQL Azure. |



## <a name="tables-and-indexes"></a>Tablas y los índices

| &nbsp; | Título | Descripción |
| --: | :-- | :-- |
| 43 | [Tipos de datos de tablas en el almacén de datos de SQL](sql-data-warehouse-tables-data-types.md) | Introducción a los tipos de datos de tablas de almacén de datos de SQL Azure. |
| 44 | [Distribución de tablas en el almacén de datos de SQL](sql-data-warehouse-tables-distribute.md) | Introducción a distribuir tablas en el almacén de datos de SQL Azure. |
| 45 | [Indización de tablas en el almacén de datos de SQL](sql-data-warehouse-tables-index.md) | Introducción a la tabla de indización en el almacén de datos de SQL Azure. |
| 46 | [Información general de las tablas en el almacén de datos de SQL](sql-data-warehouse-tables-overview.md) | Introducción a las tablas de almacén de datos de SQL Azure. |
| 47 | [Partición de tablas en el almacén de datos de SQL](sql-data-warehouse-tables-partition.md) | Introducción a las particiones de tablas en el almacén de datos de SQL Azure. |
| 48 | [Administración de estadísticas de tablas en el almacén de datos de SQL](sql-data-warehouse-tables-statistics.md) | Introducción a las estadísticas de tablas en el almacén de datos de SQL Azure. |
| 49 | [Tablas temporales en el almacén de datos de SQL](sql-data-warehouse-tables-temporary.md) | Introducción a las tablas temporales en el almacén de datos de SQL Azure. |



## <a name="integrate"></a>Integrar

| &nbsp; | Título | Descripción |
| --: | :-- | :-- |
| 50 | [Usar el generador de datos de Azure con almacén de datos SQL](sql-data-warehouse-integrate-azure-data-factory.md) | Sugerencias para usar el generador de datos de Azure (ADF) con el almacenamiento de datos de SQL Azure para desarrollar soluciones. |
| 51 | [Usar el aprendizaje Azure con el almacén de datos SQL](sql-data-warehouse-integrate-azure-machine-learning.md) | Tutorial para usar el aprendizaje Azure con el almacén de datos de SQL Azure para desarrollar soluciones. |
| 52 | [Usar el análisis de secuencia de Azure con el almacén de datos SQL](sql-data-warehouse-integrate-azure-stream-analytics.md) | Sugerencias para usar el análisis de secuencia de Azure con el almacén de datos de SQL Azure para desarrollar soluciones. |
| 53 | [Usar Power BI con almacén de datos SQL](sql-data-warehouse-integrate-power-bi.md) | Sugerencias para usar Power BI con el almacén de datos de SQL Azure para desarrollar soluciones. |
| 54 | [Aprovechar otros servicios de almacenamiento de datos de SQL](sql-data-warehouse-overview-integrate.md) | Herramientas y asociados con las soluciones que se integran en el almacén de datos de SQL.  |



## <a name="load"></a>Carga

| &nbsp; | Título | Descripción |
| --: | :-- | :-- |
| 55 | [Cargar datos de almacenamiento de blobs de Windows Azure en el almacén de datos de SQL Azure (generador de datos de Azure)](sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md) | Más información sobre cómo cargar los datos con el generador de datos de Azure |
| 56 | [Cargar datos de almacenamiento de blobs de Windows Azure en el almacén de datos de SQL (PolyBase)](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) | Obtenga información sobre cómo usar PolyBase para cargar los datos desde el almacenamiento de blobs de Windows Azure en el almacén de datos de SQL. Cargar algunas tablas de datos públicos en el esquema de almacén de datos de Contoso minorista. |
| 57 | [Cargar datos de SQL Server en el almacén de datos de SQL Azure (AZCopy)](sql-data-warehouse-load-from-sql-server-with-azcopy.md) | Utiliza bcp para exportar datos de SQL Server a archivos sin formato, AZCopy para importar datos con el almacenamiento de blobs de Windows Azure y PolyBase para recopilar los datos en el almacén de datos de SQL Azure. |
| 58 | [Cargar datos de SQL Server en el almacén de datos de SQL Azure (archivos planos)](sql-data-warehouse-load-from-sql-server-with-bcp.md) | Tamaño de datos pequeños, usa bcp para exportar datos de SQL Server a archivos planos e importar los datos directamente en el almacén de datos de SQL Azure. |
| 59 | [Cargar datos de SQL Server en almacén de datos de SQL Azure (SSIS)](sql-data-warehouse-load-from-sql-server-with-integration-services.md) | Muestra cómo crear un paquete de SQL Server Integration Services (SSIS) para mover los datos desde una amplia variedad de orígenes de datos al almacén de datos de SQL. |
| 60 | [Cargar los datos con PolyBase en el almacén de datos de SQL](sql-data-warehouse-load-from-sql-server-with-polybase.md) | Utiliza bcp para exportar datos de SQL Server a archivos sin formato, AZCopy para importar datos con el almacenamiento de blobs de Windows Azure y PolyBase para recopilar los datos en el almacén de datos de SQL Azure. |
| 61 | [Guía para el uso de PolyBase en el almacén de datos de SQL](sql-data-warehouse-load-polybase-guide.md) | Directrices y recomendaciones para utilizar PolyBase en escenarios de almacén de datos de SQL. |
| 62 | [Cargar datos de ejemplo en el almacén de datos SQL](sql-data-warehouse-load-sample-databases.md) | Cargar datos de ejemplo en el almacén de datos SQL |
| 63 | [Cargar los datos con bcp](sql-data-warehouse-load-with-bcp.md) | Descubra qué bcp y cómo usarla para escenarios de almacenamiento de datos. |
| 64 | [Cargar datos en el almacén de datos de SQL Azure](sql-data-warehouse-overview-load.md) | Obtenga información sobre los escenarios comunes para datos cargar en el almacén de datos de SQL. Incluyen la utilización de PolyBase, almacenamiento de blobs de Windows Azure, archivos sin formato y envío de disco. También puede usar herramientas de terceros. |



## <a name="migrate"></a>Migrar

| &nbsp; | Título | Descripción |
| --: | :-- | :-- |
| 65 | [Migrar el código SQL al almacén de datos de SQL](sql-data-warehouse-migrate-code.md) | Sugerencias para migrar el código SQL al almacén de datos de SQL Azure para desarrollar soluciones. |
| 66 | [Migrar los datos](sql-data-warehouse-migrate-data.md) | Sugerencias para migrar los datos al almacén de datos de SQL Azure para desarrollar soluciones. |
| 67 | [Herramienta de migración de almacén de datos (vista preliminar)](sql-data-warehouse-migrate-migration-utility.md) | Migrar al almacén de datos SQL. |
| 68 | [Migrar su esquema al almacén de datos de SQL](sql-data-warehouse-migrate-schema.md) | Sugerencias para migrar su esquema al almacén de datos de SQL Azure para desarrollar soluciones. |
| 69 | [Migrar la solución al almacén de datos de SQL](sql-data-warehouse-overview-migrate.md) | Guía de migración para poner la solución a la plataforma de almacenamiento de datos de SQL Azure. |



## <a name="partners"></a>Socios

| &nbsp; | Título | Descripción |
| --: | :-- | :-- |
| 70 | [Socios de inteligencia empresarial de almacén de datos SQL](sql-data-warehouse-partner-business-intelligence.md) | Listas de socios de inteligencia empresarial de terceros con las soluciones que admite el almacenamiento de datos de SQL. |
| 71 | [Socios de integración de datos de almacén de datos SQL](sql-data-warehouse-partner-data-integration.md) | Listas de terceros asociados con las soluciones de integración de datos que admite el almacenamiento de datos de SQL Azure. |
| 72 | [Socios de administración de datos de almacén de datos SQL](sql-data-warehouse-partner-data-management.md) | Listas de socios de administración de datos de terceros con las soluciones que admite el almacenamiento de datos de SQL. |



## <a name="reference"></a>Referencia

| &nbsp; | Título | Descripción |
| --: | :-- | :-- |
| 73 | [Temas de referencia para el almacén de datos SQL](sql-data-warehouse-overview-reference.md) | Vínculos de contenido de referencia para el almacén de datos de SQL. |
| 74 | [Cmdlets de PowerShell y las API de REST de almacén de datos SQL](sql-data-warehouse-reference-powershell-cmdlets.md) | Encontrar los cmdlets de PowerShell superiores para el almacén de datos de SQL Azure incluido cómo pausar y reanudar una base de datos. |
| 75 | [Elementos de idioma](sql-data-warehouse-reference-tsql-language-elements.md) | Lista de vínculos a contenido de referencia para los elementos del lenguaje Transact-SQL utilizado para el almacén de datos de SQL. |
| 76 | [Temas de Transact-SQL](sql-data-warehouse-reference-tsql-statements.md) | Vínculos a contenido de referencia para los temas de Transact-SQL usado por el almacén de datos de SQL. |
| 77 | [Vistas del sistema](sql-data-warehouse-reference-tsql-system-views.md) | Vínculos a sistema ve el contenido para el almacén de datos de SQL. |



## <a name="security"></a>Seguridad

| &nbsp; | Título | Descripción |
| --: | :-- | :-- |
| 78 | [Clientes de nivel inferior de almacenamiento de datos SQL - soporte técnico para los datos de auditoría y dinámicos Masking](sql-data-warehouse-auditing-downlevel-clients.md) | Obtenga más información sobre el almacén de datos SQL clientes compatibilidad con datos de auditoría |
| 79 | [Auditoría en el almacén de datos SQL Azure](sql-data-warehouse-auditing-overview.md) | Introducción a la auditoría en el almacén de datos de SQL Azure |
| 80 | [Introducción a cifrado con datos transparente (TDE) en el almacén de datos de SQL](sql-data-warehouse-encryption-tde.md) | Cifrado de datos transparente (TDE) en el almacén de datos SQL |
| 81 | [Empezar a trabajar con el cifrado de datos transparente (TDE)](sql-data-warehouse-encryption-tde-tsql.md) | Cifrado de datos transparente (TDE) en el almacén de datos SQL (T-SQL) |
| 82 | [Proteger una base de datos en el almacén de datos de SQL](sql-data-warehouse-overview-manage-security.md) | Sugerencias para proteger una base de datos en el almacén de datos de SQL Azure para desarrollar soluciones. |



## <a name="miscellaneous"></a>Varios

| &nbsp; | Título | Descripción |
| --: | :-- | :-- |
| 83 | [Instalar 2015 de Visual Studio y SSDT para el almacén de datos SQL](sql-data-warehouse-install-visual-studio.md) | Instalar Visual Studio y las herramientas de desarrollo de SQL Server (SSDT) para el almacén de datos SQL Azure |
| 84 | [Migración a los detalles de almacenamiento Premium](sql-data-warehouse-migrate-to-premium-storage.md) | Instrucciones para migrar un almacén de datos de SQL existente con el almacenamiento de premium |
| 85 | [Introducción a la detección de amenazas](sql-data-warehouse-security-threat-detection.md) | Cómo empezar a trabajar con detección de amenazas |
| 86 | [Límites de capacidad de almacenamiento de datos de SQL](sql-data-warehouse-service-capacity-limits.md) | Valores máximos de conexiones, bases de datos, tablas y consultas para el almacén de datos de SQL. |
| 87 | [Solución de problemas de almacén de datos SQL Azure](sql-data-warehouse-troubleshoot.md) | Solución de problemas de almacén de datos SQL Azure. |

