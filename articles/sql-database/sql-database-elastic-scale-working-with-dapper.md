<properties 
    pageTitle="Usar biblioteca de cliente de base de datos elástico con Dapper | Microsoft Azure" 
    description="Usar biblioteca de cliente de base de datos elástico con Dapper." 
    services="sql-database" 
    documentationCenter="" 
    manager="jhubbard" 
    authors="torsteng"/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/27/2016" 
    ms.author="torsteng"/>

# <a name="using-elastic-database-client-library-with-dapper"></a>Usar la biblioteca de cliente de base de datos elástico con Dapper 

Este documento es para desarrolladores que se basan en Dapper para generar aplicaciones, pero también desean adoptar [Herramientas de base de datos elástica](sql-database-elastic-scale-introduction.md) para crear las aplicaciones que implementan sharding para escalado su nivel de datos.  Este documento muestra los cambios en aplicaciones basadas en Dapper que son necesarias para integrar con herramientas de base de datos elástico. En redactar la administración de partes de base de datos elásticas y depende de los datos de nuestro enfoque es enrutamiento con Dapper. 

**Código de ejemplo**: [Herramientas de elásticos de la base de datos para la base de datos de Azure SQL - integración Dapper](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-e19fc77f).
 
Es fácil integrar **Dapper** y **DapperExtensions** con la biblioteca de cliente elásticos de la base de datos para la base de datos de SQL Azure. Las aplicaciones pueden usar datos dependientes enrutamiento cambiando la creación y apertura de nuevos objetos [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) para utilizar la llamada [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) desde la [biblioteca de cliente](http://msdn.microsoft.com/library/azure/dn765902.aspx). Esto limita los cambios en su aplicación sólo donde se crean y abrir nuevas conexiones. 

## <a name="dapper-overview"></a>Información general dapper
**Dapper** es un mapeador relacional de objetos. Asigna objetos .NET desde la aplicación a una base de datos relacional (y viceversa). La primera parte del código de ejemplo muestra cómo puede integrar la biblioteca de cliente de base de datos elástico con aplicaciones basadas en Dapper. La segunda parte del código de ejemplo muestra cómo integrar al utilizar Dapper y DapperExtensions.  

La funcionalidad de asignador en Dapper proporciona métodos de extensión en las conexiones de base de datos que simplifican enviar instrucciones SQL T para ejecución o consultar la base de datos. Por ejemplo, Dapper facilita para asignar entre los objetos de .NET y los parámetros de instrucciones SQL para **Ejecutar** llamadas o consumir los resultados de las consultas SQL en objetos .NET mediante llamadas de **consulta** desde Dapper. 

Al utilizar DapperExtensions, ya no necesita proporcionar las instrucciones SQL. Métodos de extensiones como **GetList** o **Insertar** sobre la conexión de base de datos crean las instrucciones SQL en segundo plano.
 
Otra ventaja de Dapper y también DapperExtensions es que la aplicación controla la creación de la conexión de base de datos. Esto le permite interactuar con la biblioteca de cliente elásticos de la base de datos que agentes en función de la asignación de shardlets a bases de datos de conexiones de base de datos.

Para obtener los ensamblados Dapper, consulte [Dapper punto neto](http://www.nuget.org/packages/Dapper/). Para las extensiones Dapper, consulte [DapperExtensions](http://www.nuget.org/packages/DapperExtensions).

## <a name="a-quick-look-at-the-elastic-database-client-library"></a>Un vistazo a la biblioteca de cliente de base de datos elásticas

Con la biblioteca de cliente elásticos de la base de datos, definir particiones de los datos de aplicación denominados *shardlets* , asignarlos a las bases de datos e identificarlos por *claves sharding*. Puede tener tantas bases de datos como necesite y distribuir el shardlets en estas bases de datos. La asignación de valores de clave de sharding a las bases de datos se almacena en un mapa de fragmentar proporcionado por las API de la biblioteca. Esta funcionalidad se denomina **fragmentar asignar administración**. El mapa de fragmentar también sirve como el agente de conexiones de base de datos para las solicitudes de llevar a cabo una clave de sharding. Esta función se conoce como **enrutamiento según los datos**.

![Fragmentar mapas y enrutamiento según los datos][1]

El Administrador de mapa fragmentar protege a los usuarios de las vistas incoherentes en los datos de shardlet que pueden producirse cuando las operaciones de administración de shardlet simultáneas se suceden en las bases de datos. Para ello, los mapas fragmentar adaptar las conexiones de base de datos para una aplicación creada con la biblioteca. Cuando las operaciones de administración de fragmentar pueden afectar el shardlet, permite la funcionalidad de mapa fragmentar eliminar automáticamente una conexión de base de datos. 

En lugar de la manera tradicional de crear conexiones para Dapper, tenemos que use el [método OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn824099.aspx). Esto garantiza que todos los la validación lleve a cabo y conexiones se administran correctamente cuando los datos se mueven entre shards.

### <a name="requirements-for-dapper-integration"></a>Requisitos para la integración de Dapper

Cuando se trabaja con la biblioteca de cliente de base de datos elásticas y las API Dapper, queremos conservar las siguientes propiedades:

* **Escala**: queremos agregar o quitar las bases de datos del nivel de datos de la aplicación sharded según sea necesario para las demandas de capacidad de la aplicación. 

-    **Coherencia**: dado que nuestra aplicación es escalar con sharding, es necesario realizar el enrutamiento según los datos. Queremos usar las capacidades de enrutamiento dependientes de datos de la biblioteca para hacerlo. En concreto, queremos conservar la validación y garantiza la coherencia proporcionadas por las conexiones que están negociadas a través del Administrador de mapa fragmentar para evitar que se dañen los o resultados de la consulta incorrecta. Así se garantiza que dicho conexiones a un shardlet determinado rechazadas o dejadas si (por ejemplo) el shardlet actualmente se mueve a un diferentes partes con la API de combinación o división.

-    **Asignación de objeto**: queremos conservar la comodidad de las asignaciones de Dapper para traducir entre las clases en la aplicación y las estructuras de base de datos subyacente. 

La siguiente sección proporciona pautas para estos requisitos para aplicaciones basadas en **Dapper** y **DapperExtensions**.

## <a name="technical-guidance"></a>Orientación técnica
### <a name="data-dependent-routing-with-dapper"></a>Datos dependientes enrutamiento de Dapper 

Con Dapper, la aplicación es normalmente responsable de crear y abrir las conexiones de la base de datos subyacente. Dados un tipo T por la aplicación, Dapper devuelve resultados de la consulta como colecciones de .NET de tipo T. Dapper realiza la asignación de las filas de resultados de SQL T a los objetos de tipo T. Del mismo modo, Dapper asigna objetos .NET a valores SQL o los parámetros de instrucciones de lenguaje (DML) de manipulación de datos. Dapper ofrece esta funcionalidad a través de métodos de extensión en el objeto [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) normal de las bibliotecas de ADO .NET SQL Client. La conexión de SQL devuelta por las API de escala elástica para DDR también son objetos de [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) normales. Esto nos permite usar directamente extensiones Dapper sobre el tipo devuelto por la API de DDR de la biblioteca de cliente, como también es una conexión de SQL Client simple.

Estas observaciones que sea fácil de usar conexiones con la biblioteca de cliente de base de datos elástica para Dapper.

Este ejemplo de código (de ejemplo de adjunto) muestra el enfoque donde se proporciona la clave de sharding por la aplicación a la biblioteca para adaptar la conexión a la derecha fragmentar.   

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                     key: tenantId1, 
                     connectionString: connStrBldr.ConnectionString, 
                     options: ConnectionOptions.Validate))
    {
        var blog = new Blog { Name = name };
        sqlconn.Execute(@"
                      INSERT INTO
                            Blog (Name)
                            VALUES (@name)", new { name = blog.Name }
                        );
    }

La llamada a la API [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) reemplaza la creación de forma predeterminada y la apertura de una conexión de cliente de SQL. La llamada [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) toma los argumentos necesarios para enrutamiento según los datos: 

-    El mapa de fragmentar para tener acceso a las interfaces de enrutamiento dependientes de datos
-    La clave de sharding para identificar el shardlet
-    Las credenciales (nombre de usuario y contraseña) para conectarse a las partes

El objeto de mapa de fragmentar crea una conexión a las partes que contiene el shardlet para la clave de sharding determinado. La API de cliente de elásticos de la base de datos también etiquetar la conexión para implementar la garantía de coherencia. Dado que la llamada a [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) devuelve un objeto de conexión de SQL Client normal, la llamada posterior al método de extensión **Execute** desde Dapper sigue la práctica Dapper estándar.

Consultas funcionan muy del mismo modo: abrir la conexión con [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) desde el cliente de API por primera vez. A continuación, utilice los métodos de extensión Dapper normal para asignar los resultados de la consulta SQL en objetos .NET:

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId1, 
                    connectionString: connStrBldr.ConnectionString, 
                    options: ConnectionOptions.Validate ))
    {    
           // Display all Blogs for tenant 1
           IEnumerable<Blog> result = sqlconn.Query<Blog>(@"
                                SELECT * 
                                FROM Blog
                                ORDER BY Name");

           Console.WriteLine("All blogs for tenant id {0}:", tenantId1);
           foreach (var item in result)
           {
                Console.WriteLine(item.Name);
            }
    }

Tenga en cuenta que el **uso de** bloque con la conexión de DDR ámbitos todas las operaciones de base de datos dentro del bloque a las partes de una donde se guarda la tenantId1. La consulta devuelve solo blogs almacenados en las partes actual, pero no los almacenados en cualquier otros shards. 

## <a name="data-dependent-routing-with-dapper-and-dapperextensions"></a>Datos dependientes enrutamiento con Dapper y DapperExtensions

Dapper incluye un ecosistema de extensiones adicionales que puede proporcionar más comodidad y abstracción desde la base de datos al desarrollo de aplicaciones de base de datos. DapperExtensions es un ejemplo. 

Utilizar DapperExtensions en su aplicación no cambia cómo crea y administra las conexiones de base de datos. Sigue siendo responsabilidad de la aplicación para abrir conexiones y objetos de conexión de SQL Client normales se esperan en los métodos de extensión. Nos podemos dependen de la [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) descritas anteriormente. Como se muestran en los siguientes ejemplos de código, el único cambio es que ya no hay que escribir las instrucciones T SQL:

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2, 
                    connectionString: connStrBldr.ConnectionString, 
                    options: ConnectionOptions.Validate))
    {
           var blog = new Blog { Name = name2 };
           sqlconn.Insert(blog);
    }

Y aquí está el código de ejemplo para la consulta: 

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2, 
                    connectionString: connStrBldr.ConnectionString, 
                    options: ConnectionOptions.Validate))
    {
           // Display all Blogs for tenant 2
           IEnumerable<Blog> result = sqlconn.GetList<Blog>();
           Console.WriteLine("All blogs for tenant id {0}:", tenantId2);
           foreach (var item in result)
           {
               Console.WriteLine(item.Name);
           }
    }

### <a name="handling-transient-faults"></a>Tratamiento de errores transitorias

El equipo de Microsoft Patterns & prácticas publicado el [Bloque de aplicación de control de errores transitorias](http://msdn.microsoft.com/library/hh680934.aspx) para ayudar a los desarrolladores de aplicaciones mitigar condiciones transitorias errores comunes encontradas cuando se ejecuta en la nube. Para obtener más información, vea [su perseverancia, secreto de todos los logros: utilizar el bloque de aplicación de tratamiento de errores transitorias](http://msdn.microsoft.com/library/dn440719.aspx).

El código de ejemplo se basa en la biblioteca de errores transitorias a protegerse frente a errores transitorias. 

    SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
    {
       using (SqlConnection sqlconn = 
          shardingLayer.ShardMap.OpenConnectionForKey(tenantId2, connStrBldr.ConnectionString, ConnectionOptions.Validate))
          {
              var blog = new Blog { Name = name2 };
              sqlconn.Insert(blog);
          }
    });

**SqlDatabaseUtils.SqlRetryPolicy** en el código anterior se define como un **SqlDatabaseTransientErrorDetectionStrategy** con un número de reintentos de 10 y tiempo de espera de 5 segundos entre reintentos. Si está utilizando transacciones, asegúrese de que el ámbito de reintento vuelve al principio de la transacción en caso de error transitoria.

## <a name="limitations"></a>Limitaciones

Las soluciones descritas en este documento implican un par de limitaciones:

* El código de ejemplo para este documento no muestra cómo administrar el esquema en shards.
* Dada una solicitud, se supone que todo el procesamiento de su base de datos está dentro de una sola fragmentar identificado por la clave de sharding proporcionada por la solicitud. Sin embargo, esta suposición no siempre mantenga, por ejemplo, cuando no es posible que esté disponible una clave de sharding. Para solucionar esto, la biblioteca de cliente de base de datos elástico incluye la [clase MultiShardQuery](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardexception.aspx). La clase implementa una abstracción de conexión para consultas en varios shards. Utilizar MultiShardQuery en combinación con Dapper está fuera del ámbito de este documento.

## <a name="conclusion"></a>Conclusión

Aplicaciones mediante Dapper y DapperExtensions fácilmente pueden beneficiarse de herramientas de base de datos elástica para base de datos de SQL Azure. Los pasos descritos en este documento, esas aplicaciones pueden usar la funcionalidad de la herramienta datos dependientes enrutamiento cambiando la creación y apertura de nuevos objetos [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) para utilizar la llamada [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) de la biblioteca de cliente de base de datos elástico. Esto limita los cambios de aplicación necesarios para los lugares donde se crean y abrir nuevas conexiones. 

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-working-with-dapper/dapperimage1.png
 