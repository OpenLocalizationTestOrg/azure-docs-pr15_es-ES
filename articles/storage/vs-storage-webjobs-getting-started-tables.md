<properties
    pageTitle="Introducción a almacenamiento de Azure y Visual Studio conectado servicios (WebJob proyectos)"
    description="Cómo empezar a usar el almacenamiento de tablas de Azure en un proyecto de Azure WebJobs en Visual Studio después de conectar con una cuenta de almacenamiento mediante Visual Studio conectado servicios"
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

# <a name="getting-started-with-azure-storage-azure-webjob-projects"></a>Introducción a Azure almacenamiento (WebJob Azure proyectos)

[AZURE.INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Información general

Este artículo se proporcionan ejemplos de código de C# que muestran muestran cómo utilizar la versión de Azure WebJobs SDK 1.x con el servicio de almacenamiento de tablas de Azure. Los ejemplos de código usar la versión de [SDK WebJobs](../app-service-web/websites-dotnet-webjobs-sdk.md) 1.x.

El servicio de almacenamiento de la tabla de Azure le permite almacenar grandes cantidades de datos estructurados. El servicio es un almacén de datos NoSQL que acepta llamadas autenticadas desde dentro y fuera de la nube de Azure. Tablas de Azure son ideales para almacenar los datos estructurados y no relacionales.  Para obtener más información, vea [Introducción a almacenamiento de tablas de Azure con .NET](storage-dotnet-how-to-use-tables.md#create-a-table) .

Algunos de los fragmentos de código muestran el atributo de **tabla** utilizado en las funciones que se denominan manualmente, es decir, no mediante uno de los atributos de desencadenador.

## <a name="how-to-add-entities-to-a-table"></a>Cómo agregar entidades a una tabla

Para agregar entidades a una tabla, use el atributo de **tabla** con un **ICollector<T> ** o **IAsyncCollector<T> ** parámetro donde **T** especifica el esquema de las entidades que desea agregar. El constructor de atributos tiene un parámetro de cadena que especifica el nombre de la tabla.

En el ejemplo de código siguiente agrega entidades de **persona** a una tabla denominada *entrada*.

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

Normalmente, el tipo que se utiliza con **ICollector** se deriva de **TableEntity** o implementa **ITableEntity**, pero no tiene por qué. Cualquiera de las siguientes clases de **persona** trabajar con el código mostrado en el método de **entrada** anterior.

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

Si desea trabajar directamente con la API de almacenamiento de Azure, puede agregar un parámetro **CloudStorageAccount** a la firma del método.

## <a name="real-time-monitoring"></a>Supervisión en tiempo real

Dado que las funciones de entrada de datos a menudo procesan grandes volúmenes de datos, el panel WebJobs SDK proporciona los datos de supervisión en tiempo real. La sección **Registro de invocación de funciones** indica si la función se está ejecutando.

![Función de entrada ejecutando](./media/vs-storage-webjobs-getting-started-tables/ingressrunning.png)

La página **Detalles de llamada** informes de progreso de la función (número de entidades escrito) mientras se está ejecutando y le da una oportunidad para interrumpirlo.

![Función de entrada ejecutando](./media/vs-storage-webjobs-getting-started-tables/ingressprogress.png)

Cuando termine de la función, la página **Detalles de invocación** informa del número de filas escritas.

![Función de entrada terminada](./media/vs-storage-webjobs-getting-started-tables/ingresssuccess.png)

## <a name="how-to-read-multiple-entities-from-a-table"></a>Cómo leer varias entidades de una tabla

Para leer una tabla, utilice el atributo de **tabla** con un **IQueryable<T> ** parámetro donde tipo **T** se deriva de **TableEntity** o implementa **ITableEntity**.

El ejemplo siguiente se lee y registra todas las filas de la tabla de **entrada** :

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

### <a name="how-to-read-a-single-entity-from-a-table"></a>Cómo leer una entidad única de una tabla

No hay un constructor de atributo de **tabla** con dos parámetros adicionales que le permiten especificar la clave de partición y fila cuando desea enlazar a una entidad de la tabla.

El ejemplo siguiente lee una fila de tabla para una entidad de **persona** basándose en partición clave y fila valores clave recibidos en el mensaje de una cola:  

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


En este ejemplo la clase de **persona** no tiene implementar **ITableEntity**.

## <a name="how-to-use-the-net-storage-api-directly-to-work-with-a-table"></a>Cómo usar la API de almacenamiento de .NET directamente a trabajar con una tabla

También puede usar el atributo de **tabla** con un objeto **CloudTable** para una mayor flexibilidad en trabajar con una tabla.

El ejemplo siguiente utiliza un objeto de **CloudTable** para agregar una sola entidad a la tabla de *entrada* .

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

Para obtener más información sobre cómo usar el objeto **CloudTable** , vea [Introducción a almacenamiento de tablas de Azure con .NET](storage-dotnet-how-to-use-tables.md).

## <a name="related-topics-covered-by-the-queues-how-to-article"></a>Temas relacionados cubiertos por el artículo de procedimientos de colas

Para obtener información sobre cómo controlar el procesamiento de tabla desencadenado un mensaje de cola o para escenarios de WebJobs SDK no específicos de procesamiento de tabla, vea [que Introducción al almacenamiento en cola de Azure y Visual Studio servicios conectados (WebJob proyectos)](vs-storage-webjobs-getting-started-queues.md).



## <a name="next-steps"></a>Pasos siguientes

En este artículo se proporciona ejemplos de código que muestran cómo controlar los escenarios comunes para trabajar con tablas de Azure. Para obtener más información sobre cómo usar el SDK de WebJobs y WebJobs de Azure, consulte [los recursos de documentación WebJobs de Azure](http://go.microsoft.com/fwlink/?linkid=390226).
