<properties
    pageTitle="Evento Azure Hubs archivar | Microsoft Azure"
    description="Información general sobre la característica de Azure evento Hubs archivo."
    services="event-hubs"
    documentationCenter=""
    authors="djrosanova"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="darosa;sethm"/>

# <a name="azure-event-hubs-archive"></a>Evento Azure Hubs archivar

Archivo de Azure evento Hubs permite entregar automáticamente los datos de secuencias en su Hubs de evento para una cuenta de almacenamiento de blobs de su elección con una mayor flexibilidad para especificar una hora o cambiar el tamaño de intervalo de su elección. Configurar el archivo es más rápido, hay no hay costos administrativos para ejecutarlo y escala automáticamente con el evento Hubs [unidades de rendimiento](event-hubs-overview.md#capacity-and-security). Evento Hubs archivar es la manera más sencilla de cargar datos streaming en Azure y le permite centrarse en el procesamiento de datos en lugar de captura de datos.

Almacenamiento de Azure evento Hubs le permite procesar canalizaciones lote y en tiempo real en la misma secuencia. Esto le permite crear soluciones que pueden crecer con sus necesidades a lo largo del tiempo. Si desea crear sistemas basados en el lote de hoy con controlando hacia el futuro procesamiento en tiempo real, o que desea agregar una ruta de acceso y eficaz a una solución en tiempo real existente, evento Hubs archivar facilita el trabajo con la transmisión de datos sea más fáciles.

## <a name="how-event-hubs-archive-works"></a>Cómo funciona el evento Hubs archivar

Evento Hubs es un búfer de tiempo de retención resistente para entrada de telemetría, similar a un registro distribuido. La clave para escalar en Hubs de evento es el [modelo de consumidores divididas](event-hubs-overview.md#partition-key). Cada partición es un segmento de datos independiente y se consume de forma independiente. Con el tiempo estos datos edades, según el período de retención configurable. Como resultado, un determinado concentrador de evento nunca se llena "demasiado."

Evento Hubs archivar le permite especificar su propia cuenta de almacenamiento de blobs de Windows Azure y el contenedor que se utilizará para almacenar los datos archivados. Esta cuenta puede ser de la misma región como su centro de evento o en otra región, agregar a la flexibilidad de la característica de evento Hubs archivo.

Datos archivados se escriben en formato de [Apache Avro][] ; formato de compacto, rápido y binario que proporciona estructuras de datos enriquecidos con esquema en línea. Este formato se utiliza ampliamente en el ecosistema Hadoop, así como el generador de datos de Azure y análisis de secuencia. Para obtener más información sobre cómo trabajar con Avro está disponible más adelante en este artículo.

### <a name="archive-windowing"></a>Ventanas de archivar

Evento Hubs archivar le permite configurar una ventana para controlar el archivado. Esta ventana es un tamaño mínimo y la configuración de hora con una "primera wins directiva", lo que significa que el primer desencadenador encontrado hará que una operación de archivo. Si tiene una ventana de archivo de quince-minuto/100 MB y enviar 1 MB/s, la ventana de tamaño se activará antes de la ventana de tiempo. Cada partición archiva de forma independiente y escribe un blob bloque completado en el momento de archivar, con el nombre de la hora cuando se ha encontrado el intervalo de almacenamiento. La convención de nomenclatura es la siguiente:

```
<Namespace>/<EventHub>/<Partition>/<YYYY>/<MM>/<DD>/<HH>/<mm>/<ss>
```

### <a name="scaling-to-throughput-units"></a>Ajuste de escala en unidades de rendimiento

[Unidades de rendimiento](event-hubs-overview.md#capacity-and-security)controla el tráfico de Hubs de eventos. Una unidad de rendimiento solo permite 1 MB/s o 1.000 eventos por segundo de entrada y dos veces dicha cantidad de salida. Hubs de evento estándar se pueden configurar con 1-20 unidades de rendimiento y más se pueden adquirir a través de un aumento de cuota [solicitud de soporte técnico][]. Uso más allá de unidades de rendimiento comprados está limitado. Evento Hubs archivar copia datos directamente desde el almacenamiento de evento Hubs interno, omitiendo las cuotas de salida de unidad de rendimiento y guardar la salida para otros lectores de procesamiento como análisis de la secuencia o el motor.

Una vez configurados, se ejecuta automáticamente evento Hubs archivar, tan pronto como enviar su primer evento. Sigue ejecutándose en todo momento. Para que sea más fácil para el procesamiento descendente para saber que el proceso está trabajando, Hubs evento escribe archivos vacíos cuando no hay ningún dato. Esto proporciona un marcador que puede alimentar los procesadores lote y la predicción.

## <a name="setting-up-event-hubs-archive"></a>Configurar el evento Hubs archivar

Evento Hubs archivar pueden configurarse en tiempo de creación de concentrador de eventos a través del portal o el Administrador de recursos de Azure. Habilitar simplemente archivar haciendo clic en el botón **en** . Configurar una cuenta de almacenamiento y el contenedor haciendo clic en la sección de **contenedor** del módulo. Como evento Hubs archivar utiliza la autenticación de servicio de servicio con almacenamiento, no necesitará especificar una cadena de conexión de almacenamiento. El selector de recursos selecciona automáticamente el URI de recurso para su cuenta de almacenamiento. Si utiliza el Administrador de recursos de Azure, debe proporcionar este URI explícitamente como una cadena.

La ventana de tiempo predeterminado es de cinco minutos. El valor mínimo es 1, el máximo 15. La ventana **tamaño** tiene un rango de 500 de 10 MB.

![][1]

## <a name="adding-archive-to-an-existing-event-hub"></a>Agregar archivo a un concentrador de evento existente

Archivos se pueden configurar en Hubs evento existente que están en un espacio de nombres de evento Hubs. La característica no está disponible en versiones anteriores espacios de tipo de mensajería o mixto. Para habilitar el archivo en un concentrador de evento existente o cambiar la configuración de archivo, haga clic en el espacio de nombres para cargar el módulo de **Essentials** , a continuación, haga clic en el Hub de evento para el que desea habilitar o cambiar la configuración del archivo. Por último, haga clic en la sección **Propiedades** del módulo abrir tal como se muestra en la siguiente ilustración.

![][2]

También puede configurar eventos Hubs archivar a través de plantillas de administrador de recursos de Azure. Para obtener más información, consulte [este artículo](event-hubs-resource-manager-namespace-event-hub-enable-archive.md).

## <a name="exploring-the-archive-and-working-with-avro"></a>Explorar el archivo y trabajar con Avro

Una vez configurada, evento Hubs archivar crea archivos en la cuenta de almacenamiento de Azure y contenedor proporcionados en la ventana de tiempo configurado. Puede ver estos archivos en cualquier herramienta como [El Explorador de almacenamiento de Azure][]. Puede descargar los archivos localmente para trabajar en ellas.

Los archivos creados por evento Hubs archivar tienen el siguiente esquema Avro:

![][3]

Es una forma fácil de explorar Avro archivos usando el tarro [Avro herramientas][] de Apache. Después de descargar este jar, puede ver el esquema de un archivo de Avro específico, ejecute el siguiente comando:

```
java -jar avro-tools-1.8.1.jar getschema \<name of archive file\>
```

Este comando devuelve

```
{

    "type":"record",
    "name":"EventData",
    "namespace":"Microsoft.ServiceBus.Messaging",
    "fields":[
                 {"name":"SequenceNumber","type":"long"},
                 {"name":"Offset","type":"string"},
                 {"name":"EnqueuedTimeUtc","type":"string"},
                 {"name":"SystemProperties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Properties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Body","type":["null","bytes"]}
             ]
}
```

También puede usar herramientas de Avro para convertir el archivo en formato JSON y realizar otras tareas de procesamiento.

Para realizar el procesamiento de más avanzada, descargue e instale Avro de su elección de plataforma. En el momento de escribir este artículo, existen implementaciones para C, C++, C\#, Java, NodeJS, Perl, PHP, Python y Ruby.

Apache Avro tiene completadas guías de introducción para [Java][] y [Python][]. También puede leer el artículo [Introducción a evento Hubs archivar](event-hubs-archive-python.md) .

## <a name="how-event-hubs-archive-is-charged"></a>¿Cómo se cargará evento Hubs archivar

Evento Hubs archivar se mide de forma similar en unidades de rendimiento, como un cargo por hora. El cargo es directamente proporcional al número de unidades de rendimiento comprado para el espacio de nombres. Como unidades de rendimiento se aumenta y disminuye, evento Hubs archivar aumenta y disminuye para proporcionar un rendimiento coincidente. Los medidores ocurren conjuntamente. El cargo por evento Hubs archivar es $0,10 por hora por unidad de rendimiento, ofrecida con un descuento de 50% durante el período de vista previa.

Evento Hubs archivar realmente es la manera más sencilla de obtener datos en Azure. Usa lago de datos de Azure, el generador de datos de Azure y HDInsight de Azure, puede realizar proceso por lotes y otro análisis de su elección mediante herramientas familiares y plataformas a cualquier escala que necesita.

## <a name="next-steps"></a>Pasos siguientes

Puede obtener más información sobre el evento Hubs visitando los siguientes vínculos:

- Una [aplicación de ejemplo que usa el evento Hubs][]completa.
- Ejemplo de [escalado de procesamiento de evento con Hubs de evento][] .
- [Información general de Hubs de evento][]

[Apache Avro]: http://avro.apache.org/
[solicitud de soporte técnico]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[1]: ./media/event-hubs-archive-overview/event-hubs-archive1.png
[2]: media/event-hubs-archive-overview/event-hubs-archive2.png
[Explorador de almacenamiento de Azure]: http://azurestorageexplorer.codeplex.com/
[3]: ./media/event-hubs-archive-overview/event-hubs-archive3.png
[Herramientas de Avro]: http://www-us.apache.org/dist/avro/avro-1.8.1/java/avro-tools-1.8.1.jar
[Java]: http://avro.apache.org/docs/current/gettingstartedjava.html
[Python]: http://avro.apache.org/docs/current/gettingstartedpython.html
[Información general de Hubs de evento]: event-hubs-overview.md
[aplicación de ejemplo que usa Hubs de evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Escalar evento procesamiento con Hubs de evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3