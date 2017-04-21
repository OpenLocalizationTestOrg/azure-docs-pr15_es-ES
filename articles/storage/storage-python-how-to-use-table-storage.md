<properties
    pageTitle="Cómo usar el almacenamiento de tablas de Python | Microsoft Azure"
    description="Almacenar datos estructurados en la nube mediante el almacenamiento de tablas de Azure, un almacén datos."
    services="storage"
    documentationCenter="python"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>


# <a name="how-to-use-table-storage-from-python"></a>Cómo usar el almacenamiento de tablas de Python

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Información general

Esta guía le muestra cómo llevar a cabo escenarios comunes mediante el servicio de almacenamiento de la tabla de Azure. Los ejemplos se escriben en Python y utilizan el [SDK de Microsoft Azure almacenamiento de Python]. Los escenarios cubiertos incluyen la creación y eliminación de una tabla, además de inserción y la consulta de entidades en una tabla.

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-table"></a>Crear una tabla

El objeto **TableService** le permite trabajar con los servicios de la tabla. El código siguiente crea un objeto **TableService** . Agregue el código cerca de la parte superior de cualquier archivo Python en la que desea tener acceso mediante programación el almacenamiento de Azure:

    from azure.storage.table import TableService, Entity

El código siguiente crea un objeto de **TableService** mediante la clave de nombre y la cuenta de la cuenta de almacenamiento.  Reemplazar 'myaccount' y 'MiClave' con el nombre de la cuenta y la clave.

    table_service = TableService(account_name='myaccount', account_key='mykey')

    table_service.create_table('tasktable')

## <a name="add-an-entity-to-a-table"></a>Agregar una entidad a una tabla

Para agregar una entidad, cree primero un diccionario o entidad que define los nombres de propiedad de entidad y los valores. Tenga en cuenta que, para cada entidad, debe especificar **PartitionKey** y **RowKey**. Estos son los identificadores únicos de las entidades. Puede crear una consulta con estos valores con mayor rapidez puede consultar sus otras propiedades. El sistema usa **PartitionKey** para distribuir automáticamente las entidades de tabla en muchos de los nodos de almacenamiento.
Entidades que tienen el mismo **PartitionKey** se almacenan en el mismo nodo. **RowKey** es el identificador único de la entidad dentro de la partición a la que pertenece.

Para agregar una entidad a la tabla, pasa un objeto de diccionario para el **Insertar\_entidad** método.

    task = {'PartitionKey': 'tasksSeattle', 'RowKey': '1', 'description' : 'Take out the trash', 'priority' : 200}
    table_service.insert_entity('tasktable', task)

También puede pasar una instancia de la clase de **entidad** para el **Insertar\_entidad** método.

    task = Entity()
    task.PartitionKey = 'tasksSeattle'
    task.RowKey = '2'
    task.description = 'Wash the car'
    task.priority = 100
    table_service.insert_entity('tasktable', task)

## <a name="update-an-entity"></a>Actualizar una entidad

Este código muestra cómo reemplazar la versión anterior de una entidad existente con una versión actualizada.

    task = {'PartitionKey': 'tasksSeattle', 'RowKey': '1', 'description' : 'Take out the garbage', 'priority' : 250}
    table_service.update_entity('tasktable', 'tasksSeattle', '1', task, content_type='application/atom+xml')

Si no existe la entidad que está actualizando, se producirá un error en la operación de actualización. Si desea almacenar una entidad independientemente de si existía antes, use **Insertar\_o\_replace_entity**.
En el ejemplo siguiente, la primera llamada reemplazará la entidad existente. La segunda llamada inserte una nueva entidad, desde ninguna entidad con el especificado **PartitionKey** y **RowKey** existe en la tabla.

    task = {'PartitionKey': 'tasksSeattle', 'RowKey': '1', 'description' : 'Take out the garbage again', 'priority' : 250}
    table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '1', task, content_type='application/atom+xml')

    task = {'PartitionKey': 'tasksSeattle', 'RowKey': '3', 'description' : 'Buy detergent', 'priority' : 300}
    table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '1', task, content_type='application/atom+xml')

## <a name="change-a-group-of-entities"></a>Cambiar un grupo de entidades

A veces tiene sentido varias operaciones juntos en un lote para garantizar atómica procesamiento por el servidor de envío. Para ello, use la clase **TableBatch** . Si desea enviar el lote, llame a **ejecutar\_por lotes**. Tenga en cuenta que todas las entidades deben estar en la misma partición para cambiar de un lote. El ejemplo siguiente suma dos entidades en un lote.

    from azure.storage.table import TableBatch
    batch = TableBatch()
    task10 = {'PartitionKey': 'tasksSeattle', 'RowKey': '10', 'description' : 'Go grocery shopping', 'priority' : 400}
    task11 = {'PartitionKey': 'tasksSeattle', 'RowKey': '11', 'description' : 'Clean the bathroom', 'priority' : 100}
    batch.insert_entity(task10)
    batch.insert_entity(task11)
    table_service.commit_batch('tasktable', batch)

Lotes también pueden utilizarse con la sintaxis de administrador de contexto:

    task12 = {'PartitionKey': 'tasksSeattle', 'RowKey': '12', 'description' : 'Go grocery shopping', 'priority' : 400}
    task13 = {'PartitionKey': 'tasksSeattle', 'RowKey': '13', 'description' : 'Clean the bathroom', 'priority' : 100}

    with table_service.batch('tasktable') as batch:
        batch.insert_entity(task12)
        batch.insert_entity(task13)


## <a name="query-for-an-entity"></a>Consulta de una entidad

Para consultar una entidad en una tabla, utilice la **obtener\_entidad** método pasando **PartitionKey** y **RowKey**.

    task = table_service.get_entity('tasktable', 'tasksSeattle', '1')
    print(task.description)
    print(task.priority)

## <a name="query-a-set-of-entities"></a>Un conjunto de entidades de la consulta

Este ejemplo busca que todas las tareas de Seattle basan en **PartitionKey**.

    tasks = table_service.query_entities('tasktable', filter="PartitionKey eq 'tasksSeattle'")
    for task in tasks:
        print(task.description)
        print(task.priority)

## <a name="query-a-subset-of-entity-properties"></a>Consulta un subconjunto de propiedades de la entidad

Una consulta a una tabla puede recuperar una serie de propiedades de una entidad.
Esta técnica, denominada *proyección*, reduce el ancho de banda y puede mejorar el rendimiento de la consulta, especialmente para entidades de gran tamaño. Usar el parámetro **Seleccione** y pase los nombres de las propiedades que desee que aparezca el cliente.

La consulta en el siguiente código devuelve sólo las descripciones de entidades de la tabla.

[AZURE.NOTE] El fragmento de código siguiente funciona sólo con el servicio de almacenamiento de la nube. Esto no es compatible con el emulador de almacenamiento.

    tasks = table_service.query_entities('tasktable', filter="PartitionKey eq 'tasksSeattle'", select='description')
    for task in tasks:
        print(task.description)

## <a name="delete-an-entity"></a>Eliminar una entidad

Puede eliminar una entidad mediante su clave de partición y fila.

    table_service.delete_entity('tasktable', 'tasksSeattle', '1')

## <a name="delete-a-table"></a>Eliminar una tabla

El código siguiente elimina una tabla de una cuenta de almacenamiento.

    table_service.delete_table('tasktable')

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido los conceptos básicos de almacenamiento de tablas, siga estos vínculos para obtener más información.

- [Centro para desarrolladores de Python](/develop/python/)
- [API de REST de servicios de almacenamiento de Azure](http://msdn.microsoft.com/library/azure/dd179355)
- [Blog del equipo de almacenamiento de Azure]
- [Almacenamiento de Microsoft Azure SDK para Python]

[Blog del equipo de almacenamiento de Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[Almacenamiento de Microsoft Azure SDK para Python]: https://github.com/Azure/azure-storage-python
