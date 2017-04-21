<properties
    pageTitle="Cómo usar el almacenamiento de blobs de Node.js | Microsoft Azure"
    description="Almacenar datos estructurados en la nube con el almacenamiento de blobs de Windows Azure (almacenamiento de objetos)."
    services="storage"
    documentationCenter="nodejs"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="tamram"/>



# <a name="how-to-use-blob-storage-from-nodejs"></a>Cómo usar el almacenamiento de blobs de Node.js

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Información general

En este artículo se muestra cómo llevar a cabo con el almacenamiento de blobs de escenarios comunes. Los ejemplos se escriben mediante la API de Node.js. Los escenarios cubiertos incluyen cómo cargar, lista, descargar y eliminar BLOB.

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Crear una aplicación de Node.js

Para obtener instrucciones sobre cómo crear una aplicación de Node.js, consulte [crear una aplicación web de Node.js en la aplicación de servicio de Azure], [Compile e implemente una aplicación de Node.js a un servicio de nube de Azure] --con Windows PowerShell o [Compile e implemente una aplicación web de Node.js a Azure con Web Matrix].

## <a name="configure-your-application-to-access-storage"></a>Configurar la aplicación para tener acceso al almacenamiento

Para usar el almacenamiento de Azure, necesita el SDK de almacenamiento de Azure para Node.js, que incluye un conjunto de bibliotecas de comodidad que comunicarse con los servicios del resto de almacenamiento.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Utilice el nodo paquete Manager (NPM) para obtener el paquete

1.  Utilizar una interfaz de línea de comandos como **PowerShell** (Windows), **Terminal** (Mac) o **Bash** (Unix), para ir a la carpeta donde se crea la aplicación de ejemplo.

2.  En la ventana de comandos, escriba **npm instalar almacenamiento de azure** . Resultado del comando es similar a en el siguiente ejemplo.

        azure-storage@0.5.0 node_modules\azure-storage
        +-- extend@1.2.1
        +-- xmlbuilder@0.4.3
        +-- mime@1.2.11
        +-- node-uuid@1.4.3
        +-- validator@3.22.2
        +-- underscore@1.4.4
        +-- readable-stream@1.0.33 (string_decoder@0.10.31, isarray@0.0.1, inherits@2.0.1, core-util-is@1.0.1)
        +-- xml2js@0.2.7 (sax@0.5.2)
        +-- request@2.57.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, oauth-sign@0.8.0, tunnel-agent@0.4.1, isstream@0.1.2, json-stringify-safe@5.0.1, bl@0.9.4, combined-stream@1.0.5, qs@3.1.0, mime-types@2.0.14, form-data@0.2.0, http-signature@0.11.0, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)

3.  El comando **ls** para comprobar que puede ejecutar manualmente una **nodo\_módulos** se creó la carpeta. Dentro de esa carpeta, busque el paquete de **almacenamiento de azure** , que contiene las bibliotecas que necesita tener acceso al almacenamiento.

### <a name="import-the-package"></a>Importar el paquete

Con el Bloc de notas u otro editor de texto, agregue lo siguiente a la parte superior del archivo **server.js** de la aplicación donde vaya a usar el almacenamiento:

    var azure = require('azure-storage');

## <a name="set-up-an-azure-storage-connection"></a>Configurar una conexión de almacenamiento de Azure

El módulo Azure leerá las variables de entorno `AZURE_STORAGE_ACCOUNT` y `AZURE_STORAGE_ACCESS_KEY`, o `AZURE_STORAGE_CONNECTION_STRING`, información necesaria para conectarse a su cuenta de almacenamiento de Azure. Si no se establecen estas variables de entorno, debe especificar la información de cuenta al llamar a **createBlobService**.

Para obtener un ejemplo de configuración de las variables de entorno en el [Portal de Azure](https://portal.azure.com) para una aplicación web de Azure, consulte [Node.js web app con el servicio de la tabla de Azure].

## <a name="create-a-container"></a>Crear un contenedor

El objeto **BlobService** le permite trabajar con contenedores y BLOB. El código siguiente crea un objeto **BlobService** . Agregue lo siguiente en la parte superior de **server.js**:

    var blobSvc = azure.createBlobService();

> [AZURE.NOTE] Puede acceder a un blob anónimamente utilizando **createBlobServiceAnonymous** y proporcionando la dirección de host. Por ejemplo, utilice `var blobSvc = azure.createBlobServiceAnonymous('https://myblob.blob.core.windows.net/');`.

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

Para crear un nuevo contenedor, use **createContainerIfNotExists**. En el ejemplo siguiente se crea un nuevo contenedor denominado 'mycontainer':

    blobSvc.createContainerIfNotExists('mycontainer', function(error, result, response){
        if(!error){
          // Container exists and is private
        }
    });

Si el contenedor recién creado, `result.created` es true. Si ya existe el contenedor, `result.created` es false. `response`contiene información sobre la operación, incluida la información de ETag para el contenedor.

### <a name="container-security"></a>Seguridad de contenedor

De forma predeterminada, los nuevos contenedores son privados y no se puede acceder de forma anónima. Para hacer que el contenedor público para que los puede acceder de forma anónima, puede establecer el nivel de acceso del contenedor **blob** o **contenedor**.

* **blob** - permite el acceso de lectura anónimo blob contenido y metadatos dentro de este contenedor, pero no a los metadatos de contenedor como enumerar todos los blobs dentro de un contenedor

* **contenedor** : permite el acceso de lectura anónimo blob contenido y metadatos, así como los metadatos de contenedor

En el ejemplo siguiente se muestra cómo establecer el nivel de acceso a **blob**:

    blobSvc.createContainerIfNotExists('mycontainer', {publicAccessLevel : 'blob'}, function(error, result, response){
        if(!error){
          // Container exists and allows
          // anonymous read access to blob
          // content and metadata within this container
        }
    });

Como alternativa, puede modificar el nivel de acceso de un contenedor mediante **setContainerAcl** para especificar el nivel de acceso. En el ejemplo siguiente se cambia el nivel de acceso al contenedor:

    blobSvc.setContainerAcl('mycontainer', null /* signedIdentifiers */, {publicAccessLevel : 'container'} /* publicAccessLevel*/, function(error, result, response){
      if(!error){
        // Container access level set to 'container'
      }
    });

El resultado contiene información sobre la operación, incluida la actual **ETag** para el contenedor.

### <a name="filters"></a>Filtros

Puede aplicar las operaciones de filtrado opcionales a operaciones realizadas mediante **BlobService**. Operaciones de filtrado pueden incluir registro, Reintentar automáticamente, etcetera. Filtros son objetos que implementa un método con la firma:

    function handle (requestOptions, next)

Después de realizar su preprocesamiento en las opciones de la solicitud, el método necesita llamar "siguiente", pasando una devolución de llamada con la firma siguiente:

    function (returnObject, finalCallback, next)

En esta devolución de llamada y después de procesar lo returnObject (la respuesta de la solicitud en el servidor), la devolución de llamada debe invocar siguiente si existe para continuar procesando otros filtros o simplemente invocarla finalCallback para finalizar la llamada de servicio.

Dos filtros que implementan la lógica de reintento se incluyen en el SDK de Azure para Node.js, **ExponentialRetryPolicyFilter** y **LinearRetryPolicyFilter**. La siguiente crea un objeto **BlobService** que usa el **ExponentialRetryPolicyFilter**:

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var blobSvc = azure.createBlobService().withFilter(retryOperations);

## <a name="upload-a-blob-into-a-container"></a>Cargar un blob en un contenedor

Existen tres tipos de BLOB: bloquear BLOB, página BLOB y anexar BLOB. Bloque BLOB permite que más eficazmente cargar grandes cantidades de datos. Anexar BLOB está optimizado para operaciones de datos anexados. BLOB de página está optimizado para operaciones de lectura y escritura. Para obtener más información, vea [Descripción bloque BLOB, anexar Blobs y Blobs de página](http://msdn.microsoft.com/library/azure/ee691964.aspx).

### <a name="block-blobs"></a>Bloque BLOB

Para cargar los datos a un blob bloque, use la siguiente:

* **createBlockBlobFromLocalFile** - crea un nuevo blob bloque y carga el contenido de un archivo

* **createBlockBlobFromStream** - crea un nuevo blob bloque y carga el contenido de una secuencia

* **createBlockBlobFromText** - crea un nuevo blob bloque y carga el contenido de una cadena

* **createWriteStreamToBlockBlob** - proporciona una secuencia de escritura a un blob de bloque

En el ejemplo siguiente se carga el contenido del archivo **test.txt** en **myblob**.

    blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', function(error, result, response){
      if(!error){
        // file uploaded
      }
    });

La `result` devuelto por estos métodos contiene información sobre la operación, como el **ETag** del blob.

### <a name="append-blobs"></a>Anexar BLOB

Para cargar los datos a un nuevo blob de datos anexados, use la siguiente:

* **createAppendBlobFromLocalFile** - crea un nuevo blob de datos anexados y carga el contenido de un archivo

* **createAppendBlobFromStream** - crea un nuevo blob de datos anexados y carga el contenido de una secuencia

* **createAppendBlobFromText** - crea un nuevo blob de datos anexados y carga el contenido de una cadena

* **createWriteStreamToNewAppendBlob** - crea un nuevo blob de datos anexados y, a continuación, proporciona una secuencia para escribir en él

En el ejemplo siguiente se carga el contenido del archivo **test.txt** en **myappendblob**.

    blobSvc.createAppendBlobFromLocalFile('mycontainer', 'myappendblob', 'test.txt', function(error, result, response){
      if(!error){
        // file uploaded
      }
    });

Para agregar un bloque a un blob de datos anexados existente, utilice la siguiente:

* **appendFromLocalFile** - anexar el contenido de un archivo a un blob existente de datos anexados

* **appendFromStream** - anexar el contenido de una secuencia a un blob existente de datos anexados

* **appendFromText** - anexar el contenido de una cadena a un blob existente de datos anexados

* **appendBlockFromStream** - anexar el contenido de una secuencia a un blob existente de datos anexados

* **appendBlockFromText** - anexar el contenido de una cadena a un blob existente de datos anexados

> [AZURE.NOTE] appendFromXXX API hará alguna validación del cliente para un error rápidamente evitar la llamada de servidor unncessary. no appendBlockFromXXX.

En el ejemplo siguiente se carga el contenido del archivo **test.txt** en **myappendblob**.

    blobSvc.appendFromText('mycontainer', 'myappendblob', 'text to be appended', function(error, result, response){
      if(!error){
        // text appended
      }
    });


### <a name="page-blobs"></a>BLOB de página

Para cargar los datos a un blob de página, use la siguiente:

* **createPageBlob** - crea un nuevo blob de página de una longitud específica

* **createPageBlobFromLocalFile** - crea un nuevo blobs de página y carga el contenido de un archivo

* **createPageBlobFromStream** - crea un nuevo blob de página y el contenido de una secuencia de las cargas

* **createWriteStreamToExistingPageBlob** - proporciona una secuencia de escritura a un blob de página existente

* **createWriteStreamToNewPageBlob** - crea un nuevo blob de página y, a continuación, proporciona una secuencia para escribir en él

En el ejemplo siguiente se carga el contenido del archivo **test.txt** en **mypageblob**.

    blobSvc.createPageBlobFromLocalFile('mycontainer', 'mypageblob', 'test.txt', function(error, result, response){
      if(!error){
        // file uploaded
      }
    });

> [AZURE.NOTE] Página BLOB consta de 512 bytes 'páginas'. Recibirá un error al cargar los datos con un tamaño que no es un múltiplo de 512.

## <a name="list-the-blobs-in-a-container"></a>El BLOB en un contenedor de lista

Para mostrar los BLOB en un contenedor, utilice el método **listBlobsSegmented** . Si desea devolver BLOB con un prefijo concreto, use **listBlobsSegmentedWithPrefix**.

    blobSvc.listBlobsSegmented('mycontainer', null, function(error, result, response){
      if(!error){
          // result.entries contains the entries
          // If not all blobs were returned, result.continuationToken has the continuation token.
      }
    });

La `result` contiene un `entries` colección, que es una matriz de objetos que describen cada blob. Si no se puede devolver todos los blobs, el `result` también proporciona una `continuationToken`, que puede usar como el segundo parámetro para recuperar las entradas adicionales.

## <a name="download-blobs"></a>Descargar BLOB

Para descargar datos desde un blob, use la siguiente:

* **getBlobToLocalFile** - escribe el contenido de blob en el archivo

* **getBlobToStream** - escribe el contenido de blob en una secuencia

* **getBlobToText** - escribe el contenido de blob en una cadena

* **createReadStream** - proporciona una secuencia para leer el blob

En el ejemplo siguiente se muestra cómo utilizar **getBlobToStream** para descargar el contenido de blobs de **myblob** y almacenar el archivo **output.txt** mediante una secuencia:

    var fs = require('fs');
    blobSvc.getBlobToStream('mycontainer', 'myblob', fs.createWriteStream('output.txt'), function(error, result, response){
      if(!error){
        // blob retrieved
      }
    });

La `result` contiene información sobre el blob, incluida la información de **ETag** .

## <a name="delete-a-blob"></a>Eliminar un blob

Por último, para eliminar un objeto binario, llame a **deleteBlob**. En el ejemplo siguiente se elimina el blob llamado **myblob**.

    blobSvc.deleteBlob(containerName, 'myblob', function(error, response){
      if(!error){
        // Blob has been deleted
      }
    });

## <a name="concurrent-access"></a>Access simultáneas

Para permitir el acceso simultáneo a un blob desde varios clientes o varias instancias de proceso, puede usar **ETags** o **arrendamientos**.

* **Etag** - proporciona una manera de detectar el blob o contenedor se ha modificado por otro proceso

* **Concesión** - proporciona una manera de obtener exclusivo, renovables, escribir o eliminar el acceso a un blob durante un período de tiempo

### <a name="etag"></a>ETag

Usar ETags si desea permitir que varios clientes o instancias escribir en el bloque de Blob o página Blob simultáneamente. ETag le permite determinar si se ha modificado el contenedor o blob desde que inicialmente lee o creado, lo que permite evitar sobrescribir cambios confirmadas por otro proceso o el cliente.

Puede establecer condiciones de ETag mediante la opcional `options.accessConditions` parámetro. En el ejemplo siguiente se carga solo el archivo **test.txt** si ya existe el blob y tiene el valor ETag contenidas por `etagToMatch`.

    blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', { accessConditions: { EtagMatch: etagToMatch} }, function(error, result, response){
        if(!error){
        // file uploaded
      }
    });

Cuando usa ETags, el patrón general es:

1. Obtener el valor ETag como resultado de una operación de obtener, lista o crear.

2. Realizar una acción, comprobar que no se ha modificado el valor ETag.

Si se ha modificado el valor, esto indica que otro cliente o instancia modificado el contenedor o blob desde había obtenido el valor ETag.

### <a name="lease"></a>Concesión

Puede adquirir una nueva concesión con el método **acquireLease** , que especifica el blob o el contenedor que desea obtener una concesión en. Por ejemplo, el siguiente código adquiere una concesión en **myblob**.

    blobSvc.acquireLease('mycontainer', 'myblob', function(error, result, response){
      if(!error) {
        console.log('leaseId: ' + result.id);
      }
    });

Las siguientes operaciones en **myblob** deben proporcionar el `options.leaseId` parámetro. La concesión ID se devuelve como `result.id` desde **acquireLease**.

> [AZURE.NOTE] De forma predeterminada, la duración de la concesión es infinita. Puede especificar una duración no infinito (entre 15 y 60 segundos) proporcionando la `options.leaseDuration` parámetro.

Para quitar una concesión, use **releaseLease**. Para dividir una concesión, pero impedir que otros usuarios Obtenga una nueva concesión hasta que ha caducado la duración original, utilice **breakLease**.

## <a name="work-with-shared-access-signatures"></a>Trabajar con firmas de acceso compartido

Acceso compartido firmas (SA) son una forma segura para proporcionar acceso granular BLOB y contenedores sin proporcionar el nombre de la cuenta de almacenamiento o teclas. Firmas de acceso compartido a menudo se usan para proporcionar acceso limitado a los datos, como permitir que una aplicación móvil tener acceso a BLOB.

> [AZURE.NOTE] Mientras también puede permitir el acceso anónimo a BLOB, acceso compartido firmas le permiten proporcionar acceso más controlado, como debe generar las asociaciones de seguridad.

Una aplicación de confianza, como un servicio basado en la nube genera firmas de acceso compartido mediante la **generateSharedAccessSignature** de la **BlobService**y proporciona a una aplicación de confianza o de confianza parcial, como una aplicación móvil. Acceso firmas se generan mediante una directiva, que describe el inicio y finalización durante el cual las firmas de acceso compartido son válidas compartido, así como el nivel de acceso concedido al titular de firmas acceso compartido.

En el ejemplo siguiente se genera una nueva directiva de acceso compartido que permite realizar operaciones de lectura en el blob **myblob** al propietario de firmas acceso compartido y caduca 100 minutos después de la hora en que se crea.

    var startDate = new Date();
    var expiryDate = new Date(startDate);
    expiryDate.setMinutes(startDate.getMinutes() + 100);
    startDate.setMinutes(startDate.getMinutes() - 100);

    var sharedAccessPolicy = {
      AccessPolicy: {
        Permissions: azure.BlobUtilities.SharedAccessPermissions.READ,
        Start: startDate,
        Expiry: expiryDate
      },
    };

    var blobSAS = blobSvc.generateSharedAccessSignature('mycontainer', 'myblob', sharedAccessPolicy);
    var host = blobSvc.host;

Tenga en cuenta que la información del host se debe proporcionar también, ya que es necesario cuando el titular de firmas acceso compartido intenta obtener acceso al contenedor.

A continuación, la aplicación cliente usa firmas acceso compartido con **BlobServiceWithSAS** para realizar operaciones de blob. La siguiente obtiene información sobre **myblob**.

    var sharedBlobSvc = azure.createBlobServiceWithSas(host, blobSAS);
    sharedBlobSvc.getBlobProperties('mycontainer', 'myblob', function (error, result, response) {
      if(!error) {
        // retrieved info
      }
    });

Dado que las firmas de acceso compartido se genera con acceso de solo lectura, si intenta modificar el blob, se devolverá un error.

### <a name="access-control-lists"></a>Listas de control de acceso

También puede usar una lista de control de acceso (ACL) para establecer la directiva de acceso para asociaciones de seguridad. Esto es útil si desea permitir que varios clientes tener acceso a un contenedor pero proporciona directivas de acceso diferente para cada cliente.

Una ACL se implementa mediante una matriz de directivas de acceso, con un identificador asociados con cada directiva. En el ejemplo siguiente se define dos directivas, uno para 'Usuario1' y otro para 'usuario2':

    var sharedAccessPolicy = {
      user1: {
        Permissions: azure.BlobUtilities.SharedAccessPermissions.READ,
        Start: startDate,
        Expiry: expiryDate
      },
      user2: {
        Permissions: azure.BlobUtilities.SharedAccessPermissions.WRITE,
        Start: startDate,
        Expiry: expiryDate
      }
    };

En el ejemplo siguiente se obtiene las ACL de **mycontainer**y, a continuación, agrega las nuevas directivas mediante **setBlobAcl**. Este enfoque permite:

    var extend = require('extend');
    blobSvc.getBlobAcl('mycontainer', function(error, result, response) {
      if(!error){
        var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
        blobSvc.setBlobAcl('mycontainer', newSignedIdentifiers, function(error, result, response){
          if(!error){
            // ACL set
          }
        });
      }
    });

Cuando se establece la ACL, a continuación, puede crear firmas de acceso compartido basadas en el identificador de una directiva. En el ejemplo siguiente se crea nuevas firmas de acceso compartido para 'usuario2':

    blobSAS = blobSvc.generateSharedAccessSignature('mycontainer', { Id: 'user2' });

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información, consulte los siguientes recursos.

-   [Almacenamiento de Azure SDK de referencia de la API de nodo][]
-   [Blog del equipo de almacenamiento de Azure][]
-   Repositorio de [Azure almacenamiento SDK para nodo][] de GitHub
-   [Centro para desarrolladores de Node.js](/develop/nodejs/)
-   [Transferir datos con la utilidad de línea de comandos de AzCopy](storage-use-azcopy.md)

[Almacenamiento de Azure SDK de nodo]: https://github.com/Azure/azure-storage-node

[Crear una aplicación web de Node.js en la aplicación de servicio de Azure]: ../app-service-web/web-sites-nodejs-develop-deploy-mac.md
[Node.js Cloud Service with Storage]: ../cloud-services/storage-nodejs-use-table-storage-cloud-service-app.md
[Node.js web app con el servicio de la tabla de Azure]: ../app-service-web/storage-nodejs-use-table-storage-web-site.md
[Compile e implemente una aplicación web de Node.js Azure con Web Matrix]: ../app-service-web/web-sites-nodejs-use-webmatrix.md
[Using the REST API]: http://msdn.microsoft.com/library/azure/hh264518.aspx
[Azure Portal]: https://portal.azure.com
[Compile e implemente una aplicación de Node.js a un servicio de nube de Azure]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Blog del equipo de almacenamiento de Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[Almacenamiento de Azure SDK de referencia de la API de nodo]: http://dl.windowsazure.com/nodestoragedocs/index.html
