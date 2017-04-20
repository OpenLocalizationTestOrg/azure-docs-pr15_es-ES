<properties 
    pageTitle="Cómo usar el almacenamiento de tablas Azure con el SDK WebJobs" 
    description="Obtenga información sobre cómo usar el almacenamiento de tablas Azure con el SDK de WebJobs. Crear tablas, agregar entidades a tablas y lea las tablas existentes." 
    services="app-service\web, storage" 
    documentationCenter=".net" 
    authors="tdykstra" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="06/01/2016" 
    ms.author="tdykstra"/>

# <a name="how-to-use-azure-table-storage-with-the-webjobs-sdk"></a>Cómo usar el almacenamiento de tablas Azure con el SDK WebJobs

## <a name="overview"></a>Información general

Esta guía proporciona ejemplos de código de C# que muestran cómo leer y escribir tablas de almacenamiento de Azure con [WebJobs SDK](websites-dotnet-webjobs-sdk.md) versión 1.x.

La guía se supone que sabe [cómo crear un proyecto WebJob en Visual Studio con las cadenas de conexión que apunten a su cuenta de almacenamiento](websites-dotnet-webjobs-sdk-get-started.md) o a [varias cuentas de almacenamiento](https://github.com/Azure/azure-webjobs-sdk/blob/master/test/Microsoft.Azure.WebJobs.Host.EndToEndTests/MultipleStorageAccountsEndToEndTests.cs).
        
Algunos de los fragmentos de código muestran la `Table` atributo utilizada en las funciones [llamar manualmente](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#manual), es decir, no son mediante uno de los atributos de desencadenador. 

## <a id="ingress"></a>Cómo agregar entidades a una tabla

Para agregar entidades a una tabla, use la `Table` atributo con un `ICollector<T>` o `IAsyncCollector<T>` parámetro donde `T` especifica el esquema de las entidades que desea agregar. El constructor de atributos tiene un parámetro de cadena que especifica el nombre de la tabla. 

En el ejemplo de código siguiente agrega `Person` entidades a una tabla denominada *entrada*.

        [NoAutomaticTrigger]
        public static void IngressDemo(
            [Table("Ingress")] ICollector<Person> tableBinding)
        {
            for (int i = 0; i < 100000; i++)
            {
                tableBinding.Add(
                    new Person() { 
                        PartitionKey = "Test", 
                        RowKey = i.ToString(), 
                        Name = "Name" }
                    );
            }
        }

Normalmente el tipo que utiliza con `ICollector` se deriva de `TableEntity` o implementa `ITableEntity`, pero no tiene por qué. Cualquiera de las siguientes `Person` clases de trabajo con el código mostrado en el anterior `Ingress` método.

        public class Person : TableEntity
        {
            public string Name { get; set; }
        }

        public class Person
        {
            public string PartitionKey { get; set; }
            public string RowKey { get; set; }
            public string Name { get; set; }
        }

Si desea trabajar directamente con la API de almacenamiento de Azure, puede agregar un `CloudStorageAccount` parámetro de la firma del método.

## <a id="monitor"></a>Supervisión en tiempo real

Dado que las funciones de entrada de datos a menudo procesan grandes volúmenes de datos, el panel WebJobs SDK proporciona los datos de supervisión en tiempo real. La sección **Registro de invocación de funciones** indica si la función se está ejecutando.

![Función de entrada ejecutando](./media/websites-dotnet-webjobs-sdk-storage-tables-how-to/ingressrunning.png)

La página **Detalles de llamada** informes de progreso de la función (número de entidades escrito) mientras se está ejecutando y le da una oportunidad para interrumpirlo. 

![Función de entrada ejecutando](./media/websites-dotnet-webjobs-sdk-storage-tables-how-to/ingressprogress.png)

Cuando termine de la función, la página **Detalles de invocación** informa del número de filas escritas.

![Función de entrada terminada](./media/websites-dotnet-webjobs-sdk-storage-tables-how-to/ingresssuccess.png)

## <a id="multiple"></a>Cómo leer varias entidades de una tabla

Para leer una tabla, utilice la `Table` atributo con un `IQueryable<T>` parámetro donde escribir `T` se deriva de `TableEntity` o implementa `ITableEntity`.

El ejemplo siguiente se lee y registra todas las filas de la `Ingress` tabla:
 
        public static void ReadTable(
            [Table("Ingress")] IQueryable<Person> tableBinding,
            TextWriter logger)
        {
            var query = from p in tableBinding select p;
            foreach (Person person in query)
            {
                logger.WriteLine("PK:{0}, RK:{1}, Name:{2}", 
                    person.PartitionKey, person.RowKey, person.Name);
            }
        }

### <a id="readone"></a>Cómo leer una entidad única de una tabla

Hay un `Table` constructor de atributo con dos parámetros adicionales que le permiten especificar la clave de partición y fila cuando desea enlazar a una entidad de la tabla.

El ejemplo siguiente lee una fila de tabla para una `Person` entidad basándose en partición clave y fila valores clave recibidos en el mensaje de una cola:  

        public static void ReadTableEntity(
            [QueueTrigger("inputqueue")] Person personInQueue,
            [Table("persontable","{PartitionKey}", "{RowKey}")] Person personInTable,
            TextWriter logger)
        {
            if (personInTable == null)
            {
                logger.WriteLine("Person not found: PK:{0}, RK:{1}",
                        personInQueue.PartitionKey, personInQueue.RowKey);
            }
            else
            {
                logger.WriteLine("Person found: PK:{0}, RK:{1}, Name:{2}",
                        personInTable.PartitionKey, personInTable.RowKey, personInTable.Name);
            }
        }


La `Person` clase en este ejemplo no tiene implementar `ITableEntity`.

## <a id="storageapi"></a>Cómo usar la API de almacenamiento de .NET directamente a trabajar con una tabla

También puede usar el `Table` atributo con un `CloudTable` objeto para una mayor flexibilidad en trabajar con una tabla.

El siguiente código de ejemplo utiliza un `CloudTable` objeto para agregar una sola entidad a la tabla de *entrada* . 
 
        public static void UseStorageAPI(
            [Table("Ingress")] CloudTable tableBinding,
            TextWriter logger)
        {
            var person = new Person()
                {
                    PartitionKey = "Test",
                    RowKey = "100",
                    Name = "Name"
                };
            TableOperation insertOperation = TableOperation.Insert(person);
            tableBinding.Execute(insertOperation);
        }

Para obtener más información sobre cómo usar la `CloudTable` del objeto, consulte [cómo usar el almacenamiento de tablas de .NET](../storage/storage-dotnet-how-to-use-tables.md). 

## <a id="queues"></a>Temas relacionados cubiertos por el artículo de procedimientos de colas

Para obtener información sobre cómo controlar el procesamiento de tabla desencadenado un mensaje de cola o para escenarios de WebJobs SDK no específicos de procesamiento de tabla, consulte [cómo usar el almacenamiento de Azure cola con el SDK de WebJobs](websites-dotnet-webjobs-sdk-storage-queues-how-to.md). 

Temas de este artículo son las siguientes:

* Funciones de asincrónica
* Instancias múltiples
* Se está saliendo
* Usar atributos de WebJobs SDK en el cuerpo de una función
* Establecer las cadenas de conexión de SDK en código
* Establecer los valores para SDK WebJobs parámetros de constructor en el código
* Desencadenar una función manualmente
* Escribir registros

## <a id="nextsteps"></a>Pasos siguientes

Esta guía proporciona ejemplos de código que muestran cómo controlar los escenarios comunes para trabajar con tablas de Azure. Para obtener más información sobre cómo usar WebJobs de Azure y el SDK WebJobs, vea [Azure WebJobs recomienda recursos](http://go.microsoft.com/fwlink/?linkid=390226).
 
