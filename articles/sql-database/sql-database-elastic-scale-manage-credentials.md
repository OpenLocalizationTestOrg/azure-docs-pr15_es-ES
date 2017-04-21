<properties 
    pageTitle="Administrar las credenciales en la biblioteca de cliente de base de datos elástico | Microsoft Azure" 
    description="Cómo configurar el nivel adecuado de credenciales, Administrador de sólo lectura, para las aplicaciones de base de datos elásticas" 
    services="sql-database" 
    documentationCenter="" 
    manager="jhubbard" 
    authors="ddove" 
    editor=""/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/27/2016" 
    ms.author="ddove"/>

# <a name="credentials-used-to-access-the-elastic-database-client-library"></a>Credenciales utilizadas para tener acceso a la biblioteca de cliente de base de datos elásticas

La [biblioteca de cliente de base de datos elástico](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) usa tres tipos distintos de credenciales para acceder al [Administrador de mapa fragmentar](sql-database-elastic-scale-shard-map-management.md). Según la necesidad, use la credencial con el nivel mínimo de acceso posible.

* **Credenciales de administración**: para crear o manipular un administrador de mapa fragmentar. (Consulte [Glosario](sql-database-elastic-scale-glossary.md)). 
* **Credenciales de acceso**: para obtener acceso a un administrador de mapa fragmentar para obtener información sobre shards.
* **Credenciales de conexión**: conectar con shards. 

Vea también [inicios de sesión en la base de datos de SQL Azure y administrar bases de datos](sql-database-manage-logins.md). 
 
## <a name="about-management-credentials"></a>Acerca de las credenciales de administración

Credenciales de administración se usan para crear un objeto [**ShardMapManager**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx) para las aplicaciones que manipular fragmentar mapas. (Por ejemplo, consulte [Agregar un fragmentar con herramientas de base de datos elásticas](sql-database-elastic-scale-add-a-shard.md) y [enrutamiento según los datos](sql-database-elastic-scale-data-dependent-routing.md)) El usuario de la biblioteca de cliente de escala elástico crea los usuarios SQL y los inicios de sesión SQL y se asegura de que cada uno se le ha concedido los permisos de lectura y escritura en la base de datos del mapa de partes global y todas las partes bases de datos. Estas credenciales se utilizan para mantener el mapa fragmentar global y los mapas fragmentar local cuando se realizan cambios en el mapa de partes. Por ejemplo, use las credenciales de administración para crear el objeto del Administrador de mapa fragmentar (con [**GetSqlShardMapManager**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager.aspx): 

    // Obtain a shard map manager. 
    ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager( 
            smmAdminConnectionString, 
            ShardMapManagerLoadPolicy.Lazy 
    ); 

La variable **smmAdminConnectionString** es una cadena de conexión que contiene las credenciales de administración. El identificador de usuario y la contraseña proporciona acceso de lectura y escritura a la base de datos de mapa de partes y shards individuales. La cadena de conexión de administración también incluye el nombre del servidor y el nombre de la base de datos para identificar la base de datos del mapa de partes global. Aquí es una cadena de conexión típica para ello:

     "Server=<yourserver>.database.windows.net;Database=<yourdatabase>;User ID=<yourmgmtusername>;Password=<yourmgmtpassword>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;” 

No utilice los valores en el formulario de "username@server"—instead simplemente use el valor de "nombre de usuario".  Esto es porque las credenciales deben trabajar con la base de datos del Administrador de mapa de partes y el shards individuales, lo cual pueden estar en diferentes servidores.

## <a name="access-credentials"></a>Credenciales de acceso
  
Al crear un fragmentar Administrador de mapa en una aplicación que no administrar fragmentar mapas, utilizar credenciales que tengan permisos de sólo lectura en el mapa fragmentar global. La información recuperada de la equivalencia de fragmentar global en estas credenciales se utilizan para el [enrutamiento dependientes de datos](sql-database-elastic-scale-data-dependent-routing.md) y para rellenar la caché de mapa de fragmentar en el cliente. Las credenciales se proporcionan a través de la misma trama de llamada para **GetSqlShardMapManager** como se muestra aquí: 

    // Obtain shard map manager. 
    ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager( 
            smmReadOnlyConnectionString, 
            ShardMapManagerLoadPolicy.Lazy
    );  

Observe el uso de la **smmReadOnlyConnectionString** para reflejar el uso de credenciales diferentes para este acceso en nombre de los usuarios **no sean administradores** : estas credenciales no deben proporcionar permisos de escritura en el mapa de fragmentar global. 

## <a name="connection-credentials"></a>Credenciales de conexión 

Cuando se usa el método [**OpenConnectionForKey**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey.aspx) para tener acceso a un fragmentar asociado a una clave de sharding, se necesitan credenciales adicionales. Estas credenciales deben proporcionar los permisos de acceso de solo lectura a las tablas de mapa fragmentar local que reside en las partes. Esto es necesario para realizar la validación de conexión para enrutamiento según los datos en las partes. Este fragmento de código permite el acceso de datos en el contexto de enrutamiento según los datos: 
 
    using (SqlConnection conn = rangeMap.OpenConnectionForKey<int>( 
    targetWarehouse, smmUserConnectionString, ConnectionOptions.Validate)) 

En este ejemplo, **smmUserConnectionString** contiene la cadena de conexión para las credenciales de usuario. Base de datos de SQL Azure, aquí es una cadena de conexión típica para las credenciales de usuario: 

    "User ID=<yourusername>; Password=<youruserpassword>; Trusted_Connection=False; Encrypt=True; Connection Timeout=30;”  

Al igual que con las credenciales de administrador, no los valores en el formulario de "username@server". En su lugar, simplemente use "nombre de usuario".  Tenga en cuenta que la cadena de conexión no contiene un nombre de servidor y nombre de la base de datos. Eso es porque la llamada **OpenConnectionForKey** dirigirá automáticamente a la conexión a la fragmentar correcto según la clave. Por lo tanto, no se proporcionan el nombre de la base de datos y el nombre del servidor. 

## <a name="see-also"></a>Vea también
[Administrar bases de datos y los inicios de sesión en la base de datos de SQL Azure](sql-database-manage-logins.md)

[Proteger la base de datos SQL](sql-database-security.md)

[Introducción a los trabajos de base de datos elásticas](sql-database-elastic-jobs-getting-started.md)

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
 