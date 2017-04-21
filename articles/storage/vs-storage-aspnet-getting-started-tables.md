<properties
    pageTitle="Introducción a almacenamiento de tablas y Visual Studio conectado servicios (ASP.NET) | Microsoft Azure"
    description="Cómo empezar a usar el almacenamiento de tablas de Azure en un proyecto ASP.NET en Visual Studio después de conectar con una cuenta de almacenamiento mediante Visual Studio conectado servicios"
    services="storage"
    documentationCenter=""
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="storage"
    ms.workload="web"
    ms.tgt_pltfrm="vs-getting-started"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/18/2016"
    ms.author="tarcher"/>

# <a name="get-started-with-table-storage-and-visual-studio-connected-services-aspnet"></a>Introducción a almacenamiento de tablas y Visual Studio conectado servicios (ASP.NET)

[AZURE.INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Información general
Este artículo se describe cómo empezar a usar el almacenamiento de tablas de Azure en Visual Studio después de haber creado o hace referencia a una cuenta de almacenamiento de Azure en un proyecto ASP.NET mediante el cuadro de diálogo de Visual Studio **Agregar servicios conectados** . En este artículo se muestra cómo realizar tareas comunes en las tablas de Azure, incluida la creación y eliminación de una tabla, así como trabajar con entidades de tabla. Los ejemplos están escritos en C\# código y usar la [Biblioteca de cliente de Microsoft Azure almacenamiento para .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx). Para obtener información general sobre el uso de Azure almacenamiento de tabla, consulte [Introducción a almacenamiento de tablas de Azure con .NET](storage-dotnet-how-to-use-tables.md).

Almacenamiento de tablas de Azure le permite almacenar grandes cantidades de datos estructurados. El servicio es un almacén de datos NoSQL que acepta llamadas autenticadas desde dentro y fuera de la nube de Azure. Tablas de Azure son ideales para almacenar los datos estructurados y no relacionales.


## <a name="access-tables-in-code"></a>Tablas de Access en el código

1. Asegúrese de que las declaraciones de espacio de nombres en la parte superior del archivo C# incluyen estas instrucciones **using** .

         using Microsoft.Azure;
         using Microsoft.WindowsAzure.Storage;
         using Microsoft.WindowsAzure.Storage.Auth;
         using Microsoft.WindowsAzure.Storage.Table;

2. Obtenga un objeto **CloudStorageAccount** que representa la información de su cuenta de almacenamiento. Utilice el siguiente código para obtener la su cadena de conexión de almacenamiento y la información de la cuenta de almacenamiento de la configuración del servicio de Azure.

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

    **Nota** : utilizar todo el código anterior delante del código en los ejemplos siguientes.

3. Obtener un objeto de **CloudTableClient** para hacer referencia a los objetos de la tabla en la cuenta de almacenamiento.  

        // Create the table client.
        CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

4. Obtener un objeto de referencia **CloudTable** hacer referencia a una tabla específica y entidades.

        // Get a reference to a table named "peopleTable"
        CloudTable table = tableClient.GetTableReference("peopleTable");

## <a name="create-a-table-in-code"></a>Crear una tabla en el código

Para crear la tabla de Azure, basta con agregar una llamada a **CreateIfNotExistsAsync()** al código anterior.

    // Create the CloudTable if it does not exist
    await table.CreateIfNotExistsAsync();

## <a name="add-an-entity-to-a-table"></a>Agregar una entidad a una tabla

Para agregar una entidad a una tabla de crear una clase que define las propiedades de la entidad. El código siguiente define una clase de entidad denominada **CustomerEntity** que usa el nombre del cliente como la clave de la fila y el apellido como la clave de partición.

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

Operaciones de tabla que implican entidades se realiza mediante la **CloudTable** del objeto que creó anteriormente en "Tablas de Access en el código". El objeto **TableOperation** representa la operación que debe hacerse. En el ejemplo siguiente se muestra cómo crear un objeto de **CloudTable** y un objeto **CustomerEntity** . Para preparar la operación, se crea un **TableOperation** para insertar la entidad de cliente en la tabla. Por último, la operación se ejecuta llamando CloudTable.ExecuteAsync.

    // Create a new customer entity.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";

    // Create the TableOperation that inserts the customer entity.
    TableOperation insertOperation = TableOperation.Insert(customer1);

    // Execute the insert operation.
    await peopleTable.ExecuteAsync(insertOperation);

## <a name="insert-a-batch-of-entities"></a>Insertar un lote de entidades

Puede insertar varias entidades en una tabla en una operación de escritura único. En el ejemplo siguiente se crea dos objetos de entidad ("Jeff Smith" y "Ben Smith"), las agrega a un objeto **TableBatchOperation** con el método de insertar y, a continuación, se inicia la operación llamando **CloudTable.ExecuteBatchAsync**.

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
    await peopleTable.ExecuteBatchAsync(batchOperation);

## <a name="get-all-of-the-entities-in-a-partition"></a>Obtener todas las entidades de una partición
Para consultar una tabla para todas las entidades de una partición, use un objeto **TableQuery** . En el ejemplo siguiente se especifica un filtro para entidades 'García' donde se encuentra la clave de partición. Este ejemplo muestra los campos de cada entidad en los resultados de la consulta en la consola.

    // Construct the query operation for all customer entities where PartitionKey="Smith".
    TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

    // Print the fields for each customer.
    TableContinuationToken token = null;
    do
    {
        TableQuerySegment<CustomerEntity>
        resultSegment = await peopleTable.ExecuteQuerySegmentedAsync(query, token);
        token = resultSegment.ContinuationToken;

        foreach (CustomerEntity entity in resultSegment.Results)
        {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
        }
        } while (token != null);

        return View();


## <a name="get-a-single-entity"></a>Obtener una entidad única
Puede escribir una consulta para obtener una sola entidad específica. El código siguiente utiliza un objeto de **TableOperation** para especificar a un cliente llamado 'Ben Smith'. Este método devuelve una entidad, en lugar de una colección y el valor devuelto en **TableResult.Result** es un objeto de **CustomerEntity** . Especificar teclas partición y fila en una consulta es la manera más rápida de recuperar una sola entidad del servicio de la tabla.

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the retrieve operation.
    TableResult retrievedResult = await peopleTable.ExecuteAsync(retrieveOperation);
    
    // Print the phone number of the result.
    if (retrievedResult.Result != null)
        Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
    else
       Console.WriteLine("The phone number could not be retrieved.");

## <a name="delete-an-entity"></a>Eliminar una entidad
Puede eliminar una entidad una vez encontrado. El código siguiente busca una entidad de cliente con el nombre "Ben Smith" y, si lo encuentra, elimina.

    // Create a retrieve operation that expects a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = peopleTable.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity object.
    CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

    // Create the Delete TableOperation and then execute it.
    if (deleteEntity != null)
    {
       TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

       // Execute the operation.
       await peopleTable.ExecuteAsync(deleteOperation);

       Console.WriteLine("Entity deleted.");
    }

    else
       Console.WriteLine("Couldn't delete the entity.");

## <a name="next-steps"></a>Pasos siguientes

[AZURE.INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]
