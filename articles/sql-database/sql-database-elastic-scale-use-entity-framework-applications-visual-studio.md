<properties 
    pageTitle="Usar biblioteca de cliente de base de datos elástico con entidad Framework | Microsoft Azure" 
    description="Usar la biblioteca de cliente de elástico base de datos y entidad Framework para codificar las bases de datos" 
    services="sql-database" 
    documentationCenter="" 
    manager="jhubbard" 
    authors="torsteng" 
    editor=""/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/27/2016" 
    ms.author="torsteng"/>

# <a name="elastic-database-client-library-with-entity-framework"></a>Elástico biblioteca de cliente de base de datos con el marco de la entidad 
 
Este documento muestra los cambios en una aplicación de marco de entidad que son necesarias para integrar con las [Herramientas de base de datos elástico](sql-database-elastic-scale-introduction.md). El foco está en Redactar [fragmentar asignar la administración](sql-database-elastic-scale-shard-map-management.md) y el [enrutamiento según los datos](sql-database-elastic-scale-data-dependent-routing.md) con el enfoque de entidad Framework **Code First** . El tutorial de [Code First-nueva base de datos](http://msdn.microsoft.com/data/jj193542.aspx) para EF sirve como ejemplo en este documento. El código de ejemplo que acompaña a este documento forma parte de las herramientas de base de datos elástico conjunto de ejemplos en los ejemplos de código de Visual Studio.
  
## <a name="downloading-and-running-the-sample-code"></a>Descargar y ejecutar el código de ejemplo
Para descargar el código de este artículo:

* Se requiere Visual Studio 2012 o posterior. 
* Inicie Visual Studio. 
* En Visual Studio, seleccione Archivo -> Nuevo proyecto. 
* En el cuadro de diálogo 'Nuevo proyecto', vaya a los **Ejemplos en línea** para **Visual C#** y escriba "db elástica" en el cuadro de búsqueda en la esquina superior derecha.
    
    ![Marco de entidad y aplicación de ejemplo elásticos de la base de datos][1] 

    Seleccione los datos de ejemplo denominada **Elástico herramientas DB de SQL Azure: integración de marco de entidad**. Después de aceptar la licencia, carga los datos de ejemplo. 

Para ejecutar el ejemplo, debe crear tres bases de datos vacías en la base de datos de SQL Azure:

* Base de datos de administrador de mapa de partes
* Base de datos de partes 1
* Base de datos de fragmentar 2

Una vez que haya creado estas bases de datos, rellene los marcadores de posición en **Program.cs** con el nombre de su servidor de base de datos de SQL Azure, los nombres de base de datos y las credenciales para conectarse a las bases de datos. Crear la solución en Visual Studio. Visual Studio descargará los paquetes de NuGet necesarios para la biblioteca de cliente de base de datos elástico Framework de entidad y a continuación, como parte del proceso de generación de control de errores transitorias. Asegúrese de que la restauración de paquetes de NuGet está habilitada para la solución. Puede habilitar a esta configuración haciendo doble clic en el archivo de solución en el Explorador de soluciones de Visual Studio. 

## <a name="entity-framework-workflows"></a>Flujos de trabajo de entidad Framework 

Los programadores de marco de entidad confiar en una de las siguientes cuatro flujos de trabajo para generar aplicaciones y garantizar la persistencia de objetos de la aplicación: 

* **Code First (nueva base de datos)**: programador EF la crea el modelo en el código de la aplicación y EF genera la base de datos de ella. 
* **Code First (base de datos existente)**: el programador permite EF generar el código de la aplicación para el modelo desde una base de datos existente.
* **Primer modelo**: el programador crea el modelo en el diseñador EF y EF crea la base de datos del modelo.
* **Primera base de datos**: el desarrollador utiliza EF herramientas para deducir el modelo desde una base de datos existente. 

Todos estos enfoques se basan en la clase DbContext transparente administrar conexiones de base de datos y el esquema de base de datos para una aplicación. Como se explica detalladamente más adelante en el documento, diferentes constructores en la clase base DbContext permiten para diferentes niveles de control sobre la creación de la conexión, creación de esquemas y arranque de base de datos. Surgen desafíos principalmente desde el hecho de que la administración de la conexión de base de datos proporcionada por EF cruza con las capacidades de administración de conexión de las interfaces enrutamiento dependientes de datos proporcionadas por la biblioteca de cliente de base de datos elástico. 

## <a name="elastic-database-tools-assumptions"></a>Suposiciones de herramientas elásticos de la base de datos 

Definiciones de términos, vea [Glosario de herramientas elásticos de la base de datos](sql-database-elastic-scale-glossary.md).

Biblioteca de cliente de base de datos elástico, se definen las particiones de los datos de aplicación denominados shardlets. Shardlets se identifican mediante una clave de sharding y se asignan a bases de datos específicas. Una aplicación puede tener las bases de datos según sea necesario y distribuir el shardlets para proporcionar suficiente capacidad o rendimiento dados los requisitos de negocio actual. La asignación de valores de clave de sharding a las bases de datos se almacena en un mapa de fragmentar proporcionado por el cliente de base de datos elástico API. Llamamos a esta capacidad de **Administración de mapa fragmentar**o SMM abreviado. El mapa de fragmentar también sirve como el agente de conexiones de base de datos para las solicitudes de llevar a cabo una clave de sharding. Nos referimos a esta capacidad como **enrutamiento según los datos**. 
 
El Administrador de mapa fragmentar protege a los usuarios de las vistas incoherentes en los datos de shardlet que pueden producirse cuando se producen operaciones de administración de shardlet simultáneas (como reubicar datos desde una fragmentar). Para ello, los mapas fragmentar administran por el agente de la biblioteca de cliente las conexiones de base de datos para una aplicación. Esto permite la funcionalidad de mapa fragmentar eliminar automáticamente una conexión de base de datos cuando las operaciones de administración de fragmentar pueden afectar el shardlet que se ha creado para la conexión. Este método se debe integrar con algunas de las funciones del EF, como la creación de nuevas conexiones desde uno existente para comprobar la existencia de la base de datos. En general, nuestros ha sido que funcionan los constructores estándares de DbContext solo trabajos confiable para conexiones de base de datos cerrada que se pueden clonar con seguridad para EF. En su lugar el principio del diseño de base de datos elástico es solo intermediario conexiones abiertas. Uno puede pensar que cerrar una conexión con la biblioteca cliente antes entregando el a la DbContext EF puede resolver este problema. Sin embargo, al cerrar la conexión y depender EF para volver a abrirlo, uno foregoes las comprobaciones de validación y coherencia realizadas por la biblioteca. Sin embargo, la funcionalidad de migraciones de EF, utiliza estas conexiones para administrar el esquema de base de datos subyacente de forma transparente para la aplicación. Lo ideal es que, vamos a conservar y combinar todas estas capacidades de la biblioteca de cliente de base de datos elásticas y el EF en la misma aplicación. La siguiente sección describe estos requisitos con más detalle y propiedades. 


## <a name="requirements"></a>Requisitos 

Cuando se trabaja con las API de marco de entidad y de biblioteca de cliente de base de datos elástico, queremos conservar las siguientes propiedades: 

* **Escalado**: agregar o quitar las bases de datos del nivel de datos de la aplicación sharded según sea necesario para las demandas de capacidad de la aplicación. Esto significa control sobre la API del administrador para administrar bases de datos y las asignaciones de shardlets de mapa de la creación y eliminación de bases de datos y el uso de las partes elásticos de la base de datos. 

* **Coherencia**: la aplicación emplea sharding y utiliza las funciones de enrutamiento dependientes de datos de la biblioteca de cliente. Para evitar que se dañen los o resultados de la consulta incorrecta, se negociada conexiones a través del Administrador de mapa fragmentar. También mantiene la validación y coherencia.
 
* **Code First**: conservar la comodidad de paradigma primera del EF código. En código en primer lugar, las clases de la aplicación se asignan transparente a las estructuras de base de datos subyacente. El código de la aplicación interactúa con DbSets que la mayoría de los aspectos implicados en el procesamiento de la base de datos subyacente de la máscara.
 
* **Esquema**: entidad Framework controla la creación de esquemas de base de datos inicial y evolución de esquema posteriores a través de las migraciones. Al mantener estas capacidades, adaptar su aplicación es fácil como evolucionar de los datos. 

Las instrucciones siguientes indica cómo cumplir estos requisitos para las aplicaciones Code First con herramientas de base de datos elástico. 

## <a name="data-dependent-routing-using-ef-dbcontext"></a>Datos dependientes enrutamiento con DbContext EF 

Conexiones de base de datos con el marco de trabajo de entidad normalmente se administran mediante subclases de **DbContext**. Crear estas subclases derivado de **DbContext**. Aquí es donde define el **DbSets** que implementan las colecciones de seguridad de base de datos de objetos CLR para la aplicación. En el contexto de enrutamiento según los datos, podemos identificar varias propiedades útiles que no contienen necesariamente para otros escenarios de aplicación EF primera de código: 

* La base de datos ya existe y se ha registrado en el mapa de fragmentar elásticos de la base de datos. 
* Ya se ha implementado el esquema de la aplicación de la base de datos (que se explica a continuación). 
* Conexiones de enrutamiento dependientes de datos a la base de datos son negociadas por el mapa fragmentar. 

Para integrar **DbContexts** con enrutamiento según los datos de escalado:

1. Crear conexiones de física de la base de datos a través de la interfaz de cliente de elásticos de la base de datos del Administrador de mapa fragmentar, 
2. Ajustar la conexión con la subclase **DbContext**
3. Pase las clases de base de **DbContext** para asegurarse de que todo el procesamiento del lado EF pasa también la conexión hacia abajo. 

En el ejemplo siguiente se muestra este enfoque. (Este código también está en el proyecto de Visual Studio adjunto)

    public class ElasticScaleContext<T> : DbContext
    {
    public DbSet<Blog> Blogs { get; set; }
    …

        // C'tor for data dependent routing. This call will open a validated connection 
        // routed to the proper shard by the shard map manager. 
        // Note that the base class c'tor call will fail for an open connection
        // if migrations need to be done and SQL credentials are used. This is the reason for the 
        // separation of c'tors into the data-dependent routing case (this c'tor) and the internal c'tor for new shards.
        public ElasticScaleContext(ShardMap shardMap, T shardingKey, string connectionStr)
            : base(CreateDDRConnection(shardMap, shardingKey, connectionStr), 
            true /* contextOwnsConnection */)
        {
        }

        // Only static methods are allowed in calls into base class c'tors.
        private static DbConnection CreateDDRConnection(
        ShardMap shardMap, 
        T shardingKey, 
        string connectionStr)
        {
            // No initialization
            Database.SetInitializer<ElasticScaleContext<T>>(null);

            // Ask shard map to broker a validated connection for the given key
            SqlConnection conn = shardMap.OpenConnectionForKey<T>
                                (shardingKey, connectionStr, ConnectionOptions.Validate);
            return conn;
        }    

## <a name="main-points"></a>Puntos principales
* Un nuevo constructor reemplaza el constructor predeterminado en la subclase DbContext 
* El nuevo constructor toma los argumentos necesarios para enrutamiento según los datos a través de la biblioteca de cliente de elásticos de la base de datos:
    * el mapa de fragmentar para tener acceso a las interfaces de enrutamiento de dependen de datos
    * la clave de sharding para identificar el shardlet
    * una cadena de conexión con las credenciales para la conexión de enrutamiento dependientes de datos a las partes. 
 
* La llamada al constructor de clase base tiene un rodeo en un método estático que realiza todos los pasos necesarios para enrutamiento según los datos. 
   * Utiliza la llamada OpenConnectionForKey de las interfaces de cliente de base de datos elásticos en el mapa de partes para establecer una conexión abierta.
   * El mapa de fragmentar crea la conexión abierta a las partes que contiene el shardlet para la clave de sharding determinado.
   * Esta conexión abierta se pasa al constructor de clase base de DbContext para indicar que esta conexión EF utilizarse en lugar de lo que EF permite crear una nueva conexión automáticamente. De este modo, la conexión se ha etiquetado por el cliente de base de datos elástico API para que puede garantizar la coherencia en las operaciones de administración de mapa fragmentar.
 
  
Use el nuevo constructor para su subclase DbContext en lugar de al constructor predeterminado en el código. Aquí tenemos un ejemplo: 

    // Create and save a new blog.

    Console.Write("Enter a name for a new blog: "); 
    var name = Console.ReadLine(); 

    using (var db = new ElasticScaleContext<int>( 
                            sharding.ShardMap,  
                            tenantId1,  
                            connStrBldr.ConnectionString)) 
    { 
        var blog = new Blog { Name = name }; 
        db.Blogs.Add(blog); 
        db.SaveChanges(); 

        // Display all Blogs for tenant 1 
        var query = from b in db.Blogs 
                    orderby b.Name 
                    select b; 
     … 
    }

El nuevo constructor abre la conexión a las partes que contiene los datos de la shardlet identificada por el valor de **tenantid1**. El código del bloque **utilizando** permanece sin cambios para tener acceso a la **DbSet** para blogs con EF en las partes para **tenantid1**. Esto cambia semántica para el código en el uso de bloquear tal que todas las operaciones de base de datos ahora se limitan a las partes de una donde se guarda la **tenantid1** . Por ejemplo, una consulta LINQ sobre los blogs **DbSet** solo devolverá blogs almacenados en las partes actual, pero no los almacenados en otros shards.  

#### <a name="transient-faults-handling"></a>Tratamiento de errores transitorias
El equipo de Microsoft Patterns & prácticas había publicado [El transitorias tratamiento aplicación bloque Fault](https://msdn.microsoft.com/library/dn440719.aspx). La biblioteca se utiliza con la biblioteca de cliente de escala elásticos en combinación con EF. Sin embargo, asegúrese de que cualquier excepción transitoria devuelve a un lugar donde es posible garantizar que se utiliza el nuevo constructor tras un error transitorio para que se produce algún intento de conexión nueva utilizando los constructores que nos hemos ajustado. En caso contrario, no se garantiza que una conexión a las partes correcto y no hay ninguna garantía de que la conexión se mantiene según se producen cambios en el mapa de partes. 

El ejemplo siguiente muestra cómo se puede utilizar una directiva de reintentos SQL alrededor de los constructores de subclase **DbContext** nuevos: 

    SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() => 
    { 
        using (var db = new ElasticScaleContext<int>( 
                                sharding.ShardMap,  
                                tenantId1,  
                                connStrBldr.ConnectionString)) 
            { 
                    var blog = new Blog { Name = name }; 
                    db.Blogs.Add(blog); 
                    db.SaveChanges(); 
            … 
            } 
        }); 

**SqlDatabaseUtils.SqlRetryPolicy** en el código anterior se define como un **SqlDatabaseTransientErrorDetectionStrategy** con un número de reintentos de 10 y tiempo de espera de 5 segundos entre reintentos. Este enfoque es similar a la guía para EF y transacciones iniciado por el usuario (consulte [limitaciones con estrategias de ejecución Reintentar (EF6 en adelante)](http://msdn.microsoft.com/data/dn307226). Ambas situaciones requieren que el programa controla el ámbito a la que se devuelve la excepción temporal: vuelva a abrir la transacción o (como se muestra), vuelva a crear el contexto del constructor adecuada que utiliza la biblioteca de cliente de elásticos de la base de datos.

La necesidad de controlar dónde excepciones transitorias tardar en el ámbito también evita el uso de la **SqlAzureExecutionStrategy** integrada que viene con EF. **SqlAzureExecutionStrategy** desea volver a abrir una conexión pero no usar **OpenConnectionForKey** y, por tanto, omitir todas la validación que se realiza como parte de la llamada **OpenConnectionForKey** . En su lugar, el código de ejemplo usa la **DefaultExecutionStrategy** integrada que también se suministra con EF. En lugar de **SqlAzureExecutionStrategy**, funciona correctamente en combinación con la directiva de intentos de transitorias tratamiento de errores. La directiva de ejecución se establece en la clase **ElasticScaleDbConfiguration** . Tenga en cuenta que hemos decidido no utilizar **DefaultSqlExecutionStrategy** ya que sugiere para usar **SqlAzureExecutionStrategy** si se producen excepciones transitorias - que podría provocar un comportamiento incorrecto como se indicó. Para obtener más información sobre las directivas de reintento diferente y el EF, consulte [La resistencia de conexión en EF](http://msdn.microsoft.com/data/dn456835.aspx).     

#### <a name="constructor-rewrites"></a>Constructor reescribe
Los ejemplos de código anteriores ilustran la predeterminada constructor volver a escribe necesarios para su aplicación para poder usar depende de enrutamiento con el marco de la entidad de los datos. La siguiente tabla generaliza este enfoque para otros constructores. 


Constructor actual  | Constructor reescrita de datos | Constructor base | Notas
---------- | ----------- | ------------|----------
MyContext() |ElasticScaleContext (ShardMap, TKey) |DbContext (DbConnection bool) |La conexión debe ser una función del mapa fragmentar y la clave de enrutamiento dependientes de datos. Creación de la conexión automática de derivación por EF que necesite y utilice el mapa de partes a establecer la conexión. 
MyContext(string)|ElasticScaleContext (ShardMap, TKey) |DbContext (DbConnection bool) |La conexión es una función del mapa fragmentar y la clave de enrutamiento dependientes de datos. Una cadena de nombre o una conexión de base de datos fija no funcionará como validación de derivación por el mapa fragmentar. 
MyContext(DbCompiledModel) |ElasticScaleContext (ShardMap, TKey, DbCompiledModel) |DbContext (DbConnection, DbCompiledModel, booleano) |Obtener creará la conexión de la clave de mapa y sharding fragmentar determinado con el modelo que proporciona. El modelo compilado se transferirán a la base c'tor.
MyContext (DbConnection bool) |ElasticScaleContext (ShardMap, TKey, booleano) |DbContext (DbConnection bool) |La conexión debe deducir del mapa de partes y la clave. No se proporcionan como una entrada (a menos que esa entrada ya estaba utilizando el mapa fragmentar y la clave). Valor booleano se transferirán. 
MyContext (cadena, DbCompiledModel) |ElasticScaleContext (ShardMap, TKey, DbCompiledModel) |DbContext (DbConnection, DbCompiledModel, booleano) |La conexión debe deducir del mapa de partes y la clave. No se proporcionan como una entrada (a menos que esa entrada estaba utilizando el mapa fragmentar y la clave). El modelo compilado se transferirán. 
MyContext (ObjectContext bool) |ElasticScaleContext (ShardMap, bool TKey, ObjectContext) |DbContext (ObjectContext bool) |El nuevo constructor debe asegurarse de que todas las conexiones de ObjectContext pasa como entrada es reenviada a una conexión administrada por escala elástico. Una descripción detallada de ObjectContexts está fuera del ámbito de este documento.
MyContext (DbConnection, DbCompiledModel, booleano) |ElasticScaleContext (ShardMap, TKey, DbCompiledModel, bool)| DbContext (DbConnection, DbCompiledModel, booleano); |La conexión debe deducir del mapa de partes y la clave. La conexión no se proporcionan como una entrada (a menos que esa entrada ya estaba utilizando el mapa fragmentar y la clave). Modelo y booleano se pasan a constructor de clase base. 

## <a name="shard-schema-deployment-through-ef-migrations"></a>Implementación del esquema fragmentar a través de migraciones de EF 

Administración de esquema automático es una comodidad proporcionada por el marco de la entidad. En el contexto de las aplicaciones con herramientas de base de datos elástico, queremos conservar esta capacidad para aprovisionar automáticamente el esquema de shards recién creados cuando las bases de datos se agregan a la aplicación sharded. Es el caso de uso principal aumentar la capacidad en el nivel de datos para aplicaciones sharded con EF. Confiar en capacidades de EF para administrar el esquema, reduce el esfuerzo de administración de la base de datos con una aplicación sharded integrada en EF. 

Implementación de esquema a través de migraciones de EF funciona mejor en **conexiones no abiertas**. Esto es a diferencia del escenario de depende de los datos de enrutamiento que se basa en la conexión abierta proporcionada por la API de cliente de base de datos elástico. Otra diferencia es el requisito de coherencia: mientras deseable para garantizar la coherencia de todas las conexiones de enrutamiento dependientes de datos para protegerse de manipulación de mapa fragmentar simultáneas, no es un problema con la implementación inicial de esquema a una nueva base de datos tiene aún no ha registrado en el mapa de fragmentar y aún no se ha asignado a mantenga shardlets. Por lo tanto, podemos dependen conexiones de base de datos normal para este escenarios, en lugar de enrutamiento según los datos.  

Esto supone un enfoque donde implementación de esquema a través de migraciones de EF se acopla estrechamente el registro de la nueva base de datos como un fragmentar en mapa de partes de la aplicación. Esto se basa en los siguientes requisitos previos: 

* Ya se ha creado la base de datos. 
* La base de datos está vacío, contiene ningún esquema de usuario y no hay datos de usuario.
* La base de datos todavía no pueden acceder a través del cliente de base de datos elástico API para enrutamiento según los datos. 

Con estos requisitos previos en su lugar, podemos crear sin abierto **SqlConnection** normal para empezar migraciones EF para su implementación de esquema. El ejemplo siguiente muestra este enfoque. 

        // Enter a new shard - i.e. an empty database - to the shard map, allocate a first tenant to it  
        // and kick off EF intialization of the database to deploy schema 

        public void RegisterNewShard(string server, string database, string connStr, int key) 
        { 

            Shard shard = this.ShardMap.CreateShard(new ShardLocation(server, database)); 

            SqlConnectionStringBuilder connStrBldr = new SqlConnectionStringBuilder(connStr); 
            connStrBldr.DataSource = server; 
            connStrBldr.InitialCatalog = database; 

            // Go into a DbContext to trigger migrations and schema deployment for the new shard. 
            // This requires an un-opened connection. 
            using (var db = new ElasticScaleContext<int>(connStrBldr.ConnectionString)) 
            { 
                // Run a query to engage EF migrations 
                (from b in db.Blogs 
                    select b).Count(); 
            } 

            // Register the mapping of the tenant to the shard in the shard map. 
            // After this step, data-dependent routing on the shard map can be used 

            this.ShardMap.CreatePointMapping(key, shard); 
        } 
 

Este ejemplo muestra el método **RegisterNewShard** que registra la fragmentar en el mapa de partes, implementa el esquema a través de migraciones de EF y almacena la asignación de una clave de sharding a las partes. Se basa en un constructor de la subclase **DbContext** (**ElasticScaleContext** en el ejemplo) que toma una cadena de conexión de SQL como entrada. El código de este constructor es directa, como se muestra en el ejemplo siguiente: 


        // C'tor to deploy schema and migrations to a new shard 
        protected internal ElasticScaleContext(string connectionString) 
            : base(SetInitializerForConnection(connectionString)) 
        { 
        } 

        // Only static methods are allowed in calls into base class c'tors 
        private static string SetInitializerForConnection(string connnectionString) 
        { 
            // We want existence checks so that the schema can get deployed 
            Database.SetInitializer<ElasticScaleContext<T>>( 
        new CreateDatabaseIfNotExists<ElasticScaleContext<T>>()); 

            return connnectionString; 
        } 
 
Uno que haya utilizado la versión del constructor heredado de la clase base. Pero el código debe asegurarse de que se utiliza el inicializador predeterminado para EF al conectarse. Por lo tanto, Rodeo de la corta en el método estático antes de llamar al constructor de clase base con la cadena de conexión. Tenga en cuenta que el registro de shards debe ejecutar en un dominio de aplicación diferente o un proceso para asegurarse de que la configuración de inicializador para EF no entran en conflicto. 


## <a name="limitations"></a>Limitaciones 

Las soluciones descritas en este documento implican un par de limitaciones: 

* Aplicaciones de EF que utilizan **LocalDb** primero deberá migrar a una base de datos de SQL Server normal antes de usar la biblioteca de cliente de base de datos elástico. No es posible con **LocalDb**escalado de una aplicación a través de sharding con escala elástico. Tenga en cuenta que desarrollo seguirá pudiendo usar **LocalDb**. 

* Los cambios a la aplicación que implican cambios de esquema de la base de datos se necesitan ir a través de migraciones de EF en todas las shards. El código de ejemplo para este documento no muestra cómo hacerlo. Piense en usar base de datos de actualización con un parámetro de cadena de conexión para buscar en todos los shards; o extraer la secuencia de comandos SQL T para la migración pendiente con la base de datos de actualización con el-Script opción y aplicar la secuencia de comandos SQL T a su shards.  

* Dada una solicitud, se supone que todo el procesamiento de la base de datos está dentro de una sola fragmentar identificado por la clave de sharding proporcionada por la solicitud. Sin embargo, esta suposición no siempre es cierta. Por ejemplo, cuando no es posible que una clave de sharding disponible. Para solucionar esto, la biblioteca de cliente proporciona la clase **MultiShardQuery** que implementa una abstracción de conexión para consultas en varios shards. Aprender a usar el **MultiShardQuery** en combinación con EF está fuera del ámbito de este documento

## <a name="conclusion"></a>Conclusión

Los pasos descritos en este documento, aplicaciones de EF pueden utilizar la capacidad de la biblioteca de cliente de base de datos elástico para enrutamiento según los datos por refactorización constructores de las subclases **DbContext** utilizados en la aplicación EF. Esto limita los cambios necesarios en los lugares donde ya existen **DbContext** clases. Además, pueden continuar aplicaciones EF beneficiarse de implementación de esquema automático combinando los pasos que invocación las migraciones EF necesarias con el registro de nuevos shards y asignaciones del mapa de partes. 


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-use-entity-framework-applications-visual-studio/sample.png
 