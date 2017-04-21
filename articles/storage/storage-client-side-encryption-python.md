<properties
    pageTitle="Cifrado de cliente con Python para Microsoft Azure almacenamiento | Microsoft Azure"
    description="La biblioteca de cliente de almacenamiento de Azure para Python admite el cifrado de cliente para una mayor seguridad para las aplicaciones de almacenamiento de Azure."
    services="storage"
    documentationCenter="python"
    authors="dineshmurthy"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="dineshm"/>


# <a name="client-side-encryption-with-python-for-microsoft-azure-storage"></a>Cifrado de cliente con Python para el almacenamiento de Microsoft Azure

[AZURE.INCLUDE [storage-selector-client-side-encryption-include](../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Información general

La [Biblioteca de cliente de almacenamiento de Azure para Python](https://pypi.python.org/pypi/azure-storage) admite el cifrado de datos en aplicaciones cliente antes de cargar con el almacenamiento de Azure y descifrar datos mientras se descarga al cliente.

>[AZURE.NOTE] La biblioteca de Azure almacenamiento Python está en vista previa.

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Cifrado y descifrado mediante la técnica de sobres
Los procesos de cifrado y descifrado siguen la técnica de sobres.

### <a name="encryption-via-the-envelope-technique"></a>Cifrado a través de la técnica de sobres
Cifrado a través de la técnica de sobres funciona de la siguiente manera:

1.  La biblioteca de cliente de almacenamiento de Azure genera una clave de cifrado de contenido (CEK), que es una clave simétrica de un uso.

2.  Datos de usuario se cifran mediante este CEK.

3.  La CEK, a continuación, se ajusta (cifrada) con la clave de cifrado de claves (KEK). La KEK se identifica mediante un identificador de clave y puede ser un par de claves asimétrico o una clave simétrica, que se administra localmente.
La biblioteca de cliente de almacenamiento nunca tiene acceso a KEK. La biblioteca invoca el algoritmo de ajuste de clave proporcionada por la KEK. Los usuarios pueden elegir usar proveedores personalizados para ajuste o desprotección clave si lo desea.

4.  A continuación, se cargan los datos cifrados con el servicio de almacenamiento de Azure. La tecla ajustada junto con algunos metadatos de cifrado adicional está almacenada como metadatos (en un blob) o interpola con los datos cifrados (cola de mensajes y entidades de tabla).

### <a name="decryption-via-the-envelope-technique"></a>Descifrado mediante la técnica de sobres
Descifrado mediante la técnica de sobres funciona de la siguiente manera:

1.  La biblioteca de cliente, se supone que el usuario controla la clave de cifrado de claves (KEK) localmente. El usuario no es necesario saber la clave específica que se ha usado para el cifrado. En su lugar, se puede configurar una resolución clave, que resuelve los identificadores de clave diferentes a las claves, y usar.

2.  La biblioteca de cliente descarga los datos cifrados junto con cualquier material de cifrado que se almacena en el servicio.

3.  La clave de cifrado de contenido ajustado (CEK) es no ajustado (descifrado) uso de la clave de cifrado de claves (KEK). Aquí de nuevo, la biblioteca de cliente no tiene acceso a KEK. Simplemente invoca algoritmo de desencapsular del proveedor personalizado.

4.  La clave de cifrado de contenido (CEK), a continuación, se usa para descifrar los datos de usuario cifrado.

## <a name="encryption-mechanism"></a>Mecanismo de cifrado  
La biblioteca de cliente de almacenamiento usa [AES](http://en.wikipedia.org/wiki/Advanced_Encryption_Standard) para cifrar datos de usuario. En concreto, el modo de [Cadena de bloque de cifrado (CBC)](http://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) con AES. En cada servicio funciona de forma algo diferente, por lo que trataremos cada uno de ellos aquí.

### <a name="blobs"></a>BLOB
La biblioteca de cliente actualmente admite el cifrado de toda BLOB solo. Más concretamente, se admite el cifrado cuando los usuarios utilizan la **crear*** métodos. Se admiten las descargas, completas y descargas de intervalo y puesta en paralelo de carga y descarga está disponible.

Durante el cifrado, la biblioteca de cliente generará un Vector de inicialización aleatorio (IV) de 16 bytes, junto con una clave de cifrado del contenido aleatorio (CEK) de 32 bytes y realizar el cifrado de sobres de los datos de blob utilizando esta información. La CEK ajustada y algunos metadatos de cifrado adicional, a continuación, se almacenan como blob metadatos junto con el cifrado blob en el servicio.

>[AZURE.WARNING] Si está modificando o cargar sus propios metadatos para el blob, debe asegurarse de que se conserven estos metadatos. Si carga nuevos metadatos sin metadatos, se perderán el CEK ajustada, IV y otros metadatos y el contenido de blob nunca se puede recuperar nuevamente.

Descargar un blob cifrado implica recuperar el contenido del blob completo con **obtener*** métodos útiles. La CEK ajustado se operación y se usa junto con el IV (en este caso se almacenan como blob metadatos) para devolver los datos descifrados a los usuarios.

Descargar un intervalo arbitrario (**obtener*** métodos con parámetros de rango pasan) en el objeto blob cifrado implica ajuste del rango proporcionado por los usuarios para obtener una pequeña cantidad de datos adicionales que pueden usarse para descifrar correctamente el intervalo solicitado.

Bloque BLOB y blobs de página sólo pueden ser cifrado/descifrado mediante este esquema. Actualmente no hay ninguna compatibilidad para cifrar anexar BLOB.

### <a name="queues"></a>Colas
Puesto que la cola de mensajes puede ser de cualquier formato, la biblioteca de cliente define un formato personalizado que incluya el Vector de inicialización (IV) y la clave de cifrado del contenido cifrado (CEK) en el texto del mensaje.

Durante el cifrado, la biblioteca de cliente genera un IV aleatorio de 16 bytes junto con un CEK aleatorio de 32 bytes y realiza cifrado sobres del texto del mensaje de cola con esta información. La CEK ajustada y algunos metadatos de cifrado adicional, a continuación, se agregan al mensaje cifrado cola. Este mensaje modificado (que se muestra a continuación) se almacena en el servicio.

    <MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>

Durante el descifrado, la clave ajustada es extrae el mensaje de cola y operación. El IV también se extrae el mensaje de la cola y se usa junto con la clave no ajustada para descifrar los datos de mensaje de cola. Observe que los metadatos de cifrado pequeño (en 500 bytes), por lo que mientras que contar hacia el límite de 64KB de una cola de mensajes, el impacto debería ser manejable.

### <a name="tables"></a>Tablas
La biblioteca de cliente admite el cifrado de propiedades de entidad para insertar y reemplazar operaciones.

>[AZURE.NOTE] Combinación no es compatible actualmente. Dado que un subconjunto de propiedades se puede tiene cifrado previamente con una clave diferente, simplemente combinar las nuevas propiedades y actualizar los metadatos da de pérdida de datos. Combinar cualquiera requiere realizar llamadas de servicios adicionales para leer la entidad existente desde el servicio o con una nueva clave por propiedad, que no son adecuadas para mejorar el rendimiento.

Cifrado de datos de tabla funciona como sigue:

1.  Los usuarios especificar las propiedades de cifrado.

2.  La biblioteca de cliente genera un Vector de inicialización aleatorio (IV) de 16 bytes junto con una clave de cifrado del contenido aleatorio (CEK) de 32 bytes para cada entidad y realiza el cifrado de sobres en las propiedades individuales cifrado deriva un nuevo IV por propiedad. La propiedad cifrada se almacena como datos binarios.

3.  La CEK ajustada y algunos metadatos de cifrado adicional, a continuación, se almacenan como dos propiedades reservadas adicionales. La primera propiedad de reserva (\_ClientEncryptionMetadata1) es una propiedad de cadena que contiene la información sobre IV, versión y clave ajustada. El segundo reservados propiedad (\_ClientEncryptionMetadata2) es una propiedad binaria que contiene la información sobre las propiedades que están cifrados. La información de esta propiedad segunda (\_ClientEncryptionMetadata2) está cifrada.

4.  Debido a estas propiedades reservadas adicionales necesarias para el cifrado, los usuarios pueden tener ahora sólo 250 propiedades personalizadas en lugar de 252. El tamaño total de la entidad debe ser menor que 1MB.

    Tenga en cuenta que se pueden cifrar sólo las propiedades de cadena. Si son otros tipos de propiedades de cifrado, debe convertir cadenas. Las cadenas cifradas se almacenan en el servicio como propiedades binarias y se convierten a cadenas (cadenas sin formato, no EntityProperties con tipo EdmType.STRING) después de descifrado.

    Para las tablas, además de la directiva de cifrado, los usuarios deben especificar las propiedades de cifrado. Esto puede hacerse almacenando estas propiedades en objetos TableEntity con el conjunto de tipos de EdmType.STRING y cifrar establecida en true o estableciendo la encryption_resolver_function en el objeto tableservice. Una resolución de cifrado es una función que toma una clave de partición, la clave de fila y el nombre de propiedad y devuelve un valor boolean que indica si se debe cifrar esa propiedad. Durante el cifrado, la biblioteca de cliente usará esta información para decidir si se debe cifrar una propiedad al escribir en la conexión. El delegado también proporciona la posibilidad de lógica alrededor de cómo se cifran propiedades. (Por ejemplo, si los argumentos X, entonces cifrar propiedad A; en caso contrario, cifrar propiedades A y B.) Tenga en cuenta que no es necesario que proporcione esta información al leer o consultar entidades.

### <a name="batch-operations"></a>Operaciones por lotes
Una directiva de cifrado se aplica a todas las filas del lote. La biblioteca de cliente internamente generará un nuevo IV aleatorio y CEK aleatorio cada fila en el lote. Los usuarios también pueden elegir cifrar propiedades diferentes para cada operación del lote definiendo este comportamiento en la resolución de cifrado.
Si un lote se creará como un administrador de contexto a través del método de batch() tableservice, directiva de cifrado de tableservice se aplicará automáticamente al lote. Si un lote se crea explícitamente llamando al constructor, la directiva de cifrado se debe pasar como un parámetro e izquierda sin modificar durante la duración del lote.
Tenga en cuenta que se cifran entidades tal como se ha insertado en el lote mediante la directiva de cifrado del lote (entidades no están cifradas en el momento de asignar el lote mediante la directiva de cifrado de tableservice).

### <a name="queries"></a>Consultas
Para realizar operaciones de consulta, debe especificar a una resolución clave que puede resolver todas las claves del conjunto de resultados. Si no se resuelve una entidad incluida en el resultado de la consulta a un proveedor, la biblioteca de cliente producirá un error. Para cualquier consulta que realiza proyecciones del lado servidor, la biblioteca de cliente agregará las propiedades de metadatos de cifrado especial (\_ClientEncryptionMetadata1 y \_ClientEncryptionMetadata2) de forma predeterminada, las columnas seleccionadas.

>[AZURE.IMPORTANT] Tenga en cuenta estos puntos importantes cuando se usa cifrado de cliente:
>
>- Al leer o escribir en un blob cifrado, utilice los comandos de carga de blob completo y comandos de descarga de blobs de rango/entero. Evitar escribir en un blob cifrado con operaciones de protocolo como poner bloque, coloque la lista de bloques, escribir páginas o borrar páginas; en caso contrario, puede alterar la blob cifrado y que no sea legible.
>
>- Para las tablas, existe una restricción similar. Tenga cuidado de no actualizar propiedades cifradas sin actualizar los metadatos de cifrado.
>
>- Si establece metadatos en el blob cifrado, puede sobrescribir los metadatos relacionados con el cifrado necesario para descifrado, puesto que la configuración de metadatos no es aditivo. Esto también es true para instantáneas; Evite especificar los metadatos al crear una instantánea de un blob cifrado. Si se deben establecer metadatos, asegúrese de llamar al método **get_blob_metadata** en primer lugar para obtener los metadatos de cifrado actual y evitar escribe simultáneas mientras se establecen los metadatos.
>
>- Habilitar el indicador **require_encryption** en el objeto de servicio para los usuarios que deben solo funcionan con datos cifrados. Para obtener más información, consulte a continuación.

La biblioteca de cliente de almacenamiento espera la KEK proporcionado y la resolución clave para implementar la interfaz siguiente. [Depósito de clave de Azure](https://azure.microsoft.com/services/key-vault/) y soporte técnico para la administración de Python KEK pendiente se integrarán en esta biblioteca cuando completado.


## <a name="client-api--interface"></a>API de cliente / interfaz
Después de haber creado un objeto de servicio de almacenamiento de información (es decir, blockblobservice), el usuario puede asignar valores a los campos que constituyen una directiva de cifrado: key_encryption_key, key_resolver_function y require_encryption. Los usuarios pueden proporcionar solo un KEK, solo una resolución o ambos. key_encryption_key es el tipo básico de la clave que se identifica mediante un identificador de clave y que proporciona la lógica de ajuste o desprotección. key_resolver_function se usa para resolver una clave durante el proceso de descifrado. Devuelve un KEK válida dado un identificador de clave. Esto proporciona a los usuarios la capacidad para elegir entre varias teclas que se administran en varias ubicaciones.

La KEK debe implementar los siguientes métodos para cifrar los datos correctamente:
- wrap_key(CEK): se ajusta la CEK especificado (bytes) mediante un algoritmo de la elección del usuario. Devuelve la clave ajustada.
- get_key_wrap_algorithm(): devuelve el algoritmo utilizado para ajustar las teclas.
- get_kid(): devuelve el identificador de clave de cadena para esta KEK.
La KEK debe implementar los métodos siguientes para descifrar correctamente los datos:
- unwrap_key (cek, algoritmo): devuelve la forma no ajustada de la CEK especificado mediante el algoritmo de la cadena especificada.
- get_kid(): devuelve un identificador de clave de cadena para esta KEK.

La resolución de clave debe implementar al menos un método que, dado un identificador de clave, devuelve el KEK correspondiente implementación de la interfaz anterior. Solo este método es que se asignará a la propiedad key_resolver_function en el objeto de servicio.

- Para el cifrado, se utiliza siempre la clave y la ausencia de una clave se producirá un error.
- Para el descifrado:
    - Si especifica para obtener la clave, se invoca la resolución clave. Si se especifica la resolución, pero no tiene una asignación para el identificador de clave, se produce un error.
    - Si no se especifica la resolución, pero se especifica una clave, se utiliza la tecla si su identificador coincide con el identificador de clave necesario. Si el identificador no coincide, se produce un error.

      Los ejemplos de cifrado de azure.storage.samples <fix URL>muestra un escenario de llevar a cabo más detallado para BLOB, colas y tablas.
        Implementaciones de ejemplo de la KEK y resolución de clave se proporcionan en los archivos de muestra como KeyWrapper y KeyResolver respectivamente.

### <a name="requireencryption-mode"></a>Modo de RequireEncryption
Los usuarios pueden habilitar opcionalmente un modo de operación donde se deben cifrar todas las cargas y descargas. En este modo, se producirá un error de intentos de datos sin una directiva de cifrado de cargar o descargar los datos que no están cifrados en el servicio en el cliente. El indicador **require_encryption** en el objeto de servicio controla este comportamiento.

### <a name="blob-service-encryption"></a>Cifrado de servicio de blobs
Establecer el cifrado campos de directiva en el objeto blockblobservice. Todo lo demás se tratará la biblioteca cliente internamente.

    # Create the KEK used for encryption.
    # KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
    kek = KeyWrapper('local:key1') # Key identifier

    # Create the key resolver used for decryption.
    # KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
    key_resolver = KeyResolver()
    key_resolver.put_key(kek)

    # Set the KEK and key resolver on the service object.
    my_block_blob_service.key_encryption_key = kek
    my_block_blob_service.key_resolver_funcion = key_resolver.resolve_key

    # Upload the encrypted contents to the blob.
    my_block_blob_service.create_blob_from_stream(container_name, blob_name, stream)

    # Download and decrypt the encrypted contents from the blob.
    blob = my_block_blob_service.get_blob_to_bytes(container_name, blob_name)

### <a name="queue-service-encryption"></a>Cifrado de servicio de cola
Establecer el cifrado campos de directiva en el objeto queueservice. Todo lo demás se tratará la biblioteca cliente internamente.

    # Create the KEK used for encryption.
    # KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
    kek = KeyWrapper('local:key1') # Key identifier

    # Create the key resolver used for decryption.
    # KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
    key_resolver = KeyResolver()
    key_resolver.put_key(kek)

    # Set the KEK and key resolver on the service object.
    my_queue_service.key_encryption_key = kek
    my_queue_service.key_resolver_funcion = key_resolver.resolve_key

    # Add message
    my_queue_service.put_message(queue_name, content)

    # Retrieve message
    retrieved_message_list = my_queue_service.get_messages(queue_name)

### <a name="table-service-encryption"></a>Cifrado de servicio de tabla
Además de crear una directiva de cifrado y establecer las opciones de solicitud de, debe especificar un **encryption_resolver_function** en la **tableservice**o establezca el atributo cifrar la EntityProperty.

### <a name="using-the-resolver"></a>Utilizar la resolución

    # Create the KEK used for encryption.
    # KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
    kek = KeyWrapper('local:key1') # Key identifier

    # Create the key resolver used for decryption.
    # KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
    key_resolver = KeyResolver()
    key_resolver.put_key(kek)

    # Define the encryption resolver_function.
    def my_encryption_resolver(pk, rk, property_name):
            if property_name == 'foo':
                    return True
            return False

    # Set the KEK and key resolver on the service object.
    my_table_service.key_encryption_key = kek
    my_table_service.key_resolver_funcion = key_resolver.resolve_key
    my_table_service.encryption_resolver_function = my_encryption_resolver

    # Insert Entity
    my_table_service.insert_entity(table_name, entity)

    # Retrieve Entity
    # Note: No need to specify an encryption resolver for retrieve, but it is harmless to leave the property set.
    my_table_service.get_entity(table_name, entity['PartitionKey'], entity['RowKey'])

### <a name="using-attributes"></a>Uso de atributos
Como se mencionó anteriormente, una propiedad puede marcarse para el cifrado almacenar en un objeto EntityProperty y estableciendo el campo cifrar.

    encrypted_property_1 = EntityProperty(EdmType.STRING, value, encrypt=True)

## <a name="encryption-and-performance"></a>Cifrado y rendimiento
Tenga en cuenta que los resultados de datos de almacenamiento en sobrecarga de rendimiento adicional de cifrado. Se deben generar la clave de contenido y el IV, se debe cifrar el contenido y metadatos adicionales se deben con formato y cargado. Esta sobrecarga varían según la cantidad de datos que se cifran. Le recomendamos que los clientes siempre prueben sus aplicaciones de rendimiento durante el desarrollo.

## <a name="next-steps"></a>Pasos siguientes

- Descargar la [Biblioteca de cliente de almacenamiento de Azure para el paquete de Java PyPi](https://pypi.python.org/pypi/azure-storage)
- Descargar el [código de GitHub de fuente de la biblioteca de cliente de almacenamiento de Azure para Python](https://github.com/Azure/azure-storage-python)
