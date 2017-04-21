<properties 
    pageTitle="Obtenga información sobre cómo proteger el acceso a los datos de DocumentDB | Microsoft Azure" 
    description="Obtenga información sobre los conceptos de control de acceso en DocumentDB, incluidas las claves principales, claves de solo lectura, los usuarios y permisos." 
    services="documentdb" 
    authors="kiratp" 
    manager="jhubbard" 
    editor="monicar" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/19/2016" 
    ms.author="kipandya"/>

# <a name="securing-access-to-documentdb-data"></a>Proteger el acceso a datos DocumentDB

Este artículo proporciona una descripción general de proteger el acceso a los datos almacenados en [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/).

Después de leer esta información general, podrá responder a las preguntas siguientes:  

-   ¿Cuáles son las claves principales de DocumentDB?
-   ¿Cuáles son las claves de solo lectura de DocumentDB?
-   ¿Cuáles son los símbolos de recursos de DocumentDB?
-   ¿Cómo puedo utilizar DocumentDB usuarios y permisos para proteger el acceso a datos DocumentDB?

## <a name="documentdb-access-control-concepts"></a>Conceptos de control de acceso de DocumentDB

DocumentDB proporciona los conceptos de primera clases con el fin de controlar el acceso a los recursos de DocumentDB.  Para el propósito de este tema, DocumentDB recursos se agrupan en dos categorías:

- Recursos administrativos
    - Cuenta
    - Base de datos
    - Usuario
    - Permisos
- Recursos de la aplicación
    - Colección
    - Oferta
    - Documento
    - Datos adjuntos
    - Procedimiento almacenado
    - Desencadenador
    - Función definida por el usuario

En el contexto de estas dos categorías, DocumentDB admite tres tipos de roles de control de acceso: la cuenta de administrador, Administrador de sólo lectura y el usuario de base de datos.  Los derechos para cada rol de control de acceso son:
 
- Administrador de la cuenta: acceso completo a todos los recursos (administrativos y aplicaciones) dentro de una cuenta de DocumentDB determinada.
- Administrador de sólo lectura: acceso de solo lectura para todos los recursos (administrativos y aplicaciones dentro de una cuenta de DocumentDB determinada. 
- Usuario de la base de datos: recursos de DocumentDB el usuario asociado con un conjunto concreto de recursos de base de datos de DocumentDB (por ejemplo, colecciones, documentos, las secuencias de comandos).  Puede haber uno o más recursos de usuario asociados a una base de datos determinado y cada usuario pueden tener uno o varios de los permisos asociados.

Con las categorías anteriores y recursos en cuenta, el modelo de control de acceso de DocumentDB define tres tipos de construcciones de access:

- Claves principales: tras la creación de una cuenta de DocumentDB, se crean dos claves de patrón (primaria y secundarias).  Estas teclas habilitar acceso administrativo completo a todos los recursos dentro de la cuenta DocumentDB.

![Ilustración de claves maestras DocumentDB](./media/documentdb-secure-access-to-data/masterkeys.png)

- Claves de solo lectura: tras la creación de una cuenta de DocumentDB, se crean dos claves de solo lectura (primaria y secundarias).  Estas teclas habilitar el acceso de solo lectura a todos los recursos dentro de la cuenta DocumentDB.

![Ilustración de DocumentDB claves de solo lectura](./media/documentdb-secure-access-to-data/readonlykeys.png)

- Símbolos de recursos: el token de un recurso está asociado a un recurso de permiso DocumentDB y captura la relación entre el usuario de una base de datos y los permisos de ese usuario tiene un recurso de aplicación DocumentDB específico (por ejemplo, colección, documento).

![Ilustración de tokens de recursos DocumentDB](./media/documentdb-secure-access-to-data/resourcekeys.png)

## <a name="working-with-documentdb-master-and-read-only-keys"></a>Trabajar con las claves principales y de solo lectura de DocumentDB

Como se mencionó anteriormente, las claves principales de DocumentDB proporcionan acceso administrativo completo a todos los recursos dentro de una cuenta de DocumentDB mientras claves de solo lectura habilitar el acceso de lectura a todos los recursos dentro de la cuenta.  Fragmento de código siguiente muestra cómo usar un extremo de la cuenta de DocumentDB y la clave principal para designar un DocumentClient y crear una nueva base de datos. 

    //Read the DocumentDB endpointUrl and authorization keys from config.
    //These values are available from the Azure Classic Portal on the DocumentDB Account Blade under "Keys".
    //NB > Keep these values in a safe and secure location. Together they provide Administrative access to your DocDB account.
    
    private static readonly string endpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
    private static readonly SecureString authorizationKey = ToSecureString(ConfigurationManager.AppSettings["AuthorizationKey"]);
        
    client = new DocumentClient(new Uri(endpointUrl), authorizationKey);
    
    // Create Database
    Database database = await client.CreateDatabaseAsync(
        new Database
        {
            Id = databaseName
        });


## <a name="overview-of-documentdb-resource-tokens"></a>Información general sobre DocumentDB tokens de recursos

Puede usar un símbolo de recursos (creando DocumentDB usuarios y permisos) que desea proporcionar acceso a los recursos en su cuenta de DocumentDB a un cliente que no es de confianza con la clave principal. Las claves principales de DocumentDB incluir tanto una clave principal y secundaria, cada una de las cuales concede acceso administrativo a su cuenta y todos los recursos en él. Exponer cualquiera de las claves de patrón, abre la cuenta para la posibilidad de usar malintencionado o negligencia. 

Del mismo modo, claves de solo lectura DocumentDB proporcionan acceso de lectura a todos los recursos - excepto los permisos de los recursos, por supuesto, dentro de una cuenta de DocumentDB y no se pueden usar para proporcionar acceso más detallado a recursos DocumentDB específicos.

Símbolos de recursos DocumentDB ofrecen una alternativa segura que permite a los clientes leer, escribir y eliminar los recursos en su cuenta de DocumentDB según los permisos que le ha concedido y sin necesidad de un patrón o una clave de solo lectura.

Aquí es un modelo de diseño típico según la cual tokens recursos pueden solicitar, generadas y entregar a los clientes:

1. Un servicio de nivel intermedio está configurado para proporcionar el servicio de una aplicación móvil para compartir fotos de usuario.
2. El servicio de nivel intermedio posee la clave principal de la cuenta DocumentDB.
3. La aplicación de la foto se instala en dispositivos móviles del usuario final. 
4. En el inicio de sesión, la aplicación fotos establece la identidad del usuario con el servicio de nivel intermedio. Este mecanismo de establecimiento de identidad puramente depende de la aplicación.
5. Una vez establecida la identidad, el servicio de nivel intermedio solicita permisos en función de la identidad.
6. El servicio de nivel intermedio, envía un token de recurso vuelva a la aplicación de teléfono.
7. La aplicación de teléfono puede seguir usando el token de recursos directamente acceso a los recursos de DocumentDB con los permisos definidos por el token de recursos y para el intervalo permitido por el token de recursos. 
8. Cuando expira el token de recursos, las solicitudes subsiguientes recibirán una excepción 401 no autorizado.  En este momento, la aplicación de teléfono restablece la identidad y solicita un nuevo token de recursos.

![Flujo de trabajo de DocumentDB recursos tokens](./media/documentdb-secure-access-to-data/resourcekeyworkflow.png)

## <a name="working-with-documentdb-users-and-permissions"></a>Trabajar con DocumentDB usuarios y permisos
Un recurso de usuario DocumentDB está asociado a una base de datos de DocumentDB.  Cada base de datos puede contener cero o más usuarios DocumentDB.  Fragmento de código siguiente muestra cómo crear un recurso de usuario DocumentDB.

    //Create a user.
    User docUser = new User
    {
        Id = "mobileuser"
    };

    docUser = await client.CreateUserAsync(UriFactory.CreateDatabaseUri("db"), docUser);

> [AZURE.NOTE] Cada usuario de DocumentDB tiene una propiedad PermissionsLink que se puede utilizar para recuperar la lista de los permisos asociados con el usuario.

Un recurso de permiso DocumentDB está asociado con un usuario DocumentDB.  Cada usuario puede contener cero o más permisos DocumentDB.  Un recurso de permisos proporciona acceso a un token de seguridad que el usuario necesita al intentar obtener acceso a un recurso de aplicación específica.
Hay dos niveles de acceso disponibles que pueden proporcionar un recurso de permisos:

- Todo: El usuario tiene permiso total en el recurso
- Lectura: El usuario solo puede leer el contenido del recurso, pero no puede realizar escritura, actualizar o eliminar operaciones en el recurso.


> [AZURE.NOTE] Para poder ejecutar DocumentDB procedimientos almacenados del que usuario debe tener el permiso todos en la colección en la que se ejecutará el procedimiento almacenado.


Fragmento de código siguiente muestra cómo crear un recurso de permiso, el token de recursos del recurso de permiso de lectura y asociar los permisos de usuario creado anteriormente.

    // Create a permission.
    Permission docPermission = new Permission
    {
        PermissionMode = PermissionMode.Read,
        ResourceLink = documentCollection.SelfLink,
        Id = "readperm"
    };
            
  docPermission = espera de cliente. CreatePermissionAsync (UriFactory.CreateUserUri ("db", "usuario"), docPermission); Console.WriteLine (docPermission.Id + "tiene el token de:" + docPermission.Token);
  
Si ha especificado una clave de partición para la colección y, a continuación, el permiso de la colección, documentos y datos adjuntos de recursos también deben incluir la ResourcePartitionKey además el vínculo.

Para obtener fácilmente todos los recursos de permisos asociados con un usuario particular, DocumentDB hacer disponible un permiso de la fuente de cada objeto de usuario.  Fragmento de código siguiente muestra cómo recuperar los permisos asociados con el usuario que creó anteriormente, crear una lista de permisos y crear una instancia de un nuevo DocumentClient en nombre del usuario.

    //Read a permission feed.
    FeedResponse<Permission> permFeed = await client.ReadPermissionFeedAsync(
      UriFactory.CreateUserUri("db", "myUser"));

    List<Permission> permList = new List<Permission>();
      
    foreach (Permission perm in permFeed)
    {
        permList.Add(perm);
    }
            
    DocumentClient userClient = new DocumentClient(new Uri(endpointUrl), permList);

> [AZURE.TIP] Símbolos de recursos tienen un intervalo de tiempo válido predeterminado de 1 hora.  Duración del token, sin embargo, puede especificar explícitamente, hasta un máximo de 5 horas.

## <a name="next-steps"></a>Pasos siguientes

- Para obtener más información sobre DocumentDB, haga clic en [aquí](http://azure.com/docdb).
- Para obtener información sobre la administración de claves principales y de sólo lectura, haga clic en [aquí](documentdb-manage-account.md).
- Para obtener información sobre cómo crear tokens de autorización de DocumentDB, haga clic en [aquí](https://msdn.microsoft.com/library/azure/dn783368.aspx)
 
