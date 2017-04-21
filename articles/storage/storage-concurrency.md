<properties
    pageTitle="Administrar la simultaneidad en Microsoft Azure almacenamiento"
    description="Cómo administrar la simultaneidad para los servicios de blobs, cola, tabla y archivo"
    services="storage"
    documentationCenter=""
    authors="jasonnewyork"
    manager="tadb"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="jahogg"/>

# <a name="managing-concurrency-in-microsoft-azure-storage"></a>Administrar la simultaneidad en Microsoft Azure almacenamiento

## <a name="overview"></a>Información general

Las aplicaciones basadas en Internet modernas normalmente cuentan con varios usuarios ver y actualizar los datos al mismo tiempo. Para ello, los desarrolladores de aplicaciones a pensar sobre cómo proporcionar una experiencia predecible a sus usuarios finales, especialmente en escenarios donde varios usuarios pueden actualizar los mismos datos. Hay tres estrategias de simultaneidad de datos principal, los desarrolladores normalmente tendrá en cuenta:  


1.  Simultaneidad optimista: última una aplicación para realizar una actualización como parte de su actualización comprobar si los datos han cambiado desde la aplicación lee datos. Por ejemplo, si dos usuarios viendo una página wiki realizan una actualización en la misma página de la plataforma de wiki debe asegurarse que la segunda actualización no sobrescribe la primera actualización, y que ambos usuarios comprenden si la actualización fue correcta o no. Esta estrategia se utiliza con mayor frecuencia en las aplicaciones web.
2.  Simultaneidad pesimista: una aplicación ¿desea realizar una actualización tardará un bloqueo en un objeto para evitar que otros usuarios actualicen los datos hasta que se libera el bloqueo. Por ejemplo, en un escenario de replicación de datos principal y subordinado donde solo el patrón realizará actualizaciones el patrón normalmente contendrá un bloqueo exclusivo durante un largo período de tiempo en los datos para asegurarse de que nadie más puede actualizarla.
3.  Último escrito gana: un enfoque que permite las operaciones de actualización continuar sin comprobar si cualquier otra aplicación actualizó primero los datos desde la aplicación lee los datos. Esta estrategia (o la falta de una estrategia formal) se usa generalmente donde se dividen los datos de forma que no hay ninguna posibilidad de que varios usuarios tendrán acceso a los mismos datos. También puede ser útil donde se procesan las secuencias de datos de corta duración.  

Este artículo proporciona una descripción general de cómo simplifica el desarrollo de la plataforma de almacenamiento de Azure al proporcionar soporte de primera clase para tres de estas estrategias de simultaneidad.  

## <a name="azure-storage--simplifies-cloud-development"></a>Desarrollo de nube simplifica el almacenamiento de Azure:
El servicio de almacenamiento de Azure es compatible con todos los tres estrategias, aunque es distintivo en su capacidad para proporcionar soporte completo para simultaneidad optimista y pesimista porque se ha diseñado para adoptar un modelo de coherencia seguro que garantiza que cuando el servicio de almacenamiento confirma un insertar datos o la operación de actualización todos los accesos aún más a que los datos muestra la actualización más reciente. Plataformas de almacenamiento que utilizan un modelo de la coherencia tienen un retraso entre cuando se realiza una escritura de un usuario y cuando los datos actualizados se pueden ver por otros usuarios complicar, por tanto, desarrollo de aplicaciones de cliente con el fin de evitar incoherencias afecten a los usuarios finales.  

Además de seleccionar una estrategia de simultaneidad adecuado los programadores también deben conocer cómo una plataforma de almacenamiento aísla cambios – especialmente al mismo objeto en las transacciones. El servicio de almacenamiento de Azure usa aislamiento de instantánea para permitir operaciones de lectura se producen al mismo tiempo operaciones de escritura dentro de una sola partición. A diferencia de otros niveles de aislamiento, aislamiento de instantánea garantiza que todas las lecturas ven una instantánea coherente de los datos incluso cuando se producen actualizaciones: esencialmente volviendo el último valores confirmados mientras una actualización transacción se está procesando.  

## <a name="managing-concurrency-in-blob-storage"></a>Administrar la simultaneidad en el almacenamiento de blobs
Puede optar por usar ambos modelos de simultaneidad optimista o pesimista para administrar el acceso a objetos binarios y contenedores en el servicio de blobs de Windows. Si no se especifica explícitamente un último escribe gana de estrategia es el valor predeterminado.  

### <a name="optimistic-concurrency-for-blobs-and-containers"></a>Simultaneidad optimista para BLOB y contenedores  

El servicio de almacenamiento asigna un identificador a todos los objetos almacenados. Este identificador se actualiza cada vez que se realiza una operación de actualización en un objeto. Devuelve el identificador del cliente como parte de una respuesta HTTP GET con encabezado ETag (etiqueta de entidad) que se define en el protocolo HTTP. Ejecutar una actualización de este tipo de objeto de usuario puede enviar en ETag original junto con un encabezado condicional para asegurarse de que solo se producirá una actualización si se cumple una condición: en este caso, la condición es un encabezado de "If coincidir" que requiere el servicio de almacenamiento para asegurarse de que el valor de ETag especificado en la solicitud de actualización es el mismo que se almacena en el servicio de almacenamiento.  

El esquema de este proceso es la siguiente:  

1.  Recuperar un objeto blob desde el servicio de almacenamiento, la respuesta incluye un valor de encabezado de HTTP ETag que identifica la versión actual del objeto en el servicio de almacenamiento.
2.  Cuando actualiza el blob, incluya el valor de ETag que ha recibido en el paso 1 en el encabezado condicional **Si coincidencia** de la solicitud de que enviar al servicio.
3.  El servicio compara el valor de ETag en la solicitud con el valor de ETag actual del blob.
4.  Si el valor de ETag actual del blob es una versión diferente ETag en el encabezado condicional **Si coincidir** en la solicitud, el servicio devuelve un error 412 al cliente. Esto indica al cliente que otro proceso ha actualizado el blob desde el cliente de leerlo.
5.  Si el valor de ETag actual del objeto binario es la misma versión que ETag en el encabezado condicional **Si coincidir** en la solicitud, el servicio realiza la operación y actualiza el valor de ETag actual de blobs de Windows para mostrar que ha creado una nueva versión.  

El siguiente fragmento de C# (con la biblioteca de almacenamiento de cliente 4.2.0) muestra un sencillo ejemplo de cómo crear una **Coincidencia si AccessCondition** según el valor de ETag que se accede desde las propiedades de un blob que se ha recuperado o insertado. A continuación, se utiliza el objeto **AccessCondition** cuando se actualizando el blob: el objeto **AccessCondition** agrega el encabezado de **Coincidencia si** a la solicitud. Si otro proceso ha actualizado el blob, el servicio de blobs devuelve un mensaje de estado 412 de HTTP (error de condición previa). Puede descargar aquí el ejemplo completo: [Administración de simultaneidad con el almacenamiento de Azure](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).  

    // Retrieve the ETag from the newly created blob
    // Etag is already populated as UploadText should cause a PUT Blob call
    // to storage blob service which returns the etag in response.
    string orignalETag = blockBlob.Properties.ETag;

    // This code simulates an update by a third party.
    string helloText = "Blob updated by a third party.";

    // No etag, provided so orignal blob is overwritten (thus generating a new etag)
    blockBlob.UploadText(helloText);
    Console.WriteLine("Blob updated. Updated ETag = {0}",
    blockBlob.Properties.ETag);

    // Now try to update the blob using the orignal ETag provided when the blob was created
    try
    {
        Console.WriteLine("Trying to update blob using orignal etag to generate if-match access condition");
        blockBlob.UploadText(helloText,accessCondition:
        AccessCondition.GenerateIfMatchCondition(orignalETag));
    }
    catch (StorageException ex)
    {
        if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
        {
            Console.WriteLine("Precondition failure as expected. Blob's orignal etag no longer matches");
            // TODO: client can decide on how it wants to handle the 3rd party updated content.
        }
        else
            throw;
    }  

El servicio de almacenamiento también incluye compatibilidad para encabezados condicional adicionales como **Si modificado desde**, **Si modificado desde** y **Si lo ninguna coincidencia** como combinaciones de los mismos. Para obtener más información, vea [Especificar encabezados condicional para operaciones de servicio de blobs de Windows](http://msdn.microsoft.com/library/azure/dd179371.aspx) en MSDN.  

La siguiente tabla resume las operaciones de contenedor que acepte encabezados condicional como **Si una coincidencia** de la solicitud y que devuelven un valor de ETag en la respuesta.  

| Operación                | Devuelve el valor ETag contenedor | Acepta encabezados condicional |
|:-------------------------|:-----------------------------|:----------------------------|
| Crear contenedor         | Sí                          | No                          |
| Obtener las propiedades de contenedor | Sí                          | No                          |
| Obtener los metadatos de contenedor   | Sí                          | No                          |
| Establecer los metadatos de contenedor   | Sí                          | Sí                         |
| Obtener contenedor ACL        | Sí                          | No                          |
| Establecer ACL del contenedor        | Sí                          | Sí (*)                     |
| Eliminar el contenedor         | No                           | Sí                         |
| Contenedor de concesión          | Sí                          | Sí                         |
| Lista BLOB               | No                           | No                          |

(*) Los permisos definidos por SetContainerACL se almacenan en caché y actualizaciones de los permisos siguientes durar 30 segundos en propagarse durante el período que las actualizaciones no se garantiza que sea coherente.  

La siguiente tabla resume las operaciones de blob que acepte encabezados condicional como **Si una coincidencia** de la solicitud y que devuelven un valor de ETag en la respuesta.

| Operación           | Devuelve el valor ETag | Acepta encabezados condicional           |
|:--------------------|:-------------------|:--------------------------------------|
| Poner Blob            | Sí                | Sí                                   |
| Obtener Blob            | Sí                | Sí                                   |
| Obtener propiedades de blobs | Sí                | Sí                                   |
| Establecer propiedades de blobs | Sí                | Sí                                   |
| Obtener los metadatos de blobs   | Sí                | Sí                                   |
| Establecer los metadatos de blobs   | Sí                | Sí                                   |
| Concesión Blob (*)      | Sí                | Sí                                   |
| Instantánea Blob       | Sí                | Sí                                   |
| Copiar Blob           | Sí                | Sí (para blobs de origen y destino) |
| Anular copia Blob     | No                 | No                                    |
| Eliminar Blob         | No                 | Sí                                   |
| Colocar el bloque           | No                 | No                                    |
| Coloque la lista de bloques      | Sí                | Sí                                   |
| Obtener la lista de bloques      | Sí                | No                                    |
| Insertar página            | Sí                | Sí                                   |
| Obtener rangos de página     | Sí                | Sí                                   |


(*) Concesión Blob no cambia el ETag en un blob.  

### <a name="pessimistic-concurrency-for-blobs"></a>Simultaneidad pesimista para blobs
Para bloquear un blob para uso exclusivo, puede adquirir una [concesión](http://msdn.microsoft.com/library/azure/ee691972.aspx) en él. Cuando adquiera una concesión, especifique durante cuánto tiempo debe la concesión: puede ser de entre 15 a 60 segundos o infinito que equivale a un bloqueo exclusivo. Puede renovar una concesión finita ampliarlo y puede liberar cualquier concesión cuando haya terminado con él. El servicio de blobs libera automáticamente arrendamientos finitas al caducar.  

Arrendamientos habilitar estrategias de sincronización diferente que sea compatible con, incluidos escritura exclusivo / comparten lectura escritura exclusivo / exclusiva lee y compartido escritura / lectura exclusivo. Cuando existe una concesión el servicio de almacenamiento exige exclusiva escribe (colocar, establecer y eliminar operaciones) sin embargo garantiza la exclusividad de las operaciones de lectura requiere que el programador garantizar que todas las aplicaciones cliente utilizan un identificador de concesión y que solo un cliente en un momento tiene un identificador de concesión válida. Lea las operaciones que no incluya un resultado de ID de concesión en lecturas compartidas.  

El siguiente fragmento de C#, muestra un ejemplo de adquirir una concesión exclusivo durante 30 segundos en un blob, actualizar el contenido del objeto binario y, a continuación, liberar la concesión. Si ya hay una concesión válida en el blob al intentar obtener una nueva concesión, el servicio de blobs devuelve un resultado de estado "Conflicto HTTP (409)". El fragmento de código siguiente utiliza un objeto **AccessCondition** para encapsulados la información de concesión cuando realiza una solicitud para actualizar el blob en el servicio de almacenamiento.  Puede descargar aquí el ejemplo completo: [Administración de simultaneidad con el almacenamiento de Azure](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

    // Acquire lease for 15 seconds
    string lease = blockBlob.AcquireLease(TimeSpan.FromSeconds(15), null);
    Console.WriteLine("Blob lease acquired. Lease = {0}", lease);

    // Update blob using lease. This operation will succeed
    const string helloText = "Blob updated";
    var accessCondition = AccessCondition.GenerateLeaseCondition(lease);
    blockBlob.UploadText(helloText, accessCondition: accessCondition);
    Console.WriteLine("Blob updated using an exclusive lease");

    //Simulate third party update to blob without lease
    try
    {
        // Below operation will fail as no valid lease provided
        Console.WriteLine("Trying to update blob without valid lease");
        blockBlob.UploadText("Update without lease, will fail");
    }
    catch (StorageException ex)
    {
        if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
            Console.WriteLine("Precondition failure as expected. Blob's lease does not match");
        else
            throw;
    }  

Si trata de una operación de escritura en un blob concedida sin pasar el identificador de concesión, la solicitud produce un error 412. Tenga en cuenta que si venza antes de llamar al método **UploadText** pero aún pasa el identificador de concesión, la solicitud también produce un error **412** . Para obtener más información acerca de cómo administrar los tiempos de expiración de concesión e identificadores de concesión, consulte la documentación del resto de [Blobs de concesión](http://msdn.microsoft.com/library/azure/ee691972.aspx) .  

Las siguientes operaciones de blob pueden utilizar permisos para administrar la simultaneidad pesimista:  


-   Poner Blob
-   Obtener Blob
-   Obtener propiedades de blobs
-   Establecer propiedades de blobs
-   Obtener los metadatos de blobs
-   Establecer los metadatos de blobs
-   Eliminar Blob
-   Colocar el bloque
-   Coloque la lista de bloques
-   Obtener la lista de bloques
-   Insertar página
-   Obtener rangos de página
-   Instantánea Blob - ID de concesión opcional si existe una concesión
-   Copiar Blob - concesión ID requerido si existe una concesión de blobs de destino
-   ID de concesión de anulación Copiar Blob - requerido si existe una concesión de infinita en el blob de destino
-   Concesión Blob  

### <a name="pessimistic-concurrency-for-containers"></a>Simultaneidad pesimista de contenedores
Arrendamientos en contenedores habilitar la misma estrategias de sincronización que sea compatible con como en BLOB (exclusivo escribir / compartidas de escritura exclusivo lectura / exclusiva lee y compartido escritura / lectura exclusivo) sin embargo a diferencia de BLOB el servicio de almacenamiento impone solo exclusividad en las operaciones de eliminación. Para eliminar un contenedor con una concesión activa, un cliente debe incluir el identificador de concesión activa con la solicitud de eliminación. Todas las demás operaciones de contenedor correctamente en un contenedor concedido sin incluir el identificador de concesión en cuyo caso son compartidos operaciones. Si se requiere la exclusividad de actualización (superponer o conjunto) o las operaciones de lectura, a continuación, los desarrolladores deben asegurarse de todos los clientes utilizan un identificador de concesión y que solo un cliente en un momento tiene un identificador de concesión válida.  

Las siguientes operaciones de contenedor pueden utilizar permisos para administrar la simultaneidad pesimista:  

-   Eliminar el contenedor
-   Obtener las propiedades de contenedor
-   Obtener los metadatos de contenedor
-   Establecer los metadatos de contenedor
-   Obtener contenedor ACL
-   Establecer ACL del contenedor
-   Contenedor de concesión  

Para obtener más información, vea:  

- [Especificar encabezados condicional para operaciones de servicio de blobs](http://msdn.microsoft.com/library/azure/dd179371.aspx)
- [Contenedor de concesión](http://msdn.microsoft.com/library/azure/jj159103.aspx)
- [Concesión Blob](http://msdn.microsoft.com/library/azure/ee691972.aspx)

## <a name="managing-concurrency-in-the-table-service"></a>Administrar la simultaneidad en el servicio de tabla
El servicio de tabla usa optimista de simultaneidad comprueba como el comportamiento predeterminado cuando se trabaja con entidades, a diferencia del servicio de blobs de Windows donde debe elegir explícitamente para realizar comprobaciones de simultaneidad optimista. La otra diferencia entre los servicios de tabla y blob es que solo puede administrar el comportamiento de simultaneidad de entidades que puede administrar la simultaneidad de contenedores y BLOB con el servicio de blobs de Windows.  

Usar simultaneidad optimista y comprobar si otro proceso modificado una entidad desde la que ha recuperado del servicio de almacenamiento de la tabla, puede usar el valor de ETag que recibir cuando el servicio de tabla devuelve una entidad. El esquema de este proceso es la siguiente:  

1.  Recuperar una entidad del servicio de almacenamiento de la tabla, la respuesta incluye un valor de ETag que identifica el identificador actual asociado a esa entidad en el servicio de almacenamiento.
2.  Cuando actualiza la entidad, incluya el valor de ETag que ha recibido en el paso 1 en el encabezado de **Coincidencia si** obligatorio de la solicitud de que enviar al servicio.
3.  El servicio compara el valor de ETag en la solicitud con el valor de ETag actual de la entidad.
4.  Si el valor de ETag actual de la entidad es diferente de ETag en el encabezado de **Coincidencia si** obligatorio en la solicitud, el servicio devuelve un error 412 al cliente. Esto indica al cliente que otro proceso ha actualizado la entidad desde el cliente de leerlo.
5.  Si el valor de ETag actual de la entidad es el mismo que el ETag en el encabezado de **Coincidencia si** obligatorio en la convocatoria o el encabezado de **Coincidencia si** contiene el carácter comodín (*), el servicio realiza la operación y actualiza el valor de ETag actual de la entidad para mostrar que se ha actualizado.  

Observe que, a diferencia del servicio de blobs de Windows, el servicio de tabla requiere el cliente para incluir un encabezado **Si coincidir** en solicitudes de actualización. Sin embargo, es posible forzar una incondicional actualizar (último escritor estrategia) y omitir las comprobaciones de simultaneidad si el cliente establece el encabezado de **Coincidencia si** el carácter comodín (*) en la solicitud.  

El siguiente fragmento de C# muestra una entidad de cliente que anteriormente fue creada o recuperen tener su dirección de correo electrónico actualizado. La inicial insertar o recuperar operación almacena el valor de ETag en el objeto de cliente y porque el ejemplo utiliza la misma instancia del objeto cuando se ejecuta la operación de reemplazo, envía automáticamente el valor ETag al servicio de la tabla, habilitar el servicio comprobar si las transgresiones de simultaneidad. Si otro proceso ha actualizado la entidad en el almacenamiento de tablas, el servicio devuelve un mensaje de estado 412 de HTTP (error de condición previa).  Puede descargar aquí el ejemplo completo: [Administración de simultaneidad con el almacenamiento de Azure](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

    try
    {
        customer.Email = "updatedEmail@contoso.org";
        TableOperation replaceCustomer = TableOperation.Replace(customer);
        customerTable.Execute(replaceCustomer);
        Console.WriteLine("Replace operation succeeded.");
    }
    catch (StorageException ex)
    {
        if (ex.RequestInformation.HttpStatusCode == 412)
            Console.WriteLine("Optimistic concurrency violation – entity has changed since it was retrieved.");
        else
            throw;
    }  

Para deshabilitar explícitamente la comprobación de simultaneidad, debe establecer la propiedad **ETag** del objeto **empleado** para "*" antes de ejecutar la operación de reemplazo.  

    customer.ETag = "*";  

La siguiente tabla resume cómo las operaciones de la entidad de tabla usar valores ETag:

| Operación                | Devuelve el valor ETag | Requiere el encabezado de la solicitud de coincidencia If |
|:-------------------------|:-------------------|:---------------------------------|
| Entidades de consulta           | Sí                | No                               |
| Insertar la entidad            | Sí                | No                               |
| Actualizar entidad            | Sí                | Sí                              |
| Combinar la entidad             | Sí                | Sí                              |
| Eliminar la entidad            | No                 | Sí                              |
| Insertar o reemplazar la entidad | Sí                | No                               |
| Insertar o entidad de combinación   | Sí                | No                               |

Tenga en cuenta que las operaciones de **Insertar o reemplazar entidad** **Insertar o entidad combinar** y hacer *no* realiza ninguna comprobación de simultaneidad porque no enviar un valor de ETag al servicio de tabla.  

En general, los desarrolladores mediante tablas deben confiar en simultaneidad optimista al desarrollar aplicaciones scalable. Si el bloqueo pesimista es necesaria, pueden tomar los desarrolladores de un enfoque cuando tenga acceso a tablas a asignar un blob designado para cada tabla e intenta tomar una concesión en el blob antes de la tabla. Este enfoque requiere la aplicación para garantizar que todas las rutas de acceso de datos obtengan la concesión antes de la tabla. También debe tener en cuenta que el tiempo de concesión mínimo es de 15 segundos que requiere minucioso escalabilidad.  

Para obtener más información, vea:  

- [Operaciones de entidades](http://msdn.microsoft.com/library/azure/dd179375.aspx)  

## <a name="managing-concurrency-in-the-queue-service"></a>Administrar la simultaneidad en el servicio de cola
Un escenario que en qué simultaneidad es un problema en el servicio de cola es donde varios clientes están recuperando los mensajes de una cola. Cuando se recupera un mensaje de la cola, la respuesta incluye el mensaje y un valor de confirmación de pop, que es necesario para eliminar el mensaje. El mensaje no se eliminan automáticamente de la cola, pero después de que se ha recuperado, no es visible para los demás clientes para el intervalo de tiempo especificado por el parámetro visibilitytimeout. El cliente que recupera el mensaje que se espera para eliminar el mensaje después de que se ha procesado y antes de la hora especificada por el TimeNextVisible elemento de la respuesta, que se calcula según el valor del parámetro visibilitytimeout. El valor de visibilitytimeout se agrega a la hora en que se recupera el mensaje para determinar el valor de TimeNextVisible.  

El servicio de cola no tiene compatibilidad para simultaneidad optimista o pesimista y para este clientes motivo mensajes recuperados de una cola de procesamiento deben Asegúrese de que se procesan los mensajes de manera idempotente. Una estrategia de escritor última se utiliza para operaciones de actualización, como SetQueueServiceProperties, SetQueueMetaData, SetQueueACL y UpdateMessage.  

Para obtener más información, vea:  

- [API de REST de servicio de cola](http://msdn.microsoft.com/library/azure/dd179363.aspx)
- [Obtener mensajes](http://msdn.microsoft.com/library/azure/dd179474.aspx)  

## <a name="managing-concurrency-in-the-file-service"></a>Administrar la simultaneidad en el servicio de archivos
Puede tener acceso al servicio de archivo con dos puntos finales de protocolo diferente: SMB y el resto. El servicio REST no dispone de compatibilidad para bloqueo optimista o bloqueo pesimista y todas las actualizaciones siguen una estrategia de escritor última. Los clientes SMB que montaje recursos compartidos de archivos pueden aprovechar mecanismos de bloqueo de sistema de archivos para administrar el acceso a archivos compartidos, como la capacidad para realizar el bloqueo pesimista. Cuando un cliente SMB abre un archivo, especifica el acceso a los archivos y el recurso compartido de modo. Establecer una opción de acceso a los archivos de "Escribir" o "De sólo lectura" junto con un modo de uso compartido de archivos de "Ninguno" se producirá en el archivo está bloqueado por un cliente SMB hasta que se cierra el archivo. Si se intenta la operación de resto en un archivo en un cliente SMB tiene el archivo bloqueado el servicio REST devolverá el código de estado 409 (conflicto) con código de error SharingViolation.  

Cuando un cliente SMB abre un archivo para eliminar, marca el archivo como eliminación hasta que todos los demás cliente SMB pendiente están cerrados identificadores abiertos en el archivo. Mientras un archivo está marcado como eliminación pendiente, cualquier operación de resto en ese archivo devolverá el código de estado 409 (conflicto) con código de error SMBDeletePending. No se devuelve el código de estado 404 (no encontrado) ya que es posible que el cliente SMB quitar la marca de eliminación pendiente antes de cerrar el archivo. En otras palabras, el código de estado 404 (no encontrado) solo se espera cuando se ha quitado el archivo. Tenga en cuenta que un archivo mientras está en un SMB Eliminar estado pendiente, no se incluirán en los resultados de la lista de archivos. Tenga en cuenta que las operaciones resto eliminar archivo y eliminar el resto directorio atómica están asignadas y no se producen en el estado de eliminación pendiente.  

Para obtener más información, vea:  

- [Administrar el archivo se bloquea](http://msdn.microsoft.com/library/azure/dn194265.aspx)  

## <a name="summary-and-next-steps"></a>Resumen y pasos siguientes
El servicio de Microsoft Azure almacenamiento se ha diseñado para satisfacer las necesidades de las aplicaciones más complejas en línea sin obligar a los desarrolladores peligro o pensar suposiciones de diseño clave como simultaneidad y coherencia de datos que proceden que atienda concedidos.  

Para la aplicación de ejemplo completo que se hace referencia en este blog:  

- [Administrar la simultaneidad con el almacenamiento de Azure: aplicación de ejemplo](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114)  

Para obtener más información sobre, consulte el almacenamiento de Azure:  

- [Página principal de Microsoft Azure almacenamiento](https://azure.microsoft.com/services/storage/)
- [Introducción al almacenamiento de Azure](storage-introduction.md)
- Introducción para [blobs](storage-dotnet-how-to-use-blobs.md), [tabla](storage-dotnet-how-to-use-tables.md), [colas](storage-dotnet-how-to-use-queues.md)y [archivos](storage-dotnet-how-to-use-files.md) de almacenamiento
- Arquitectura de almacenamiento: [almacenamiento de Azure: un servicio de almacenamiento de la nube altamente disponible con fuerte coherencia](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
