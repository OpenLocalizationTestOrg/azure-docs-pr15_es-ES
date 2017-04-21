<properties
    pageTitle="Cómo usar el almacenamiento de tablas de PHP | Microsoft Azure"
    description="Obtenga información sobre cómo usar el servicio de la tabla de PHP para crear y eliminar una tabla, insertar, eliminar y consultar la tabla."
    services="storage"
    documentationCenter="php"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="php"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>


# <a name="how-to-use-table-storage-from-php"></a>Cómo usar el almacenamiento de tablas de PHP

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Información general

Esta guía le muestra cómo llevar a cabo escenarios comunes mediante el servicio de la tabla de Azure. Los ejemplos se escriben en PHP y usar el [SDK de Azure para PHP][download]. Los escenarios cubiertos incluyen **crear y eliminar una tabla e Insertar, eliminar y consulta de entidades en una tabla**. Para obtener más información sobre el servicio de la tabla de Azure, consulte la sección [pasos siguientes](#next-steps) .

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-php-application"></a>Crear una aplicación PHP

El único requisito para crear una aplicación de PHP que tiene acceso al servicio de la tabla de Azure es la referencia de clases en el SDK de Azure para PHP desde dentro del código. Puede usar las herramientas de desarrollo para crear la aplicación, incluyendo el Bloc de notas.

En esta guía, utilice las características de servicio de tabla que se pueden llamar desde dentro de una aplicación PHP localmente o en el código que se ejecuta dentro de una función de Azure web, trabajo o sitio Web.

## <a name="get-the-azure-client-libraries"></a>Obtener las bibliotecas de cliente de Azure

[AZURE.INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-access-the-table-service"></a>Configurar la aplicación para tener acceso al servicio de tabla

Para usar las API del servicio de tabla de Azure, debe:

1. Hacer referencia al archivo cargador automático mediante la [require_once] [ require_once] instrucción, y
2. Hacer referencia a las clases que puede usar.

En el ejemplo siguiente se muestra cómo incluir el archivo de cargador automático y hacer referencia a la clase **ServicesBuilder** .

> [AZURE.NOTE] En este ejemplo (y otros ejemplos de este artículo) se suponen que ha instalado las bibliotecas de cliente de PHP para Azure a través de compositor. Si ha instalado las bibliotecas de forma manual, debe hacer referencia a la <code>WindowsAzure.php</code> archivo cargador automático.

    require_once 'vendor/autoload.php';
    use WindowsAzure\Common\ServicesBuilder;


En los ejemplos siguientes, el `require_once` siempre se muestra la instrucción, pero solo las clases necesarias para ejecutar el ejemplo se hace referencia.

## <a name="set-up-an-azure-storage-connection"></a>Configurar una conexión de almacenamiento de Azure

Para crear una instancia de un cliente de servicios de la tabla de Azure, primero debe tener una cadena de conexión válida. El formato de la cadena de conexión de servicio de tabla es:

Para obtener acceso a un servicio live:

    DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]

Para obtener acceso al almacenamiento de emulador:

    UseDevelopmentStorage=true


Para crear a un cliente de servicio de Azure, debe usar la clase **ServicesBuilder** . Puedes:

* pase la cadena de conexión directamente a él o
* Utilice la **CloudConfigurationManager (CCM)** para comprobar varios orígenes externos de la cadena de conexión:
    * de forma predeterminada, se suministra con soporte para un origen externo - variables de entorno
    * Puede agregar nuevos orígenes de ampliar la clase **ConnectionStringSource**

Para los ejemplos descritos a continuación, se pasará directamente la cadena de conexión.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;

    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);


## <a name="create-a-table"></a>Crear una tabla

Un objeto **TableRestProxy** le permite crear una tabla con el método **createTable** . Al crear una tabla, puede establecer el tiempo de espera del servicio de tabla. (Para obtener más información sobre el tiempo de espera del servicio de tabla, vea el [Tiempo de espera de configuración para operaciones de servicio de tabla][table-service-timeouts].)

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    try {
        // Create table.
        $tableRestProxy->createTable("mytable");
    }
    catch(ServiceException $e){
        $code = $e->getCode();
        $error_message = $e->getMessage();
        // Handle exception based on error codes and messages.
        // Error codes and messages can be found here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
    }

Para obtener información acerca de las restricciones en los nombres de tabla, consulte [Descripción del modelo de datos de servicio de tabla][table-data-model].

## <a name="add-an-entity-to-a-table"></a>Agregar una entidad a una tabla

Para agregar una entidad a una tabla, cree un nuevo objeto **entidad** y pasar a **TableRestProxy -> insertEntity**. Tenga en cuenta que cuando se crea una entidad, debe especificar un `PartitionKey` y `RowKey`. Estos son los identificadores únicos de una entidad y los valores que se pueden consultar mucho más rápido que otras propiedades de la entidad. El sistema utiliza `PartitionKey` a distribuir automáticamente las entidades de la tabla en muchos de los nodos de almacenamiento. Entidades con el mismo `PartitionKey` se almacenan en el mismo nodo. (Realizan operaciones en varias entidades almacenadas en el mismo nodo superior a la de entidades almacenadas en distintos nodos.) La `RowKey` es el identificador único de una entidad dentro de una partición.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;
    use MicrosoftAzure\Storage\Table\Models\Entity;
    use MicrosoftAzure\Storage\Table\Models\EdmType;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    $entity = new Entity();
    $entity->setPartitionKey("tasksSeattle");
    $entity->setRowKey("1");
    $entity->addProperty("Description", null, "Take out the trash.");
    $entity->addProperty("DueDate",
                         EdmType::DATETIME,
                         new DateTime("2012-11-05T08:15:00-08:00"));
    $entity->addProperty("Location", EdmType::STRING, "Home");

    try{
        $tableRestProxy->insertEntity("mytable", $entity);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
    }

Para obtener información sobre tipos y propiedades de tabla, consulte [Descripción del modelo de datos de servicio de tabla][table-data-model].

La clase **TableRestProxy** ofrece dos métodos alternativos para insertar entidades: **insertOrMergeEntity** y **insertOrReplaceEntity**. Para usar estos métodos, cree una nueva **entidad** y pasar como parámetro a cualquiera de los métodos. Si no existe, cada método insertará la entidad. Si ya existe la entidad, **insertOrMergeEntity** actualiza los valores de propiedad si ya existen las propiedades y agrega nuevas propiedades si no existen, mientras que **insertOrReplaceEntity** reemplaza completamente una entidad existente. En el ejemplo siguiente se muestra cómo usar **insertOrMergeEntity**. Si la entidad con `PartitionKey` "tasksSeattle" y `RowKey` ya no existe "1", se insertarán. Sin embargo, si previamente se ha insertado (como se muestra en el ejemplo anterior), el `DueDate` propiedad se actualizará y la `Status` se agregará la propiedad. La `Description` y `Location` propiedades también se actualizan, pero con valores que eficaz permanecen sin cambios. Si estas dos últimas propiedades no se han agregado como se muestra en el ejemplo, pero existía en la entidad de destino, se modifican sus valores existentes.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;
    use MicrosoftAzure\Storage\Table\Models\Entity;
    use MicrosoftAzure\Storage\Table\Models\EdmType;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    //Create new entity.
    $entity = new Entity();

    // PartitionKey and RowKey are required.
    $entity->setPartitionKey("tasksSeattle");
    $entity->setRowKey("1");

    // If entity exists, existing properties are updated with new values and
    // new properties are added. Missing properties are unchanged.
    $entity->addProperty("Description", null, "Take out the trash.");
    $entity->addProperty("DueDate", EdmType::DATETIME, new DateTime()); // Modified the DueDate field.
    $entity->addProperty("Location", EdmType::STRING, "Home");
    $entity->addProperty("Status", EdmType::STRING, "Complete"); // Added Status field.

    try {
        // Calling insertOrReplaceEntity, instead of insertOrMergeEntity as shown,
        // would simply replace the entity with PartitionKey "tasksSeattle" and RowKey "1".
        $tableRestProxy->insertOrMergeEntity("mytable", $entity);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }


## <a name="retrieve-a-single-entity"></a>Recuperar una entidad única

El método **TableRestProxy -> getEntity** permite recuperar una sola entidad puede consultar para su `PartitionKey` y `RowKey`. En el ejemplo siguiente, la clave de partición `tasksSeattle` y la clave de la fila `1` se pasan al método **getEntity** .

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    try {
        $result = $tableRestProxy->getEntity("mytable", "tasksSeattle", 1);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

    $entity = $result->getEntity();

    echo $entity->getPartitionKey().":".$entity->getRowKey();

## <a name="retrieve-all-entities-in-a-partition"></a>Recuperar todas las entidades de una partición

Consultas de entidad se crean mediante filtros (para obtener más información, vea [realizar consultas tablas y entidades][filters]). Para recuperar todas las entidades de partición, use el filtro "PartitionKey EC *partición*". En el ejemplo siguiente se muestra cómo recuperar todas las entidades de la `tasksSeattle` partición pasando un filtro para el método **queryEntities** .

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    $filter = "PartitionKey eq 'tasksSeattle'";

    try {
        $result = $tableRestProxy->queryEntities("mytable", $filter);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

    $entities = $result->getEntities();

    foreach($entities as $entity){
        echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
    }

## <a name="retrieve-a-subset-of-entities-in-a-partition"></a>Recuperar un subconjunto de entidades de una partición

El mismo patrón que se usan en el ejemplo anterior se puede usar para recuperar cualquier subconjunto de entidades de una partición. El filtro usa determinan el subconjunto de entidades recupera (para obtener más información, vea [realizar consultas tablas y entidades][filters]). En el ejemplo siguiente se muestra cómo usar un filtro para recuperar todas las entidades con un determinado `Location` y un `DueDate` inferior a una fecha especificada.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    $filter = "Location eq 'Office' and DueDate lt '2012-11-5'";

    try {
        $result = $tableRestProxy->queryEntities("mytable", $filter);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

    $entities = $result->getEntities();

    foreach($entities as $entity){
        echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
    }

## <a name="retrieve-a-subset-of-entity-properties"></a>Recuperar un subconjunto de propiedades de la entidad

Una consulta puede recuperar un subconjunto de propiedades de la entidad. Esta técnica, denominada *proyección*, reduce el ancho de banda y puede mejorar el rendimiento de la consulta, especialmente para entidades de gran tamaño. Para especificar una propiedad para recuperar, pase el nombre de la propiedad al método **consulta -> addSelectField** . Puede llamar a este método varias veces para agregar más propiedades. Después de ejecutar **TableRestProxy -> queryEntities**, las entidades devueltas solo tendrá las propiedades seleccionadas. (Si desea devolver un subconjunto de entidades de tabla, use un filtro como se muestra en las consultas anteriores.)

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;
    use MicrosoftAzure\Storage\Table\Models\QueryEntitiesOptions;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    $options = new QueryEntitiesOptions();
    $options->addSelectField("Description");

    try {
        $result = $tableRestProxy->queryEntities("mytable", $options);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

    // All entities in the table are returned, regardless of whether
    // they have the Description field.
    // To limit the results returned, use a filter.
    $entities = $result->getEntities();

    foreach($entities as $entity){
        $description = $entity->getProperty("Description")->getValue();
        echo $description."<br />";
    }

## <a name="update-an-entity"></a>Actualizar una entidad

Mediante los métodos de **entidad -> DefinirPropiedad** y **entidad -> addProperty** de la entidad y, a continuación, llamando **TableRestProxy -> updateEntity**se puede actualizar una entidad existente. En el ejemplo siguiente se recupera una entidad, modifica una propiedad, quita otra propiedad y agrega una nueva propiedad. Tenga en cuenta que puede eliminar una propiedad estableciendo su valor en **null**.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;
    use MicrosoftAzure\Storage\Table\Models\Entity;
    use MicrosoftAzure\Storage\Table\Models\EdmType;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    $result = $tableRestProxy->getEntity("mytable", "tasksSeattle", 1);

    $entity = $result->getEntity();

    $entity->setPropertyValue("DueDate", new DateTime()); //Modified DueDate.

    $entity->setPropertyValue("Location", null); //Removed Location.

    $entity->addProperty("Status", EdmType::STRING, "In progress"); //Added Status.

    try {
        $tableRestProxy->updateEntity("mytable", $entity);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <a name="delete-an-entity"></a>Eliminar una entidad

Para eliminar una entidad, pase el nombre de tabla y a continuación, la entidad `PartitionKey` y `RowKey` al método **TableRestProxy -> deleteEntity** .

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    try {
        // Delete entity.
        $tableRestProxy->deleteEntity("mytable", "tasksSeattle", "2");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Tenga en cuenta que las comprobaciones de simultaneidad, puede establecer Etag de una entidad se eliminarán del método **DeleteEntityOptions -> setEtag** y pasando el objeto de **DeleteEntityOptions** a **deleteEntity** como un cuarto parámetro.

## <a name="batch-table-operations"></a>Operaciones de tabla por lotes

El método **TableRestProxy -> lote** le permite ejecutar varias operaciones en una sola solicitud. El modelo aquí consiste en agregar operaciones **BatchRequest** objeto y, a continuación, pase el objeto de **BatchRequest** al método **TableRestProxy -> lote** . Para agregar una operación a un objeto **BatchRequest** , puede llamar cualquiera de los métodos siguientes varias veces:

* **addInsertEntity** (agrega una operación de insertEntity)
* **addUpdateEntity** (agrega una operación de updateEntity)
* **addMergeEntity** (agrega una operación de mergeEntity)
* **addInsertOrReplaceEntity** (agrega una operación de insertOrReplaceEntity)
* **addInsertOrMergeEntity** (agrega una operación de insertOrMergeEntity)
* **addDeleteEntity** (agrega una operación de deleteEntity)

En el ejemplo siguiente se muestra cómo ejecutar las operaciones de **insertEntity** y **deleteEntity** en una sola solicitud:

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;
    use MicrosoftAzure\Storage\Table\Models\Entity;
    use MicrosoftAzure\Storage\Table\Models\EdmType;
    use MicrosoftAzure\Storage\Table\Models\BatchOperations;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    // Create list of batch operation.
    $operations = new BatchOperations();

    $entity1 = new Entity();
    $entity1->setPartitionKey("tasksSeattle");
    $entity1->setRowKey("2");
    $entity1->addProperty("Description", null, "Clean roof gutters.");
    $entity1->addProperty("DueDate",
                          EdmType::DATETIME,
                          new DateTime("2012-11-05T08:15:00-08:00"));
    $entity1->addProperty("Location", EdmType::STRING, "Home");

    // Add operation to list of batch operations.
    $operations->addInsertEntity("mytable", $entity1);

    // Add operation to list of batch operations.
    $operations->addDeleteEntity("mytable", "tasksSeattle", "1");

    try {
        $tableRestProxy->batch($operations);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Para obtener más información acerca de las operaciones de tabla por lotes, vea [Realizar transacciones de grupo de entidad][entity-group-transactions].

## <a name="delete-a-table"></a>Eliminar una tabla

Por último, para eliminar una tabla, pase el nombre de tabla para el método **TableRestProxy -> deleteTable** .

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    try {
        // Delete table.
        $tableRestProxy->deleteTable("mytable");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido los conceptos básicos del servicio de tabla de Azure, siga estos vínculos para obtener información sobre las tareas de almacenamiento más complejas.

- Visite el [blog del equipo de almacenamiento de Azure](http://blogs.msdn.com/b/windowsazurestorage/)

Para obtener más información, vea también el [Centro para desarrolladores de PHP](/develop/php/).

[download]: http://go.microsoft.com/fwlink/?LinkID=252473
[require_once]: http://php.net/require_once
[table-service-timeouts]: http://msdn.microsoft.com/library/azure/dd894042.aspx

[table-data-model]: http://msdn.microsoft.com/library/azure/dd179338.aspx
[filters]: http://msdn.microsoft.com/library/azure/dd894031.aspx
[entity-group-transactions]: http://msdn.microsoft.com/library/azure/dd894038.aspx
