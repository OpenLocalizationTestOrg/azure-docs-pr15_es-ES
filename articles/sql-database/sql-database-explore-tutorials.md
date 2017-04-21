<properties
   pageTitle="Explorar los tutoriales de base de datos SQL Azure | Microsoft Azure"
   description="Obtenga más información sobre las características y funciones de base de datos SQL"
   keywords=""
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
   ms.workload="data-management"
   ms.date="08/24/2016"
   ms.author="carlrab"/>
   
# <a name="explore-azure-sql-database-tutorials"></a>Explorar los tutoriales de base de datos SQL Azure

Los siguientes vínculos le llevarán a una descripción general de cada área de la característica de lista y un tutorial paso por inicio simple para cada área. Para el ámbito de la solución de inicio rápido que muestran el uso de la base de datos SQL en una solución completa basada en escenarios reales, consulte [Tutoriales rápidos de Azure SQL base de datos de solución](sql-database-solution-quick-starts.md).

## <a name="using-sql-server-management-studio"></a>Usar SQL Server Management Studio

En los siguientes tutoriales, aprenderá sobre el uso de SQL Server Management Studio para administrar y la consulta de base de datos de SQL Azure.


> [AZURE.IMPORTANT] Se recomienda usar siempre la versión más reciente de Management Studio permanezca sincronizado con las actualizaciones de Microsoft Azure y base de datos de SQL. [Actualizar SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


| Tutorial  | Descripción  |
|---|---|---|
| [Conectarse con un inicio de sesión principal de nivel de servidor de base de datos SQL Azure](sql-database-get-started-security.md#connect-to-azure-sql-database-using-a-server-level-principal-login)| En este tutorial, aprenderá a conectarse utilizando un inicio de sesión principal de nivel de servidor de base de datos SQL Azure.|
| [Conectarse a la base de datos de SQL Azure como un usuario](sql-database-get-started-security.md#connect-to-azure-sql-database-as-a-user) | En este tutorial, aprenderá a conectarse a una base de datos de SQL Azure con una cuenta de usuario de la base de datos.|
||||

## <a name="elastic-pools"></a>Grupos de elásticos

En los siguientes tutoriales, aprenderá sobre el uso de [grupos de elásticos](sql-database-elastic-pool.md) para administrar los objetivos de rendimiento para varias bases de datos que tienen imprevistos y uso son amplios diversos patrones de uso.

| Tutorial  | Descripción  |
|---|---|---|
| [Crear un grupo de elástico](sql-database-elastic-pool-create-portal.md) | En este tutorial, aprenderá a crear un grupo de scalable de bases de datos de SQL Azure. |
| [Monitor de una base de datos elástica](sql-database-elastic-pool-manage-portal.md#elastic-database-monitoring) | En este tutorial, aprenderá a supervisar una base de datos individual elástica para un problema potencial. |
| [Agregar una alerta para un recurso de grupo](sql-database-elastic-pool-manage-portal.md#add-an-alert-to-a-pool-resource) | En este tutorial, aprenderá a agregar reglas a los recursos que enviar correo electrónico a personas o cadenas de alerta a los extremos de la dirección URL cuando llega a un umbral de utilización que haya configurado el recurso. |
| [Mover una base de datos a un grupo de elástico](sql-database-elastic-pool-manage-portal.md#move-a-database-into-an-elastic-pool) | En este tutorial, aprenderá cómo mover una base de datos a un grupo de elástico. |
| [Mover una base de datos fuera de un grupo de elástico](sql-database-elastic-pool-manage-portal.md#move-a-database-out-of-an-elastic-pool) | En este tutorial, aprenderá cómo mover una base de datos de un grupo de elástico. |
| [Cambiar la configuración de rendimiento de un grupo](sql-database-elastic-pool-manage-portal.md#change-performance-settings-of-a-pool) | En este tutorial, aprenderá a ajustar los límites de almacenamiento y el rendimiento de un grupo. |
||||

## <a name="elastic-database-jobs"></a>Trabajos elásticos de la base de datos

En los siguientes tutoriales, aprenderá sobre el uso de [trabajos de elásticos de la base de datos](sql-database-elastic-jobs-overview.md).

| Tutorial  | Descripción  |
|---|---|---|
| [Introducción a las herramientas de base de datos elásticas](sql-database-elastic-scale-get-started.md) | En este tutorial, aprenderá a usar las funciones de herramientas elásticos de la base de datos con una aplicación de sharded simple. |
| [Introducción a trabajos elásticos de base de datos de SQL Azure](sql-database-elastic-jobs-getting-started.md)  | En este tutorial, aprenderá cómo a cómo crear y administrar trabajos que administrar un grupo de bases de datos relacionados.  | 
||||

## <a name="elastic-queries"></a>Consultas elásticas

En los siguientes tutoriales, aprenderá acerca de cómo ejecutar [consultas elásticas](sql-database-elastic-query-overview.md). 

| Tutorial  | Descripción  |
|---|---|---|
| [Consultas a través de una base de datos dividido (sharded))](sql-database-elastic-query-getting-started.md) | En este tutorial, aprenderá a crear informes de todas las bases de datos en una base de datos dividido (sharded) con [query elástica](sql-database-elastic-query-overview.md) |
| [Consultas a través de una base de datos dividida verticalmente)](sql-database-elastic-query-getting-started-vertical.md#create-database-objects) | En este tutorial, aprenderá a crear informes de todas las bases de datos en un verticalmente base de datos mediante [una consulta elástica](sql-database-elastic-query-overview.md) |
| [Migrar una base de datos existente de escalado](sql-database-elastic-convert-to-use-elastic-tools.md)| En este tutorial, aprenderá a escalar horizontalmente la base de datos de SQL Azure (fragmentar). |
||||

## <a name="performance-optimization"></a>Optimización del rendimiento

En los siguientes tutoriales, aprenderá sobre la optimización del [rendimiento de bases de datos](sql-database-performance-guidance.md). Para optimizar el rendimiento de varias bases de datos, vea [elásticos grupos](#elastic-pools).

| Tutorial  | Descripción  |
|---|---|---|
| [Cambiar el nivel de rendimiento y los niveles de servicio de la base de datos](sql-database-scale-up.md#change-the-service-tier-and-performance-level-of-your-database) | En este tutorial, aprenderá a ampliar o reducir el rendimiento de una base de datos de SQL Azure con los niveles de servicio. |
| [Información de Performance de consulta asesor SQL base de datos](sql-database-performance.md#performance-overview) | En este tutorial, aprenderá a abrir y usar una perspectiva de rendimiento de SQL base de datos Asesor de consulta.|
| [Recomendaciones de rendimiento Asesor de base de datos de SQL](sql-database-advisor.md#viewing-recommendations) | En este tutorial, aprenderá a ver y aplicar recomendaciones de rendimiento Asesor de base de datos de SQL. |
| [Revise la CPU superior consumir consultas](sql-database-query-performance.md#review-top-cpu-consuming-queries)| En este tutorial, aprenderá a usar una perspectiva de rendimiento de SQL base de datos Asesor de consulta para revisar CPU superior consumir consultas.|
| [Ver los detalles de la consulta individual](sql-database-query-performance.md#viewing-individual-query-details)| En este tutorial, aprenderá a usar una perspectiva de rendimiento de SQL base de datos Asesor de consulta para ver los detalles de rendimiento de la consulta individual.|
||||

## <a name="sql-database-migration-and-archive"></a>Archivar y migración de base de datos SQL 

En los siguientes tutoriales, aprenderá acerca de cómo [migrar una base de datos existente de SQL Server a la base de datos de SQL Azure](sql-database-cloud-migrate.md).

| Tutorial  | Descripción  |
|---|---|---|
| [Detectar problemas de compatibilidad con SQL Server Data Tools para Visual Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md#detecting-compatibility-issues-using-sql-server-data-tools-for-visual-studio) | En este tutorial, aprenderá a usar SQL Server Data Tools para Visual Studio para determinar la compatibilidad de la base de datos de SQL Azure. |
| [Solución de problemas de compatibilidad con SQL Server Data Tools para Visual Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssdt#fixing-compatibility-issues-using-sql-server-data-tools-for-visual-studio) | En este tutorial, aprenderá a usar SQL Server Data Tools para Visual Studio para corregir problemas de compatibilidad de base de datos de SQL Azure. |
| [Determinar la compatibilidad de base de datos SQL con SqlPackage.exe](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md#using-sqlpackageexe) | En este tutorial, aprenderá a usar la utilidad de línea de comandos de SQLPackage.exe para determinar la compatibilidad de la base de datos de SQL Azure.|
| [Determinar la compatibilidad de base de datos SQL con SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md#using-sql-server-management-studio) |En este tutorial, aprenderá a usar SQL Server Management Studio para determinar la compatibilidad de la base de datos de SQL Azure.|
| [Migrar la base de datos de SQL Server a la base de datos SQL implementar base de datos en el Asistente de base de datos de Microsoft Azure](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md#use-the-deploy-database-to-microsoft-azure-database-wizard) | En este tutorial, aprenderá cómo migrar una base de datos de SQL Server compatible con el Asistente para la base de datos de Microsoft Azure implementar base de datos de SQL Server Management Studio de base de datos SQL Azure.
| [Exportar una base de datos de SQL Server a un archivo de MOCHILA con SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md) | En este tutorial, aprenderá cómo exportar una base de datos de SQL Server compatible a un archivo de MOCHILA mediante el Asistente para exportar aplicaciones de nivel de datos de SQL Server Management Studio.|
| [Exportar una base de datos de SQL Server a un archivo de MOCHILA con SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md) | En este tutorial, aprenderá cómo exportar una base de datos de SQL Server compatible con un archivo de MOCHILA mediante la utilidad de línea de comandos de SQLPackage.exe.|
| [Importar un archivo de MOCHILA a la base de datos de SQL Azure con SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md) | En este tutorial, aprenderá cómo importar una base de datos a la base de datos de Azure SQL desde un archivo de MOCHILA mediante el Asistente para exportar aplicaciones de nivel de datos de SQL Server Management Studio. |
| [Importar un archivo de MOCHILA a la base de datos de SQL Azure con SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md#import-from-a-bacpac-file-into-azure-sql-database-using-sqlpackage) | En este tutorial, aprenderá cómo importar una base de datos a la base de datos de Azure SQL desde un archivo de MOCHILA mediante la utilidad de línea de comandos de SQLPackage. |
| [Importar un archivo de MOCHILA a la base de datos de SQL Azure con el portal de Azure](sql-database-import.md) | En este tutorial, aprenderá cómo importar una base de datos a la base de datos de Azure SQL desde un archivo de MOCHILA que se almacena en un blobs de Windows Azure con el Portal de Azure.|
| [Importar un archivo de MOCHILA a la base de datos de SQL Azure con PowerShell](sql-database-import-powershell.md) | En este tutorial, aprenderá cómo importar una base de datos a la base de datos de Azure SQL desde un archivo de MOCHILA con PowerShell.|
| [Archivar una base de datos de SQL Azure con el portal de Azure](sql-database-export.md#export-your-database) | En este tutorial, aprenderá a archivar una base de datos de SQL Azure a un archivo de MOCHILA con el portal de Azure. |
| [Archivar una base de datos de SQL Azure con PowerShell](sql-database-export-powershell.md) | En este tutorial, aprenderá a archivar una base de datos de SQL Azure en un archivo de MOCHILA utilizando PowerShell. |
| [Copiar una base de datos de SQL Azure con el portal de Azure](sql-database-copy.md#copy-your-sql-database) | En este tutorial, aprenderá cómo copiar una base de datos de SQL Azure con el portal de Azure. |
| [Copiar una base de datos de SQL Azure con PowerShell](sql-database-copy-powershell#copy-your-sql-database) | En este tutorial, aprenderá cómo copiar una base de datos de SQL Azure con PowerShell. |
| [Copiar una base de datos de SQL Azure con Transact-SQL](sql-database-copy-transact-sql.md#copy-your-sql-database) | En este tutorial, aprenderá cómo copiar una base de datos de SQL Azure con Transact-SQL. |
||||

##<a name="develop"></a>Desarrollar

En los siguientes tutoriales, aprenderá sobre [Desarrollo de base de datos de SQL](sql-database-develop-overview.md) y el uso de [bibliotecas de conectividad](sql-database-libraries.md).

| Tutorial  | Descripción  |
|---|---|---|
| [Conectarse a la base de datos SQL con .NET (C#)](sql-database-develop-dotnet-simple.md) | En este tutorial, aprenderá a conectarse a una base de datos de SQL Azure con C#. |
| [Conectarse a la base de datos SQL con Java](sql-database-develop-java-simple.md) | En este tutorial, aprenderá a conectarse a una base de datos de SQL Azure con Java. |
| [Conectarse a la base de datos SQL con Node.js](sql-database-develop-nodejs-simple.md) | En este tutorial, aprenderá a conectarse a una base de datos de SQL Azure con Node.js. |
| [Conectarse a la base de datos SQL con PHP](sql-database-develop-php-simple.md) | En este tutorial, aprenderá a conectarse a una base de datos de SQL Azure con PHP. |
| [Conectarse a la base de datos SQL con Python](sql-database-develop-python-simple.md) | En este tutorial, aprenderá a conectarse a una base de datos de SQL Azure con Python. |
| [Conectarse a la base de datos SQL con Ruby](sql-database-develop-ruby-simple.md) | En este tutorial, aprenderá a conectarse a una base de datos de SQL Azure con Ruby. |
||||
 
## <a name="database-access"></a>Acceso a la base de datos

En los siguientes tutoriales, aprenderá a [crear y administrar inicios de sesión y los usuarios](sql-database-manage-logins.md).

| Tutorial  | Descripción  |
|---|---|---|
| [Crear una regla de firewall de nivel de servidor de base de datos de SQL Azure con el portal de Azure](sql-database-configure-firewall-settings.md)  | En este tutorial, aprenderá a configurar un firewall de nivel de servidor de base de datos SQL con el portal de Azure.  |
| [Crear una regla de firewall de nivel de base de datos con Transact-SQL](sql-database-configure-firewall-settings-tsql.md#database-level-firewall-rules) | En este tutorial, aprenderá a crear una regla de firewall de nivel de base de datos con Transact-SQL.|
| [Administrar las reglas de firewall de nivel de servidor con Transact-SQL](sql-database-configure-firewall-settings-tsql.md#manage-server-level-firewall-rules-through-transact-sql) | En este tutorial, aprenderá cómo administrar un firewall de nivel de servidor de base de datos de SQL Azure con Transact-SQL.|
| [Administrar las reglas de firewall de nivel de servidor con PowerShell](sql-database-configure-firewall-settings-powershell.md#manage-firewall-rules-using-powershell) | En este tutorial, aprenderá cómo administrar un firewall de nivel de servidor de base de datos de SQL Azure con PowerShell.|
| [Administrar las reglas de firewall de nivel de servidor con la API de REST](sql-database-configure-firewall-settings-rest.md#manage-firewall-rules-using-the-service-management-rest-api) | En este tutorial, aprenderá cómo administrar un firewall de nivel de servidor de base de datos de SQL Azure con la API de restablecer.|
| [Conectarse con un inicio de sesión principal de nivel de servidor de base de datos SQL Azure](sql-database-get-started-security.md#connect-to-azure-sql-database-using-a-server-level-principal-login)| En este tutorial, aprenderá a conectarse utilizando un inicio de sesión principal de nivel de servidor de base de datos SQL Azure.|
| [Conceder acceso de base de datos a un inicio de sesión] (sql-database-manage-logins.md#granting-database-access-to-a-login() | En este tutorial, aprenderá a conceder acceso de base de datos para un inicio de sesión de nivel de servidor.|
| [Crear nuevo usuario de base de datos con SSMS](sql-database-get-started-security.md#create-new-database-user-using-ssms) | En este tutorial, aprenderá a crear un nuevo usuario de base de datos en una base de datos con SSMS.|
| [Conceder permisos de db_owner de usuario de base de datos nueva](sql-database-get-started-security.md#grant-new-database-user-dbowner-permissions) | En este tutorial, aprenderá a conceder permisos de usuario db_owner de una base de datos existente.|
| [Conectarse a la base de datos de SQL Azure como un usuario](sql-database-get-started-security.md#connect-to-azure-sql-database-as-a-user) | En este tutorial, aprenderá a conectarse a una base de datos de SQL Azure con una cuenta de usuario de la base de datos.|
||||


## <a name="data-security"></a>Seguridad de los datos

En los siguientes tutoriales, aprenderá sobre cómo [proteger los datos de la base de datos de SQL Azure](sql-database-security.md). 

| Tutorial  | Descripción  |
|---|---|---|
| [Habilitar la detección de amenaza para la base de datos con el portal de Azure](sql-database-threat-detection-get-started.md#set-up-threat-detection-for-your-database) | En este tutorial, aprenderá a configurar detección de amenazas en el portal de Azure para la base de datos.|
| [Usar datos confidenciales siempre cifrados de seguro](sql-database-always-encrypted-azure-key-vault.md) | En este tutorial, usará al asistente siempre cifrados para proteger datos confidenciales en una base de datos de SQL Azure.|
| [Proteger los datos de dejar mediante el cifrado de datos transparente](https://msdn.microsoft.com/library/dn948096.aspx)| En este tutorial, aprenderá a usar el cifrado de datos transparente para proteger los datos de dejar.|
| [Cifrar una columna de datos](https://msdn.microsoft.com/library/ms179331.aspx)| En este tutorial, aprenderá a cifrar una columna de datos con Transact-SQL.|
| [Configurar masking dinámico de datos](sql-database-dynamic-data-masking-get-started.md#set-up-dynamic-data-masking-for-your-database-using-the-azure-portal)  | En este tutorial, aprenderá a configurar masking dinámico de datos para la base de datos de SQL Azure. |
||||

## <a name="business-continuity-and-query-scale-out"></a>Continuidad empresarial y escalado de consulta

En los siguientes tutoriales, aprenderá a usar [Geo restaurar y la replicación de Geo Active](sql-database-business-continuity.md) para reccover de errores, para continuidad empresarial y escalado de consulta.

| Tutorial  | Descripción  |
|---|---|---|
| [Restaurar una base de datos de SQL Azure a un punto anterior en el tiempo con el Portal de Azure](sql-database-point-in-time-restore-portal.md)| En este tutorial, aprenderá a restaurar la base de datos en un momento anterior en el tiempo con el portal de Azure.|
| [Restaurar una base de datos de SQL Azure a un punto anterior en el tiempo con PowerShell](sql-database-point-in-time-restore-powershell.md) | En este tutorial, aprenderá a restaurar la base de datos en un momento anterior en el tiempo con PowerShell|
| [Restaurar una base de datos de SQL Azure eliminado con el Portal de Azure](sql-database-restore-deleted-database-portal.md) | En este tutorial, aprenderá cómo restaurar una base de datos eliminado con el portal de Azure.|
| [Restaurar una base de datos de SQL Azure eliminado mediante el PowerShell](sql-database-restore-deleted-database-powershell.md) | En este tutorial, aprenderá cómo restaurar una base de datos eliminado con PowerShell.|
| [Configurar la replicación de Geo para la base de datos de SQL Azure con el portal de Azure](sql-database-geo-replication-portal.md)| En este tutorial, aprenderá a configurar replicación Geo activa con el portal de Azure.|
| [Configurar la replicación de Geo para la base de datos de SQL Azure con PowerShell](sql-database-geo-replication-powershell.md)| En este tutorial, aprenderá a configurar la replicación de Geo Active con PowerShell.|
| [Configurar la replicación de Geo para la base de datos de SQL Azure con Transact-SQL](sql-database-geo-replication-transact-sql.md)| En este tutorial, aprenderá a configurar la replicación de Geo Active mediante Transact-SQL.|
| [Iniciar un error planificado o para la base de datos de SQL Azure con el portal de Azure](sql-database-geo-replication-failover-portal.md) | En este tutorial, aprenderá cómo para conmutar por error a una réplica secundaria replicar geo con el portal de Azure.|
| [Iniciar un error planificado o para la base de datos de SQL Azure con PowerShell](sql-database-geo-replication-failover-powershell.md) | En este tutorial, aprenderá cómo para conmutar por error a una réplica secundaria replicar geo con PowerShell.|
| [Iniciar un error planificado o para la base de datos de SQL Azure con Transact-SQL](sql-database-geo-replication-failover-transact-sql.md) | En este tutorial, aprenderá cómo para conmutar por error a una réplica secundaria replicar geo mediante Transact-SQL.|
||||

## <a name="data-sync"></a>Sincronización de datos

En este tutorial, aprenderá acerca de la [Sincronización de datos](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf).

| Tutorial  | Descripción  |
|---|---|---| 
| [Introducción a la sincronización de datos SQL Azure (vista preliminar)](sql-database-get-started-sql-data-sync.md)  | En este tutorial, aprenderá los fundamentos de la sincronización de datos de SQL Azure con el Portal de clásico de Azure. |
||||

## <a name="next-steps"></a>Pasos siguientes

[Explorar el inicio rápido de solución de base de datos SQL Azure](sql-database-solution-quick-starts.md)
