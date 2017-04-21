<properties
    pageTitle="Conexión de datos: secuencia de datos de entradas de una secuencia de evento | Microsoft Azure"
    description="Obtenga información sobre cómo configurar una conexión de datos para el análisis de secuencia denominado 'entradas'. Entradas de incluyen un flujo de datos de eventos y hacer referencia a datos."
    keywords="flujo de datos, la conexión de datos, la secuencia de eventos"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>

# <a name="data-connection-learn-about-data-stream-inputs-from-events-to-stream-analytics"></a>Conexión de datos: Obtenga más información sobre los datos de entradas de secuencia de eventos para el análisis de secuencia

La conexión de datos para el análisis de secuencia es un flujo de datos de eventos de un origen de datos. Esto se denomina "entrada". Análisis de la secuencia tiene integración de primera clase con datos de Azure orígenes concentrador de evento, IoT concentrador y Blob almacenamiento de secuencias que puede ser de la suscripción de Azure misma u otra como el proceso de análisis.

## <a name="data-input-types-data-stream-and-reference-data"></a>Tipos de entrada de datos: referencia y flujo de datos
Cuando se insertan datos a un origen de datos, es consumido por el trabajo de análisis de secuencia y se procesan en tiempo real. Entradas se dividen en dos tipos distintos: secuencia de entradas de datos y hacen referencia a las entradas de datos.

### <a name="data-stream-inputs"></a>Entradas de flujo de datos
Un flujo de datos es secuencia ilimitada de eventos que llegan a lo largo del tiempo. Tareas de análisis de secuencia deben incluir datos al menos una entrada de la secuencia para ser consumida y transformación de la tarea. Almacenamiento de blobs, Hubs de evento y Hubs IoT se admiten como orígenes de entrada de flujo de datos. Evento Hubs se usan para recopilar secuencias de eventos de varios dispositivos y servicios, como las fuentes de actividades de medios sociales, información de comercio de cotizaciones o datos de sensores. IoT Hubs están optimizados para recopilar datos de los dispositivos conectados en escenarios de Internet de cosas (IoT).  Almacenamiento de blobs de puede usarse como un origen de entrada para recopilar datos de forma masiva como una secuencia.  

### <a name="reference-data"></a>Datos de referencia
Análisis de secuencia admite un segundo tipo de entrada conocido como datos de referencia. Se trata de datos auxiliares que es estático o cambian lentamente el tiempo y normalmente se utilizan para realizar búsquedas y correlación. Almacenamiento de blobs de Windows Azure actualmente es el único origen de entrada admitido para los datos de referencia. BLOB de origen de datos de referencia se limitan a 100MB de tamaño.
Para obtener información sobre cómo crear entradas de datos de referencia, vea [Usar datos de referencia](stream-analytics-use-reference-data.md)  

## <a name="create-a-data-stream-input-with-an-event-hub"></a>Crear una entrada de la secuencia de datos con un concentrador de evento

[Azure evento Hubs](https://azure.microsoft.com/services/event-hubs/) están muy scalable publicación suscripción ingestor del evento. Pueden recopilar millones de eventos por segundo, para que pueda procesar y analizar el grandes cantidades de datos creados mediante los dispositivos conectados y las aplicaciones. Es una de las entradas más utilizadas para el análisis de secuencia. Evento Hubs y análisis de secuencia juntos proporcionan a los clientes una solución de llevar a cabo para análisis en tiempo real. Evento Hubs permiten a los clientes fuente eventos a Azure en tiempo real y trabajos de análisis de secuencia pueden procesar en tiempo real. Por ejemplo, los clientes pueden enviar web clics, lecturas de sensor y eventos de registro en línea al evento Hubs y crear tareas de análisis de secuencia para usar Hubs de evento como las secuencias de entrada de datos de filtrado en tiempo real, agregar y correlación.

Es importante que tenga en cuenta que la marca de tiempo predeterminada de eventos procedentes de evento Hubs en análisis de secuencia de la marca de hora del evento recibidos en Hub de evento que es EventEnqueuedUtcTime. Para procesar los datos como una secuencia con una marca de tiempo en la carga de evento, se debe utilizar la [Marca de tiempo por](https://msdn.microsoft.com/library/azure/dn834998.aspx) palabra clave.

### <a name="consumer-groups"></a>Grupos de consumidores

Cada concentrador de eventos de análisis de secuencia de entrada debe estar configurado para tener su propio grupo de consumidores. Cuando un trabajo contiene una autocombinación o varias entradas, se pueden leer algunas entrada por más de un lector descendente, lo que afecta la cantidad de lectores de un grupo único consumidor. Para evitar que excedan el límite de evento concentrador de lectores de 5 por grupo consumidor por partición, es recomendable designar un grupo de consumidor para cada tarea de análisis de secuencia. Tenga en cuenta que también hay un límite de 20 grupos de consumidores por concentrador de evento. Para obtener información detallada, consulte la [Guía de programación de Hubs de evento](../event-hubs/event-hubs-programming-guide.md).

### <a name="configure-event-hub-as-an-input-data-stream"></a>Configurar evento concentrador como una secuencia de entrada de datos

La tabla siguiente explica cada propiedad en el caso de ficha con la descripción de la entrada de concentrador:

| NOMBRE DE PROPIEDAD | DESCRIPCIÓN |
|------|------|
| Alias de entrada | Un nombre descriptivo que se utilizará en la consulta de trabajo para hacer referencia a esta entrada |
| Namespace de Bus de servicio | Un espacio de nombres de Bus de servicio es un contenedor para un conjunto de entidades de mensajería. Cuando crea un nuevo evento de concentrador, también se crea un espacio de nombres de Bus de servicio. |
| Hub de evento | El nombre de su centro de evento de entrada. |
| Nombre de la directiva de evento concentrador | La directiva de acceso compartido, que puede crearse en la pestaña configurar de concentrador de evento. Cada directiva de acceso compartido tiene un nombre, los permisos que ha configurado, y las teclas de acceso. |
| Clave de directiva de concentrador de evento | La clave de acceso compartido que se utiliza para autenticar el acceso al espacio de nombres de Bus de servicio. |
| Grupo de consumidores de concentrador de evento (opcional) | El grupo de consumidor a recopilar datos desde el Hub de evento. Si no se especifica, trabajos de análisis de secuencia usará el grupo de consumidor predeterminado para recopilar datos desde el Hub de evento. Se recomienda usar un grupo de consumidor distinto para cada tarea de análisis de la secuencia. |
| Formato de serialización de evento | Para asegurarse de que las consultas funcionan según lo esperado, análisis de secuencia necesita saber qué formato de serialización (JSON, CSV o Avro) usa para las secuencias de datos entrantes. |
| Codificación | UTF-8 es el formato de codificación compatible solo en este momento. |

Cuando los datos proceden de un origen de evento concentrador, puede tener acceso a algunos de los campos de metadatos en la consulta de análisis de la secuencia. La siguiente tabla enumera los campos y sus descripciones.

| (PROPIEDAD) | DESCRIPCIÓN |
|------|------|
| EventProcessedUtcTime | La fecha y hora en que se ha procesado el evento de análisis de secuencia. |
| EventEnqueuedUtcTime | La fecha y hora en que se ha recibido el evento Hubs de evento. |
| PartitionId | El identificador de partición basado en cero para el adaptador de entrada. |

Por ejemplo, puede escribir una consulta como la siguiente:

````
SELECT
    EventProcessedUtcTime,
    EventEnqueuedUtcTime,
    PartitionId
FROM Input
````

## <a name="create-an-iot-hub-data-stream-input"></a>Crear una entrada de la secuencia de concentrador IoT datos

Hub de Azure Iot es una gran escalabilidad publicación suscripción ingestor evento optimizada para IoT escenarios.
Es importante que tenga en cuenta que la marca de tiempo predeterminada de eventos procedentes de Hubs IoT en análisis de secuencia de la marca de tiempo que el evento llegado en concentrador IoT que es EventEnqueuedUtcTime. Para procesar los datos como una secuencia con una marca de tiempo en la carga de evento, se debe utilizar la [Marca de tiempo por](https://msdn.microsoft.com/library/azure/dn834998.aspx) palabra clave.

> [AZURE.NOTE] Sólo los mensajes enviados con una propiedad DeviceClient pueden procesarse.

### <a name="consumer-groups"></a>Grupos de consumidores

Cada entrada de secuencia análisis IoT concentrador debe estar configurado para tiene su propio grupo de consumidores. Cuando un trabajo contiene una autocombinación o varias entradas, se pueden leer algunas entrada por más de un lector descendente, lo que afecta la cantidad de lectores de un grupo único consumidor. Para evitar que excedan el límite de IoT concentrador de lectores de 5 por grupo consumidor por partición, es recomendable designar un grupo de consumidor para cada tarea de análisis de secuencia.

### <a name="configure-iot-hub-as-an-data-stream-input"></a>Configurar IoT concentrador como una secuencia de datos de entrada

La tabla siguiente explica cada propiedad en la ficha entrada IoT concentrador con su descripción:

| NOMBRE DE PROPIEDAD | DESCRIPCIÓN |
|------|------|
| Alias de entrada | Un nombre descriptivo que se utilizará en la consulta de trabajo para hacer referencia a esta entrada. |
| Concentrador IoT | Un concentrador IoT es un contenedor para un conjunto de entidades mensajería. |
| Punto final | El nombre del extremo IoT concentrador. |
| Nombre de la directiva de acceso compartido | La directiva de acceso compartido para conceder acceso al concentrador IoT. Cada directiva de acceso compartido tiene un nombre, los permisos que ha configurado, y las teclas de acceso. |
| Clave de la directiva de acceso compartido | La clave de acceso compartido que se utiliza para autenticar el acceso al concentrador IoT. |
| Grupo de consumidor (opcional) | El grupo de consumidor a recopilar datos desde el Hub de IoT. Si no se especifica, trabajos de análisis de secuencia usará el grupo de consumidor predeterminado para recopilar datos desde el Hub de IoT. Se recomienda usar un grupo de consumidor distinto para cada tarea de análisis de la secuencia. |
| Formato de serialización de evento | Para asegurarse de que las consultas funcionan según lo esperado, análisis de secuencia necesita saber qué formato de serialización (JSON, CSV o Avro) usa para las secuencias de datos entrantes. |
| Codificación | UTF-8 es el formato de codificación compatible solo en este momento. |

Cuando los datos procede de un origen de IoT concentrador, puede tener acceso a algunos de los campos de metadatos en la consulta de análisis de la secuencia. La siguiente tabla enumera los campos y sus descripciones.

| (PROPIEDAD) | DESCRIPCIÓN |
|------|------|
| EventProcessedUtcTime | La fecha y hora en que se ha procesado el evento. |
| EventEnqueuedUtcTime | La fecha y hora en que el concentrador IoT recibió el evento. |
| PartitionId | El identificador de partición basado en cero para el adaptador de entrada. |
| IoTHub.MessageId | Se usa para relacionar comunicación bidireccional en IoT concentrador. |
| IoTHub.CorrelationId | Se usa en las respuestas a mensajes y comentarios en el IoT Hub. |
| IoTHub.ConnectionDeviceId | Id. autenticado utilizado para enviar este mensaje, en los mensajes de servicebound una marca de concentrador IoT. |
| IoTHub.ConnectionDeviceGenerationId | La generationId del dispositivo autenticado usado para enviar este mensaje a los mensajes de servicebound una marca de concentrador IoT. |
| IoTHub.EnqueuedTime | Cuando se ha recibido el mensaje IoT concentrador de tiempo. |
| IoTHub.StreamId | Propiedad de evento personalizado agregada por el dispositivo remitente. |

## <a name="create-a-blob-storage-data-stream-input"></a>Crear una entrada de secuencia de datos de almacenamiento de blobs

Escenarios con grandes cantidades de datos estructurados para almacenar en la nube, almacenamiento de blobs ofrece una solución rentable y scalable. Datos en el [almacenamiento de blobs](https://azure.microsoft.com/services/storage/blobs/) generalmente se consideran datos "inactivos", pero puede procesarse como un flujo de datos de análisis de secuencia. Un escenario común para entradas de almacenamiento de blobs con el análisis de secuencia es el procesamiento de registro, donde telemetría se genera desde un sistema y debe analizar y procesar para extraer datos significativos.

Es importante que tenga en cuenta que la marca de tiempo predeterminada de eventos de almacenamiento de blobs de análisis de secuencia de la marca de tiempo que el blob se modificó por última vez que *isBlobLastModifiedUtcTime*. Para procesar los datos como una secuencia con una marca de tiempo en la carga de evento, se debe utilizar la [Marca de tiempo por](https://msdn.microsoft.com/library/azure/dn834998.aspx) palabra clave.

Tenga en cuenta que el formato CSV entradas **requieren** una fila de encabezado para definir campos para el conjunto de datos. Más campos de fila de encabezado deben ser **únicos**.

> [AZURE.NOTE] Análisis de secuencia no admite agregar contenido a un blob existente. Análisis de secuencia sólo verán un blob una vez y los cambios realizados después de que no se puede procesar este lectura. El procedimiento recomendado es cargar todos los datos de una vez y no agregar eventos adicionales para el almacenamiento de blobs de Windows.

La tabla siguiente explica cada propiedad en la ficha de entrada de almacenamiento de blobs con su descripción:

<table>
<tbody>
<tr>
<td>NOMBRE DE PROPIEDAD</td>
<td>DESCRIPCIÓN</td>
</tr>
<tr>
<td>Alias de entrada</td>
<td>Un nombre descriptivo que se utilizará en la consulta de trabajo para hacer referencia a esta entrada.</td>
</tr>
<tr>
<td>Cuenta de almacenamiento</td>
<td>El nombre de la cuenta de almacenamiento donde se encuentran los archivos de blobs de Windows.</td>
</tr>
<tr>
<td>Clave de cuenta de almacenamiento</td>
<td>La clave secreta asociada con la cuenta de almacenamiento.</td>
</tr>
<tr>
<td>Contenedor de almacenamiento
</td>
<td>Los contenedores proporcionan una agrupación lógica para blobs almacenado en el servicio de Microsoft Azure Blob. Cuando se carga un blob al servicio Blob, debe especificar un contenedor de blob.</td>
</tr>
<tr>
<td>Trama de prefijo del ruta [opcional]</td>
<td>La ruta de acceso para localizar el BLOB dentro del contenedor especificado.
Dentro de la ruta de acceso, puede especificar una o varias instancias de las siguientes variables de 3:<BR>{date}, {hora}<BR>{partición}<BR>Ejemplo 1: cluster1/registros / {date} / {time} / {partición}<BR>Ejemplo 2: cluster1/registros / {date}<P>Tenga en cuenta que "*" no es un valor permitido para pathprefix. Se permiten solo válido <a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">caracteres de blobs de Windows Azure</a> .</td>
</tr>
<tr>
<td>Formato de fecha [opcional]</td>
<td>Si el token de fecha se usa en la ruta de acceso de prefijo, puede seleccionar el formato de fecha en la que se organizan los archivos. Ejemplo: Aaaa/MM/DD</td>
</tr>
<tr>
<td>Formato de hora [opcional]</td>
<td>Si el token de tiempo se usa en la ruta de acceso de prefijo, especifique el formato de hora en que se organizan los archivos. Actualmente, el único valor admitido es HH.</td>
</tr>
<tr>
<td>Formato de serialización de evento</td>
<td>Para asegurarse de que las consultas funcionan según lo esperado, análisis de secuencia necesita saber qué formato de serialización (JSON, CSV o Avro) usa para las secuencias de datos entrantes.</td>
</tr>
<tr>
<td>Codificación</td>
<td>CSV y JSON, UTF-8 es el formato de codificación compatible solo en este momento.</td>
</tr>
<tr>
<td>Delimitador</td>
<td>Análisis de secuencia es compatible con un número de delimitadores comunes para serializar datos en formato CSV. Valores admitidos son coma, punto y coma, espacio, ficha y barra vertical.</td>
</tr>
</tbody>
</table>

Cuando los datos proceden de un origen de almacenamiento de blobs de Windows, puede tener acceso a algunos de los campos de metadatos en la consulta de análisis de la secuencia. La siguiente tabla enumera los campos y sus descripciones.

| (PROPIEDAD) | DESCRIPCIÓN |
|------|------|
| BlobName | El nombre de la entrada blob que procede el evento. |
| EventProcessedUtcTime | La fecha y hora en que se ha procesado el evento de análisis de secuencia. |
| BlobLastModifiedUtcTime | La fecha y hora en que se modificó por última vez el blob. |
| PartitionId | El identificador de partición basado en cero para el adaptador de entrada. |

Por ejemplo, puede escribir una consulta como la siguiente:

````
SELECT
    BlobName,
    EventProcessedUtcTime,
    BlobLastModifiedUtcTime
FROM Input
````


## <a name="get-help"></a>Obtener ayuda
Para obtener más ayuda, pruebe nuestro [foro de análisis de secuencia de Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Pasos siguientes
Ha aprendido sobre las opciones de conexión de datos de Azure para los trabajos de análisis de la secuencia. Para obtener más información sobre el análisis de secuencia, consulte:

- [Empezar a usar el análisis de secuencia de Azure](stream-analytics-get-started.md)
- [Escala trabajos de análisis de secuencia de Azure](stream-analytics-scale-jobs.md)
- [Referencia del lenguaje de consulta de análisis de secuencia de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referencia de la API de REST de administración de secuencia de Azure análisis](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
