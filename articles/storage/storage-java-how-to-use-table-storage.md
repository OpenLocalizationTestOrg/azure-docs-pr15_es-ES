<properties
    pageTitle="Cómo usar el almacenamiento de tablas de Java | Microsoft Azure"
    description="Almacenar datos estructurados en la nube mediante el almacenamiento de tablas de Azure, un almacén datos."
    services="storage"
    documentationCenter="java"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>


# <a name="how-to-use-table-storage-from-java"></a>Cómo usar el almacenamiento de tablas de Java

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Información general

Esta guía le mostrará cómo llevar a cabo escenarios comunes mediante el servicio de almacenamiento de la tabla de Azure. Los ejemplos están escritos en Java y usan el [SDK de almacenamiento de Azure para Java][]. Los escenarios cubiertos incluyen **crear**, **Enumerar**y **Eliminar** tablas, así como **Insertar**, **consultar**, **Modificar**y **Eliminar** entidades en una tabla. Para obtener más información sobre tablas, vea la sección [pasos siguientes](#Next-Steps) .

Nota: Un SDK está disponible para los desarrolladores que usan el almacenamiento de Azure en dispositivos Android. Para obtener más información, consulte el [SDK de almacenamiento de Azure para Android][].

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Crear una aplicación de Java

En esta guía, que usa las funciones de almacenamiento que se pueden ejecutar en una aplicación de Java localmente o en el código que se ejecuta dentro de una función web o trabajo en Azure.

Para ello, debe instalar el Kit de desarrollo de Java (JDK) y crear una cuenta de almacenamiento de Azure en la suscripción de Azure. Una vez hecho, deberá comprobar que el sistema de desarrollo cumple los requisitos mínimos y las dependencias que se muestran en el repositorio de [Azure almacenamiento SDK para Java][] en GitHub. Si su sistema cumple estos requisitos, puede seguir las instrucciones para descargar e instalar las bibliotecas de almacenamiento de Azure para Java en su sistema de repositorio. Cuando haya terminado esas tareas, podrá crear una aplicación de Java que utiliza los ejemplos de este artículo.

## <a name="configure-your-application-to-access-table-storage"></a>Configurar la aplicación para tener acceso a almacenamiento de tablas

Agregue las siguientes instrucciones de importación a la parte superior del archivo Java donde desea utilizar Microsoft Azure almacenamiento de las API de acceso a tablas:

    // Include the following imports to use table APIs
    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.table.*;
    import com.microsoft.azure.storage.table.TableQuery.*;

## <a name="setup-an-azure-storage-connection-string"></a>Configuración de una cadena de conexión de almacenamiento de Azure

Un cliente de almacenamiento de Azure utiliza una cadena de conexión de almacenamiento para almacenar los extremos y las credenciales para acceder a los servicios de administración de datos. Cuando se ejecuta en una aplicación de cliente, debe proporcionar la cadena de conexión de almacenamiento en el siguiente formato, con el nombre de su cuenta de almacenamiento y la tecla de acceso principal de la cuenta de almacenamiento aparece en el [Portal de Azure](https://portal.azure.com) para los valores de *nombre de la cuenta* y *AccountKey* . Este ejemplo muestra cómo puede declarar un campo estático para almacenar la cadena de conexión:

    // Define the connection-string with your values.
    public static final String storageConnectionString =
        "DefaultEndpointsProtocol=http;" +
        "AccountName=your_storage_account;" +
        "AccountKey=your_storage_account_key";

En una aplicación que se ejecuta dentro de una función en Microsoft Azure, esta cadena se pueden almacenar en el archivo de configuración de servicio, *ServiceConfiguration.cscfg*y se puede acceder con una llamada al método **RoleEnvironment.getConfigurationSettings** . Aquí tiene un ejemplo de obtener la cadena de conexión de un elemento de **configuración** denominado *StorageConnectionString* en el archivo de configuración de servicio:

    // Retrieve storage account from connection-string.
    String storageConnectionString =
        RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");

Los ejemplos siguientes, se supone que ha utilizado uno de estos dos métodos para obtener la cadena de conexión de almacenamiento.

## <a name="how-to-create-a-table"></a>Cómo: crear una tabla

Un objeto **CloudTableClient** le permite obtener objetos de referencia para entidades y tablas. El código siguiente crea un objeto de **CloudTableClient** y usa para crear un nuevo objeto **CloudTable** que representa una tabla denominada "personas". (Nota: hay otras maneras de crear objetos **CloudStorageAccount** ; para obtener más información, consulte **CloudStorageAccount** en la [Referencia de SDK de cliente de almacenamiento de Azure].)

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

       // Create the table client.
       CloudTableClient tableClient = storageAccount.createCloudTableClient();

       // Create the table if it doesn't exist.
       String tableName = "people";
       CloudTable cloudTable = tableClient.getTableReference(tableName);
       cloudTable.createIfNotExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-list-the-tables"></a>Cómo: lista de las tablas

Para obtener una lista de tablas, llame al método **CloudTableClient.listTables()** para recuperar una lista de nombres de tabla iterable.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Loop through the collection of table names.
        for (String table : tableClient.listTables())
        {
          // Output each table name.
          System.out.println(table);
       }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-add-an-entity-to-a-table"></a>Cómo: agregar una entidad a una tabla

Mapa de entidades a objetos de Java mediante una clase personalizada que se implementa **TableEntity**. Para su comodidad, la clase **TableServiceEntity** implementa **TableEntity** y utiliza la reflexión para asignar propiedades a métodos Get y Set con el nombre de las propiedades. Para agregar una entidad a una tabla, cree primero una clase que define las propiedades de la entidad. El código siguiente define una clase de entidad que utiliza el nombre del cliente como la clave de la fila y el apellido como la clave de partición. Juntas, partición de la entidad y la clave de fila para identificar la entidad de la tabla. Pueden consultar entidades con la misma clave de partición más rápido que aquellos con claves de partición diferente.

    public class CustomerEntity extends TableServiceEntity {
        public CustomerEntity(String lastName, String firstName) {
            this.partitionKey = lastName;
            this.rowKey = firstName;
        }

        public CustomerEntity() { }

        String email;
        String phoneNumber;

        public String getEmail() {
            return this.email;
        }

        public void setEmail(String email) {
            this.email = email;
        }

        public String getPhoneNumber() {
            return this.phoneNumber;
        }

        public void setPhoneNumber(String phoneNumber) {
            this.phoneNumber = phoneNumber;
        }
    }

Operaciones de tabla que implican entidades requieren un objeto **TableOperation** . Este objeto define la operación que debe realizarse en una entidad, que se puede ejecutar con un objeto **CloudTable** . El código siguiente crea una nueva instancia de la clase **CustomerEntity** con algunos datos de cliente. El código siguiente llama **TableOperation.insertOrReplace** para crear un objeto **TableOperation** para insertar una entidad en una tabla y asocia el nuevo **CustomerEntity** . Por último, el código llama al método **Ejecutar** en el objeto **CloudTable** , que especifica la tabla "personas" y la nueva **TableOperation**, que, a continuación, se envía una solicitud al servicio de almacenamiento para insertar la nueva entidad de cliente en la tabla "personas" o reemplazar la entidad si ya existe.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Create a cloud table object for the table.
        CloudTable cloudTable = tableClient.getTableReference("people");

        // Create a new customer entity.
        CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
        customer1.setEmail("Walter@contoso.com");
        customer1.setPhoneNumber("425-555-0101");

        // Create an operation to add the new customer to the people table.
        TableOperation insertCustomer1 = TableOperation.insertOrReplace(customer1);

        // Submit the operation to the table service.
        cloudTable.execute(insertCustomer1);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-insert-a-batch-of-entities"></a>Cómo: insertar un lote de entidades

Puede insertar un lote de entidades al servicio de tabla en una operación de escritura. El código siguiente crea un objeto **TableBatchOperation** y luego agrega que tres insertar operaciones en él. Crear un nuevo objeto entidad, establecer sus valores y, a continuación, llamando al método de **Insertar** en el objeto de **TableBatchOperation** para asociar la entidad con una nueva operación de insertar agrega cada operación de inserción. A continuación, el código llama a **Ejecutar** en el objeto **CloudTable** , que especifica la tabla "personas" y el objeto **TableBatchOperation** , que envía el lote de operaciones de tablas con el servicio de almacenamiento en una sola solicitud.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Define a batch operation.
        TableBatchOperation batchOperation = new TableBatchOperation();

        // Create a cloud table object for the table.
        CloudTable cloudTable = tableClient.getTableReference("people");

        // Create a customer entity to add to the table.
        CustomerEntity customer = new CustomerEntity("Smith", "Jeff");
        customer.setEmail("Jeff@contoso.com");
        customer.setPhoneNumber("425-555-0104");
        batchOperation.insertOrReplace(customer);

       // Create another customer entity to add to the table.
       CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
       customer2.setEmail("Ben@contoso.com");
       customer2.setPhoneNumber("425-555-0102");
       batchOperation.insertOrReplace(customer2);

       // Create a third customer entity to add to the table.
       CustomerEntity customer3 = new CustomerEntity("Smith", "Denise");
       customer3.setEmail("Denise@contoso.com");
       customer3.setPhoneNumber("425-555-0103");
       batchOperation.insertOrReplace(customer3);

       // Execute the batch of operations on the "people" table.
       cloudTable.execute(batchOperation);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

Algunas cosas que debe saber sobre las operaciones por lotes:

- Puede realizar hasta 100 Insertar, eliminar, combinar, reemplazar, insertar o combinar e insertar o reemplazar las operaciones en cualquier combinación en un único lote.
- Una operación por lotes puede tener una operación de recuperación, si es la única operación del lote.
- Todas las entidades de una única operación por lotes deben tener la misma clave de partición.
- Una operación por lotes está limitada a una carga de datos de 4MB.

## <a name="how-to-retrieve-all-entities-in-a-partition"></a>Cómo: recuperar todas las entidades de una partición

Para consultar una tabla de entidades en una partición, puede usar un **TableQuery**. Llamar a **TableQuery.from** para crear una consulta en una tabla concreta que devuelve un tipo de resultado especificado. El código siguiente especifica un filtro para entidades 'García' donde se encuentra la clave de partición. **TableQuery.generateFilterCondition** es un método auxiliar para crear filtros de consultas. Llamar a **donde** en la referencia devuelta por el método **TableQuery.from** para aplicar el filtro a la consulta. Cuando se ejecuta la consulta con una llamada para **Ejecutar** en el objeto **CloudTable** , devuelve un **iterador** con el tipo de resultado de **CustomerEntity** especificado. A continuación, puede usar el **iterador** devuelto en un bucle for each consumir los resultados. Este código imprime los campos de cada entidad en los resultados de la consulta en la consola.

    try
    {
        // Define constants for filters.
        final String PARTITION_KEY = "PartitionKey";
        final String ROW_KEY = "RowKey";
        final String TIMESTAMP = "Timestamp";

        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

       // Create a cloud table object for the table.
       CloudTable cloudTable = tableClient.getTableReference("people");

        // Create a filter condition where the partition key is "Smith".
        String partitionFilter = TableQuery.generateFilterCondition(
           PARTITION_KEY,
           QueryComparisons.EQUAL,
           "Smith");

       // Specify a partition query, using "Smith" as the partition key filter.
       TableQuery<CustomerEntity> partitionQuery =
           TableQuery.from(CustomerEntity.class)
           .where(partitionFilter);

        // Loop through the results, displaying information about the entity.
        for (CustomerEntity entity : cloudTable.execute(partitionQuery)) {
            System.out.println(entity.getPartitionKey() +
                " " + entity.getRowKey() +
                "\t" + entity.getEmail() +
                "\t" + entity.getPhoneNumber());
       }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-retrieve-a-range-of-entities-in-a-partition"></a>Cómo: recuperar un rango de entidades de una partición

Si no desea que todas las entidades de una partición de la consulta, puede especificar un rango usando operadores de comparación de un filtro. El siguiente código combina dos filtros para obtener todas las entidades de partición "Gracia" donde la clave de fila (nombre) empieza por una letra hasta 'E' del alfabeto. A continuación, imprime los resultados de la consulta. Si usa las entidades que se agregan a la tabla en el lote de Insertar sección de esta guía, solo dos entidades se devuelven esta vez (Miguel y Denise Smith); Jeff Smith no se incluye.

    try
    {
        // Define constants for filters.
        final String PARTITION_KEY = "PartitionKey";
        final String ROW_KEY = "RowKey";
        final String TIMESTAMP = "Timestamp";

        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

       // Create the table client.
       CloudTableClient tableClient = storageAccount.createCloudTableClient();

       // Create a cloud table object for the table.
       CloudTable cloudTable = tableClient.getTableReference("people");

        // Create a filter condition where the partition key is "Smith".
        String partitionFilter = TableQuery.generateFilterCondition(
           PARTITION_KEY,
           QueryComparisons.EQUAL,
           "Smith");

        // Create a filter condition where the row key is less than the letter "E".
        String rowFilter = TableQuery.generateFilterCondition(
           ROW_KEY,
           QueryComparisons.LESS_THAN,
           "E");

        // Combine the two conditions into a filter expression.
        String combinedFilter = TableQuery.combineFilters(partitionFilter,
            Operators.AND, rowFilter);

        // Specify a range query, using "Smith" as the partition key,
        // with the row key being up to the letter "E".
        TableQuery<CustomerEntity> rangeQuery =
           TableQuery.from(CustomerEntity.class)
           .where(combinedFilter);

        // Loop through the results, displaying information about the entity
        for (CustomerEntity entity : cloudTable.execute(rangeQuery)) {
            System.out.println(entity.getPartitionKey() +
                " " + entity.getRowKey() +
                "\t" + entity.getEmail() +
                "\t" + entity.getPhoneNumber());
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-retrieve-a-single-entity"></a>Cómo: recuperar una entidad única

Puede escribir una consulta para recuperar una sola entidad específica. El siguiente código llama **TableOperation.retrieve** con la fila y la clave de partición parámetros clave para especificar al cliente "Jeff Smith" en lugar de crear un **TableQuery** y usar filtros para hacer lo mismo. Cuando se ejecuta, la operación de recuperar devuelve una entidad, en lugar de una colección. El método **getResultAsType** convierte el resultado en el tipo de destino de asignación, un objeto **CustomerEntity** . Si este tipo no es compatible con el tipo especificado para la consulta, se producirá una excepción. Si ninguna entidad tiene una partición exacta y la clave de la fila que coincida con, se devuelve un valor nulo. Especificar teclas partición y fila en una consulta es la manera más rápida de recuperar una sola entidad del servicio de la tabla.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Create a cloud table object for the table.
        CloudTable cloudTable = tableClient.getTableReference("people");

        // Retrieve the entity with partition key of "Smith" and row key of "Jeff"
        TableOperation retrieveSmithJeff =
           TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

       // Submit the operation to the table service and get the specific entity.
       CustomerEntity specificEntity =
            cloudTable.execute(retrieveSmithJeff).getResultAsType();

        // Output the entity.
        if (specificEntity != null)
        {
            System.out.println(specificEntity.getPartitionKey() +
                " " + specificEntity.getRowKey() +
                "\t" + specificEntity.getEmail() +
                "\t" + specificEntity.getPhoneNumber());
       }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-modify-an-entity"></a>Cómo: modificar una entidad

Para modificar una entidad, recuperar del servicio de la tabla, realizar cambios en el objeto de entidad y guardar los cambios en el servicio de la tabla con una operación de reemplazar o combinar. El código siguiente cambia el número de teléfono de un cliente existente. En lugar de llamar a **TableOperation.insert** como se ha hecho para insertar, este código llama **TableOperation.replace**. El método **CloudTable.execute** llama al servicio de la tabla y se reemplaza la entidad, a menos que otra aplicación lo cambiaron en el tiempo ya que esta aplicación leerlo. Cuando esto ocurre, se produce una excepción y la entidad debe recuperar, modificar y guardar nuevamente. Este patrón de intentos de simultaneidad optimista es común en un sistema de almacenamiento distribuido.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Create a cloud table object for the table.
        CloudTable cloudTable = tableClient.getTableReference("people");

        // Retrieve the entity with partition key of "Smith" and row key of "Jeff".
        TableOperation retrieveSmithJeff =
           TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

        // Submit the operation to the table service and get the specific entity.
        CustomerEntity specificEntity =
          cloudTable.execute(retrieveSmithJeff).getResultAsType();

        // Specify a new phone number.
        specificEntity.setPhoneNumber("425-555-0105");

        // Create an operation to replace the entity.
        TableOperation replaceEntity = TableOperation.replace(specificEntity);

        // Submit the operation to the table service.
        cloudTable.execute(replaceEntity);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-query-a-subset-of-entity-properties"></a>Cómo: consultar un subconjunto de propiedades de la entidad

Una consulta a una tabla puede recuperar una serie de propiedades de una entidad. Esta técnica, denominada proyección, reduce el ancho de banda y puede mejorar el rendimiento de la consulta, especialmente para entidades de gran tamaño. La consulta en el código siguiente usa el método **select** para devolver sólo las direcciones de correo electrónico de entidades en la tabla. Los resultados se proyectan en una colección de **cadena** con la Ayuda de un **EntityResolver**, que realiza la conversión de tipo sobre las entidades devuelto desde el servidor. Puede obtener más información sobre la proyección en [tablas de Azure: Introducción Upsert y proyección de consultas][]. Tenga en cuenta que no se admite proyección en el emulador almacenamiento local, para que se ejecute este código solo cuando se usa una cuenta en el servicio de la tabla.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Create a cloud table object for the table.
        CloudTable cloudTable = tableClient.getTableReference("people");

        // Define a projection query that retrieves only the Email property
        TableQuery<CustomerEntity> projectionQuery =
           TableQuery.from(CustomerEntity.class)
           .select(new String[] {"Email"});

        // Define a Entity resolver to project the entity to the Email value.
        EntityResolver<String> emailResolver = new EntityResolver<String>() {
            @Override
            public String resolve(String PartitionKey, String RowKey, Date timeStamp, HashMap<String, EntityProperty> properties, String etag) {
                return properties.get("Email").getValueAsString();
            }
        };

        // Loop through the results, displaying the Email values.
        for (String projectedString :
            cloudTable.execute(projectionQuery, emailResolver)) {
                System.out.println(projectedString);
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-insert-or-replace-an-entity"></a>Cómo: insertar o reemplazar una entidad

Frecuencia con que desea agregar una entidad a una tabla sin necesidad de conocer si ya existe en la tabla. Una operación de insertar o reemplazar permite realizar una única solicitud en la cual se insertará la entidad si no existe o reemplazar uno existente si hace. Basándose en los ejemplos anteriores, el siguiente código inserta o reemplaza la entidad para "Walter arpa". Después de crear una nueva entidad, este código llama al método de **TableOperation.insertOrReplace** . A continuación, este código llama a **Ejecutar** en el objeto **CloudTable** con la tabla y el insertar o reemplazar la operación de tabla como parámetros. Para actualizar solo una parte de una entidad, el método **TableOperation.insertOrMerge** puede usarse en su lugar. Tenga en cuenta que insertar o reemplazar no es compatible con el emulador almacenamiento local, para que se ejecute este código solo cuando se usa una cuenta en el servicio de la tabla. Puede obtener más información acerca de insertar o reemplazar e insertar o combinación en esta [tablas de Azure: Introducción Upsert y proyección de consultas][].

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Create a cloud table object for the table.
        CloudTable cloudTable = tableClient.getTableReference("people");

        // Create a new customer entity.
        CustomerEntity customer5 = new CustomerEntity("Harp", "Walter");
        customer5.setEmail("Walter@contoso.com");
        customer5.setPhoneNumber("425-555-0106");

        // Create an operation to add the new customer to the people table.
        TableOperation insertCustomer5 = TableOperation.insertOrReplace(customer5);

        // Submit the operation to the table service.
        cloudTable.execute(insertCustomer5);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-delete-an-entity"></a>Cómo: eliminar una entidad

Puede eliminar fácilmente una entidad después de que ha recuperado. Una vez que se recupera la entidad, llame a **TableOperation.delete** con la entidad de eliminar. A continuación, llame al **Ejecutar** en el objeto **CloudTable** . El siguiente código recupera y elimina una entidad de cliente.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Create a cloud table object for the table.
        CloudTable cloudTable = tableClient.getTableReference("people");

        // Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
        TableOperation retrieveSmithJeff = TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

        // Retrieve the entity with partition key of "Smith" and row key of "Jeff".
        CustomerEntity entitySmithJeff =
            cloudTable.execute(retrieveSmithJeff).getResultAsType();

        // Create an operation to delete the entity.
        TableOperation deleteSmithJeff = TableOperation.delete(entitySmithJeff);

        // Submit the delete operation to the table service.
        cloudTable.execute(deleteSmithJeff);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-delete-a-table"></a>Cómo: eliminar una tabla

Por último, el siguiente código elimina una tabla de una cuenta de almacenamiento. Una tabla que se ha eliminado no estará disponible para volver a crear durante un período de tiempo después de la eliminación, normalmente menos de 40 segundos.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Delete the table and all its data if it exists.
        CloudTable cloudTable = new CloudTable("people",tableClient);
        cloudTable.deleteIfExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido los conceptos básicos de almacenamiento de tablas, siga estos vínculos para obtener información sobre cómo realizar tareas de almacenamiento más complejas.

- [Almacenamiento de Azure SDK de Java][]
- [Referencia SDK de cliente de almacenamiento de Azure][]
- [API de REST de almacenamiento de Azure][]
- [Blog del equipo de almacenamiento de Azure][]

Para obtener más información, vea también el [Centro para desarrolladores de Java](/develop/java/).


[Azure SDK for Java]: http://go.microsoft.com/fwlink/?LinkID=525671
[Almacenamiento de Azure SDK de Java]: https://github.com/azure/azure-storage-java
[Almacenamiento de Azure SDK para Android]: https://github.com/azure/azure-storage-android
[Referencia SDK de cliente de almacenamiento de Azure]: http://dl.windowsazure.com/storage/javadoc/
[API de REST de almacenamiento de Azure]: https://msdn.microsoft.com/library/azure/dd179355.aspx
[Blog del equipo de almacenamiento de Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[Tablas de Azure: Introducir Upsert y proyección de consultas]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx
