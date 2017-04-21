<properties
    pageTitle="Introducción a almacenamiento de tablas de Azure con .NET | Microsoft Azure"
    description="Almacenar datos estructurados en la nube mediante el almacenamiento de tablas de Azure, un almacén datos."
    services="storage"
    documentationCenter=".net"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/18/2016"
    ms.author="tamram"/>


# <a name="get-started-with-azure-table-storage-using-net"></a>Introducción a almacenamiento de tablas de Azure con .NET

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Información general

Almacenamiento de tablas de Azure es un servicio que almacena datos estructurados de NoSQL en la nube. Almacenamiento de tablas es un almacén de clave o atributo con un diseño schemaless. Dado que es schemaless almacenamiento de tablas, es fácil adaptar los datos como las necesidades de su evolucionar de aplicación. Acceso a datos es rápido y rentable para todos los tipos de aplicaciones. Almacenamiento de tablas suele ser mucho más bajo de costo que SQL tradicional para similares volúmenes de datos.

Puede usar el almacenamiento de tablas para almacenar flexible conjuntos de datos, como los datos de usuario para las aplicaciones web, libretas de direcciones, la información del dispositivo y cualquier otro tipo de metadatos que requiere el servicio. Puede almacenar cualquier número de entidades en una tabla y una cuenta de almacenamiento puede contener cualquier número de tablas, hasta el límite de capacidad de la cuenta de almacenamiento.

### <a name="about-this-tutorial"></a>Acerca de este tutorial

Este tutorial muestra cómo escribir código .NET para algunos escenarios comunes mediante el almacenamiento de tablas de Azure, incluidos crear y eliminar una tabla e Insertar, actualizar, eliminar y consultar datos de tabla.

**Tiempo estimado para completar:** 45 minutos

**Requisitos previos:**

- [Microsoft Visual Studio](https://www.visualstudio.com/en-us/visual-studio-homepage-vs.aspx)
- [Biblioteca de cliente de almacenamiento de Azure para .NET](https://www.nuget.org/packages/WindowsAzure.Storage/)
- [Administrador de configuración de Azure para .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)
- Una [cuenta de almacenamiento de Azure](storage-create-storage-account.md#create-a-storage-account)

[AZURE.INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

### <a name="more-samples"></a>Más muestras

Para obtener más ejemplos de uso de almacenamiento de tablas, vea [Introducción a Azure almacenamiento de tablas en .NET](https://azure.microsoft.com/documentation/samples/storage-table-dotnet-getting-started/). Puede descargar la aplicación de ejemplo y ejecutarla o examinar el código en GitHub.


[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-development-environment-include](../../includes/storage-development-environment-include.md)]

### <a name="add-namespace-declarations"></a>Agregue las declaraciones de espacio de nombres

Agregue las siguientes `using` instrucciones a la parte superior de la `program.cs` archivo:

    using Microsoft.Azure; // Namespace for CloudConfigurationManager
    using Microsoft.WindowsAzure.Storage; // Namespace for CloudStorageAccount
    using Microsoft.WindowsAzure.Storage.Table; // Namespace for Table storage types

### <a name="parse-the-connection-string"></a>Analizar la cadena de conexión

[AZURE.INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="create-the-table-service-client"></a>Crear al cliente de servicio de tabla

La clase **CloudTableClient** permite recuperar tablas y las entidades almacenadas en el almacenamiento de tablas. Esta es una forma para crear al cliente de servicio:

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

Ahora está listo para escribir código que lee y escribe datos en el almacenamiento de tablas.

## <a name="create-a-table"></a>Crear una tabla

Este ejemplo muestra cómo crear una tabla si aún no existe:

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Retrieve a reference to the table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create the table if it doesn't exist.
    table.CreateIfNotExists();

## <a name="add-an-entity-to-a-table"></a>Agregar una entidad a una tabla

Asignan entidades a C\# objetos mediante una clase personalizada que se derivan de **TableEntity**. Para agregar una entidad a una tabla, cree una clase que define las propiedades de la entidad. El código siguiente define una clase de entidad que usa el nombre del cliente como la clave de la fila y el apellido como la clave de partición. Juntas, partición de la entidad y la clave de fila para identificar la entidad de la tabla. Pueden consultar más rápido que aquellos con claves de partición diferentes entidades con la misma clave de partición, pero el uso de claves de partición diverso permite mayor escalabilidad de operaciones en paralelo.  Para cualquier propiedad que se almacenarán en el servicio de la tabla, la propiedad debe ser una propiedad pública de un tipo admitido que expone ambos `get` y `set`.
Además, el tipo de entidad *debe* exponer un constructor sin parámetros.

    public class CustomerEntity : TableEntity
    {
        public CustomerEntity(string lastName, string firstName)
        {
            this.PartitionKey = lastName;
            this.RowKey = firstName;
        }

        public CustomerEntity() { }

        public string Email { get; set; }

        public string PhoneNumber { get; set; }
    }

Operaciones de tabla que implican entidades se realizan mediante el objeto **CloudTable** que creó anteriormente en la sección "Crear una tabla". La operación que debe realizar se representa con un objeto **TableOperation** .  En el ejemplo siguiente se muestra la creación del objeto **CloudTable** y, a continuación, un objeto **CustomerEntity** .  Para preparar la operación, se crea un objeto de **TableOperation** para insertar la entidad de cliente en la tabla.  Por último, la operación se ejecuta llamando **CloudTable.Execute**.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a new customer entity.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";

    // Create the TableOperation object that inserts the customer entity.
    TableOperation insertOperation = TableOperation.Insert(customer1);

    // Execute the insert operation.
    table.Execute(insertOperation);

## <a name="insert-a-batch-of-entities"></a>Insertar un lote de entidades

Puede insertar un lote de entidades en una tabla en una operación de escritura. Algunas otras notas en operaciones por lotes:

-  Puede realizar actualizaciones, se elimina y se inserta en la misma operación único lote.
-  Una única operación por lotes puede incluir hasta 100 entidades.
-  Todas las entidades de una única operación por lotes deben tener la misma clave de partición.
-  Aunque es posible realizar una consulta como una operación por lotes, debe ser la única operación del lote.

<!-- -->
En el ejemplo siguiente se crea dos objetos de entidad y se agrega al **TableBatchOperation** a través del método de **Insertar** . A continuación, se denomina **CloudTable.Execute** para ejecutar la operación.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create the batch operation.
    TableBatchOperation batchOperation = new TableBatchOperation();

    // Create a customer entity and add it to the table.
    CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
    customer1.Email = "Jeff@contoso.com";
    customer1.PhoneNumber = "425-555-0104";

    // Create another customer entity and add it to the table.
    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.Email = "Ben@contoso.com";
    customer2.PhoneNumber = "425-555-0102";

    // Add both customer entities to the batch insert operation.
    batchOperation.Insert(customer1);
    batchOperation.Insert(customer2);

    // Execute the batch operation.
    table.ExecuteBatch(batchOperation);

## <a name="retrieve-all-entities-in-a-partition"></a>Recuperar todas las entidades de una partición

Para consultar una tabla para todas las entidades de una partición, use un objeto **TableQuery** .
En el ejemplo siguiente se especifica un filtro para entidades 'García' donde se encuentra la clave de partición. Este ejemplo muestra los campos de cada entidad en los resultados de la consulta en la consola.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Construct the query operation for all customer entities where PartitionKey="Smith".
    TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

    // Print the fields for each customer.
    foreach (CustomerEntity entity in table.ExecuteQuery(query))
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

## <a name="retrieve-a-range-of-entities-in-a-partition"></a>Recuperar un rango de entidades de una partición

Si no desea que todas las entidades de una partición de la consulta, puede especificar un rango combinando los filtros clave partición con un filtro de clave de fila. En el ejemplo siguiente se usa dos filtros para obtener todas las entidades de partición 'García' donde la clave de fila (nombre) empieza por una letra anterior a 'E' en el alfabeto y, a continuación, imprime los resultados de la consulta.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create the table query.
    TableQuery<CustomerEntity> rangeQuery = new TableQuery<CustomerEntity>().Where(
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"),
            TableOperators.And,
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "E")));

    // Loop through the results, displaying information about the entity.
    foreach (CustomerEntity entity in table.ExecuteQuery(rangeQuery))
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

## <a name="retrieve-a-single-entity"></a>Recuperar una entidad única

Puede escribir una consulta para recuperar una sola entidad específica. El código siguiente utiliza **TableOperation** para especificar al cliente 'Ben Smith'.
Este método devuelve una entidad en lugar de una colección y el valor devuelto en **TableResult.Result** es un objeto **CustomerEntity** .
Especificar teclas partición y fila en una consulta es la manera más rápida de recuperar una sola entidad del servicio de la tabla.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the retrieve operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Print the phone number of the result.
    if (retrievedResult.Result != null)
       Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
    else
       Console.WriteLine("The phone number could not be retrieved.");

## <a name="replace-an-entity"></a>Reemplazar una entidad

Para actualizar una entidad, recuperar del servicio de la tabla, modificar el objeto de entidad y, a continuación, guarde los cambios al servicio de la tabla. El código siguiente cambia el número de teléfono de un cliente existente. En lugar de llamar a **Insertar**, este código utiliza **Reemplazar**. Esto hace que la entidad de reemplazar completamente en el servidor, a menos que la entidad en el servidor ha cambiado desde que se ha recuperado, en cuyo caso se producirá un error de la operación.  Este error es impedir que la aplicación sobrescriba accidentalmente un cambio realizado entre la recuperación y actualización por otro componente de la aplicación.  El tratamiento adecuado de este error es recuperar la entidad de nuevo, realice los cambios (si todavía válido) y, a continuación, realizar otra operación **Reemplazar** .  La siguiente sección le mostrará cómo reemplazar este comportamiento.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity object.
    CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;

    if (updateEntity != null)
    {
       // Change the phone number.
       updateEntity.PhoneNumber = "425-555-0105";

       // Create the Replace TableOperation.
       TableOperation updateOperation = TableOperation.Replace(updateEntity);

       // Execute the operation.
       table.Execute(updateOperation);

       Console.WriteLine("Entity updated.");
    }

    else
       Console.WriteLine("Entity could not be retrieved.");

## <a name="insert-or-replace-an-entity"></a>Insertar o reemplazar una entidad

Operaciones de **Reemplazar** se producirá un error si se ha cambiado la entidad se ha recuperado desde el servidor.  Además, debe recuperar la entidad desde el servidor primero en el orden de la operación **Reemplazar** se realice correctamente.
A veces, sin embargo, no sabe si la entidad existe en el servidor y los valores actuales almacenados en él están relevante. La actualización debería sobrescribir todas ellas.  Para ello, debe usar una operación de **InsertOrReplace** .  Esta operación inserta la entidad si no existe, o reemplaza si es así, independientemente de la que se realizó la última actualización.  En el ejemplo siguiente, la entidad de cliente para Ben Smith aún se recupera, pero a continuación, se guarda al servidor a través de **InsertOrReplace**.  Las actualizaciones realizadas a la entidad entre las operaciones de recuperación y actualización se sobrescribirán.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity object.
    CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;

    if (updateEntity != null)
    {
       // Change the phone number.
       updateEntity.PhoneNumber = "425-555-1234";

       // Create the InsertOrReplace TableOperation.
       TableOperation insertOrReplaceOperation = TableOperation.InsertOrReplace(updateEntity);

       // Execute the operation.
       table.Execute(insertOrReplaceOperation);

       Console.WriteLine("Entity was updated.");
    }

    else
       Console.WriteLine("Entity could not be retrieved.");

## <a name="query-a-subset-of-entity-properties"></a>Consulta un subconjunto de propiedades de la entidad

Una consulta de tabla puede recuperar una serie de propiedades de una entidad en lugar de todas las propiedades de la entidad. Esta técnica, denominada proyección, reduce el ancho de banda y puede mejorar el rendimiento de la consulta, especialmente para entidades de gran tamaño. La consulta en el siguiente código devuelve sólo las direcciones de correo electrónico de entidades de la tabla. Esto se hace mediante una consulta de **DynamicTableEntity** y también **EntityResolver**. Puede obtener más información sobre la proyección en el [blog de proyección de consultas y presentación Upsert publican][]. Tenga en cuenta que no se admite proyección en el emulador almacenamiento local, por lo que este código sólo se ejecuta cuando está usando una cuenta en el servicio de la tabla.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Define the query, and select only the Email property.
    TableQuery<DynamicTableEntity> projectionQuery = new TableQuery<DynamicTableEntity>().Select(new string[] { "Email" });

    // Define an entity resolver to work with the entity after retrieval.
    EntityResolver<string> resolver = (pk, rk, ts, props, etag) => props.ContainsKey("Email") ? props["Email"].StringValue : null;

    foreach (string projectedEmail in table.ExecuteQuery(projectionQuery, resolver, null, null))
    {
        Console.WriteLine(projectedEmail);
    }

## <a name="delete-an-entity"></a>Eliminar una entidad

Puede eliminar fácilmente una entidad después leerlo, usando el mismo patrón que se muestra para actualizar una entidad.  El siguiente código recupera y elimina una entidad de cliente.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that expects a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity.
    CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

    // Create the Delete TableOperation.
    if (deleteEntity != null)
    {
       TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

       // Execute the operation.
       table.Execute(deleteOperation);

       Console.WriteLine("Entity deleted.");
    }

    else
       Console.WriteLine("Could not retrieve the entity.");

## <a name="delete-a-table"></a>Eliminar una tabla

Por último, en el ejemplo siguiente se elimina una tabla de una cuenta de almacenamiento. Una tabla que se ha eliminado no estará disponible para volver a crearse durante un período de tiempo después de la eliminación.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Delete the table it if exists.
    table.DeleteIfExists();

## <a name="retrieve-entities-in-pages-asynchronously"></a>Recuperar entidades en las páginas de forma asincrónica

Si está leyendo una gran cantidad de entidades y desea proceso o mostrar entidades como se recuperan en lugar de en espera para que todos ellos devolver, puede recuperar entidades mediante una consulta segmentada. Este ejemplo muestra cómo devolver resultados en páginas utilizando el modelo de espera asincrónico para que la ejecución no se bloquea mientras espera para un amplio conjunto de resultados para devolver. Para obtener más detalles sobre cómo utilizar el modelo asincrónico espera en .NET, vea [programación asincrónica con asincrónica y espera (C# y Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx).

    // Initialize a default TableQuery to retrieve all the entities in the table.
    TableQuery<CustomerEntity> tableQuery = new TableQuery<CustomerEntity>();

    // Initialize the continuation token to null to start from the beginning of the table.
    TableContinuationToken continuationToken = null;

    do
    {
        // Retrieve a segment (up to 1,000 entities).
        TableQuerySegment<CustomerEntity> tableQueryResult =
            await table.ExecuteQuerySegmentedAsync(tableQuery, continuationToken);

        // Assign the new continuation token to tell the service where to
        // continue on the next iteration (or null if it has reached the end).
        continuationToken = tableQueryResult.ContinuationToken;

        // Print the number of rows retrieved.
        Console.WriteLine("Rows retrieved {0}", tableQueryResult.Results.Count);

    // Loop until a null continuation token is received, indicating the end of the table.
    } while(continuationToken != null);

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido los conceptos básicos de almacenamiento de tablas, siga estos vínculos para obtener información acerca de las tareas más complejas de almacenamiento:

- Ver más muestras de almacenamiento de tabla en [Introducción a Azure almacenamiento de tablas en .NET](https://azure.microsoft.com/documentation/samples/storage-table-dotnet-getting-started/)
- Ver la documentación de referencia de servicio de tabla para obtener información detallada acerca de la API disponibles:
    - [Biblioteca de cliente de almacenamiento para la referencia de .NET](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
    - [Referencia de la API de REST](http://msdn.microsoft.com/library/azure/dd179355)
- Aprenda a simplificar el código que escriba para trabajar con el almacenamiento de Azure mediante el [SDK de WebJobs de Azure](../app-service-web/websites-dotnet-webjobs-sdk-get-started.md)
- Ver más características guías para obtener información sobre las opciones adicionales para almacenar los datos en Azure.
    - [Introducción a almacenamiento de blobs de Windows Azure con .NET](storage-dotnet-how-to-use-blobs.md) para almacenar datos no estructurados.
    - [Conectarse a la base de datos de SQL mediante .NET (C#)](../sql-database/sql-database-develop-dotnet-simple.md) para almacenar datos relacionales.

  [Download and install the Azure SDK for .NET]: /develop/net/
  [Creating an Azure Project in Visual Studio]: http://msdn.microsoft.com/library/azure/ee405487.aspx

  [Blob5]: ./media/storage-dotnet-how-to-use-table-storage/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-table-storage/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-table-storage/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-table-storage/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-table-storage/blob9.png

  [Entrada de blog Introducción Upsert y proyección de consultas]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx
  [.NET Client Library reference]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [Azure Storage Team blog]: http://blogs.msdn.com/b/windowsazurestorage/
  [Configure Azure Storage connection strings]: http://msdn.microsoft.com/library/azure/ee758697.aspx
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
  [How to: Programmatically access Table storage]: #tablestorage
