##<a name="create-client"></a>Crear una conexión de cliente

Crear una conexión de cliente mediante la creación de un `WindowsAzure.MobileServiceClient` objeto.  Reemplazar `appUrl` con la dirección URL para la aplicación móvil.

```
var client = WindowsAzure.MobileServiceClient(appUrl);
```

##<a name="table-reference"></a>Trabajar con tablas

En access o actualizar los datos, crear una referencia a la tabla de back-end. Reemplazar `tableName` con el nombre de la tabla

```
var table = client.getTable(tableName);
```

Una vez que tenga una referencia de tabla, puede trabajar con la tabla:

* [Una tabla de consulta](#querying)
  * [Filtrado de datos](#table-filter)
  * [Paginación de datos](#table-paging)
  * [Ordenar datos](#sorting-data)
* [Insertar datos](#inserting)
* [Modificación de los datos](#modifying)
* [Eliminar datos](#deleting)

###<a name="querying"></a>Cómo: consultar una referencia de tabla

Una vez que tenga una referencia de tabla, puede usar para consultar datos en el servidor.  Las consultas se realizan en un idioma de "Me gusta de LINQ".
Para devolver todos los datos de la tabla, utilice la siguiente:

```
/**
 * Process the results that are received by a call to table.read()
 *
 * @param {Object} results the results as a pseudo-array
 * @param {int} results.length the length of the results array
 * @param {Object} results[] the individual results
 */
function success(results) {
   var numItemsRead = results.length;

   for (var i = 0 ; i < results.length ; i++) {
       var row = results[i];
       // Each row is an object - the properties are the columns
   }
}

function failure(error) {
    throw new Error('Error loading data: ', error);
}

table
    .read()
    .then(success, failure);
```

La función de éxito se denomina con los resultados.   No utilice `for (var i in results)` en el éxito como que creará una iteración sobre la información que se incluye en los resultados de la función cuando otras funciones de consulta (como `.includeTotalCount()`) se usan.

Para obtener más información sobre la sintaxis de la consulta, consulte [documentación de objeto de consulta].

####<a name="table-filter"></a>Filtrar datos en el servidor

Puede usar un `where` cláusula en la referencia de tabla:

```
table
    .where({ userId: user.userId, complete: false })
    .read()
    .then(success, failure);
```

También puede usar una función que filtra el objeto.  En este caso el `this` variable está asignada al objeto actual que se está filtrando.  Es equivalente del ejemplo anterior:

```
function filterByUserId(currentUserId) {
    return this.userId === currentUserId && this.complete === false;
}

table
    .where(filterByUserId, user.userId)
    .read()
    .then(success, failure);
```

####<a name="table-paging"></a>Paginación de datos

Utilizar los métodos take() y skip().  Por ejemplo, si desea dividir la tabla en la fila 100 registros:

```
var totalCount = 0, pages = 0;

// Step 1 - get the total number of records
table.includeTotalCount().take(0).read(function (results) {
    totalCount = results.totalCount;
    pages = Math.floor(totalCount/100) + 1;
    loadPage(0);
}, failure);

function loadPage(pageNum) {
    let skip = pageNum * 100;
    table.skip(skip).take(100).read(function (results) {
        for (var i = 0 ; i < results.length ; i++) {
            var row = results[i];
            // Process each row
        }
    }
}
```

La `.includeTotalCount()` método se utiliza para agregar un campo totalCount al objeto de resultados.  El campo totalCount se rellena con el número total de registros que se devolverá si no se utiliza paginación.

A continuación, puede usar la variable de páginas y algunos botones de la interfaz de usuario para proporcionar una lista de la página; Utilice loadPage() para cargar los nuevos registros para cada página.  Debe implementar a algún tipo de almacenamiento en caché para acelerar el acceso a los registros que ya se ha cargado.


####<a name="sorting-data"></a>Cómo: devolver datos ordenados

Usar los métodos de consulta .orderBy() o .orderByDescending():

```
table
    .orderBy('name')
    .read()
    .then(success, failure);
```

Para obtener más información sobre el objeto de consulta, consulte [documentación de objeto de consulta].

###<a name="inserting"></a>Cómo: insertar datos

Crear un objeto de JavaScript con la fecha adecuada y llamar de forma asincrónica table.insert():

```
var newItem = {
    name: 'My Name',
    signupDate: new Date()
};

table
    .insert(newItem)
    .done(function (insertedItem) {
        var id = insertedItem.id;
    }, failure);
```

En la inserción correcta, se devuelve el elemento insertado con los campos adicionales necesarios para operaciones de sincronización.  Debe actualizar su propia caché con esta información de las actualizaciones posteriores.

Observe que el SDK de servidor de Azure Mobile aplicaciones Node.js admite esquema dinámico fines de desarrollo.
En el caso de esquema dinámicas, se actualiza el esquema de la tabla sobre la marcha, lo que le permite agregar columnas a la tabla haciendo especificarlos en una instrucción insert o la operación de actualización.  Se recomienda desactivar esquema dinámico antes de pasar a la aplicación de producción.

###<a name="modifying"></a>Cómo: modificar datos

De forma similar al método .insert(), debe crear un objeto de actualización y, a continuación, llame a .update().  El objeto de actualización debe contener el identificador del registro que se actualicen: se obtiene cuando el registro de lectura o al llamar a .insert().

```
var updateItem = {
    id: '7163bc7a-70b2-4dde-98e9-8818969611bd',
    name: 'My New Name'
};

table
    .update(updateItem)
    .done(function (updatedItem) {
        // You can now update your cached copy
    }, failure);
```

###<a name="deleting"></a>Cómo: eliminar datos

Llame al método .del() para eliminar un registro.  Pase el identificador de una referencia de objeto:

```
table
    .del({ id: '7163bc7a-70b2-4dde-98e9-8818969611bd' })
    .done(function () {
        // Record is now deleted - update your cache
    }, failure);
```
