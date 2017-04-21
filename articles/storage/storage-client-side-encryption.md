<properties
    pageTitle="Cifrado de cliente con .NET para el almacenamiento de Microsoft Azure | Microsoft Azure"
    description="La biblioteca de cliente de almacenamiento de Azure para .NET admite el cifrado de cliente y la integración con Azure clave depósito para una mayor seguridad para las aplicaciones de almacenamiento de Azure."
    services="storage"
    documentationCenter=".net"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="robinsh"/>


# <a name="client-side-encryption-and-azure-key-vault-for-microsoft-azure-storage"></a>Cifrado de cliente y Azure depósito clave para el almacenamiento de Microsoft Azure

[AZURE.INCLUDE [storage-selector-client-side-encryption-include](../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Información general

La [Biblioteca de cliente de almacenamiento de Azure para paquete Nuget .NET](https://www.nuget.org/packages/WindowsAzure.Storage) es compatible con el cifrado de los datos en aplicaciones cliente antes de cargar con el almacenamiento de Azure y descifrar datos mientras se descarga al cliente. La biblioteca también admite la integración con [Azure clave depósito](https://azure.microsoft.com/services/key-vault/) de administración de claves de cuenta de almacenamiento.

Para obtener un tutorial paso a paso que le guiará por el proceso de cifrado BLOB mediante el cifrado de cliente y depósito de clave de Azure, vea [cifrar y descifrar BLOB en Microsoft Azure almacenamiento mediante depósito de clave de Azure](storage-encrypt-decrypt-blobs-key-vault.md).

Para el cifrado de cliente con Java, vea [Cifrado de cliente con Java para Microsoft Azure almacenamiento](storage-client-side-encryption-java.md).

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Cifrado y descifrado mediante la técnica de sobres

Los procesos de cifrado y descifrado siguen la técnica de sobres.

### <a name="encryption-via-the-envelope-technique"></a>Cifrado a través de la técnica de sobres

Cifrado a través de la técnica de sobres funciona de la siguiente manera:

1. La biblioteca de cliente de almacenamiento de Azure genera una clave de cifrado de contenido (CEK), que es una clave simétrica de un uso.
2. Datos de usuario se cifran mediante este CEK.
3. La CEK, a continuación, se ajusta (cifrada) con la clave de cifrado de claves (KEK). La KEK se identifica mediante un identificador de clave y puede ser un par de claves asimétrico o una clave simétrica y pueden administrar de forma local o almacenarse en Azure clave depósitos.

    La biblioteca de cliente de almacenamiento nunca tiene acceso a KEK. La biblioteca invoca el algoritmo de ajuste de clave proporcionada por la clave de cámara. Los usuarios pueden elegir usar proveedores personalizados para ajuste o desprotección clave si lo desea.

4. A continuación, se cargan los datos cifrados con el servicio de almacenamiento de Azure. La tecla ajustada junto con algunos metadatos de cifrado adicional está almacenada como metadatos (en un blob) o interpola con los datos cifrados (cola de mensajes y entidades de tabla).

### <a name="decryption-via-the-envelope-technique"></a>Descifrado mediante la técnica de sobres

Descifrado mediante la técnica de sobres funciona de la siguiente manera:

1. La biblioteca de cliente, se supone que el usuario está administrando la clave de cifrado de claves (KEK) localmente o en Azure clave depósitos. El usuario no es necesario saber la clave específica que se ha usado para el cifrado. En su lugar, se puede configurar una resolución clave que resuelve diferentes identificadores de clave a claves y usar.
2. La biblioteca de cliente descarga los datos cifrados junto con cualquier material de cifrado que se almacena en el servicio.
3. La clave de cifrado de contenido ajustado (CEK) es no ajustado (descifrado) uso de la clave de cifrado de claves (KEK). Aquí de nuevo, la biblioteca de cliente no tiene acceso a KEK. Simplemente invoca personalizado o algoritmo de desencapsular del proveedor depósito de clave.
4. La clave de cifrado de contenido (CEK), a continuación, se usa para descifrar los datos de usuario cifrado.

## <a name="encryption-mechanism"></a>Mecanismo de cifrado

La biblioteca de cliente de almacenamiento usa [AES](http://en.wikipedia.org/wiki/Advanced_Encryption_Standard) para cifrar datos de usuario. En concreto, el modo de [Cadena de bloque de cifrado (CBC)](http://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) con AES. En cada servicio funciona de forma algo diferente, por lo que trataremos cada uno de ellos aquí.

### <a name="blobs"></a>BLOB

La biblioteca de cliente actualmente admite el cifrado de toda BLOB solo. Más concretamente, se admite el cifrado cuando los usuarios utilizan la **UploadFrom** * métodos o la * *OpenWrite** método. Para descargas, completas y descargas de rango son compatibles.

Durante el cifrado, la biblioteca de cliente generará un Vector de inicialización aleatorio (IV) de 16 bytes, junto con una clave de cifrado del contenido aleatorio (CEK) de 32 bytes y realizar el cifrado de sobres de los datos de blob utilizando esta información. La CEK ajustada y algunos metadatos de cifrado adicional, a continuación, se almacenan como blob metadatos junto con el cifrado blob en el servicio.

> [AZURE.WARNING] Si está modificando o cargar sus propios metadatos para el blob, debe asegurarse de que se conserven estos metadatos. Si carga se perderán los nuevos metadatos sin metadatos, la CEK ajustada, IV y otros metadatos y el contenido de blob nunca se puede recuperar nuevamente.

Descargar un blob cifrado implica recuperar el contenido del blob completo con la **DownloadTo***/**BlobReadStream** métodos útiles. La CEK ajustado se operación y se usa junto con el IV (en este caso se almacenan como blob metadatos) para devolver los datos descifrados a los usuarios.

Descargar un intervalo arbitrario (**DownloadRange*** métodos) en el objeto blob cifrado implica ajuste del rango proporcionado por los usuarios para obtener una pequeña cantidad de datos adicionales que pueden usarse para descifrar correctamente el intervalo solicitado.

Todos los tipos de objetos binarios (bloquear BLOB, página BLOB y anexar BLOB) se pueden cifrar/descifrar con este esquema.

### <a name="queues"></a>Colas

Puesto que la cola de mensajes puede ser de cualquier formato, la biblioteca de cliente define un formato personalizado que incluya el Vector de inicialización (IV) y la clave de cifrado del contenido cifrado (CEK) en el texto del mensaje.

Durante el cifrado, la biblioteca de cliente genera un IV aleatorio de 16 bytes junto con un CEK aleatorio de 32 bytes y realiza cifrado sobres del texto del mensaje de cola con esta información. La CEK ajustada y algunos metadatos de cifrado adicional, a continuación, se agregan al mensaje cifrado cola. Este mensaje modificado (que se muestra a continuación) se almacena en el servicio.

    <MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>

Durante el descifrado, la clave ajustada es extrae el mensaje de cola y operación. El IV también se extrae el mensaje de la cola y se usa junto con la clave no ajustada para descifrar los datos de mensaje de cola. Observe que los metadatos de cifrado pequeño (en 500 bytes), por lo que mientras que contar hacia el límite de 64KB de una cola de mensajes, el impacto debería ser manejable.

### <a name="tables"></a>Tablas

La biblioteca de cliente admite el cifrado de propiedades de entidad para insertar y reemplazar operaciones.

>[AZURE.NOTE] Combinación no es compatible actualmente. Dado que un subconjunto de propiedades se puede tiene cifrado previamente con una clave diferente, simplemente combinar las nuevas propiedades y actualizar los metadatos da de pérdida de datos. Combinar cualquiera requiere realizar llamadas de servicios adicionales para leer la entidad existente desde el servicio o con una nueva clave por propiedad, que no son adecuadas para mejorar el rendimiento.

Cifrado de datos de tabla funciona como sigue:  

1. Los usuarios especificar las propiedades de cifrado.
2. La biblioteca de cliente genera un Vector de inicialización aleatorio (IV) de 16 bytes junto con una clave de cifrado del contenido aleatorio (CEK) de 32 bytes para cada entidad y realiza el cifrado de sobres en las propiedades individuales cifrado deriva un nuevo IV por propiedad. La propiedad cifrada se almacena como datos binarios.
3. La CEK ajustada y algunos metadatos de cifrado adicional, a continuación, se almacenan como dos propiedades reservadas adicionales. La primera propiedad reservada (_ClientEncryptionMetadata1) es una propiedad de cadena que contiene la información sobre IV, versión y clave ajustada. La propiedad segundo reservada (_ClientEncryptionMetadata2) es una propiedad binaria que contiene la información sobre las propiedades que están cifrados. La información de esta propiedad segunda (_ClientEncryptionMetadata2) está cifrada.
4. Debido a estas propiedades reservadas adicionales necesarias para el cifrado, los usuarios pueden tener ahora sólo 250 propiedades personalizadas en lugar de 252. El tamaño total de la entidad debe ser menor que 1 MB.

Tenga en cuenta que se pueden cifrar sólo las propiedades de cadena. Si son otros tipos de propiedades de cifrado, debe convertir cadenas. Las cadenas cifradas se almacenan en el servicio como propiedades binarias y se convierten a cadenas después descifrado.

Para las tablas, además de la directiva de cifrado, los usuarios deben especificar las propiedades de cifrado. Para ello puede especificar un atributo [EncryptProperty] (para entidades POCO que derivan de TableEntity) o una resolución de cifrado en Opciones de la solicitud. Una resolución de cifrado es un delegado que toma una clave de partición, la clave de fila y el nombre de propiedad y devuelve un valor Boolean que indica si se debe cifrar esa propiedad. Durante el cifrado, la biblioteca de cliente usará esta información para decidir si se debe cifrar una propiedad al escribir en la conexión. El delegado también proporciona la posibilidad de lógica alrededor de cómo se cifran propiedades. (Por ejemplo, si los argumentos X, entonces cifrar propiedad A; en caso contrario, cifrar propiedades A y B.) Tenga en cuenta que no es necesario que proporcione esta información al leer o consultar entidades.

### <a name="batch-operations"></a>Operaciones por lotes

En operaciones por lotes, el mismo KEK se utilizará en todas las filas de esa operación por lotes porque la biblioteca de cliente solo permite a un objeto de opciones (y por tanto una directiva/KEK) por operación por lotes. Sin embargo, la biblioteca de cliente generará internamente un nuevo IV aleatorio y CEK aleatorio cada fila en el lote. Los usuarios también pueden elegir cifrar propiedades diferentes para cada operación del lote definiendo este comportamiento en la resolución de cifrado.

### <a name="queries"></a>Consultas

Para realizar operaciones de consulta, debe especificar a una resolución clave que puede resolver todas las claves del conjunto de resultados. Si no se resuelve una entidad incluida en el resultado de la consulta a un proveedor, la biblioteca de cliente producirá un error. Para cualquier consulta que realiza proyecciones de servidor, la biblioteca de cliente agregará las propiedades de metadatos de cifrado especial (_ClientEncryptionMetadata1 y _ClientEncryptionMetadata2) de forma predeterminada a las columnas seleccionadas.

## <a name="azure-key-vault"></a>Depósito de clave de Azure

Depósito de Azure clave ayuda a proteger las claves de cifrado y usado por aplicaciones de la nube y los servicios de información confidencial. Mediante depósito de clave de Azure, los usuarios pueden cifrar claves y la información confidencial (como claves de autenticación, las claves de cuenta de almacenamiento, claves de cifrado de datos. Archivos PFX y contraseñas) mediante las teclas que están protegidas por módulos de seguridad de hardware (HSM). Para obtener más información, vea [¿Qué es depósito de clave de Azure?](../key-vault/key-vault-whatis.md).

La biblioteca de cliente de almacenamiento usa la biblioteca de depósito de clave principal para proporcionar un marco común a través de Azure para administración de claves. Los usuarios también obtienen las ventajas adicionales del uso de la biblioteca de extensiones de depósito de clave. La biblioteca de extensiones proporciona funcionalidad útil alrededor simple y transparente simétrica/RSA local y proveedores clave de nube, así como con la agregación y almacenamiento en caché.

### <a name="interface-and-dependencies"></a>Interfaz y dependencias

Hay tres paquetes de depósito de clave:

- Microsoft.Azure.KeyVault.Core contiene el IKey y IKeyResolver. Es un paquete pequeño sin dependencias. La biblioteca de cliente de almacenamiento para .NET define como una dependencia.
- Microsoft.Azure.KeyVault contiene al cliente del resto del depósito de clave.
- Microsoft.Azure.KeyVault.Extensions contiene el código de extensión que incluye implementaciones de algoritmos de cifrado y un RSAKey y un SymmetricKey. Depende de los espacios de nombres principales y KeyVault y proporciona funcionalidad para definir una resolución agregada (cuando los usuarios desean usar varios proveedores de clave) y una resolución de clave de caché. Aunque la biblioteca de cliente de almacenamiento no dependen directamente de este paquete, si los usuarios desean utilizar Azure clave depósito para almacenar sus claves o para utilizar las extensiones de clave depósito para consumir a local y la nube proveedores de cifrado, tendrá este paquete.

Depósito de clave está diseñado para claves de patrón de alto valor y límites límites por depósito clave están diseñados con esto en cuenta. Al realizar el cifrado de cliente con clave de cámara, el modelo preferido es usar claves simétricas de patrón almacenan como información confidencial en depósito de clave y caché localmente. Los usuarios deben hacer lo siguiente:

1. Crear un secreto sin conexión y cárguelo en depósito de clave.
2. Use el identificador de base de secreto como un parámetro para resolver la versión actual de la clave para el cifrado y esta información localmente en caché. Usar CachingKeyResolver para almacenamiento en caché; los usuarios no se esperan para implementar sus propio almacenamiento en caché lógica.
3. Utilice a la resolución de caché como entrada al crear la directiva de cifrado.

Encontrará más información sobre el uso de la cámara de clave en los [ejemplos de código de cifrado](https://github.com/Azure/azure-storage-net/tree/master/Samples/GettingStarted/EncryptionSamples).

## <a name="best-practices"></a>Prácticas recomendadas

Compatibilidad con cifrado sólo está disponible en la biblioteca de cliente de almacenamiento para .NET. Windows Phone y tiempo de ejecución de Windows no admiten actualmente cifrado.

>[AZURE.IMPORTANT] Tenga en cuenta estos puntos importantes cuando se usa cifrado de cliente:
>
>- Al leer o escribir en un blob cifrado, utilice los comandos de carga de blob completo y comandos de descarga de blobs de rango/entero. Evitar escribir en un blob cifrado las operaciones de protocolo como poner bloque, coloque la lista de bloques, escribir páginas, borrar páginas o anexar bloque; en caso contrario, puede alterar la blob cifrado y que no sea legible.
>- Para las tablas, existe una restricción similar. Tenga cuidado de no actualizar propiedades cifradas sin actualizar los metadatos de cifrado.
>- Si establece metadatos en el blob cifrado, puede sobrescribir los metadatos relacionados con el cifrado necesario para descifrado, puesto que la configuración de metadatos no es aditivo. Esto también es true para instantáneas; Evite especificar los metadatos al crear una instantánea de un blob cifrado. Si se deben establecer metadatos, asegúrese de llamar al método **FetchAttributes** en primer lugar para obtener los metadatos de cifrado actual y evitar escribe simultáneas mientras se establecen los metadatos.
>- Habilitar la propiedad **RequireEncryption** en las opciones predeterminadas de solicitud para los usuarios que deben solo funcionan con datos cifrados. Para obtener más información, consulte a continuación.


## <a name="client-api--interface"></a>API de cliente / interfaz

Al crear un objeto de directiva de cifrado, los usuarios pueden proporcionar solo una clave (implementación de IKey), solo una resolución (implementar IKeyResolver) o ambas. IKey es el tipo básico de la clave que se identifica mediante un identificador de clave y que proporciona la lógica de ajuste o desprotección. IKeyResolver se usa para resolver una clave durante el proceso de descifrado. Define un método ResolveKey que devuelve una IKey dado un identificador de clave. Esto proporciona a los usuarios la capacidad para elegir entre varias teclas que se administran en varias ubicaciones.

- Para el cifrado, se utiliza siempre la clave y la ausencia de una clave se producirá un error.
- Para el descifrado:
    - Si especifica para obtener la clave, se invoca la resolución clave. Si se especifica la resolución, pero no tiene una asignación para el identificador de clave, se produce un error.
    - Si no se especifica la resolución, pero se especifica una clave, se utiliza la tecla si su identificador coincide con el identificador de clave necesario. Si el identificador no coincide, se produce un error.

Los [ejemplos de cifrado](https://github.com/Azure/azure-storage-net/tree/master/Samples/GettingStarted/EncryptionSamples) muestran un escenario de llevar a cabo más detallado para BLOB, colas y tablas, junto con la integración de depósito de clave.

### <a name="requireencryption-mode"></a>Modo de RequireEncryption

Los usuarios pueden habilitar opcionalmente un modo de operación donde se deben cifrar todas las cargas y descargas. En este modo, se producirá un error de intentos de datos sin una directiva de cifrado de cargar o descargar los datos que no están cifrados en el servicio en el cliente. La propiedad **RequireEncryption** la opciones del objeto de solicitud controla este comportamiento. Si la aplicación cifra todos los objetos almacenados en el almacenamiento de Azure, puede establecer la propiedad **RequireEncryption** en las opciones predeterminadas de solicitud para el objeto de cliente de servicio. Por ejemplo, establezca **CloudBlobClient.DefaultRequestOptions.RequireEncryption** en **Verdadero** requerir cifrado para todas las operaciones de blob realizadas por ese objeto de cliente.

### <a name="blob-service-encryption"></a>Cifrado de servicio de blobs

Crear un objeto de **BlobEncryptionPolicy** y establezca en las opciones de la solicitud (por API o en un nivel de cliente usando **DefaultRequestOptions**). Todo lo demás se tratará la biblioteca cliente internamente.

    // Create the IKey used for encryption.
    RsaKey key = new RsaKey("private:key1" /* key identifier */);

    // Create the encryption policy to be used for upload and download.
    BlobEncryptionPolicy policy = new BlobEncryptionPolicy(key, null);

    // Set the encryption policy on the request options.
    BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

    // Upload the encrypted contents to the blob.
    blob.UploadFromStream(stream, size, null, options, null);

    // Download and decrypt the encrypted contents from the blob.
    MemoryStream outputStream = new MemoryStream();
    blob.DownloadToStream(outputStream, null, options, null);

### <a name="queue-service-encryption"></a>Cifrado de servicio de cola

Crear un objeto de **QueueEncryptionPolicy** y establezca en las opciones de la solicitud (por API o en un nivel de cliente usando **DefaultRequestOptions**). Todo lo demás se tratará la biblioteca cliente internamente.


    // Create the IKey used for encryption.
    RsaKey key = new RsaKey("private:key1" /* key identifier */);

    // Create the encryption policy to be used for upload and download.
    QueueEncryptionPolicy policy = new QueueEncryptionPolicy(key, null);

    // Add message
    QueueRequestOptions options = new QueueRequestOptions() { EncryptionPolicy = policy };
    queue.AddMessage(message, null, null, options, null);

    // Retrieve message
    CloudQueueMessage retrMessage = queue.GetMessage(null, options, null);

### <a name="table-service-encryption"></a>Cifrado de servicio de tabla

Además de crear una directiva de cifrado y establecer las opciones de solicitud de, debe especificar un **EncryptionResolver** en **TableRequestOptions**o establecer el atributo [EncryptProperty] en la entidad.

#### <a name="using-the-resolver"></a>Utilizar la resolución


    // Create the IKey used for encryption.
    RsaKey key = new RsaKey("private:key1" /* key identifier */);

    // Create the encryption policy to be used for upload and download.
    TableEncryptionPolicy policy = new TableEncryptionPolicy(key, null);

    TableRequestOptions options = new TableRequestOptions()
    {
        EncryptionResolver = (pk, rk, propName) =>
        {
            if (propName == "foo")
            {
                return true;
            }
            return false;
        },
        EncryptionPolicy = policy
    };

    // Insert Entity
    currentTable.Execute(TableOperation.Insert(ent), options, null);

    // Retrieve Entity
    // No need to specify an encryption resolver for retrieve
    TableRequestOptions retrieveOptions = new TableRequestOptions()
    {
        EncryptionPolicy = policy
    };

    TableOperation operation = TableOperation.Retrieve(ent.PartitionKey, ent.RowKey);
    TableResult result = currentTable.Execute(operation, retrieveOptions, null);

#### <a name="using-attributes"></a>Uso de atributos

Como se mencionó anteriormente, si la entidad implementa TableEntity, las propiedades se pueden decorar con el atributo [EncryptProperty] en lugar de especificar la **EncryptionResolver**.

    [EncryptProperty]
    public string EncryptedProperty1 { get; set; }

## <a name="encryption-and-performance"></a>Cifrado y rendimiento

Tenga en cuenta que los resultados de datos de almacenamiento en sobrecarga de rendimiento adicional de cifrado. Se deben generar la clave de contenido y el IV, se debe cifrar el contenido y metadatos adicionales se deben con formato y cargado. Esta sobrecarga varían según la cantidad de datos que se cifran. Le recomendamos que los clientes siempre prueben sus aplicaciones de rendimiento durante el desarrollo.

## <a name="next-steps"></a>Pasos siguientes

- [Tutorial: Cifrar y descifrar BLOB en Microsoft Azure almacenamiento mediante depósito de clave de Azure](storage-encrypt-decrypt-blobs-key-vault.md)
- Descargar la [Biblioteca de cliente de almacenamiento de Azure para paquete NuGet .NET](https://www.nuget.org/packages/WindowsAzure.Storage)
- Descargar los paquetes de Azure clave depósito NuGet [Core](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core/), [cliente](http://www.nuget.org/packages/Microsoft.Azure.KeyVault/)y [extensiones](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions/)  
- Visite la [clave de Azure depósito documentación](../key-vault/key-vault-whatis.md)

