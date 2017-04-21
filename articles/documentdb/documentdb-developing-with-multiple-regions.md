<properties
   pageTitle="Desarrollo de varias regiones en DocumentDB | Microsoft Azure"
   description="Obtenga información sobre cómo obtener acceso a los datos en varias áreas de Azure DocumentDB, un servicio de base de datos NoSQL completamente administrado."
   services="documentdb"
   documentationCenter=""
   authors="kiratp"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="documentdb"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/25/2016"
   ms.author="kipandya"/>
   
# <a name="developing-with-multi-region-documentdb-accounts"></a>Desarrollar con cuentas de región múltiples DocumentDB

> [AZURE.NOTE] Distribución global de bases de datos de DocumentDB está disponible de forma general y automáticamente habilitado para las cuentas de DocumentDB recién creadas. Estamos trabajando para habilitar la distribución global en todas las cuentas existentes, pero mientras tanto, si desea que la distribución global habilitado en su cuenta, por favor, [póngase en contacto con soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) y se deberá habilitarlo para ahora.

Para sacar partido de [distribución global](documentdb-distribute-data-globally.md), aplicaciones cliente pueden especificar la lista ordenada preferencia de regiones que se utilizará para realizar operaciones de documento. Esto puede hacerse mediante la configuración de la directiva de conexión. Según la configuración de cuenta de Azure DocumentDB, disponibilidad regional actual y la lista de preferencias especificado, se selecciona el extremo óptimo el SDK para realizar la escritura y las operaciones de lectura. 

Esta lista de preferencia se especifica cuando iniciando una conexión con el cliente de DocumentDB SDK. Los SDK aceptan un parámetro opcional "PreferredLocations" es una lista ordenada de Azure regiones.

El SDK enviará automáticamente todos los escribe en el actual escribe región. 

Todas las lecturas se enviarán a la primera área disponibles en la lista de PreferredLocations. Si falla la solicitud, el cliente no se realice correctamente hacia abajo en la lista a la siguiente área y así sucesivamente. 

El cliente que SDK sólo intentará lee de las regiones especificado en PreferredLocations. Por lo tanto, por ejemplo, si la cuenta de base de datos está disponible en tres regiones, pero el cliente dos de las regiones de escritura no especifica sólo para PreferredLocations, a continuación, lecturas no se servirá fuera de la región de escritura, incluso en el caso de migración tras error.

La aplicación puede comprobar el extremo de escritura actual y leer elegido por el SDK activando dos propiedades, WriteEndpoint y ReadEndpoint, disponible en la versión SDK 1,8 y encima de extremo. 

Si no se establece la propiedad PreferredLocations, se servirá todas las solicitudes de la región de escritura actual. 


## <a name="net-sdk"></a>.NET SDK
El SDK de puede usarse sin realizar cambios de código. En este caso, el SDK automáticamente dirige ambos lee y escribe en el área de escritura actual. 

En la versión 1,8 y posteriores de .NET SDK, el parámetro ConnectionPolicy para el constructor DocumentClient tiene una propiedad denominada Microsoft.Azure.Documents.ConnectionPolicy.PreferredLocations. Esta propiedad es de tipo colección `<string>` y debe contener una lista de nombres de región. Los valores de cadena se da formato a la columna nombre de la región de las [Regiones de Azure]  [ regions] página, sin espacios antes o después del primer y último carácter respectivamente.

La escritura actual y lecturas extremos están disponibles en DocumentClient.WriteEndpoint y DocumentClient.ReadEndpoint respectivamente.

> [AZURE.NOTE] No se deben considerar las direcciones URL de los extremos como constantes larga. Puede actualizar el servicio en cualquier punto. El SDK controla automáticamente este cambio.

    // Getting endpoints from application settings or other configuration location
    Uri accountEndPoint = new Uri(Properties.Settings.Default.GlobalDatabaseUri);
    string accountKey = Properties.Settings.Default.GlobalDatabaseKey;

    //Setting read region selection preference 
    connectionPolicy.PreferredLocations.Add(LocationNames.WestUS); // first preference
    connectionPolicy.PreferredLocations.Add(LocationNames.EastUS); // second preference
    connectionPolicy.PreferredLocations.Add(LocationNames.NorthEurope); // third preference

    // initialize connection
    DocumentClient docClient = new DocumentClient(
        accountEndPoint,
        accountKey,
        connectionPolicy);

    // connect to DocDB 
    await docClient.OpenAsync().ConfigureAwait(false);


## <a name="nodejs-javascript-and-python-sdks"></a>NodeJS, JavaScript y Python SDK
El SDK de puede usarse sin realizar cambios de código. En este caso, el SDK dirigirá automáticamente lee y escribe en el actual escribe región. 

En versión 1,8 y posterior de cada SDK, el parámetro ConnectionPolicy para el constructor DocumentClient una nueva propiedad denominada DocumentClient.ConnectionPolicy.PreferredLocations. Esto es el parámetro es una matriz de cadenas que tome una lista de nombres de región. Los nombres se da formato a la columna nombre de la región de las [Regiones de Azure]  [ regions] página. También puede usar las constantes predefinidas en el objeto de comodidad AzureDocuments.Regions

La escritura actual y lecturas extremos están disponibles en DocumentClient.getWriteEndpoint y DocumentClient.getReadEndpoint respectivamente.

> [AZURE.NOTE] No se deben considerar las direcciones URL de los extremos como constantes larga. Puede actualizar el servicio en cualquier punto. El SDK controlará este cambio automáticamente.

A continuación es un ejemplo de código de Javascript/NodeJS. Python y Java seguirá el mismo patrón.

    // Creating a ConnectionPolicy object
    var connectionPolicy = new DocumentBase.ConnectionPolicy();
    
    // Setting read region selection preference, in the following order -
    // 1 - West US
    // 2 - East US
    // 3 - North Europe
    connectionPolicy.PreferredLocations = ['West US', 'East US', 'North Europe'];
    
    // initialize the connection
    var client = new DocumentDBClient(host, { masterKey: masterKey }, connectionPolicy);


## <a name="rest"></a>REST 
Una vez que se ha realizado una cuenta de base de datos disponible en varias regiones, los clientes pueden consultar su disponibilidad al realizar una solicitud GET en el siguiente URI.

    https://{databaseaccount}.documents.azure.com/

El servicio devolverá una lista de regiones y su correspondiente extremo de DocumentDB URI para las réplicas. El área de escritura actual se indicará en la respuesta. El cliente puede seleccionar el extremo adecuado para todos los demás las solicitudes de la API de REST de manera.

Respuesta de ejemplo

    {
        "_dbs": "//dbs/",
        "media": "//media/",
        "writableLocations": [
            {
                "Name": "West US",
                "DatabaseAccountEndpoint": "https://globaldbexample-westus.documents.azure.com:443/"
            }
        ],
        "readableLocations": [
            {
                "Name": "East US",
                "DatabaseAccountEndpoint": "https://globaldbexample-eastus.documents.azure.com:443/"
            }
        ],
        "MaxMediaStorageUsageInMB": 2048,
        "MediaStorageUsageInMB": 0,
        "ConsistencyPolicy": {
            "defaultConsistencyLevel": "Session",
            "maxStalenessPrefix": 100,
            "maxIntervalInSeconds": 5
        },
        "addresses": "//addresses/",
        "id": "globaldbexample",
        "_rid": "globaldbexample.documents.azure.com",
        "_self": "",
        "_ts": 0,
        "_etag": null
    }


-   Las solicitudes de todas las ubicaciones, publicación y eliminar deben ir a la escritura indicada URI
-   Obtiene todos los y otras peticiones de solo lectura (por ejemplo, consultas) pueden ir a cualquier extremo de elección del cliente

Escribir las solicitudes de las áreas de solo lectura se producirá un error con código de error HTTP 403 ("prohibido").

Si cambia la región de escritura después de la fase de descubrimiento inicial del cliente, escribe posteriores a la región de escritura anterior se producirá un error con código de error HTTP 403 ("prohibido"). El cliente, a continuación, debe obtener la lista de regiones nuevamente para obtener la región de escritura actualizado.

## <a name="next-steps"></a>Pasos siguientes

Más información sobre los datos distribuir globalmente con DocumentDB en los siguientes artículos:

- [Distribuir datos globalmente con DocumentDB](documentdb-distribute-data-globally.md)
- [Niveles de coherencia](documentdb-consistency-levels.md)
- [Cómo funciona el rendimiento con varias regiones](documentdb-manage.md#how-throughput-works-with-multiple-regions)
- [Agregar regiones con el portal de Azure](documentdb-portal-global-replication.md)

[regions]: https://azure.microsoft.com/regions/ 
