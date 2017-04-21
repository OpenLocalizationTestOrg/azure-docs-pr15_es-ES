<properties
    pageTitle="Todos los temas de servicio de base de datos SQL | Microsoft Azure"
    description="Tabla de todos los temas para el servicio de Azure denominada base de datos de SQL que existe en http://azure.microsoft.com/documentation/articles/, el título y la descripción."
    services="sql-database"
    documentationCenter=""
    authors="MightyPen"
    manager="jhubbard"
    editor="MightyPen"/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="genemi"/>


# <a name="all-topics-for-azure-sql-database-service"></a>Todos los temas de servicio de base de datos de SQL Azure

En este tema se enumeran todos los temas que se aplican directamente al servicio de **Base de datos SQL** de Azure. Puede buscar esta página Web para las palabras clave mediante **CTRL+f**, para buscar los temas de interés actual.




## <a name="new"></a>Nuevo

| &nbsp; | Título | Descripción |
| --: | :-- | :-- |
| 1 | [Daxko/CSI utiliza Azure para acelerar su ciclo de desarrollo y para mejorar sus servicios de atención al cliente y el rendimiento](sql-database-implementation-daxko.md) | Obtenga más información sobre cómo Daxko/CSI usa la base de datos SQL para acelerar su ciclo de desarrollo y para mejorar sus servicios de atención al cliente y el rendimiento |
| 2 | [Azure proporciona alcance global de GEP y mayor eficiencia](sql-database-implementation-gep.md) | Obtenga más información sobre cómo GEP usa la base de datos SQL para llegar a los clientes más globales y lograr una mayor eficiencia |
| 3 | [Con Azure, SnelStart ha ampliado rápidamente sus servicios de empresa a una velocidad de 1.000 bases de SQL Azure nuevo por mes.](sql-database-implementation-snelstart.md) | Obtenga más información sobre cómo SnelStart usa la base de datos SQL expandir rápidamente sus servicios de empresa a una velocidad de 1.000 bases de SQL Azure nuevo al mes |
| 4 | [Umbraco usa la base de datos de SQL Azure rápidamente los servicios de aprovisionamiento y escala miles de inquilinos en la nube](sql-database-implementation-umbraco.md) | Obtenga información sobre cómo Umbraco usa la base de datos SQL aprovisionar rápidamente y servicios de escala miles de inquilinos en la nube |
| 5 | [Administrar la base de datos SQL Azure con PowerShell](sql-database-manage-powershell.md) | Administración de base de datos de SQL Azure con PowerShell. |
| 6 | [Compatibilidad con SSMS Azure AD AMF con la base de datos de SQL y almacén de datos SQL](sql-database-ssms-mfa-authentication.md) | Usar autenticación de factores múltiples con SSMS para base de datos SQL y almacén de datos SQL. |
| 7 | [Explicación de unidades de transacción de base de datos (DTUs) y elástico unidades de transacción de base de datos (eDTUs)](sql-database-what-is-a-dtu.md) | Comprender qué una base de datos de SQL de Azure es de unidad de transacción. |


## <a name="updated-articles-sql-database"></a>Artículos actualizados, la base de datos SQL

En esta sección se enumera los artículos que se han actualizado recientemente, donde la actualización fue grande o significativa. Para cada artículo actualizado, se muestra un fragmento de código aproximada del texto agregado descuento. Los artículos se actualizaron dentro del intervalo de fechas de **2016-08-22** a **2016-10-05**.

| &nbsp; | Artículo | Texto actualizado, fragmento de código | Se actualiza cuando |
| --: | :-- | :-- | :-- |
| 8 | [Administrar la base de datos SQL Azure con PowerShell](sql-database-command-line-tools.md) | Crear un grupo de recursos para nuestra base de datos SQL y recursos de Azure relacionados con el cmdlet New-AzureRmResourceGroup (https://msdn.microsoft.com/library/azure/mt759837.aspx). *$resourceGroupName = "resourcegroup1" $resourceGroupLocation = "northcentralus" nuevo AzureRmResourceGroup-nombre $resourceGroupName-ubicación $resourceGroupLocation* Para obtener más información, vea con Azure con el Administrador de recursos de Azure (.. / powershell-azure-recursos-manager.md). Para un script de ejemplo, vea Crear un script de PowerShell (sql-base de datos-get-Introducción-powershell.md create-a-sql-database-powershell-script) de la base de datos SQL. **Crear un servidor de base de datos SQL** Crear un servidor de base de datos SQL con el cmdlet New-AzureRmSqlServer (https://msdn.microsoft.com/library/azure/mt603715.aspx). Reemplazar el *servidor 1* con el nombre de su servidor. Los nombres de servidor deben ser únicos en todos los servidores de base de datos de SQL Azure. Si ya se toma el nombre del servidor, recibe un error. Este comando puede tardar varios minutos en completarse. La resou | 2016-09-14 |
| 9 | [Copiar una base de datos de SQL Azure con PowerShell](sql-database-copy-powershell.md) |   Base de datos origen SQL (las existentes de base de datos para copiar)--$sourceDbName = "db1" $sourceDbServerName = "servidor 1" $sourceDbResourceGroupName = copia de base de datos SQL de "rg1" (db nueva creación): $copyDbName = "db1_copy" $copyDbServerName = "servidor 2" $copyDbResourceGroupName = "rg2" copia una base de datos en el mismo servidor--nuevo AzureRmSqlDatabaseCopy - ResourceGroupName $sourceDbResourceGroupName - nombre_de_servidor $sourceDbServerName - DatabaseName $sourceDbName - CopyDatabaseName $copyDbName copiar una base de datos a otro servidor--nuevo AzureRmSqlDatabaseCopy - ResourceGroupName $sourceDbResourceGroupName - nombre_de_servidor $sourceDbServerName - DatabaseName $sourceDbName - CopyResourceGroupName $copyDbResourceGroupName - CopyServerName $copyDbServerName - CopyDatabaseName $copyDbName copiar una base de datos en un grupo de base de datos elástico--$poolName = "grupo 1" nuevo AzureRmSqlDatabaseCopy - ResourceGroupName $ sourceDbResourceGroupName nombre_de_servidor - $sourceDbServerName - DatabaseName $sourceDbName - CopyReso | 2016-09-08 |
| 10 | [Crear un grupo de la base de datos elástico con C#](sql-database-elastic-pool-create-csharp.md) |   Console.WriteLine ("servidor firewall...");  FirewallRuleGetResponse fwr = CreateOrUpdateFirewallRule (_sqlMgmtClient, _resourceGroupName, _serverName, _firewallRuleName, _startIpAddress, _endIpAddress);  Console.WriteLine ("servidor de seguridad:" + fwr. FirewallRule.Id);  Console.WriteLine ("elástico grupo...");  Epr ElasticPoolCreateOrUpdateResponse = CreateOrUpdateElasticDatabasePool (_sqlMgmtClient, _resourceGroupName, _serverName, _poolName, _poolEdition, _poolDtus, _databaseMinDtus, _databaseMaxDtus);  Console.WriteLine ("grupo elástico:" + epr. ElasticPool.Id);  Console.WriteLine("Database...");  DatabaseCreateOrUpdateResponse dbr = CreateOrUpdateDatabase (_sqlMgmtClient, _resourceGroupName, _serverName, _databaseName, _poolName);  Console.WriteLine ("base de datos:" + dbr. Database.Id);  Console.WriteLine ("Presione cualquier tecla para continuar...");  Console.ReadKey();  } estático CreateOrUpdateResourceGroup(ResourceManagementClient resourceMgmtClient, string subscriptionId, string resourceGroupNa ResourceGroup | 2016-09-14 |
| 11 | [Script de PowerShell para identificar las bases de datos adecuados para un grupo de elásticos de la base de datos](sql-database-elastic-pool-database-assessment-powershell.md) | **Grupo de base de datos para elástico candidatos, nos excluir patrón y las bases de datos que ya están en un grupo. Puede agregar más bases de datos a la lista excluida siguiente según sea necesario** $ListOfDBs = AzureRmSqlDatabase Get - nombre_de_servidor $servername. Split('.') 0 - ResourceGroupName $ResourceGroupName / Where-Object {$_. DatabaseName - notin ("principal") - y $_. CurrentServiceLevelObjectiveName - notin ("ElasticPool")- y $_. CurrentServiceObjectiveName - notin ("ElasticPool")} $outputConnectionString = "origen de datos = $outputServerName; seguridad integrada = false; inicial catálogo = $outputdatabaseName; Identificador de usuario = $outputDBUsername; Contraseña = $outputDBpassword "$destinationTableName ="resource_stats_output" **crear una tabla de base de datos de salida de la colección de métricas** $sql =" si NOT EXISTS (seleccionar * desde sys.objects dónde object_id = OBJECT_ID(N'$($destinationTableName)') y escriba en (N'U')) $($destinationTableName) inicial crear una tabla (database_name varchar(128), slo varchar (20), end_time datetime, avg_cpu flotación, avg_ | 29-09-2016 |
| 12 | [Tutorial de base de datos SQL: crear una base de datos SQL en minutos mediante el portal de Azure](sql-database-get-started.md) |   ! Nueva base de datos de sql 1 (. / media/sql-database-get-started/sql-database-new-database-1.png) 3. Haga clic en **Base de datos SQL** para abrir el módulo de base de datos de SQL. El contenido de este módulo varía según el número de las suscripciones y los objetos existentes (como servidores existentes).  ! Nueva base de datos de sql 2 (. / media/sql-database-get-started/sql-database-new-database-2.png) 4. En el cuadro de texto **nombre de la base de datos** , proporcione un nombre para la primera base de datos - como "mi base de datos". Una marca de verificación verde indica que ha proporcionado un nombre válido.  ! Nueva base de datos de sql 3 (. / media/sql-database-get-started/sql-database-new-database-3.png) 5. Si tiene varias suscripciones, seleccione una suscripción. 6. En **el grupo de recursos**, haga clic en **Crear nuevo** y proporcione un nombre para el primer grupo de recursos - como "Mi de grupo de recursos". Una marca de verificación verde indica que ha proporcionado un nombre válido.  ! Nueva base de datos de sql 4 (. / media/sql-database-get-started/sql-database-new-database-4.png) 7. En **Seleccione origen* | 2016-09-08 |
| 13 | [Pruebe la base de datos SQL: Utilizar C# para crear una base de datos SQL con la biblioteca de base de datos de SQL para .NET](sql-database-get-started-csharp.md) | **Crear un servicio principal para tener acceso a los recursos** El siguiente script de PowerShell crea la aplicación de Active Directory (AD) y la principal del servicio que se debe autenticar nuestra aplicación C. La secuencia de comandos genera valores que necesitamos para C el ejemplo anterior. Para obtener información detallada, consulte usar PowerShell de Azure para crear una entidad de seguridad de servicio acceso a los recursos (.. / recurso-grupo-autenticar-servicio-principal.md).  Inicie sesión en Azure.  AzureRmAccount agregar si tiene varias suscripciones, quite los comentarios y establecer la suscripción que desea trabajar con.  $subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}" conjunto AzureRmContext - SubscriptionId $subscriptionId proporcionar estos valores para la nueva aplicación AAD.  $appName es el nombre para mostrar de la aplicación, debe ser único en el directorio.  $uri no es necesario ser un uri real.  $secret es una contraseña que cree.  $appName = "{aplicación nombre}" $uri = "http://{app-name"} $secret = "{-contraseña de aplicación}" crear una aplicación AAD $azureAdApplication = AzureRmADApp de nuevo | 2016-09-23 |
| 14 | [Administrar bases de datos de SQL Azure con el portal de Azure](sql-database-manage-portal.md) | Para ver o actualizar la configuración de la base de datos, haga clic en la opción que desee en el módulo de base de datos SQL:! Configuración de la base de datos SQL (. / media/sql-database-manage-portal/settings.png) **¿cómo encontrar un nombre completo del servidor de bases de datos SQL?** Para ver el nombre de su servidor de bases de datos, haga clic en **Descripción general** en el módulo de la **base de datos SQL** y anote el nombre de servidor:! Configuración de la base de datos SQL (. / media/sql-database-manage-portal/server-name.png) **¿cómo administrar las reglas de firewall para controlar el acceso a mi SQL server y la base de datos?** Para ver, crear o actualizar las reglas de firewall, haga clic en **Configurar el servidor de seguridad** en el módulo de la **base de datos SQL** . Para obtener información detallada, vea Configurar una regla de firewall de nivel de servidor de base de datos de SQL Azure con el portal de Azure (sql-base de datos-Configurar-firewall-settings.md). ! reglas del firewall (. / media/sql-database-manage-portal/sql-database-firewall.png) **¿cómo cambiar el nivel de rendimiento o nivel del servicio de base de datos SQL?** Para actualizar el nivel de rendimiento o nivel de servicio de una base de datos SQL, haga clic en ** nivel de precios (s | 20 de 09 de 2016 |





## <a name="get-started"></a>Introducción

| &nbsp; | Título | Descripción |
| --: | :-- | :-- |
| 15 | [Crea inquilinos varias aplicaciones con la base de datos SQL Azure con aislamiento y eficacia](sql-database-build-multi-tenant-apps.md) | Obtenga información sobre cómo se crea la base de datos de SQL inquilinos varias aplicaciones |
| 16 | [Conectarse a la base de datos de SQL con SQL Server Management Studio y ejecutar una consulta de SQL de T de ejemplo](sql-database-connect-query-ssms.md) | Obtenga información sobre cómo conectarse a la base de datos SQL Azure con SQL Server Management Studio (SSMS). A continuación, ejecute una consulta de ejemplo con Transact-SQL (T-SQL). |
| 17 | [Conectarse a la base de datos SQL con .NET (C#)](sql-database-develop-dotnet-simple.md) | Usar el código de ejemplo en este rápido empezar a crear una aplicación moderna con C# y copia una base de datos relacional eficaces en la nube con la base de datos de SQL Azure. |
| 18 | [Crear un nuevo grupo de elásticos de la base de datos con el portal de Azure](sql-database-elastic-pool-create-portal.md) | Cómo agregar un grupo de base de datos elástico scalable a la configuración de la base de datos SQL de fácil administración y recursos compartidos a través de muchas bases de datos. |
| 19 | [Explorar los tutoriales de base de datos SQL Azure](sql-database-explore-tutorials.md) | Obtenga más información sobre las características y funciones de base de datos SQL |
| 20 | [Tutorial de base de datos SQL: crear una base de datos SQL en minutos mediante el portal de Azure](sql-database-get-started.md) | Obtenga información sobre cómo configurar un servidor de base de datos SQL lógico, regla de firewall del servidor, base de datos SQL y datos de ejemplo. Además, aprenda a conectar con las herramientas cliente, configurar usuarios y configurar una regla de firewall de la base de datos. |
| 21 | [Base de datos SQL Azure protege y protege](sql-database-helps-secures-and-protects.md) | Obtenga información sobre cómo ayuda segura la base de datos SQL y proteger |
| 22 | [Base de datos SQL Azure aprende &amp; adaptándose](sql-database-learn-and-adapt.md) | Aprender a aprendiendo y adaptándose de base de datos SQL |
| 23 | [Elija una opción de SQL Server de nube: base de datos de SQL Azure (PaaS) o SQL Server en máquinas virtuales de Azure (IaaS)](sql-database-paas-vs-sql-server-iaas.md) | Obtenga información sobre qué opción de SQL Server de nube se ajusta a la aplicación: base de datos de SQL Azure (PaaS) o SQL Server en la nube en Azure máquinas virtuales de Windows. |
| 24 | [Escalas de base de datos de SQL Azure sobre la marcha](sql-database-scale-on-the-fly.md) | Obtenga información sobre cómo se ajusta la base de datos de SQL sobre la marcha |
| 25 | [Explorar el inicio rápido de solución de base de datos SQL Azure](sql-database-solution-quick-starts.md) | Obtenga más información sobre las soluciones de base de datos SQL Azure |
| 26 | [Base de datos SQL Azure funciona en su entorno](sql-database-works-in-your-environment.md) | Aprender a base de datos SQL Ayuda, protege y protege |



## <a name="build-an-app"></a>Crear una aplicación

| &nbsp; | Título | Descripción |
| --: | :-- | :-- |
| 27 | [Solucionar problemas, diagnosticar y evitar errores de conexión de SQL y transitorias para base de datos de SQL](sql-database-connectivity-issues.md) | Obtenga información sobre cómo solucionar problemas, diagnosticar y evitar que un error de conexión de SQL o transitorias en base de datos de SQL Azure.  |
| 28 | [Conectarse a una base de datos SQL con Visual Studio](sql-database-connect-query.md) | Escribir un programa en C# de la consulta y conectarse a la base de datos SQL. Información sobre las direcciones IP, las cadenas de conexión, inicio de sesión seguro y libre de Visual Studio. |
| 29 | [Puertos más allá de 1433 para ADO.NET 4.5 y V12 de base de datos SQL](sql-database-develop-direct-route-ports-adonet-v12.md) | Conexiones de cliente de ADO.NET a V12 de base de datos de SQL Azure a veces utilizar al servidor proxy e interactúan directamente con la base de datos. Se convierten en puertos distintos 1433 importantes. |
| 30 | [Códigos de error SQL para las aplicaciones cliente de base de datos de SQL: error de conexión y otros problemas de la base de datos](sql-database-develop-error-messages.md) | Obtenga más información sobre códigos de error SQL para las aplicaciones de cliente de base de datos SQL, como los errores comunes de conexión de base de datos, problemas de copia de la base de datos y errores generales.  |
| 31 | [Conectarse a la base de datos SQL con PHP en Windows](sql-database-develop-php-simple.md) | Presenta un ejemplo de un programa PHP que se conecta a la base de datos de SQL Azure desde un cliente de Windows y proporciona vínculos a los componentes de software necesario necesarios el cliente. |
| 32 | [Pruebe la base de datos SQL: Utilizar C# para crear una base de datos SQL con la biblioteca de base de datos de SQL para .NET](sql-database-get-started-csharp.md) | Pruebe la base de datos SQL para desarrollar aplicaciones SQL y C# y crear una base de datos de SQL Azure con el uso de la biblioteca de base de datos de SQL para .NET C#. |
| 33 | [Introducción a las características de JSON de base de datos de SQL Azure](sql-database-json-features.md) | Base de datos de SQL Azure le permite análisis, consulta y formato de datos en notación de notación de objetos JavaScript (JSON). |
| 34 | [Solucionar problemas de conexión a la base de datos de SQL Azure](sql-database-troubleshoot-common-connection-issues.md) | Pasos para identificar y resolver los errores comunes de conexión de base de datos de SQL Azure. |
| 35 | [Error "No está disponible actualmente en el servidor de base de datos" al conectarse a la base de datos sql](sql-database-troubleshoot-connection.md) | Solucionar problemas de la base de datos de servidor no está disponible actualmente error cuando una aplicación se conecta a la base de datos de SQL. |



## <a name="develop"></a>Desarrollar

| &nbsp; | Título | Descripción |
| --: | :-- | :-- |
| 36 | [Obtener los valores requeridos para la autenticación de una aplicación para tener acceso a la base de datos SQL de código](sql-database-client-id-keys.md) | Crear una entidad de seguridad de servicio para obtener acceso a la base de datos SQL de código. |
| 37 | [Conectarse a la base de datos SQL con Java con JDBC en Windows](sql-database-develop-java-simple.md) | Presenta una muestra de código de Java que puede usar para conectarse a la base de datos de SQL Azure. El ejemplo utiliza JDBC y se ejecuta en un equipo cliente Windows. |
| 38 | [Conectarse a la base de datos SQL con Node.js](sql-database-develop-nodejs-simple.md) | Presenta una muestra de código de Node.js que puede usar para conectarse a la base de datos de SQL Azure. |
| 39 | [Información general sobre el desarrollo de base de datos SQL](sql-database-develop-overview.md) | Obtenga información sobre los procedimientos recomendados para conectarse a la base de datos SQL de aplicaciones y bibliotecas de conectividad disponibles. |
| 40 | [Conectarse a la base de datos SQL con Python](sql-database-develop-python-simple.md) | Presenta una muestra de código de Python que puede usar para conectarse a la base de datos de SQL Azure. |
| 41 | [Conectarse a la base de datos SQL con Ruby](sql-database-develop-ruby-simple.md) | Dar un ejemplo de código ideogramas y transcripción fonética que se puede ejecutar para conectarse a la base de datos de SQL Azure. |
| 42 | [Introducción a las tablas temporales de base de datos SQL Azure](sql-database-temporal-tables.md) | Obtenga información sobre cómo empezar a usar tablas Temporal en la base de datos de SQL Azure. |



## <a name="performance-and-scale"></a>Rendimiento y escala

| &nbsp; | Título | Descripción |
| --: | :-- | :-- |
| 43 | [Asesor de base de datos SQL](sql-database-advisor.md) | El Asistente de base de datos de SQL Azure proporciona recomendaciones para las bases de datos de SQL existente que puede mejorar el rendimiento de la consulta actual. |
| 44 | [Asesor de base de datos de SQL con el portal de Azure](sql-database-advisor-portal.md) | Puede usar el Asistente de base de datos de SQL Azure en el portal de Azure para revisar e implementar recomendaciones para las bases de datos de SQL existente que puede mejorar el rendimiento de la consulta actual. |
| 45 | [Información general de referencia de base de datos de SQL Azure](sql-database-benchmark-overview.md) | En este tema se describe la referencia de base de datos de SQL Azure usados en medir el rendimiento de la base de datos de SQL Azure. |
| 46 | [¿Cuándo se debe utilizar un grupo de la base de datos elástico?](sql-database-elastic-pool-guidance.md) | Un grupo de la base de datos elástico es una colección de recursos disponibles que están compartidos por un grupo de bases de datos elásticos. Este documento proporciona instrucciones para ayudar a evaluar la idoneidad de uso de un grupo de elásticos de la base de datos para un grupo de bases de datos. |
| 47 | [Introducción a las herramientas de base de datos elásticas](sql-database-elastic-scale-get-started.md) | Explicación básica de la característica de herramientas de base de datos elástico de base de datos de SQL Azure, incluidos fácil para ejecutar la aplicación de ejemplo. |
| 48 | [Preguntas más frecuentes de base de datos SQL](sql-database-faq.md) | Solicitar respuestas a los clientes de preguntas comunes acerca de las bases de datos de la nube y base de datos de SQL Azure, sistema de administración de base de datos relacional de Microsoft (RDBMS) y base de datos como un servicio en la nube. |
| 49 | [Introducción a en memoria (Preview) en la base de datos SQL](sql-database-in-memory.md) | Tecnologías de SQL en memoria mejoran el rendimiento de transacciones y las cargas de trabajo de análisis. Aprenda a aprovechar las ventajas de estas tecnologías. |
| 50 | [Uso de memoria OLTP (preview) para mejorar el rendimiento de la aplicación de base de datos de SQL](sql-database-in-memory-oltp-migration.md) | En la memoria de adoptar OLTP para mejorar el rendimiento de transacciones en una base de datos SQL existente. |
| 51 | [Almacenamiento en memoria OLTP de Monitor](sql-database-in-memory-oltp-monitoring.md) | Estimación y monitor de almacenamiento en memoria XTP usan, capacidad; resolver el error de capacidad 41823 |
| 52 | [Funcionamiento de la tienda de consulta de base de datos SQL Azure](sql-database-operate-query-store.md) | Obtenga información sobre cómo trabajar con el almacén de consulta de base de datos de SQL Azure |
| 53 | [Información de rendimiento de base de datos SQL](sql-database-performance.md) | La base de datos de SQL Azure proporciona herramientas de rendimiento para ayudarle a identificar las áreas que pueden mejorar el rendimiento de la consulta actual. |
| 54 | [Base de datos de SQL Azure y el rendimiento para bases de datos](sql-database-performance-guidance.md) | En este artículo puede ayudarle a determinar qué nivel de servicio para elegir para su aplicación. También se recomienda métodos para optimizar la aplicación para obtener el máximo partido de base de datos de SQL Azure. |
| 55 | [Información de Performance de consulta de base de datos SQL Azure](sql-database-query-performance.md) | Supervisión de rendimiento de consulta identifica la mayoría consumo de CPU de las consultas para una base de datos de SQL Azure. |
| 56 | [Cambiar el nivel y rendimiento de nivel de servicio (nivel de precios) de una base de datos SQL](sql-database-scale-up.md) | Cambiar el nivel de servicio y nivel de rendimiento de una base de datos de SQL Azure muestra cómo ampliar su base de datos SQL hacia arriba o hacia abajo. Cambiar el nivel de precio de una base de datos de SQL Azure. |
| 57 | [Supervisar el rendimiento de la base de datos en la base de datos de SQL Azure](sql-database-single-database-monitor.md) | Obtenga información sobre las opciones para supervisar la base de datos con herramientas de Azure y vistas dinámicas de administración. |
| 58 | [Sugerencias de ajuste de rendimiento de base de datos SQL](sql-database-troubleshoot-performance.md) | Sugerencias para la optimización de base de datos de SQL Azure a través de evaluación y mejora del rendimiento. |
| 59 | [Cómo usar el procesamiento por lotes para mejorar el rendimiento de la aplicación de base de datos SQL](sql-database-use-batching-to-improve-performance.md) | El tema proporciona una prueba que operaciones de base de datos por lotes en gran medida imroves la velocidad y escalabilidad de las aplicaciones de base de datos de SQL Azure. Aunque estas técnicas por lotes funcionan para cualquier base de datos de SQL Server, el foco del artículo está en Azure. |



## <a name="tools-for-scaling-out"></a>Herramientas para el escalado

| &nbsp; | Título | Descripción |
| --: | :-- | :-- |
| 60 | [Diseñar modelos para las aplicaciones SaaS multiempresa y base de datos de SQL Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md) | En este artículo se describe los requisitos y patrones de arquitectura de datos comunes de base de datos multiempresa aplicaciones que se ejecutan en un entorno de nube deben tener en cuenta y las compensaciones diversos asociadas con estos modelos. También se explica cómo base de datos de SQL Azure, con sus grupos elásticas y herramientas elásticos, ayudar a estos requisitos de forma sin compromisos de direcciones. |
| 61 | [Migrar bases de datos existentes a escalado](sql-database-elastic-convert-to-use-elastic-tools.md) | Convertir bases de datos sharded para usar herramientas de elásticos de la base de datos creando una fragmentar Administrador de mapa |
| 62 | [Creación de bases de datos de nube scalable](sql-database-elastic-database-client-library.md) | Crear aplicaciones de base de datos de .NET scalable con la biblioteca de cliente de base de datos elásticas |
| 63 | [Contadores de rendimiento para el administrador del mapa de partes](sql-database-elastic-database-perf-counters.md) | ShardMapManager clase y datos dependientes enrutamiento contadores de rendimiento |
| 64 | [Agregar un fragmentar con herramientas de base de datos elásticas](sql-database-elastic-scale-add-a-shard.md) | Describe cómo usar elástico API de escala para agregar nuevos shards a un fragmentar conjunto. |
| 65 | [Implementar un servicio de combinación de división](sql-database-elastic-scale-configure-deploy-split-and-merge.md) | Dividir y combinar con herramientas de base de datos elásticas |
| 66 | [Enrutamiento según los datos](sql-database-elastic-scale-data-dependent-routing.md) | Cómo usar la clase ShardMapManager en las aplicaciones de .NET para enrutamiento, una característica de elásticos bases de datos para la base de datos de SQL Azure dependen de datos |
| 67 | [Herramientas de bases de datos elástico preguntas más frecuentes](sql-database-elastic-scale-faq.md) | Preguntas más frecuentes sobre escala elástico de base de datos SQL Azure. |
| 68 | [Elástico Glosario de herramientas de base de datos](sql-database-elastic-scale-glossary.md) | Explicación de los términos usados para herramientas de base de datos elástico |
| 69 | [Escalar con la base de datos de SQL Azure](sql-database-elastic-scale-introduction.md) | Software como desarrolladores de servicio (SaaS) puede crear fácilmente elásticos, scalable bases de datos en la nube mediante estas herramientas |
| 70 | [Credenciales utilizadas para tener acceso a la biblioteca de cliente de base de datos elásticas](sql-database-elastic-scale-manage-credentials.md) | Cómo configurar el nivel adecuado de credenciales, Administrador de sólo lectura, para las aplicaciones de base de datos elásticas |
| 71 | [Consulta de varias partes](sql-database-elastic-scale-multishard-querying.md) | Ejecutar consultas a través de shards mediante la biblioteca de cliente elásticos de la base de datos. |
| 72 | [Mover datos entre las bases de datos de escala de salida de la nube](sql-database-elastic-scale-overview-split-and-merge.md) | Explica cómo manipular shards y mover los datos a través de un servicio independiente con las API de base de datos elástico. |
| 73 | [Cambiar la escala de bases de datos con el administrador del mapa de partes](sql-database-elastic-scale-shard-map-management.md) | Cómo usar la ShardMapManager, la biblioteca de cliente de base de datos elástico |
| 74 | [Configuración de seguridad de la combinación de división](sql-database-elastic-scale-split-merge-security-configuration.md) | Configurar x409 certificados de cifrado |
| 75 | [Actualizar una aplicación para usar la biblioteca de cliente de base de datos elástico más reciente](sql-database-elastic-scale-upgrade-client-library.md) | Actualizar aplicaciones y bibliotecas con Nuget |
| 76 | [Elástico biblioteca de cliente de base de datos con el marco de la entidad](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) | Usar la biblioteca de cliente de elástico base de datos y entidad Framework para codificar las bases de datos |
| 77 | [Usar la biblioteca de cliente de base de datos elástico con Dapper](sql-database-elastic-scale-working-with-dapper.md) | Usar biblioteca de cliente de base de datos elástico con Dapper. |
| 78 | [Aplicaciones de múltiples arrendatarios con herramientas de base de datos elásticas y seguridad a nivel de fila](sql-database-elastic-tools-multi-tenant-row-level-security.md) | Obtenga información sobre cómo usar herramientas de elásticos de la base de datos junto con la seguridad de nivel de fila para crear una aplicación con un nivel de datos de gran escalabilidad en base de datos SQL Azure que admita varios inquilinos shards. |



## <a name="elastic-jobs"></a>Elásticos trabajos

| &nbsp; | Título | Descripción |
| --: | :-- | :-- |
| 79 | [Crear y administrar escala a bases de datos de Azure SQL con trabajos elásticos (vista preliminar)](sql-database-elastic-jobs-create-and-manage.md) | Repase la creación y administración de un trabajo elásticos de la base de datos. |
| 80 | [Introducción a los trabajos de base de datos elásticas](sql-database-elastic-jobs-getting-started.md) | cómo usar trabajos elásticos de la base de datos |
| 81 | [Administrar bases de datos de escala de salida de la nube](sql-database-elastic-jobs-overview.md) | Muestra el servicio de trabajo elásticos de la base de datos |
| 82 | [Crear y administrar un trabajos de elásticos de la base de datos de base de datos SQL con PowerShell (vista preliminar)](sql-database-elastic-jobs-powershell.md) | Usa PowerShell para administrar grupos de la base de datos de SQL Azure |
| 83 | [Base de datos elástico trabajos Introducción a la instalación](sql-database-elastic-jobs-service-installation.md) | Repase la instalación de la función de trabajo elástico. |
| 84 | [Desinstalar componentes de base de datos elástico trabajos](sql-database-elastic-jobs-uninstall.md) | Cómo desinstalar la herramienta de trabajos elásticos de la base de datos |



## <a name="elastic-pools"></a>Grupos de elásticos

| &nbsp; | Título | Descripción |
| --: | :-- | :-- |
| 85 | [¿Qué es un grupo de elástico Azure?](sql-database-elastic-pool.md) | Administrar cientos o miles de bases de datos mediante un fondo. Puede distribuirse un precio de un conjunto de unidades de rendimiento sobre el fondo. Va a mover bases de datos o de salida. |
| 86 | [Crear un grupo de la base de datos elástico con C#](sql-database-elastic-pool-create-csharp.md) | Utilice técnicas de desarrollo de base de datos de C# para crear un grupo de base de datos elástico scalable en base de datos de SQL Azure lo que puede compartir recursos en muchas bases de datos. |
| 87 | [Crear un nuevo grupo de base de datos elástico con PowerShell](sql-database-elastic-pool-create-powershell.md) | Obtenga información sobre cómo usar PowerShell para la base de datos de SQL Azure escalado recursos mediante la creación de un grupo de base de datos elástico scalable para administrar varias bases de datos. |
| 88 | [Script de PowerShell para identificar las bases de datos adecuados para un grupo de elásticos de la base de datos](sql-database-elastic-pool-database-assessment-powershell.md) | Un grupo de la base de datos elástico es una colección de recursos disponibles que están compartidos por un grupo de bases de datos elásticos. Este documento proporciona un script de Powershell para ayudar a evaluar la idoneidad de uso de un grupo de elásticos de la base de datos para un grupo de bases de datos. |
| 89 | [Supervisar y administrar un grupo de base de datos elástico con C#](sql-database-elastic-pool-manage-csharp.md) | Utilizar técnicas de desarrollo de base de datos de C# para administrar un grupo de elásticos de la base de datos de la base de datos de SQL Azure. |
| 90 | [Supervisar y administrar un grupo de elásticos de la base de datos con el portal de Azure](sql-database-elastic-pool-manage-portal.md) | Obtenga información sobre cómo usar el portal de Azure e inteligencia integrada de base de datos de SQL para administrar, supervisar y ajustar un grupo de base de datos elástico scalable para optimizar el rendimiento de la base de datos y administrar los costos. |
| 91 | [Supervisar y administrar un grupo de base de datos elástico con PowerShell](sql-database-elastic-pool-manage-powershell.md) | Obtenga información sobre cómo usar PowerShell para administrar un grupo de elásticos de la base de datos. |
| 92 | [Supervisar y administrar un grupo de elásticos de la base de datos con Transact-SQL](sql-database-elastic-pool-manage-tsql.md) | Use T SQL para crear una base de datos de SQL Azure en un grupo de elástico. O T SQL para mover la datbase dentro y fuera de los grupos. |
| 93 | [Información de precios y facturación de grupo elásticos de la base de datos](sql-database-elastic-pool-price.md) | Información de precios específicos de grupos de elásticos de la base de datos. |



## <a name="elastic-query"></a>Consulta elástica

| &nbsp; | Título | Descripción |
| --: | :-- | :-- |
| 94 | [Crear informes en bases de datos de escala de salida de la nube (vista preliminar)](sql-database-elastic-query-getting-started.md) | cómo usar cruzada consultas de base de datos de la base de datos |
| 95 | [Introducción a las consultas de base de datos cruzados (partición vertical) (vista preliminar)](sql-database-elastic-query-getting-started-vertical.md) | cómo usar la consulta de base de datos elástico con bases de datos divididas verticalmente |
| 96 | [Notificar en bases de datos de escala de salida de la nube (vista preliminar)](sql-database-elastic-query-horizontal-partitioning.md) | Cómo configurar elásticas consultas sobre particiones horizontales |
| 97 | [Introducción de consulta elásticos de la base de datos de Azure base de datos SQL (vista preliminar)](sql-database-elastic-query-overview.md) | Información general sobre la característica de consulta elásticas |
| 98 | [Hacer consultas en bases de datos de nube con diferentes esquemas (vista preliminar)](sql-database-elastic-query-vertical-partitioning.md) | Cómo configurar las consultas de bases de datos cruzadas sobre particiones verticales |



## <a name="elastic-transaction"></a>Transacción elástica

| &nbsp; | Título | Descripción |
| --: | :-- | :-- |
| 99 | [Transacciones distribuidas en bases de datos de nube](sql-database-elastic-transactions-overview.md) | Información general sobre las transacciones elásticos de la base de datos con la base de datos SQL Azure |



## <a name="backup-and-recovery"></a>Copia de seguridad y recuperación

| &nbsp; | Título | Descripción |
| --: | :-- | :-- |
| 100 | [Realizar copias de seguridad de base de datos SQL](sql-database-automated-backups.md) | Obtenga más información sobre la base de datos de SQL integradas copias de seguridad de base de datos que le permite funcionar hacer copia de una base de datos de SQL Azure a un punto anterior en el tiempo o copiar una base de datos a una nueva base de datos en una región geográfica (días hasta 35). |
| 101 | [Información general sobre continuidad empresarial con la base de datos de SQL Azure](sql-database-business-continuity.md) | Obtenga información sobre cómo la base de datos de SQL Azure admite continuidad empresarial de nube y recuperación de la base de datos y ayuda a mantener aplicaciones críticas nube que se ejecutan. |
| 102 | [Cómo restaurar una sola tabla desde una copia de seguridad de base de datos de SQL Azure](sql-database-cloud-migrate-restore-single-table-azure-backup.md) | Obtenga información sobre cómo restaurar una sola tabla de copia de seguridad de base de datos de SQL Azure. |
| 103 | [Diseñar una aplicación para la recuperación de nube con la replicación de Geo Active de base de datos de SQL](sql-database-designing-cloud-solutions-for-disaster-recovery.md) | Obtenga información sobre cómo diseñar soluciones de recuperación de desastres de nube de planificación de continuidad del negocio con replicación geo para copia de seguridad de datos de aplicación con la base de datos de SQL Azure. |
| 104 | [Restaurar una base de datos de SQL Azure o una migración tras error a un secundario](sql-database-disaster-recovery.md) | Obtenga información sobre cómo recuperar una base de datos de un error con las capacidades de restauración Geo y Azure base de datos activa Geo replicación de SQL o interrupciones del centro de datos regional. |
| 105 | [Realizar el ejercicio de recuperación de desastres](sql-database-disaster-recovery-drills.md) | Obtener instrucciones y procedimientos recomendados para usar la base de datos de SQL Azure para realizar tareas de recuperación de desastres que le ayudarán a mantienen sus objetivos críticos las aplicaciones de negocio y son resistentes a errores y las interrupciones. |
| 106 | [Estrategias para aplicaciones mediante grupo elástico de base de datos de SQL](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md) | Aprenda a diseñar su solución de nube para recuperación eligiendo la trama derecha migración tras error. |
| 107 | [Uso de la clase RecoveryManager para solucionar problemas de mapa de partes de](sql-database-elastic-database-recovery-manager.md) | Uso de la clase RecoveryManager para solucionar problemas con mapas de partes |
| 108 | [Importar un archivo de MOCHILA para crear una base de datos de SQL Azure](sql-database-import.md) | Crear una base de datos de SQL Azure importando un archivo de MOCHILA existente. |
| 109 | [Restaurar una base de datos de SQL Azure a un punto anterior en el tiempo con el Portal de Azure](sql-database-point-in-time-restore-portal.md) | Restaurar una base de datos de SQL Azure a un punto anterior en el tiempo. |
| 110 | [Restaurar una base de datos de SQL Azure a un punto anterior en el tiempo con PowerShell](sql-database-point-in-time-restore-powershell.md) | Restaurar una base de datos de SQL Azure a un punto anterior en el tiempo |
| 112 | [Recuperar una base de datos de SQL Azure con copias de seguridad de base de datos automática](sql-database-recovery-using-backups.md) | Obtenga información sobre la restauración de punto en el tiempo, que le permite restaurar una base de datos de SQL Azure a un punto anterior en el tiempo (hasta 35 días). |
| 113 | [Restaurar una base de datos de SQL Azure eliminado con el Portal de Azure](sql-database-restore-deleted-database-portal.md) | Restaurar una base de datos de SQL Azure eliminado (Portal de Azure). |
| 114 | [Restaurar una base de datos de SQL Azure eliminada con PowerShell](sql-database-restore-deleted-database-powershell.md) | Restaurar una base de datos eliminados SQL de Azure (PowerShell). |
| 115 | [Restaurar una base de datos a un punto anterior en el tiempo, restaurar una base de datos eliminado o recuperarse de una interrupción de centro de datos](sql-database-troubleshoot-backup-and-restore.md) | Obtenga información sobre cómo recuperar una base de datos de nube de errores e interrupciones mediante réplicas y copias de seguridad de base de datos de SQL Azure. |



## <a name="migrate"></a>Migrar

| &nbsp; | Título | Descripción |
| --: | :-- | :-- |
| 116 | [Exportar una base de datos de SQL Server a un archivo de MOCHILA con SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md) | Base de datos de Microsoft Azure SQL, migración de base de datos, exportar a base de datos, Exportar archivo MOCHILA, sqlpackage |
| 117 | [Exportar una base de datos de SQL Server a un archivo de MOCHILA con SQL Server Management Studio](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md) | Base de datos de Microsoft Azure SQL, migración de base de datos, exportar a base de datos, Exportar archivo MOCHILA, Asistente para exportar aplicaciones de nivel de datos |
| 118 | [Importar a la base de datos SQL desde un archivo MOCHILA SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md) | Base de datos de Microsoft Azure SQL, migración de base de datos, importar base de datos, importar archivo MOCHILA, sqlpackage |
| 119 | [Importar desde MOCHILA a la base de datos de SQL con SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md) | Base de datos de SQL de Microsoft Azure, implementar de base de datos, migración de base de datos, base de datos de importación, exportación de base de datos, Asistente para la migración |
| 120 | [Migrar la base de datos de SQL Server a la base de datos SQL implementar base de datos en el Asistente de base de datos de Microsoft Azure](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md) | Base de datos de Microsoft Azure SQL, migración de base de datos, el Asistente para la base de datos de Microsoft Azure |
| 121 | [Migrar la base de datos de SQL Server para utilizar la replicación de transacciones de base de datos SQL Azure](sql-database-cloud-migrate-compatible-using-transactional-replication.md) | Base de datos de Microsoft Azure SQL, migración de base de datos, importar base de datos, replicación de transacciones |
| 122 | [Determinar la compatibilidad de base de datos SQL con SqlPackage.exe](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md) | Microsoft Azure base de datos SQL, migración de base de datos, la compatibilidad de base de datos de SQL, SqlPackage |
| 123 | [Usar SQL Server Management Studio para la compatibilidad de la base de datos de SQL de determinar antes de la migración a la base de datos de SQL Azure](sql-database-cloud-migrate-determine-compatibility-ssms.md) | Microsoft Azure base de datos SQL, migración de base de datos, la compatibilidad de base de datos de SQL, exportar Asistente de aplicación de nivel de datos |
| 124 | [Usar el Asistente para la migración de SQL Azure para problemas de compatibilidad de base de datos de corregir SQL Server antes de la migración a la base de datos de SQL Azure](sql-database-cloud-migrate-fix-compatibility-issues.md) | Microsoft Azure base de datos SQL, migración de base de datos, compatibilidad, Asistente para la migración de SQL Azure |
| 125 | [Migrar una base de datos SQL Server a la base de datos SQL Azure con SQL Server Data Tools para Visual Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md) | Microsoft Azure base de datos SQL, migración de base de datos, compatibilidad, el Asistente para migración de SQL Azure, SSDT |
| 126 | [Solucionar problemas de compatibilidad de base de datos de SQL Server con SQL Server Management Studio antes de la migración a la base de datos SQL](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md) | Microsoft Azure base de datos SQL, migración de base de datos, compatibilidad, Asistente para la migración de SQL Azure |
| 127 | [Archivar una base de datos de SQL Azure a un archivo de MOCHILA con PowerShell](sql-database-export-powershell.md) | Archivar una base de datos de SQL Azure a un archivo de MOCHILA con PowerShell |
| 128 | [Importar un archivo de MOCHILA para crear una base de datos de SQL Azure con PowerShell](sql-database-import-powershell.md) | Importar un archivo de MOCHILA para crear una base de datos de SQL Azure con PowerShell |
| 129 | [Cargar datos de CSV en el almacén de datos de SQL Azure (archivos planos)](sql-database-load-from-csv-with-bcp.md) | Tamaño de datos pequeños, utiliza bcp para importar datos en la base de datos de SQL Azure. |
| 130 | [Mover las bases de datos entre servidores, entre las suscripciones y dentro y fuera de Azure](sql-database-troubleshoot-moving-data.md) | Pasos rápidos para copiar, mover y migrar datos y bases de datos en la base de datos de SQL Azure. |



## <a name="move-data-in-and-out"></a>Mover los datos de entrada y salida

| &nbsp; | Título | Descripción |
| --: | :-- | :-- |
| 131 | [Migración de base de datos de SQL Server a la base de datos de SQL en la nube](sql-database-cloud-migrate.md) | Obtenga información sobre cómo se pueden local base de datos migración de SQL Server a la base de datos de SQL de Azure en la nube. Usar herramientas de migración de base de datos para probar la compatibilidad antes de la migración de base de datos. |
| 132 | [Copiar una base de datos SQL Azure](sql-database-copy.md) | Crear una copia de una base de datos de SQL Azure |
| 133 | [Archivar una base de datos de SQL Azure a un archivo de MOCHILA con el Portal de Azure](sql-database-export.md) | Archivar una base de datos de SQL Azure a un archivo de MOCHILA con el Portal de Azure |



## <a name="security"></a>Seguridad

| &nbsp; | Título | Descripción |
| --: | :-- | :-- |
| 134 | [Conectarse a la base de datos SQL o almacén de datos SQL mediante la autenticación de Azure Active Directory](sql-database-aad-authentication.md) | Obtenga información sobre cómo conectarse a la base de datos SQL con autenticación de Azure Active Directory. |
| 135 | [Siempre se cifra: Proteger datos confidenciales en la base de datos SQL y almacenar las claves de cifrado en el almacén de certificados de Windows](sql-database-always-encrypted.md) | Proteger datos confidenciales en la base de datos SQL en minutos. |
| 136 | [Siempre se cifra: Proteger datos confidenciales en la base de datos SQL y almacenar las claves de cifrado en depósito de clave de Azure](sql-database-always-encrypted-azure-key-vault.md) | Proteger datos confidenciales en la base de datos SQL en minutos. |
| 137 | [Base de datos SQL: compatibilidad con clientes y cambios de extremo IP de auditoría](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md) | Obtenga información sobre compatibilidad con clientes de nivel inferior de base de datos SQL y IP cambios de extremo para auditoría. |
| 138 | [Configurar las reglas de firewall de nivel de servidor de base de datos de SQL Azure con PowerShell](sql-database-configure-firewall-settings-powershell.md) | Obtenga información sobre cómo configurar el firewall para las direcciones IP que tienen acceso a las bases de datos de SQL Azure. |
| 139 | [Configurar las reglas de firewall de nivel de servidor de base de datos de SQL Azure con la API de REST](sql-database-configure-firewall-settings-rest.md) | Obtenga información sobre cómo configurar el firewall para las direcciones IP que tienen acceso a las bases de datos de SQL Azure. |
| 140 | [Configurar reglas de firewall de nivel de base de datos y servidor de base de datos de SQL Azure con SQL T](sql-database-configure-firewall-settings-tsql.md) | Obtenga información sobre cómo configurar el firewall para las direcciones IP que tienen acceso a las bases de datos de SQL Azure. |
| 141 | [Introducción a SQL base de datos dinámicos datos Masking (Portal de Azure)](sql-database-dynamic-data-masking-get-started.md) | Cómo empezar a trabajar con SQL bases de datos dinámicos datos Masking en el Portal de Azure |
| 142 | [Introducción a SQL base de datos dinámicos datos Masking (Portal de Azure clásico)](sql-database-dynamic-data-masking-get-started-portal.md) | Cómo empezar a trabajar con SQL bases de datos dinámicos datos Masking en el Portal de clásico de Azure |
| 143 | [Configurar las reglas de firewall de base de datos de SQL Azure \- información general](sql-database-firewall-configure.md) | Obtenga información sobre cómo configurar un servidor de seguridad de base de datos SQL con las reglas de firewall de nivel de servidor y de nivel de base de datos para administrar el acceso. |
| 144 | [Tutorial de base de datos SQL: crear cuentas de usuario para acceder y administrar una base de datos de base de datos SQL](sql-database-get-started-security.md) | Aprenda a crear cuentas de usuario para acceder y administrar una base de datos. |
| 145 | [Autenticación de base de datos SQL y la autorización: conceder acceso](sql-database-manage-logins.md) | Obtenga más información sobre la administración de seguridad de base de datos de SQL, específicamente cómo administrar la seguridad de access e inicio de sesión de base de datos a través de la cuenta principal del nivel de servidor. |
| 146 | [Limitaciones y directrices de seguridad de base de datos de SQL azure](sql-database-security-guidelines.md) | Obtenga información sobre consideraciones de base de datos de SQL de Microsoft Azure y limitaciones relacionados con la seguridad. |
| 147 | [Introducción a la detección de amenaza de base de datos de SQL](sql-database-threat-detection-get-started.md) | Cómo empezar a trabajar con detección de amenaza de base de datos de SQL en el Portal de Azure |
| 148 | [Cómo llevar a cabo tareas administrativas comunes como restablecer la contraseña de administrador en la base de datos de SQL Azure](sql-database-troubleshoot-permissions.md) | Describe cómo realizar tareas administrativas comunes de base de datos de SQL. Por ejemplo, restablecer la contraseña de administrador, conceder y revocar el acceso. |



## <a name="manage-your-database"></a>Administrar la base de datos

| &nbsp; | Título | Descripción |
| --: | :-- | :-- |
| 149 | [Introducción a la auditoría de la base de datos SQL](sql-database-auditing-get-started.md) | Introducción a la auditoría de la base de datos SQL |
| 150 | [Configurar una regla de firewall de nivel de servidor de base de datos de SQL Azure con el Portal de Azure](sql-database-configure-firewall-settings.md) | Obtenga información sobre cómo configurar el firewall para las direcciones IP que acceso el servidor de SQL Azure. |
| 151 | [Copiar una base de datos de SQL Azure con el portal de Azure](sql-database-copy-portal.md) | Crear una copia de una base de datos de SQL Azure |
| 152 | [Administrar bases de datos de SQL Azure mediante la automatización de Azure](sql-database-manage-automation.md) | Obtenga información acerca de cómo puede utilizar el servicio de automatización de Azure para administrar bases de datos de SQL Azure en escala. |
| 153 | [Información general: herramientas de administración para base de datos de SQL](sql-database-manage-overview.md) | Compara las herramientas y opciones de administración de la base de datos de SQL Azure |
| 154 | [Supervisión con vistas dinámicas de administración de base de datos SQL Azure](sql-database-monitoring-with-dmvs.md) | Aprenda a detectar y diagnosticar problemas comunes de rendimiento mediante el uso de vistas dinámicas de administración para supervisar la base de datos de SQL de Microsoft Azure. |
| 155 | [Proteger la base de datos SQL](sql-database-security.md) | Obtenga más información sobre la seguridad de base de datos de SQL Azure y SQL Server, incluidas las diferencias entre la nube y SQL Server local cuando se trata de autenticación, autorización, seguridad de la conexión, el cifrado y cumplimiento. |



## <a name="extended-events"></a>Eventos ampliados

| &nbsp; | Título | Descripción |
| --: | :-- | :-- |
| 156 | [Código de destino de archivo de evento para eventos ampliados de base de datos de SQL](sql-database-xevent-code-event-file.md) | Proporciona PowerShell y Transact-SQL para código en dos fases de ejemplo que muestra el destino del archivo de evento en un evento ampliado en base de datos de SQL Azure. Almacenamiento de Azure es una parte requerida de este escenario. |
| 157 | [Llamar a código de destino de búfer de eventos ampliados de base de datos de SQL](sql-database-xevent-code-ring-buffer.md) | Proporciona un ejemplo de código de Transact-SQL que se realiza fácil y rápida mediante el uso de destino de búfer de base de datos de SQL Azure. |
| 158 | [Ampliado eventos de base de datos de SQL](sql-database-xevent-db-diff-from-svr.md) | Describe eventos ampliados (XEvents) en la base de datos de SQL Azure y cómo sesiones de eventos difieren ligeramente de las sesiones de eventos en Microsoft SQL Server. |



## <a name="geo-replication"></a>Replicación de geo

| &nbsp; | Título | Descripción |
| --: | :-- | :-- |
| 159 | [Iniciar un error planificado o para la base de datos de SQL Azure con el portal de Azure](sql-database-geo-replication-failover-portal.md) | Iniciar un error planificado o para la base de datos de SQL Azure con el portal de Azure |
| 160 | [Iniciar un error planificado o para la base de datos de Azure SQL con PowerShell](sql-database-geo-replication-failover-powershell.md) | Iniciar un error planificado o para la base de datos de SQL Azure con PowerShell |
| 161 | [Iniciar un error planificado o para la base de datos de Azure SQL con Transact-SQL](sql-database-geo-replication-failover-transact-sql.md) | Iniciar un error planificado o para la base de datos de SQL Azure con Transact-SQL |
| 162 | [Información general: Base de datos activa Geo replicación de SQL](sql-database-geo-replication-overview.md) | Replicación de Geo Active le permite configurar 4 réplicas de la base de datos en cualquiera de los centros de datos de Azure. |
| 163 | [Configurar la replicación de Geo para base de datos de SQL Azure con el portal de Azure](sql-database-geo-replication-portal.md) | Configurar la replicación de Geo para la base de datos de SQL Azure con el portal de Azure |
| 164 | [Configurar la replicación de Geo para base de datos SQL Azure con PowerShell](sql-database-geo-replication-powershell.md) | Configurar la replicación de Geo Active para base de datos de SQL Azure con PowerShell |
| 165 | [Cómo administrar la seguridad de la base de datos de SQL Azure tras la recuperación](sql-database-geo-replication-security-config.md) | Este tema explica las consideraciones de seguridad para administrar la seguridad tras la restauración de una base de datos o un error. |
| 166 | [Configurar la replicación de Geo para base de datos SQL Azure con Transact-SQL](sql-database-geo-replication-transact-sql.md) | Configurar la replicación de Geo para la base de datos de SQL Azure con Transact-SQL |
| 167 | [Geo-restaurar una base de datos de SQL Azure desde una copia de seguridad geo redundantes con el Portal de Azure](sql-database-geo-restore-portal.md) | Geo-restaurar una base de datos de SQL Azure desde una copia de seguridad geo redundantes (Portal de Azure). |
| 168 | [Restaurar una base de datos de SQL Azure desde una copia de seguridad geo redundantes con PowerShell](sql-database-geo-restore-powershell.md) | Restaurar una base de datos de SQL Azure en un nuevo servidor desde una copia de seguridad geo redundantes |



## <a name="upgrade"></a>Actualizar

| &nbsp; | Título | Descripción |
| --: | :-- | :-- |
| 169 | [Mejora el rendimiento de la consulta con 130 de nivel de compatibilidad de base de datos de SQL Azure](sql-database-compatibility-level-query-performance-130.md) | Pasos y herramientas para determinar qué nivel de compatibilidad es la mejor para la base de datos en la base de datos de SQL Azure o Microsoft SQL Server |
| 170 | [Administrar actualizaciones graduales de aplicaciones de nube con la base de datos activa Geo replicación de SQL](sql-database-manage-application-rolling-upgrade.md) | Obtenga información sobre cómo usar la replicación de base de datos de SQL Azure geo para admitir actualizaciones en línea de la aplicación de nube. |
| 171 | [Actualización a V12 de base de datos de SQL Azure con el portal de Azure](sql-database-upgrade-server-portal.md) | Se explica cómo actualizar a V12 de base de datos de SQL Azure incluyendo cómo actualizar bases de datos Web y empresa y cómo se actualiza un servidor V11 migrar sus bases de datos directamente en un grupo de elásticos de la base de datos con el portal de Azure. |
| 172 | [Actualización a V12 de base de datos de SQL Azure con PowerShell](sql-database-upgrade-server-powershell.md) | Se explica cómo actualizar a V12 de base de datos de SQL Azure e incluye cómo actualizar bases de datos Web y empresa y cómo se actualiza un servidor V11 migrar sus bases de datos directamente en un grupo de base de datos elástico con PowerShell. |
| 173 | [Planear y prepararse para actualizar a V12 de base de datos de SQL](sql-database-v12-plan-prepare-upgrade.md) | Describe los preparativos y limitaciones implicados en la actualización a la versión V12 de Azure base de datos SQL. |
| 174 | [Novedades de V12 de base de datos de SQL](sql-database-v12-whats-new.md) | Describe por qué le vendrá sistemas empresariales que están utilizando la base de datos de SQL Azure en la nube con la actualización a versión V12 ahora. |
| 175 | [P+F de puesta de sol Web y Business Edition](sql-database-web-business-sunset-faq.md) | Averigüe cuándo se retirará de las bases de datos Web de SQL Azure y empresa y obtenga más información sobre las características y funcionalidades de los niveles de servicio de nuevo. |



## <a name="tools"></a>Herramientas

| &nbsp; | Título | Descripción |
| --: | :-- | :-- |
| 176 | [Administrar la base de datos SQL Azure con PowerShell](sql-database-command-line-tools.md) | Administración de base de datos de SQL Azure con PowerShell. |
| 177 | [Tutorial de base de datos SQL: conectar Excel a una base de datos de SQL Azure y crear un informe](sql-database-connect-excel.md) | Obtenga información sobre cómo conectar Microsoft Excel a la base de datos de SQL Azure en la nube. Importar datos en Excel para la exploración de datos e informes. |
| 178 | [Crear una base de datos SQL y realizar tareas comunes de instalación de base de datos con los cmdlets de PowerShell](sql-database-get-started-powershell.md) | Obtenga información sobre ahora crear una base de datos SQL con PowerShell. Tareas comunes de instalación de base de datos se pueden administrar a través de los cmdlets de PowerShell. |
| 179 | [Introducción a la sincronización de datos SQL Azure (vista preliminar)](sql-database-get-started-sql-data-sync.md) | Este tutorial le ayuda a empezar a trabajar con la sincronización de datos de SQL Azure (vista preliminar). |
| 180 | [Administrar la base de datos de SQL Azure con SQL Server Management Studio](sql-database-manage-azure-ssms.md) | Aprenda a usar SQL Server Management Studio para administrar bases de datos y servidores de base de datos de SQL. |
| 181 | [Administrar bases de datos de SQL Azure con el portal de Azure](sql-database-manage-portal.md) | Aprenda a usar el Portal de Azure para administrar una base de datos relacional en la nube con el Portal de Azure. |
| 182 | [Cambiar el nivel y rendimiento de nivel de servicio (nivel de precios) de una base de datos SQL con PowerShell](sql-database-scale-up-powershell.md) | Cambiar el nivel de servicio y cómo ampliar su base de datos SQL hacia arriba o hacia abajo con PowerShell muestra el nivel de rendimiento de una base de datos de SQL Azure. Cambiar el nivel de precio de una base de datos de SQL Azure con PowerShell. |
| 183 | [Usar SQL Server Management Studio en Azure RemoteApp para conectarse a la base de datos SQL](sql-database-ssms-remoteapp.md) | Use este tutorial para aprender a usar SQL Server Management Studio en Azure RemoteApp de seguridad y el rendimiento al conectarse a la base de datos SQL |



## <a name="reference"></a>Referencia

| &nbsp; | Título | Descripción |
| --: | :-- | :-- |
| 184 | [Bibliotecas de conexión de base de datos de SQL y SQL Server](sql-database-libraries.md) | Muestra el número de versión mínima para cada controlador que pueden usar los programas de cliente para conectarse a la base de datos de SQL Azure o Microsoft SQL Server. Se proporciona un vínculo para obtener información de versión acerca de los controladores que se publican en la Comunidad en lugar de Microsoft. |
| 185 | [Límites de recursos de base de datos de SQL Azure](sql-database-resource-limits.md) | Esta página describe algunos límites de recursos comunes de base de datos de SQL Azure. |
| 186 | [Diferencias de Transact-SQL de base de datos de SQL Azure](sql-database-transact-sql-information.md) | Instrucciones de Transact-SQL que son menores totalmente compatibles con la base de datos de SQL Azure |



## <a name="miscellaneous"></a>Varios

| &nbsp; | Título | Descripción |
| --: | :-- | :-- |
| 187 | [Copiar una base de datos de SQL Azure con PowerShell](sql-database-copy-powershell.md) | Crear copia de una base de datos de SQL Azure con PowerShell |
| 188 | [Copiar una base de datos de SQL Azure con Transact-SQL](sql-database-copy-transact-sql.md) | Crear copia de una base de datos de SQL Azure con Transact-SQL |
| 189 | [Directrices y limitaciones de General de la base de datos SQL Azure](sql-database-general-limitations.md) | Esta página describe algunas limitaciones generales para la base de datos de SQL Azure, así como las áreas de interoperabilidad y soporte técnico. |
| 190 | [Recomendaciones de nivel de precios de base de datos SQL](sql-database-service-tier-advisor.md) | Al cambiar los precios niveles en el portal de Azure, recomendaciones de nivel de precios son siempre que recomienda el nivel que mejor se adapta para ejecutar la carga de trabajo de existente Azure base de datos SQL. Niveles de precios describen el nivel de rendimiento y los niveles de servicio de una base de datos SQL. |


&nbsp;

<hr/>

<a name="extras_append"></a>

## <a name="extras"></a>Adicionales

<a name="extra_related_articles"></a>

### <a name="related-articles-from-other-azure-services"></a>Artículos relacionados de otros servicios de Azure

- [Realizar copias de seguridad de la aplicación de servicios de aplicación de Azure en Azure](../app-service-web/web-sites-backup.md)

<a name="extra_documentation_tools"></a>

### <a name="documentation-tools"></a>Herramientas de documentación

- [Actualizaciones presentó para la base de datos de SQL Azure](http://azure.microsoft.com/updates/?service=sql-database)

- [Buscar todos los tipos de documentación de Microsoft Azure, con los filtros](http://azure.microsoft.com/search/documentation/)

<a name="extra_learning_path_graphics"></a>

### <a name="learning-path-graphics"></a>Gráficos de ruta de aprendizaje

- [Gráficos de ruta de aprendizaje para todos los servicios de Microsoft Azure](http://azure.microsoft.com/documentation/learning-paths/)

- [Ruta de aprendizaje: Obtenga información sobre la base de datos SQL Azure](http://azure.microsoft.com/documentation/learning-paths/sql-database-training-learn-sql-database/)

- [Ruta de aprendizaje: Escala elástico de base de datos SQL](http://azure.microsoft.com/documentation/learning-paths/sql-database-elastic-scale/)


<!--
AzIxAA.ExtraAppend.sql-database.txt
C:\_MainW\VS15\AzureIndexAllArticles\AzureIndexAllArticles\In-Out\In\

This bullet link is improperly disallowed by publishing automation due to presence of string 'docuXXmentation/arXXticles':
- [Search SQL Database documentation, with filters](http://azure.microsoft.com/docuXXmentation/arXXticles/?service=sql-database)
-->


