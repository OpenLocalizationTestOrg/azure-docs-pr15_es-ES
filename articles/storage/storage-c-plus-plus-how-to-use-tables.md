<properties
    pageTitle="Cómo usar el almacenamiento de tablas (C++) | Microsoft Azure"
    description="Almacenar datos estructurados en la nube mediante el almacenamiento de tablas de Azure, un almacén datos."
    services="storage"
    documentationCenter=".net"
    authors="dineshmurthy"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="dineshm"/>

# <a name="how-to-use-table-storage-from-c"></a>Cómo usar el almacenamiento de tablas desde C++

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Información general  
Esta guía le mostrará cómo llevar a cabo escenarios comunes mediante el servicio de almacenamiento de la tabla de Azure. Los ejemplos están escritos en C++ y usar la [Biblioteca de cliente de almacenamiento de Azure para C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md). Las situaciones de ejemplo incluyen la **creación y eliminación de una tabla** y **trabajar con entidades de tabla**.

>[AZURE.NOTE] Esta guía está destinado a la biblioteca de cliente de almacenamiento de Azure para C++ versión 1.0.0 y superiores. La versión recomendada es biblioteca de cliente de almacenamiento 2.2.0, que está disponible a través de [NuGet](http://www.nuget.org/packages/wastorage) o [GitHub](https://github.com/Azure/azure-storage-cpp/).

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]
[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]


## <a name="create-a-c-application"></a>Crear una aplicación de C++  
En esta guía, que usa las características de almacenamiento que se pueden ejecutar en una aplicación de C++. Para ello, debe instalar la biblioteca de cliente de almacenamiento de Azure para C++ y cree una cuenta de almacenamiento de Azure en la suscripción de Azure.  

Para instalar la biblioteca de cliente de almacenamiento de Azure para C++, puede usar los métodos siguientes:

-   **Linux:** Siga las instrucciones proporcionadas en la página de la [Biblioteca de cliente de almacenamiento de Azure para C++ Léame](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) .  
-   **Windows:** En Visual Studio, haga clic en **Herramientas > Administrador de paquetes de NuGet > consola del Administrador de paquete**. Escriba el comando siguiente en la [consola del Administrador de paquetes de NuGet](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) y presione ENTRAR.  

        Install-Package wastorage

## <a name="configure-your-application-to-access-table-storage"></a>Configurar la aplicación para tener acceso a almacenamiento de tablas  
Agregue que las siguientes instrucciones a la parte superior del archivo C++ donde desea usar el almacenamiento de Azure API para tener acceso a tablas include:  

    #include "was/storage_account.h"
    #include "was/table.h"

## <a name="set-up-an-azure-storage-connection-string"></a>Configurar una cadena de conexión de almacenamiento de Azure  
Un cliente de almacenamiento de Azure utiliza una cadena de conexión de almacenamiento para almacenar los extremos y las credenciales para acceder a los servicios de administración de datos. Cuando se ejecuta una aplicación de cliente, debe proporcionar la cadena de conexión de almacenamiento en el siguiente formato. Use el nombre de su cuenta de almacenamiento y la tecla de acceso de almacenamiento de la cuenta de almacenamiento aparece en el [Portal de Azure](https://portal.azure.com) para los valores de *nombre de la cuenta* y *AccountKey* . Para obtener información sobre las cuentas de almacenamiento y las teclas de acceso, vea [cuentas de almacenamiento de Azure sobre](storage-create-storage-account.md). Este ejemplo muestra cómo puede declarar un campo estático para almacenar la cadena de conexión:  

    // Define the connection string with your values.
    const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));

Para probar la aplicación en su equipo local basado en Windows, puede usar el Azure [emulador de almacenamiento](storage-use-emulator.md) que se instala con el [SDK de Azure](https://azure.microsoft.com/downloads/). El emulador de almacenamiento es una herramienta que simula los servicios de blobs de Windows Azure, cola y tabla disponibles en el equipo de desarrollo local. En el ejemplo siguiente se muestra cómo puede declarar un campo estático para almacenar la cadena de conexión para su emulador almacenamiento local:  

    // Define the connection string with Azure storage emulator.
    const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  

Para iniciar el emulador de almacenamiento de Azure, haga clic en el botón **Inicio** o presione la tecla de Windows. Empiece a escribir **Emulador de almacenamiento de Azure**y, a continuación, seleccione **Microsoft Azure almacenamiento emulador** desde la lista de aplicaciones.  

Los ejemplos siguientes, se supone que ha utilizado uno de estos dos métodos para obtener la cadena de conexión de almacenamiento.  

## <a name="retrieve-your-connection-string"></a>Recuperar la cadena de conexión  
Puede usar la clase **cloud_storage_account** para representar la información de su cuenta de almacenamiento. Para recuperar información de su cuenta de almacenamiento de la cadena de conexión de almacenamiento, puede usar el método de análisis.

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

A continuación, obtenga una referencia a una clase **cloud_table_client** , que le permite obtener objetos de referencia para las tablas y las entidades almacenadas en el servicio de almacenamiento de la tabla. El código siguiente crea un objeto de **cloud_table_client** mediante el objeto de la cuenta de almacenamiento que se recuperan anteriormente:  

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

## <a name="create-a-table"></a>Crear una tabla
Un objeto **cloud_table_client** le permite obtener objetos de referencia para entidades y tablas. El código siguiente crea un objeto de **cloud_table_client** y usa para crear una nueva tabla.

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);  

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Retrieve a reference to a table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Create the table if it doesn't exist.
    table.create_if_not_exists();  

## <a name="add-an-entity-to-a-table"></a>Agregar una entidad a una tabla
Para agregar una entidad a una tabla, cree un nuevo objeto **table_entity** y pasar a **table_operation::insert_entity**. El código siguiente utiliza el nombre del cliente como la clave de la fila y el apellido como la clave de partición. Juntas, partición de la entidad y la clave de fila para identificar la entidad de la tabla. Pueden consultar más rápido que aquellos con claves de partición diferentes entidades con la misma clave de partición, pero el uso de claves de partición diverso permite mayor escalabilidad de operación en paralelo. Para obtener más información, vea la [lista de comprobación de escalabilidad y rendimiento de almacenamiento de Microsoft Azure](storage-performance-checklist.md).

El código siguiente crea una nueva instancia de **table_entity** con algunos datos de cliente. El código siguiente llama a **table_operation::insert_entity** para crear un objeto **table_operation** para insertar una entidad en una tabla y asocia la nueva tabla de entidad. Por último, el código llama al método execute en el objeto **cloud_table** . Y el nuevo **table_operation** envía una solicitud al servicio de tabla para insertar la nueva entidad de cliente en la tabla "personas".  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Retrieve a reference to a table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Create the table if it doesn't exist.
    table.create_if_not_exists();

    // Create a new customer entity.
    azure::storage::table_entity customer1(U("Harp"), U("Walter"));

    azure::storage::table_entity::properties_type& properties = customer1.properties();
    properties.reserve(2);
    properties[U("Email")] = azure::storage::entity_property(U("Walter@contoso.com"));

    properties[U("Phone")] = azure::storage::entity_property(U("425-555-0101"));

    // Create the table operation that inserts the customer entity.
    azure::storage::table_operation insert_operation = azure::storage::table_operation::insert_entity(customer1);

    // Execute the insert operation.
    azure::storage::table_result insert_result = table.execute(insert_operation);

## <a name="insert-a-batch-of-entities"></a>Insertar un lote de entidades
Puede insertar un lote de entidades al servicio de tabla en una operación de escritura. El código siguiente crea un objeto **table_batch_operation** y, a continuación, agrega que tres insertar operaciones en él. Crear un nuevo objeto entidad, establecer sus valores y, a continuación, llamando al método de insertar en el objeto de **table_batch_operation** para asociar la entidad con una nueva operación de insertar agrega cada operación de inserción. A continuación, se denomina **cloud_table.execute** para ejecutar la operación.  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Define a batch operation.
    azure::storage::table_batch_operation batch_operation;

    // Create a customer entity and add it to the table.
    azure::storage::table_entity customer1(U("Smith"), U("Jeff"));

    azure::storage::table_entity::properties_type& properties1 = customer1.properties();
    properties1.reserve(2);
    properties1[U("Email")] = azure::storage::entity_property(U("Jeff@contoso.com"));
    properties1[U("Phone")] = azure::storage::entity_property(U("425-555-0104"));

    // Create another customer entity and add it to the table.
    azure::storage::table_entity customer2(U("Smith"), U("Ben"));

    azure::storage::table_entity::properties_type& properties2 = customer2.properties();
    properties2.reserve(2);
    properties2[U("Email")] = azure::storage::entity_property(U("Ben@contoso.com"));
    properties2[U("Phone")] = azure::storage::entity_property(U("425-555-0102"));

    // Create a third customer entity to add to the table.
    azure::storage::table_entity customer3(U("Smith"), U("Denise"));

    azure::storage::table_entity::properties_type& properties3 = customer3.properties();
    properties3.reserve(2);
    properties3[U("Email")] = azure::storage::entity_property(U("Denise@contoso.com"));
    properties3[U("Phone")] = azure::storage::entity_property(U("425-555-0103"));

    // Add customer entities to the batch insert operation.
    batch_operation.insert_or_replace_entity(customer1);
    batch_operation.insert_or_replace_entity(customer2);
    batch_operation.insert_or_replace_entity(customer3);

    // Execute the batch operation.
    std::vector<azure::storage::table_result> results = table.execute_batch(batch_operation);

Algunas cosas que debe saber sobre las operaciones por lotes:  

-   Puede realizar hasta 100 Insertar, eliminar, combinar, reemplazar, insertar o combinar y a continuación, insertar o reemplazar operaciones en cualquier combinación en un único lote.  
-   Una operación por lotes puede tener una operación de recuperación, si es la única operación del lote.  
-   Todas las entidades de una única operación por lotes deben tener la misma clave de partición.  
-   Una operación por lotes está limitada a una carga de datos de 4 MB.  

## <a name="retrieve-all-entities-in-a-partition"></a>Recuperar todas las entidades de una partición
Para consultar una tabla para todas las entidades de una partición, use un objeto **table_query** . En el ejemplo siguiente se especifica un filtro para entidades 'García' donde se encuentra la clave de partición. Este ejemplo muestra los campos de cada entidad en los resultados de la consulta en la consola.  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Construct the query operation for all customer entities where PartitionKey="Smith".
    azure::storage::table_query query;

    query.set_filter_string(azure::storage::table_query::generate_filter_condition(U("PartitionKey"), azure::storage::query_comparison_operator::equal, U("Smith")));

    // Execute the query.
    azure::storage::table_query_iterator it = table.execute_query(query);

    // Print the fields for each customer.
    azure::storage::table_query_iterator end_of_results;
    for (; it != end_of_results; ++it)
    {
        const azure::storage::table_entity::properties_type& properties = it->properties();

        std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key()
            << U(", Property1: ") << properties.at(U("Email")).string_value()
            << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
    }  

La consulta en este ejemplo incluye todas las entidades que cumplen los criterios de filtro. Si tiene tablas de gran tamaño y necesitamos para descargar las entidades de tabla a menudo, se recomienda los datos se almacenan en blobs de Azure almacenamiento en su lugar.

## <a name="retrieve-a-range-of-entities-in-a-partition"></a>Recuperar un rango de entidades de una partición
Si no desea que todas las entidades de una partición de la consulta, puede especificar un rango combinando los filtros clave partición con un filtro de clave de fila. En el ejemplo siguiente se usa dos filtros para obtener todas las entidades de partición 'García' donde la clave de fila (nombre) empieza por una letra anterior a 'E' en el alfabeto y, a continuación, imprime los resultados de la consulta.  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Create the table query.
    azure::storage::table_query query;

    query.set_filter_string(azure::storage::table_query::combine_filter_conditions(
        azure::storage::table_query::generate_filter_condition(U("PartitionKey"),
        azure::storage::query_comparison_operator::equal, U("Smith")),
        azure::storage::query_logical_operator::op_and,
        azure::storage::table_query::generate_filter_condition(U("RowKey"), azure::storage::query_comparison_operator::less_than, U("E"))));

    // Execute the query.
    azure::storage::table_query_iterator it = table.execute_query(query);

    // Loop through the results, displaying information about the entity.
    azure::storage::table_query_iterator end_of_results;
    for (; it != end_of_results; ++it)
    {
        const azure::storage::table_entity::properties_type& properties = it->properties();

        std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key()
            << U(", Property1: ") << properties.at(U("Email")).string_value()
            << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
    }  

## <a name="retrieve-a-single-entity"></a>Recuperar una entidad única
Puede escribir una consulta para recuperar una sola entidad específica. El código siguiente utiliza **table_operation::retrieve_entity** para especificar al cliente "Jeff Smith". Este método devuelve una entidad, en lugar de una colección y, a continuación, el valor devuelto es en **table_result**. Especificar teclas partición y fila en una consulta es la manera más rápida de recuperar una sola entidad del servicio de la tabla.  

    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Retrieve the entity with partition key of "Smith" and row key of "Jeff".
    azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
    azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

    // Output the entity.
    azure::storage::table_entity entity = retrieve_result.entity();
    const azure::storage::table_entity::properties_type& properties = entity.properties();

    std::wcout << U("PartitionKey: ") << entity.partition_key() << U(", RowKey: ") << entity.row_key()
        << U(", Property1: ") << properties.at(U("Email")).string_value()
        << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;

## <a name="replace-an-entity"></a>Reemplazar una entidad
Para reemplazar una entidad, recuperar del servicio de la tabla, modifique el objeto de entidad y, a continuación, guardar los cambios al servicio de la tabla. El código siguiente cambia la dirección de correo electrónico y número de teléfono de un cliente existente. En lugar de llamar **table_operation::insert_entity**, este código usa **table_operation::replace_entity**. Esto hace que la entidad de reemplazar completamente en el servidor, a menos que la entidad en el servidor ha cambiado desde que se ha recuperado, en cuyo caso se producirá un error de la operación. Este error es impedir que la aplicación sobrescriba accidentalmente un cambio realizado entre la recuperación y actualización por otro componente de la aplicación. El tratamiento adecuado de este error es recuperar la entidad de nuevo, realice los cambios (si todavía válido) y, a continuación, realizar otra operación **table_operation::replace_entity** . La siguiente sección le mostrará cómo reemplazar este comportamiento.  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Replace an entity.
    azure::storage::table_entity entity_to_replace(U("Smith"), U("Jeff"));
    azure::storage::table_entity::properties_type& properties_to_replace = entity_to_replace.properties();
    properties_to_replace.reserve(2);

    // Specify a new phone number.
    properties_to_replace[U("Phone")] = azure::storage::entity_property(U("425-555-0106"));

    // Specify a new email address.
    properties_to_replace[U("Email")] = azure::storage::entity_property(U("JeffS@contoso.com"));

    // Create an operation to replace the entity.
    azure::storage::table_operation replace_operation = azure::storage::table_operation::replace_entity(entity_to_replace);

    // Submit the operation to the Table service.
    azure::storage::table_result replace_result = table.execute(replace_operation);

## <a name="insert-or-replace-an-entity"></a>Insertar o reemplazar una entidad
operaciones de **table_operation::replace_entity** se producirá un error si se ha cambiado la entidad se ha recuperado desde el servidor. Además, debe recuperar la entidad desde el servidor primero en el orden de **table_operation::replace_entity** sea correcta. A veces, sin embargo, no sabe si la entidad existe en el servidor y los valores actuales almacenados en él están relevante: la actualización debe sobrescribir todas ellas. Para ello, debe usar una operación de **table_operation::insert_or_replace_entity** . Esta operación inserta la entidad si no existe, o reemplaza si es así, independientemente de la que se realizó la última actualización. En el ejemplo siguiente, la entidad de cliente para Jeff Smith aún se recupera, pero a continuación, se guarda al servidor a través de **table_operation::insert_or_replace_entity**. Las actualizaciones realizadas a la entidad entre la recuperación y la operación de actualización se sobrescribirán.  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Insert-or-replace an entity.
    azure::storage::table_entity entity_to_insert_or_replace(U("Smith"), U("Jeff"));
    azure::storage::table_entity::properties_type& properties_to_insert_or_replace = entity_to_insert_or_replace.properties();

    properties_to_insert_or_replace.reserve(2);

    // Specify a phone number.
    properties_to_insert_or_replace[U("Phone")] = azure::storage::entity_property(U("425-555-0107"));

    // Specify an email address.
    properties_to_insert_or_replace[U("Email")] = azure::storage::entity_property(U("Jeffsm@contoso.com"));

    // Create an operation to insert-or-replace the entity.
    azure::storage::table_operation insert_or_replace_operation = azure::storage::table_operation::insert_or_replace_entity(entity_to_insert_or_replace);

    // Submit the operation to the Table service.
    azure::storage::table_result insert_or_replace_result = table.execute(insert_or_replace_operation);

## <a name="query-a-subset-of-entity-properties"></a>Consulta un subconjunto de propiedades de la entidad  
Una consulta a una tabla puede recuperar una serie de propiedades de una entidad. La consulta en el código siguiente usa el método **table_query::set_select_columns** para devolver las direcciones de correo electrónico de entidades en la tabla.  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Define the query, and select only the Email property.
    azure::storage::table_query query;
    std::vector<utility::string_t> columns;

    columns.push_back(U("Email"));
    query.set_select_columns(columns);

    // Execute the query.
    azure::storage::table_query_iterator it = table.execute_query(query);

    // Display the results.
    azure::storage::table_query_iterator end_of_results;
    for (; it != end_of_results; ++it)
    {
        std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key();

        const azure::storage::table_entity::properties_type& properties = it->properties();
        for (auto prop_it = properties.begin(); prop_it != properties.end(); ++prop_it)
        {
            std::wcout << ", " << prop_it->first << ": " << prop_it->second.str();
        }

        std::wcout << std::endl;
    }

>[AZURE.NOTE] Consultar algunas propiedades de una entidad es una operación más eficaz que la recuperación de todas las propiedades.

## <a name="delete-an-entity"></a>Eliminar una entidad
Puede eliminar fácilmente una entidad después de que ha recuperado. Una vez que se recupera la entidad, llame a **table_operation::delete_entity** con la entidad de eliminar. A continuación, llame al método **cloud_table.execute** . El siguiente código recupera y elimina una entidad con una clave de partición de "Gracia" y una clave de la fila de "Juan".  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
    azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
    azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

    // Create an operation to delete the entity.
    azure::storage::table_operation delete_operation = azure::storage::table_operation::delete_entity(retrieve_result.entity());

    // Submit the delete operation to the Table service.
    azure::storage::table_result delete_result = table.execute(delete_operation);  

## <a name="delete-a-table"></a>Eliminar una tabla
Por último, en el ejemplo siguiente se elimina una tabla de una cuenta de almacenamiento. Una tabla que se ha eliminado no estará disponible para volver a crearse durante un período de tiempo después de la eliminación.  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
    azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
    azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

    // Create an operation to delete the entity.
    azure::storage::table_operation delete_operation = azure::storage::table_operation::delete_entity(retrieve_result.entity());

    // Submit the delete operation to the Table service.
    azure::storage::table_result delete_result = table.execute(delete_operation);

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha aprendido los conceptos básicos de almacenamiento de tablas, siga estos vínculos para obtener más información sobre el almacenamiento de Azure:  

-   [Cómo usar el almacenamiento de blobs de C++](storage-c-plus-plus-how-to-use-blobs.md)
-   [Cómo usar el almacenamiento de la cola de C++](storage-c-plus-plus-how-to-use-queues.md)
-   [Lista de recursos de almacenamiento de Azure en C++](storage-c-plus-plus-enumeration.md)
-   [Biblioteca de cliente de almacenamiento para referencia de C++](http://azure.github.io/azure-storage-cpp)
-   [Documentación de almacenamiento de Azure](https://azure.microsoft.com/documentation/services/storage/)
