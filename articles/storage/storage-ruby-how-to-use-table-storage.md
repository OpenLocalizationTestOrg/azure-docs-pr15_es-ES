<properties
    pageTitle="Cómo usar el almacenamiento de tablas de Azure de rubí | Microsoft Azure"
    description="Almacenar datos estructurados en la nube mediante el almacenamiento de tablas de Azure, un almacén datos."
    services="storage"
    documentationCenter="ruby"
    authors="tamram"
    manager="carmonm"
    editor=""/>
<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="ruby"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>


# <a name="how-to-use-azure-table-storage-from-ruby"></a>Cómo usar el almacenamiento de tablas de Azure de rubí

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Información general

Esta guía le muestra cómo llevar a cabo escenarios comunes mediante el servicio de la tabla de Azure. Los ejemplos se escriben mediante la API de Ruby. Los escenarios cubiertos incluyen **crear y eliminar una tabla, insertar y consulta de entidades en una tabla**.

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Crear una aplicación de ideogramas y transcripción fonética

Para obtener instrucciones sobre cómo crear una aplicación de ideogramas y transcripción fonética, vea [Ruby en la aplicación Web de guías en una máquina virtual de Azure](../virtual-machines/linux/classic/virtual-machines-linux-classic-ruby-rails-web-app.md).


## <a name="configure-your-application-to-access-storage"></a>Configurar la aplicación para tener acceso al almacenamiento

Para usar el almacenamiento de Azure, debe descargar y usar el paquete de azure ideogramas y transcripción fonética que incluye un conjunto de bibliotecas de comodidad que comunicarse con los servicios del resto de almacenamiento.

### <a name="use-rubygems-to-obtain-the-package"></a>Usar RubyGems para obtener el paquete

1. Use una interfaz de línea de comandos como **PowerShell** (Windows), **Terminal** (Mac) o **Bash** (Unix).

2. Escriba el **indicador instalar azure** en la ventana de comandos para instalar el indicador y dependencias.

### <a name="import-the-package"></a>Importar el paquete

Usar el editor de texto, agregue lo siguiente a la parte superior del archivo Ruby donde vaya a usar el almacenamiento:

    require "azure"

## <a name="set-up-an-azure-storage-connection"></a>Configurar una conexión de almacenamiento de Azure

El módulo azure leerá las variables de entorno **AZURE\_almacenamiento\_cuenta** y **AZURE\_almacenamiento\_ACCESS\_clave** información necesaria para conectarse a su cuenta de almacenamiento de Azure. Si no se establecen estas variables de entorno, debe especificar la información de cuenta antes de utilizar **Azure::TableService** con el siguiente código:

    Azure.config.storage_account_name = "<your azure storage account>"
    Azure.config.storage_access_key = "<your azure storage access key>"

Para obtener los valores de un clásico o cuenta de almacenamiento de administrador de recursos en el Portal de Azure:

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Vaya a la cuenta de almacenamiento que desee usar.
3. En el módulo de configuración de la derecha, haga clic en **Las teclas de acceso**.
4. En el módulo de teclas de acceso que aparece, verá la tecla de acceso 1 y 2 de tecla de acceso. Puede usar cualquiera de estos. 
5. Haga clic en el icono de copiar para copiar la clave en el Portapapeles. 

Para obtener los valores de una cuenta de almacenamiento clásico en el portal de Azure clásica:

1. Inicie sesión en el [portal de Azure clásico](https://manage.windowsazure.com).
2. Vaya a la cuenta de almacenamiento que desee usar.
3. Haga clic en **Administrar las teclas de acceso** en la parte inferior del panel de navegación.
4. En el cuadro de diálogo emergente, verá el nombre de la cuenta de almacenamiento, la tecla de acceso principal y la clave de acceso secundario. De tecla de acceso, puede usar principal o el elemento secundario. 
5. Haga clic en el icono de copiar para copiar la clave en el Portapapeles.

## <a name="create-a-table"></a>Crear una tabla

El objeto **Azure::TableService** le permite trabajar con tablas y entidades. Para crear una tabla, use la **crear\_table()** método. En el ejemplo siguiente se crea una tabla o se imprime el error si hay alguna.

    azure_table_service = Azure::TableService.new
    begin
      azure_table_service.create_table("testtable")
    rescue
      puts $!
    end

## <a name="add-an-entity-to-a-table"></a>Agregar una entidad a una tabla

Para agregar una entidad, cree primero un objeto de hash que define las propiedades de la entidad. Tenga en cuenta que, para cada entidad debe especificar un **PartitionKey** y **RowKey**. Estos son los identificadores únicos de las entidades y son valores que se pueden consultar más rápidas que las otras propiedades. Almacenamiento de Azure usa **PartitionKey** para distribuir automáticamente las entidades de la tabla en muchos de los nodos de almacenamiento. Entidades con el mismo **PartitionKey** se almacenan en el mismo nodo. El **RowKey** es el identificador único de la entidad dentro de la partición a la que pertenece.

    entity = { "content" => "test entity",
      :PartitionKey => "test-partition-key", :RowKey => "1" }
    azure_table_service.insert_entity("testtable", entity)

## <a name="update-an-entity"></a>Actualizar una entidad

Hay varios métodos disponibles para actualizar una entidad existente:

* **actualizar\_entity():** Actualizar una entidad existente reemplazándola.
* **combinación\_entity():** Actualiza una entidad existente mediante la combinación de nuevos valores de propiedad de la entidad existente.
* **Insertar\_o\_combinación\_entity():** Actualiza una entidad existente reemplazarlo. Si no existe ninguna entidad, se insertará una nueva:
* **Insertar\_o\_reemplazar\_entity():** Actualiza una entidad existente mediante la combinación de nuevos valores de propiedad de la entidad existente. Si no existe ninguna entidad, se insertará una nueva.

En el ejemplo siguiente se muestra la actualización de una entidad con **actualizar\_entity()**:

    entity = { "content" => "test entity with updated content",
      :PartitionKey => "test-partition-key", :RowKey => "1" }
    azure_table_service.update_entity("testtable", entity)

Con **actualizar\_entity()** y **combinación\_entity()**, si no existe la entidad que esté actualizando, a continuación, se producirá un error en la operación de actualización. Por lo tanto, si desea almacenar una entidad independientemente de si ya existe, se debe utilizar **Insertar\_o\_reemplazar\_entity()** o **Insertar\_o\_combinación\_entity()**.

## <a name="work-with-groups-of-entities"></a>Trabajar con grupos de entidades

A veces tiene sentido varias operaciones juntos en un lote para garantizar atómica procesamiento por el servidor de envío. Para lograr que, se crea por primera vez un objeto de **lote** y, a continuación, use la **ejecutar\_batch()** método en **TableService**. En el ejemplo siguiente se muestra cómo enviar dos entidades con RowKey 2 y 3 en un lote. Observe que solo funciona para entidades con el mismo PartitionKey.

    azure_table_service = Azure::TableService.new
    batch = Azure::Storage::Table::Batch.new("testtable",
      "test-partition-key") do
      insert "2", { "content" => "new content 2" }
      insert "3", { "content" => "new content 3" }
    end
    results = azure_table_service.execute_batch(batch)

## <a name="query-for-an-entity"></a>Consulta de una entidad

Para consultar una entidad en una tabla, utilice la **obtener\_entity()** método, pasando el nombre de tabla, **PartitionKey** y **RowKey**.

    result = azure_table_service.get_entity("testtable", "test-partition-key",
      "1")

## <a name="query-a-set-of-entities"></a>Un conjunto de entidades de la consulta

Para consultar un conjunto de entidades en una tabla, cree un objeto de hash de consulta y use la **consulta\_entities()** método. En el ejemplo siguiente se muestra cómo obtener todas las entidades con el mismo **PartitionKey**:

    query = { :filter => "PartitionKey eq 'test-partition-key'" }
    result, token = azure_table_service.query_entities("testtable", query)

> [AZURE.NOTE] Si el resultado es demasiado grande para una única consulta devolver, se devolverá un token de continuación que puede usar para recuperar las páginas siguientes.

## <a name="query-a-subset-of-entity-properties"></a>Consulta un subconjunto de propiedades de la entidad

Una consulta a una tabla puede recuperar una serie de propiedades de una entidad. Esta técnica, denominada "proyección", reduce el ancho de banda y puede mejorar el rendimiento de la consulta, especialmente para entidades de gran tamaño. Utilice la cláusula select y pase los nombres de las propiedades que le gustaría para que aparezca el cliente.

    query = { :filter => "PartitionKey eq 'test-partition-key'",
      :select => ["content"] }
    result, token = azure_table_service.query_entities("testtable", query)

## <a name="delete-an-entity"></a>Eliminar una entidad

Para eliminar una entidad, use la **Eliminar\_entity()** método. Debe pasar el nombre de la tabla que contiene la entidad, la PartitionKey y RowKey de la entidad.

        azure_table_service.delete_entity("testtable", "test-partition-key", "1")

## <a name="delete-a-table"></a>Eliminar una tabla

Para eliminar una tabla, use la **Eliminar\_table()** método y pase el nombre de la tabla que desea eliminar.

        azure_table_service.delete_table("testtable")

## <a name="next-steps"></a>Pasos siguientes

Para obtener información sobre las tareas más complejas de almacenamiento, siga estos vínculos:

- [Blog del equipo de almacenamiento de Azure](http://blogs.msdn.com/b/windowsazurestorage/)
- Repositorio de [Azure SDK de rubí](http://github.com/WindowsAzure/azure-sdk-for-ruby) en GitHub
