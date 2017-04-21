<properties
    pageTitle="Cómo usar el almacenamiento de tablas de Azure desde Node.js | Microsoft Azure"
    description="Almacenar datos estructurados en la nube mediante el almacenamiento de tablas de Azure, un almacén datos."
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


# <a name="how-to-use-azure-table-storage-from-nodejs"></a>Cómo usar el almacenamiento de tablas de Azure desde Node.js

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Información general

Este tema muestra cómo llevar a cabo escenarios comunes mediante el servicio de la tabla de Azure en una aplicación de Node.js.

Los ejemplos de código de este tema se supone que ya tiene una aplicación de Node.js. Para obtener información sobre cómo crear una aplicación de Node.js en Azure, consulte cualquiera de estos temas:

- [Crear una aplicación web de Node.js en la aplicación de servicio de Azure](../app-service-web/web-sites-nodejs-develop-deploy-mac.md)
- [Compile e implemente una aplicación web de Node.js Azure con WebMatrix](../app-service-web/web-sites-nodejs-use-webmatrix.md)
- [Compile e implemente una aplicación de Node.js a un servicio de nube de Azure](../cloud-services/cloud-services-nodejs-develop-deploy-app.md) (con Windows PowerShell)


[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]


## <a name="configure-your-application-to-access-azure-storage"></a>Configurar la aplicación para tener acceso al almacenamiento de Azure

Para usar el almacenamiento de Azure, necesita el SDK de almacenamiento de Azure para Node.js, que incluye un conjunto de bibliotecas de comodidad que comunicarse con los servicios del resto de almacenamiento.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Usar el Administrador de paquete de nodo (NPM) para instalar el paquete

1.  Usar una interfaz de línea de comandos como **PowerShell** (Windows), **Terminal** (Mac) o **Bash** (Unix) y vaya a la carpeta donde se crea la aplicación.

2.  En la ventana de comandos, escriba **npm instalar almacenamiento de azure** . Resultado del comando es similar al siguiente ejemplo.

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

3.  El comando **ls** para comprobar que puede ejecutar manualmente una **nodo\_módulos** se creó la carpeta. Dentro de esa carpeta, encontrará el paquete de **almacenamiento de azure** , que contiene las bibliotecas que debe tener acceso al almacenamiento.

### <a name="import-the-package"></a>Importar el paquete

Agregue el código siguiente a la parte superior del archivo **server.js** en la aplicación:

    var azure = require('azure-storage');

## <a name="set-up-an-azure-storage-connection"></a>Configurar una conexión de almacenamiento de Azure

El módulo azure leerá las variables de entorno AZURE\_almacenamiento\_cuenta y AZURE\_almacenamiento\_ACCESS\_clave o AZURE\_almacenamiento\_conexión\_cadena información necesaria para conectarse a su cuenta de almacenamiento de Azure. Si no se establecen estas variables de entorno, debe especificar la información de cuenta al llamar a **TableService**.

Para obtener un ejemplo de configuración de las variables de entorno en el [Portal de Azure](https://portal.azure.com) para un sitio Web de Azure, consulte [Node.js web app con el servicio de la tabla de Azure].

## <a name="create-a-table"></a>Crear una tabla

El código siguiente crea un objeto de **TableService** y usa para crear una nueva tabla. Agregue lo siguiente en la parte superior de **server.js**.

    var tableSvc = azure.createTableService();

La llamada a **createTableIfNotExists** creará una nueva tabla con el nombre especificado, si aún no existe. En el ejemplo siguiente se crea una nueva tabla denominada 'mytable' Si aún no existe:

    tableSvc.createTableIfNotExists('mytable', function(error, result, response){
      if(!error){
        // Table exists or created
      }
    });

La `result.created` será `true` si se crea una nueva tabla, y `false` si la tabla ya existe. La `response` contendrá información sobre la solicitud.

### <a name="filters"></a>Filtros

Las operaciones opcionales de filtrado se pueden aplicar a operaciones realizadas mediante **TableService**. Operaciones de filtrado pueden incluir registro, Reintentar automáticamente, etcetera. Filtros son objetos que implementa un método con la firma:

    function handle (requestOptions, next)

Después de realizar su preprocesamiento en las opciones de la solicitud, el método necesita llamar "siguiente", pasando una devolución de llamada con la firma siguiente:

    function (returnObject, finalCallback, next)

En esta devolución de llamada y después de procesar lo returnObject (la respuesta de la solicitud en el servidor), la devolución de llamada debe invocar siguiente si existe para continuar procesando otros filtros o simplemente invocarla finalCallback en caso contrario, para finalizar la llamada de servicio.

Dos filtros que implementan la lógica de reintento se incluyen en el SDK de Azure para Node.js, **ExponentialRetryPolicyFilter** y **LinearRetryPolicyFilter**. La siguiente crea un objeto **TableService** que usa el **ExponentialRetryPolicyFilter**:

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var tableSvc = azure.createTableService().withFilter(retryOperations);

## <a name="add-an-entity-to-a-table"></a>Agregar una entidad a una tabla

Para agregar una entidad, cree primero un objeto que define las propiedades de la entidad. Todas las entidades deben contener un **PartitionKey** y **RowKey**, que son identificadores únicos de la entidad.

* **PartitionKey** - determina la partición almacenado en la entidad

* **RowKey** - identifica la entidad dentro de la partición

**PartitionKey** y **RowKey** deben ser valores de cadena. Para obtener más información, vea la [Descripción del modelo de datos de servicio de tabla](http://msdn.microsoft.com/library/azure/dd179338.aspx).

El siguiente es un ejemplo de la definición de una entidad. Tenga en cuenta que **dueDate** se define como un tipo de **Edm.DateTime**. Especifica el tipo es opcional, y tipos se deduce si no se especifica.

    var task = {
      PartitionKey: {'_':'hometasks'},
      RowKey: {'_': '1'},
      description: {'_':'take out the trash'},
      dueDate: {'_':new Date(2015, 6, 20), '$':'Edm.DateTime'}
    };

> [AZURE.NOTE] También es un campo de **marca de tiempo** para cada registro, que está establecida de Azure cuando se inserta o actualiza una entidad.

También puede usar el **entityGenerator** para crear entidades. En el ejemplo siguiente se crea la misma entidad de tarea con la **entityGenerator**.

    var entGen = azure.TableUtilities.entityGenerator;
    var task = {
      PartitionKey: entGen.String('hometasks'),
      RowKey: entGen.String('1'),
      description: entGen.String('take out the trash'),
      dueDate: entGen.DateTime(new Date(Date.UTC(2015, 6, 20))),
    };

Para agregar una entidad a la tabla, pase el objeto de entidad al método **insertEntity** .

    tableSvc.insertEntity('mytable',task, function (error, result, response) {
      if(!error){
        // Entity inserted
      }
    });

Si la operación se realiza correctamente, `result` contendrá el [ETag](http://en.wikipedia.org/wiki/HTTP_ETag) del registro insertado y `response` contendrá información sobre la operación.

Respuesta de ejemplo:

    { '.metadata': { etag: 'W/"datetime\'2015-02-25T01%3A22%3A22.5Z\'"' } }

> [AZURE.NOTE] De forma predeterminada, **insertEntity** no devuelve la entidad insertada como parte de la `response` información. Si pretende realizar otras operaciones en esta entidad, o si desea almacenar la información en caché, puede ser útil para que se devuelve como parte de la `result`. Puede hacerlo activando **echoContent** como sigue:
>
> `tableSvc.insertEntity('mytable', task, {echoContent: true}, function (error, result, response) {...}`

## <a name="update-an-entity"></a>Actualizar una entidad

Hay varios métodos disponibles para actualizar una entidad existente:

* **replaceEntity** - actualiza una entidad existente reemplazándola

* **mergeEntity** - actualiza una entidad existente mediante la combinación de nuevos valores de propiedad de la entidad existente

* **insertOrReplaceEntity** - actualiza una entidad existente reemplazarlo. Si no existe ninguna entidad, se insertará una nueva

* **insertOrMergeEntity** - actualiza una entidad existente combinando los nuevos valores de propiedad en el existente. Si no existe ninguna entidad, se insertará una nueva

En el ejemplo siguiente se muestra cómo actualizar una entidad mediante **replaceEntity**:

    tableSvc.replaceEntity('mytable', updatedTask, function(error, result, response){
      if(!error) {
        // Entity updated
      }
    });

> [AZURE.NOTE] De forma predeterminada, la actualización de una entidad no compruebe si los datos se actualicen previamente se ha modificado por otro proceso. Para admitir actualizaciones simultáneas:
>
> 1. Obtener el ETag del objeto que está actualizando. Esto se devuelve como parte de la `response` para cualquier entidad relacionados con la operación y se pueden recuperar mediante `response['.metadata'].etag`.
>
> 2. Al realizar una operación de actualización en una entidad, agregue la información de ETag recuperada previamente a la nueva entidad. Por ejemplo:
>
>     `entity2['.metadata'].etag = currentEtag;`
>
> 3. Realice la operación de actualización. Si se ha modificado la entidad puesto que recupera el valor de ETag, como otra instancia de la aplicación, un `error` se devolverá que indica que no no se cumpla la condición de actualización especificada en la solicitud.

Con **replaceEntity** y **mergeEntity**, si no existe la entidad que está actualizando, la operación de actualización generará errores. Por lo tanto, si desea almacenar una entidad independientemente de si ya existe, use **insertOrReplaceEntity** o **insertOrMergeEntity**.

El `result` para actualización correcta operaciones contendrá **Etag** de la entidad actualizada.

## <a name="work-with-groups-of-entities"></a>Trabajar con grupos de entidades

A veces tiene sentido varias operaciones juntos en un lote para garantizar atómica procesamiento por el servidor de envío. Para ello, use la clase **TableBatch** para crear un lote y, a continuación, use el método **executeBatch** **TableService** para realizar las operaciones por lotes.

 El ejemplo siguiente muestra dos entidades en un lote de envío:

    var task1 = {
      PartitionKey: {'_':'hometasks'},
      RowKey: {'_': '1'},
      description: {'_':'Take out the trash'},
      dueDate: {'_':new Date(2015, 6, 20)}
    };
    var task2 = {
      PartitionKey: {'_':'hometasks'},
      RowKey: {'_': '2'},
      description: {'_':'Wash the dishes'},
      dueDate: {'_':new Date(2015, 6, 20)}
    };

    var batch = new azure.TableBatch();

    batch.insertEntity(task1, {echoContent: true});
    batch.insertEntity(task2, {echoContent: true});

    tableSvc.executeBatch('mytable', batch, function (error, result, response) {
      if(!error) {
        // Batch completed
      }
    });

Para operaciones de proceso con éxito, `result` contendrá información para cada operación del lote.

### <a name="work-with-batched-operations"></a>Trabajar con operaciones por lotes

Pueden inspeccionar las operaciones de agregado a un lote de la visualización de la `operations` propiedad. También puede usar los siguientes métodos para trabajar con operaciones:

* **Borrar** - borra todas las operaciones de un lote

* **getOperations** - Obtiene una operación del lote

* **hasOperations** - devuelve Verdadero si el lote contiene operaciones

* **removeOperations** - quita una operación

* **tamaño** : devuelve el número de operaciones en el lote

## <a name="retrieve-an-entity-by-key"></a>Recuperar una entidad por clave

Para obtener una entidad específica en función de la **PartitionKey** y **RowKey**, use el método **retrieveEntity** .

    tableSvc.retrieveEntity('mytable', 'hometasks', '1', function(error, result, response){
      if(!error){
        // result contains the entity
      }
    });

Una vez completada, esta operación `result` contendrá la entidad.

## <a name="query-a-set-of-entities"></a>Un conjunto de entidades de la consulta

Para consultar una tabla, utilice el objeto **TableQuery** para construir una expresión de consulta usando las siguientes cláusulas:

* **Seleccione** - los campos que se devolverá de la consulta

* **donde** - where cláusula

    * **y** - un `and` condición where

    * **o** bien - una `or` condición where

* **parte superior** : el número de elementos para capturar


En el ejemplo siguiente se crea una consulta que devuelve los elementos superiores cinco con un PartitionKey de 'hometasks'.

    var query = new azure.TableQuery()
      .top(5)
      .where('PartitionKey eq ?', 'hometasks');

Puesto que no se usa la **Seleccione** , se devolverá todos los campos. Para realizar la consulta en una tabla, use **queryEntities**. En el ejemplo siguiente se usa esta consulta para devolver entidades de 'mytable'.

    tableSvc.queryEntities('mytable',query, null, function(error, result, response) {
      if(!error) {
        // query was successful
      }
    });

Si se realiza correctamente, `result.entries` contendrá una matriz de entidades que coinciden con la consulta. Si la consulta no pudo devolver todas las entidades `result.continuationToken` será no es*null* y puede usarse como tercer parámetro de **queryEntities** para recuperar más resultados. Para la consulta inicial, utilice el tercer parámetro *null* .

### <a name="query-a-subset-of-entity-properties"></a>Consulta un subconjunto de propiedades de la entidad

Una consulta a una tabla puede recuperar unos cuantos campos de una entidad.
Esto reduce el ancho de banda y puede mejorar el rendimiento de la consulta, especialmente para entidades de gran tamaño. Utilice la cláusula **select** y pase los nombres de los campos que se devolverá. Por ejemplo, la siguiente consulta devolverá únicamente los campos de **Descripción** y **dueDate** .

    var query = new azure.TableQuery()
      .select(['description', 'dueDate'])
      .top(5)
      .where('PartitionKey eq ?', 'hometasks');

## <a name="delete-an-entity"></a>Eliminar una entidad

Puede eliminar una entidad mediante sus claves partición y fila. En este ejemplo, el objeto **Tarea1** contiene los valores de **RowKey** y **PartitionKey** de la entidad que desea eliminar. A continuación, el objeto se pasa al método **deleteEntity** .

    var task = {
      PartitionKey: {'_':'hometasks'},
      RowKey: {'_': '1'}
    };

    tableSvc.deleteEntity('mytable', task, function(error, response){
      if(!error) {
        // Entity deleted
      }
    });

> [AZURE.NOTE] Considere la posibilidad de usar ETags al eliminar elementos, para asegurarse de que no se ha modificado el elemento por otro proceso. Para obtener información sobre cómo usar ETags, consulte [actualizar una entidad](#update-an-entity) .

## <a name="delete-a-table"></a>Eliminar una tabla

El código siguiente elimina una tabla de una cuenta de almacenamiento.

    tableSvc.deleteTable('mytable', function(error, response){
        if(!error){
            // Table deleted
        }
    });

Si no está seguro de si existe la tabla, use **deleteTableIfExists**.

## <a name="use-continuation-tokens"></a>Usar tokens de continuación

Cuando se consulta tablas para grandes cantidades de resultados, busque tokens de continuación. Puede haber grandes cantidades de datos disponibles para la consulta que podrían darse cuenta si no genera reconocer cuando hay un token de continuación.

Los resultados del objeto devuelto durante consultar conjuntos de entidades un `continuationToken` propiedad cuando hay un símbolo de este tipo. A continuación, puede utilizarla al realizar una consulta para continuar para moverse por las entidades de partición y tabla.

Al consultar, puede proporcionar un parámetro continuationToken entre la instancia de objeto de la consulta y la función de devolución de llamada:

```
var nextContinuationToken = null;
dc.table.queryEntities(tableName,
    query,
    nextContinuationToken,
    function (error, results) {
        if (error) throw error;

        // iterate through results.entries with results

        if (results.continuationToken) {
            nextContinuationToken = results.continuationToken;
        }

    });
```

Si inspecciona el `continuationToken` objeto, encontrará propiedades como `nextPartitionKey`, `nextRowKey` y `targetLocation`, que puede usarse para recorrer todos los resultados.

También es un ejemplo de continuación dentro de la repo Node.js de almacenamiento de Azure en GitHub. Busque `examples/samples/continuationsample.js`.

## <a name="work-with-shared-access-signatures"></a>Trabajar con firmas de acceso compartido

Acceso compartido firmas (SA) son una forma segura para proporcionar acceso granular a tablas sin proporcionar el nombre de la cuenta de almacenamiento o teclas. Asociaciones de seguridad se usan a menudo para proporcionar acceso limitado a los datos, por ejemplo, lo que permite una aplicación móvil para consultar los registros.

Una aplicación de confianza, como un servicio basado en la nube genera una SA utilizando la **generateSharedAccessSignature** de la **TableService**y proporciona a una aplicación de confianza o de confianza parcial, como una aplicación móvil. Las asociaciones de seguridad se generan mediante una directiva, que describe las fechas de inicio y finalización durante el cual las SA es válida, así como el nivel de acceso concedido al titular SA.

En el ejemplo siguiente se genera una nueva directiva de acceso compartido que permitirá al titular SA ('r') en la tabla de consulta y expira 100 minutos después de la hora en que se crea.

    var startDate = new Date();
    var expiryDate = new Date(startDate);
    expiryDate.setMinutes(startDate.getMinutes() + 100);
    startDate.setMinutes(startDate.getMinutes() - 100);

    var sharedAccessPolicy = {
      AccessPolicy: {
        Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
        Start: startDate,
        Expiry: expiryDate
      },
    };

    var tableSAS = tableSvc.generateSharedAccessSignature('mytable', sharedAccessPolicy);
    var host = tableSvc.host;

Tenga en cuenta que la información del host se debe proporcionar también, ya que es necesario cuando el titular SA intenta obtener acceso a la tabla.

A continuación, la aplicación cliente usa las asociaciones de seguridad con **TableServiceWithSAS** para realizar operaciones de la tabla. En el ejemplo siguiente se conecta a la tabla y realiza una consulta.

    var sharedTableService = azure.createTableServiceWithSas(host, tableSAS);
    var query = azure.TableQuery()
      .where('PartitionKey eq ?', 'hometasks');

    sharedTableService.queryEntities(query, null, function(error, result, response) {
      if(!error) {
        // result contains the entities
      }
    });

Dado que las asociaciones de seguridad se generan con solo el acceso de consulta, si se realiza un intento de insertar, actualizar o eliminar entidades, se devolverá un error.

### <a name="access-control-lists"></a>Listas de Control de acceso

También puede usar una lista de Control de acceso (ACL) para establecer la directiva de acceso para un SA. Esto es útil si desea permitir que varios clientes a acceder a la tabla, pero proporciona directivas de acceso diferente para cada cliente.

Una ACL se implementa mediante una matriz de directivas de acceso, con un identificador asociados con cada directiva. En el ejemplo siguiente se define dos directivas, uno para 'Usuario1' y otro para 'usuario2':

    var sharedAccessPolicy = {
      user1: {
        Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
        Start: startDate,
        Expiry: expiryDate
      },
      user2: {
        Permissions: azure.TableUtilities.SharedAccessPermissions.ADD,
        Start: startDate,
        Expiry: expiryDate
      }
    };

En el ejemplo siguiente se obtiene las ACL de la tabla **hometasks** y, a continuación, agrega las nuevas directivas mediante **setTableAcl**. Este enfoque permite:

    var extend = require('extend');
    tableSvc.getTableAcl('hometasks', function(error, result, response) {
    if(!error){
        var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
        tableSvc.setTableAcl('hometasks', newSignedIdentifiers, function(error, result, response){
          if(!error){
            // ACL set
          }
        });
      }
    });

Una vez que se ha establecido las ACL, a continuación, puede crear un SA según el identificador de una directiva. En el ejemplo siguiente se crea una nueva SA para 'usuario2':

    tableSAS = tableSvc.generateSharedAccessSignature('hometasks', { Id: 'user2' });

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información, consulte los siguientes recursos.

-   [Blog del equipo de almacenamiento de azure][].
-   Repositorio de [Azure almacenamiento SDK para nodo][] de GitHub.
-   [Centro para desarrolladores de Node.js](/develop/nodejs/)

  [Almacenamiento de Azure SDK de nodo]: https://github.com/Azure/azure-storage-node
  [OData.org]: http://www.odata.org/
  [Using the REST API]: http://msdn.microsoft.com/library/azure/hh264518.aspx
  [Azure Portal]: portal.azure.com

  [Node.js Cloud Service]: ../cloud-services-nodejs-develop-deploy-app.md
  [Blog del equipo de almacenamiento de Azure]: http://blogs.msdn.com/b/windowsazurestorage/
  [Website with WebMatrix]: ../web-sites-nodejs-use-webmatrix.md
  [Node.js Cloud Service with Storage]: ../storage-nodejs-use-table-storage-cloud-service-app.md
  [Node.js web app con el servicio de la tabla de Azure]: ../storage-nodejs-use-table-storage-web-site.md
  [Create and deploy a Node.js application to an Azure website]: ../web-sites-nodejs-develop-deploy-mac.md
