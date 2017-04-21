   <properties
   pageTitle="Cargar datos en el almacén de datos de SQL Azure | Microsoft Azure"
   description="Obtenga información sobre los escenarios comunes para datos cargar en el almacén de datos de SQL. Incluyen la utilización de PolyBase, almacenamiento de blobs de Windows Azure, archivos sin formato y envío de disco. También puede usar herramientas de terceros."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/12/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# <a name="load-data-into-azure-sql-data-warehouse"></a>Cargar datos en el almacén de datos de SQL Azure

Un resumen de las opciones de escenario y recomendaciones para cargar datos en el almacén de datos de SQL.

La parte más difícil de cargar datos normalmente es preparar los datos para la carga. Azure simplifica la carga con el almacenamiento de blobs de Windows Azure como un almacén de datos común para muchos de los servicios y usando el generador de datos de Azure para organizar la comunicación y movimiento de datos entre los servicios de Azure. Estos procesos se integran con la tecnología de PolyBase que usa procesamiento enormemente paralelo (MPP) para cargar datos en paralelo de almacenamiento de blobs de Windows Azure en el almacén de datos de SQL. 

Para obtener tutoriales que se cargan las bases de datos de ejemplo, vea [cargar bases de datos de ejemplo][].

## <a name="load-from-azure-blob-storage"></a>Cargar desde el almacenamiento de blobs de Windows Azure
Importar datos en el almacén de datos SQL de la forma más rápida es usar PolyBase para cargar los datos desde el almacenamiento de blobs de Windows Azure. PolyBase utiliza diseño de procesamiento enormemente paralelo (MPP) del almacén de datos de SQL para cargar datos en paralelo desde el almacenamiento de blobs de Windows Azure. Para usar PolyBase, puede usar comandos SQL T o una canalización del generador de datos de Azure.

### <a name="1-use-polybase-and-t-sql"></a>1. Utilice PolyBase y T-SQL

Resumen del proceso de carga:

2. Aplicar formato UTF-8 a los datos ya PolyBase no admite actualmente UTF-16.
2. Mover los datos al almacenamiento de blobs de Windows Azure y almacenar en archivos de texto.
3. Configurar los objetos externos en el almacén de datos de SQL para definir la ubicación y el formato de los datos
4. Ejecutar un comando SQL T para cargar los datos en paralelo en una nueva tabla de base de datos.

<!-- 5. Schedule and run a loading job. --> 

Para obtener un tutorial, vea [cargar datos desde el almacenamiento de blobs de Windows Azure al almacén de datos de SQL (PolyBase)][].

### <a name="2-use-azure-data-factory"></a>2. usar el generador de datos de Azure

Para que una forma más sencilla de usar PolyBase, puede crear una canalización de generador de datos de Azure que usa PolyBase para cargar los datos desde el almacenamiento de blobs de Windows Azure en el almacén de datos de SQL. Esto es rápido para configurar ya no necesita definir los objetos T SQL. Si necesita consultar los datos externos sin importarlo, use T SQL. 

Resumen del proceso de carga:

2. Aplicar formato UTF-8 a los datos ya PolyBase no admite actualmente UTF-16.
2. Mover los datos al almacenamiento de blobs de Windows Azure y almacenar en archivos de texto.
3. Crear una canalización de generador de datos de Azure para recopilar los datos. Use la opción PolyBase.
4. Programar y ejecutar la canalización.

Para obtener un tutorial, vea [cargar datos desde el almacenamiento de blobs de Windows Azure al almacén de datos de SQL (generador de datos de Azure)][].


## <a name="load-from-sql-server"></a>Cargar desde SQL Server
Para cargar los datos de SQL Server al almacén de datos de SQL puede utilizar Integration Services (SSIS), transferencia de archivos sin formato o discos de envío a Microsoft. Carga de lectura para ver un resumen de los diferentes procesos y vínculos a los tutoriales.

Para planear una migración total de los datos de SQL Server al almacén de datos de SQL, vea la [información general de migración][]. 

### <a name="use-integration-services-ssis"></a>Utilizar Integration Services (SSIS)
Si ya está usando los paquetes de Integration Services (SSIS) para cargar en SQL Server, puede actualizar los paquetes para usar SQL Server como el origen y el almacén de datos SQL como destino. Esto es rápida y fácil de hacer, y es una buena opción si no desea migrar su proceso de carga para usar los datos ya en la nube. La desventaja es que la carga será más lenta que utilizar PolyBase porque este SSIS no realiza la carga en paralelo.

Resumen del proceso de carga:

1. Revisar el paquete de Integration Services para que apunten a la instancia de SQL Server para el origen y destino de la base de datos de almacén de datos de SQL.
2. Migrar su esquema al almacén de datos de SQL, si aún no está allí.
3. Cambiar la asignación en los paquetes de usar los tipos de datos que son compatibles con el almacén de datos de SQL.
3. Programar y ejecutar el paquete.

Para obtener un tutorial, vea [cargar datos desde SQL Server al almacén de datos de SQL Azure (SSIS)][].

### <a name="use-azcopy-recommended-for--10-tb-data"></a>Usar AZCopy (recomendado para < 10 TB de datos)
Si el tamaño de datos es < 10 TB, exportar los datos de SQL Server a archivos sin formato, copie los archivos al almacenamiento de blobs de Windows Azure y luego use PolyBase para cargar los datos en el almacén de datos SQL

Resumen del proceso de carga:

1. Use la herramienta de línea de comandos bcp para exportar datos de SQL Server a archivos sin formato.
2. Use la utilidad de línea de comandos AZCopy para copiar los datos de archivos sin formato para el almacenamiento de blobs de Windows Azure.
3. Utilice PolyBase para cargar en el almacén de datos de SQL.

Para obtener un tutorial, vea [cargar datos desde el almacenamiento de blobs de Windows Azure al almacén de datos de SQL (PolyBase)][].

### <a name="use-bcp"></a>Usar bcp
Si tiene una cantidad pequeña de datos puede usar bcp para cargar directamente en el almacén de datos de SQL Azure.

Resumen del proceso de carga:
1. Use la herramienta de línea de comandos bcp para exportar datos de SQL Server a archivos sin formato.
2. Usar bcp para cargar los datos de archivos planos directamente al almacén de datos de SQL.

Para obtener un tutorial, vea [cargar datos desde SQL Server al almacén de datos de SQL Azure (bcp)][].


### <a name="use-importexport-recommended-for--10-tb-data"></a>Usar importar o exportar (recomendado para > 10 TB de datos)
Si el tamaño de los datos es > 10 TB y desea mover a Azure, le recomendamos que utilice nuestro servicio de [Importación o exportación][]de envío de disco. 

Resumen del proceso de carga
2. Use la herramienta de línea de comandos bcp para exportar datos de SQL Server a archivos sin formato en discos pueden transferir.
3. Enviar los discos a Microsoft.
4. Microsoft carga los datos en el almacén de datos SQL

## <a name="load-from-hdinsight"></a>Cargar desde HDInsight
Almacén de datos SQL es compatible con la carga de datos desde HDInsight a través de PolyBase. El proceso es el mismo que cargar datos desde el almacenamiento de blobs de Windows Azure: utilizar PolyBase para conectarse a HDInsight para cargar los datos. 

### <a name="1-use-polybase-and-t-sql"></a>1. Utilice PolyBase y T-SQL

Resumen del proceso de carga:

2. Aplicar formato UTF-8 a los datos ya PolyBase no admite actualmente UTF-16.
2. Mover los datos a HDInsight y almacenar en archivos de texto, formato ORC o parqués.
3. Configurar objetos externos en el almacén de datos de SQL para definir la ubicación y el formato de los datos.
4. Ejecutar un comando SQL T para cargar los datos en paralelo en una nueva tabla de base de datos.

Para obtener un tutorial, vea [cargar datos desde el almacenamiento de blobs de Windows Azure al almacén de datos de SQL (PolyBase)][].

## <a name="recommendations"></a>Recomendaciones

Muchos de nuestros socios tienen cargar soluciones. Para obtener más información, vea una lista de nuestros [socios de soluciones][]. 

Si los datos proceden de un origen relacional de y desea cargar en el almacén de datos de SQL debe transformar los datos en filas y columnas antes de cargarlo. Los datos transformados no necesitan almacenarse en una base de datos, se pueden almacenar en archivos de texto.

Crear estadísticas de los datos recién cargadas. Almacén de datos de SQL Azure hace aún no automática de soporte técnico, crear o actualizará las estadísticas automáticamente.  Para obtener el mejor rendimiento de las consultas, es importante crear estadísticas de todas las columnas de todas las tablas después de la primera carga o se producen cambios importantes en los datos.  Para obtener más información, consulte [estadísticas][].


## <a name="next-steps"></a>Pasos siguientes
Para obtener más sugerencias de desarrollo, vea la [información general de desarrollo][].

<!--Image references-->

<!--Article references-->
[Cargar datos de almacenamiento de blobs de Windows Azure al almacén de datos de SQL (PolyBase)]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md
[Cargar datos de almacenamiento de blobs de Windows Azure al almacén de datos de SQL (generador de datos de Azure)]: ./sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md
[Cargar datos de SQL Server al almacén de datos de SQL Azure (SSIS)]: ./sql-data-warehouse-load-from-sql-server-with-integration-services.md
[Cargar datos de SQL Server en el almacén de datos de SQL Azure (bcp)]: ./sql-data-warehouse-load-from-sql-server-with-bcp.md
[Load data from SQL Server to Azure SQL Data Warehouse (AZCopy)]: ./sql-data-warehouse-load-from-sql-server-with-azcopy.md

[Cargar bases de datos de ejemplo]: ./sql-data-warehouse-load-sample-databases.md
[Introducción a la migración]: ./sql-data-warehouse-overview-migrate.md
[socios de soluciones]: ./sql-data-warehouse-partner-business-intelligence.md
[Introducción al desarrollo]: ./sql-data-warehouse-overview-develop.md
[Estadísticas]: ./sql-data-warehouse-tables-statistics.md

<!--MSDN references-->

<!--Other Web references-->
[Importar o exportar]: https://azure.microsoft.com/documentation/articles/storage-import-export-service/
