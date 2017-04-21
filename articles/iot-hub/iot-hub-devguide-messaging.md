<properties
 pageTitle="Guía de programador - mensajería | Microsoft Azure"
 description="Azure Guía de programador de concentrador IoT - dispositivo a la nube y la mensajería de dispositivo de nube"
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="dobett"/>

# <a name="send-and-receive-messages-with-iot-hub"></a>Enviar y recibir mensajes con concentrador IoT

## <a name="overview"></a>Información general

Concentrador IoT proporciona la siguiente Fundamentos de mensajería para comunicarse con un dispositivo:

- [Nube de dispositivo] [ lnk-d2c] desde un dispositivo a una aplicación back-end.
- [Dispositivo de nube] [ lnk-c2d] desde una aplicación back-end (*servicio* o *nube*).

Propiedades básicas de la funcionalidad de mensajería IoT concentrador son la confiabilidad y la duración de los mensajes. Estas propiedades permiten resistencia a la conectividad intermitente en el lado del dispositivo y cargar picos de procesamiento en el lado de la nube de eventos. Concentrador IoT implementa *al menos una vez* garantías de entrega para la nube de dispositivo y el dispositivo de nube mensajería.

Concentrador IoT admite varios [protocolos de orientación de dispositivo] [ lnk-protocols] (por ejemplo, MQTT, AMQP y HTTP). Para admitir una interoperabilidad entre protocolos, concentrador IoT define un [formato de mensaje común] [ lnk-message-format] que admiten todos los protocolos de orientación del dispositivo.

Concentrador IoT expone un [extremo compatible con evento concentrador] [ lnk-compatible-endpoint] habilitar aplicaciones de back-end para leer los mensajes de la nube de dispositivo recibidos por el concentrador.

### <a name="when-to-use"></a>Cuándo usar

Mensajería es una característica fundamental de concentrador IoT. Utilizar siempre debe enviar mensajes desde el dispositivo a su back-end o enviar mensajes de back-end a un dispositivo.

Para ver una comparación de los servicios de concentrador IoT y Hubs de evento, vea [comparación de IoT concentrador y Hubs de evento][lnk-compare].

## <a name="device-to-cloud-messages"></a>Mensajes de la nube de dispositivo

Enviar mensajes de nube de dispositivos a través de un extremo de la orientación del dispositivo (**/devices/ {deviceId} / mensajes y eventos**). Su servicio de back-end recibe mensajes de nube de dispositivos a través de un extremo de servicio orientación (**/messages/events**) que es compatible con el [Evento Hubs][lnk-event-hubs]. Por lo tanto, puede usar estándar de [integración de evento Hubs y SDK] [ lnk-compatible-endpoint] para recibir mensajes de dispositivo a la nube.

Concentrador IoT implementa mensajería dispositivo a la nube de forma similar al [Evento Hubs][lnk-event-hubs]. Mensajes del dispositivo a la nube del concentrador IoT son más como evento Hubs *eventos* que [Bus de servicio] [ lnk-servicebus] *mensajes*.

Esta implementación tiene las siguientes consecuencias:

* De forma similar a eventos de Hubs de evento, mensajes de nube de dispositivo son resistentes y conservarse de un concentrador IoT hasta siete días (vea [Opciones de configuración de dispositivo a la nube][lnk-d2c-configuration]).
* Mensajes de la nube de dispositivo se dividen a través de un conjunto de particiones fijo se establece en tiempo de creación (vea [Opciones de configuración de dispositivo a la nube][lnk-d2c-configuration]).
* Forma análoga a los centros de evento, clientes leer mensajes de la nube de dispositivo deben controlar las particiones y puntos de control. Ver [Evento Hubs - consumir eventos][lnk-event-hubs-consuming-events].
* Como evento Hubs eventos, mensajes de nube de dispositivo pueden tener más de 256 KB y se pueden agrupar en lotes optimizar envía. Lotes pueden ser como máximo 256 KB y 500 mensajes como máximo.

Sin embargo, existen algunas diferencias importantes entre mensajería de concentrador IoT dispositivo a la nube y Hubs de evento:

* Como se explica en el [Control de acceso a IoT concentrador] [ lnk-devguide-security] sección, concentrador IoT permite por dispositivo autenticación y control de acceso.
* Concentrador IoT permite millones de dispositivos conectados simultáneamente (vea [cuotas y límite de][lnk-quotas]), mientras que evento Hubs se limita a 5000 conexiones de AMQP por espacio de nombres.
* IoT concentrador no permite arbitrario particiones con un **PartitionKey**. Mensajes de la nube de dispositivo se dividen en función de su origen **deviceId**.
* Escalar IoT concentrador es ligeramente diferente a escala Hubs de evento. Para obtener más información, consulte [Escala IoT Hub][lnk-guidance-scale].

> [AZURE.NOTE] No puede reemplazar IoT concentrador para Hubs de evento en todos los escenarios. Por ejemplo, en algunos cálculos de procesamiento de evento, podría ser necesario volver a crear particiones eventos con respecto a una propiedad diferente o campo antes de analizar las secuencias de datos. En este escenario, puede utilizar un concentrador de evento desacoplar dos partes de la secuencia de procesamiento de canalización. Para obtener más información, vea *particiones* en [Introducción a Azure evento Hubs][lnk-eventhub-partitions].

Para obtener detalles acerca de cómo usar la mensajería dispositivo a la nube, vea [IoT concentrador API y SDK][lnk-sdks].

> [AZURE.NOTE] Cuando se utiliza HTTP para enviar mensajes de dispositivo a la nube, nombres y los valores sólo pueden contener caracteres alfanuméricos ASCII, además de ``{'!', '#', '$', '%, '&', "'", '*', '*', '+', '-', '.', '^', '_', '`', '|', '~'}``.

### <a name="non-telemetry-traffic"></a>Tráfico de telemetría no

A menudo, además de puntos de datos de telemetría dispositivos envían mensajes y solicitudes que requieren la ejecución y administración de la capa de lógica empresarial de aplicación. Por ejemplo, alertas críticas que deben activar una acción específica en el back-end o respuestas de dispositivo comandos enviados desde el back-end.

Para obtener más información acerca de la mejor manera de procesar este tipo de mensaje, vea la [Tutorial: cómo procesar los mensajes de dispositivo a la nube Concentrador IoT] [ lnk-d2c-tutorial] tutorial.

### <a name="device-to-cloud-configuration-options"></a>Opciones de configuración de dispositivo a la nube

Un concentrador IoT expone las propiedades siguientes para habilitar la mensajería de nube de dispositivo de control.

* **Recuento de partición**. Establecer esta propiedad durante la creación para definir el número de particiones de recopilación de eventos de dispositivo a la nube.
* **Tiempo de retención**. Esta propiedad especifica el tiempo de retención para los mensajes de la nube de dispositivo. El valor predeterminado es un día, pero se puede aumentar hasta siete días.

Además, forma análoga a los centros de evento, concentrador IoT permite administrar grupos de consumidores en el dispositivo a la nube reciban extremo.

Puede modificar todas estas propiedades mediante programación mediante el [proveedor de recursos de concentrador IoT API de REST][lnk-resource-provider-apis], o a través del [portal de Azure][lnk-management-portal].

### <a name="anti-spoofing-properties"></a>Contra la suplantación de propiedades

Para evitar la suplantación en mensajes de dispositivo a la nube, IoT concentrador de dispositivo marcas de todos los mensajes con las siguientes propiedades:

* **ConnectionDeviceId**
* **ConnectionDeviceGenerationId**
* **ConnectionAuthMethod**

Los dos primeros contienen la **deviceId** y **generationId** del dispositivo de origen, según las [Propiedades de identidad de dispositivo][lnk-device-properties].

La propiedad **ConnectionAuthMethod** contiene un objeto JSON serializada, con las siguientes propiedades:

```
{
  "scope": "{ hub | device}",
  "type": "{ symkey | sas}",
  "issuer": "iothub"
}
```

## <a name="cloud-to-device-messages"></a>Mensajes de dispositivo de nube

Enviar mensajes de dispositivo de nube a través de un extremo de servicio orientación (**/messages/devicebound**). Un dispositivo recibe a través de un extremo específico del dispositivo (**/devices/ {deviceId} / mensajes/devicebound**).

Cada mensaje de dispositivo de nube está destinado a un único dispositivo estableciendo la propiedad **a** **/devices/ {deviceId} / mensajes/devicebound**.

>[AZURE.IMPORTANT] Cada cola de dispositivo contiene máximo 50 mensajes de nube al dispositivo. Intenta enviar más mensajes a los mismos resultados de dispositivo en un error.

> [AZURE.NOTE] Al enviar mensajes de dispositivo de nube, nombres y los valores sólo pueden contener caracteres alfanuméricos ASCII, además de ``{'!', '#', '$', '%, '&', "'", '*', '*', '+', '-', '.', '^', '_', '`', '|', '~'}``.

### <a name="message-lifecycle"></a>Ciclo de vida del mensaje

Para garantizar la entrega de mensajes al menos una vez, concentrador IoT continúa mensajes de dispositivo de nube en colas por dispositivo. Dispositivos explícitamente deben confirmar la *finalización* IoT concentrador quitarlas de la cola. Así garantiza resistencia contra conectividad y errores del dispositivo.

El siguiente diagrama muestra el gráfico de estado del ciclo de vida de un mensaje de nube al dispositivo.

![Ciclo de vida del mensaje de dispositivo de nube][img-lifecycle]

Cuando el servicio envía un mensaje, se considera *en cola*. Cuando un dispositivo desea *recibir* un mensaje, concentrador IoT *bloqueos* del mensaje (establece el estado a **Invisible**) permitir que otros subprocesos en el mismo dispositivo para empezar a recibir otros mensajes. Cuando un subproceso del dispositivo complete el procesamiento de un mensaje, notifica IoT concentrador *Complete* el mensaje.

Un dispositivo también puede:

- *Rechazar* el mensaje, que hace que IoT concentrador establecer el estado de **Deadlettered** . Nota: los dispositivos que se conectan con MQTT no pueden rechazar mensajes de C2D.
- *Abandonar* el mensaje, que hace que IoT concentrador devolver el mensaje a la cola, con el estado se establece en **cola**.

Un subproceso puede fallar procesar un mensaje sin notificar IoT concentrador. En este caso, mensajes automáticamente una transición desde el estado **Invisible** al estado **en cola** después de un *tiempo de espera de visibilidad (o bloquear)*. El valor predeterminado del tiempo de espera es un minuto.

Un mensaje puede realizar la transición entre la **cola** y **Invisible** Estados de máximo, el número de veces especificado en la propiedad de **recuento de entrega max** IoT concentrador. Después de ese número de transiciones, concentrador IoT establece el estado del mensaje a **Deadlettered**. Asimismo, concentrador IoT establece el estado de un mensaje a **Deadlettered** después de la hora de expiración (consulte [tiempo de vida][lnk-ttl]).

Para obtener un tutorial en los mensajes de dispositivo de nube, consulte [Tutorial: cómo enviar mensajes de dispositivo de nube con concentrador IoT][lnk-c2d-tutorial]. Para temas de referencia sobre cómo diferentes API y SDK exponen la funcionalidad de dispositivo de nube, vea [IoT concentrador API y SDK][lnk-sdks].

> [AZURE.NOTE] Normalmente, los mensajes de dispositivo de nube completar siempre la pérdida del mensaje no se afectan a la lógica de la aplicación. Por ejemplo, el contenido del mensaje se ha conservado correctamente en el almacenamiento local o una operación se ha ejecutado correctamente. El mensaje también podría llevar información transitoria, cuya pérdida no afecte a la funcionalidad de la aplicación. A veces, tareas de larga duración, puede realizar el mensaje de la nube a dispositivo después de guardar la descripción de la tarea en el almacenamiento local. A continuación, puede notificar a la aplicación de fondo con uno o más mensajes de nube de dispositivo en distintas fases del progreso de la tarea.

### <a name="message-expiration-time-to-live"></a>Caducidad del mensaje (tiempo de vida)

Cada mensaje de dispositivo de nube con una fecha de caducidad. Esta vez se establece el servicio (en la propiedad **ExpiryTimeUtc** ) o por IoT concentrador mediante la predeterminada *tiempo de vida* especificado como una propiedad IoT concentrador. Consulte [Opciones de configuración de dispositivo de nube][lnk-c2d-configuration].

> [AZURE.NOTE] Una forma común para aprovechar las ventajas de caducidad del mensaje y evitar enviar mensajes a dispositivos desconectados, es establecer hora corta Live valores. Este enfoque consigue el mismo resultado que mantiene el estado de conexión de dispositivo, mientras se está más eficaz. Cuando solicitar confirmaciones de mensajes, concentrador IoT notifica qué dispositivos son capaces de recibir mensajes, y los dispositivos que no están en línea o error.

### <a name="message-feedback"></a>Comentarios de mensaje

Cuando envía un mensaje de dispositivo de nube, el servicio puede solicitar el envío de comentarios de cada mensaje sobre el estado final del mensaje.

- Si establece la propiedad **Ack** a **positivo**, concentrador IoT genera un mensaje de comentarios si y solo si se ha alcanzado el mensaje del dispositivo de nube estado **completado** .
- Si establece la propiedad **Ack** a **negativo**, concentrador IoT genera un mensaje de comentarios, y solo si, el mensaje de dispositivo de nube alcanza el estado de **Deadlettered** .
- Si establece la propiedad **Ack** a **completa**, concentrador IoT genera un mensaje de comentarios en cualquier caso.

> [AZURE.NOTE] Si está **lleno** **Ack** y no recibe un mensaje de comentarios, significa que el mensaje de comentarios expirado. El servicio no puede saber qué ha ocurrido con el mensaje original. En la práctica, un servicio debe asegurarse de que puede procesar los comentarios antes de que caduque. El tiempo de caducidad máximo dos días, que permite una gran cantidad de tiempo para obtener el servicio de volver a ejecutar si se produce un error.

Como se explica en los [extremos][lnk-endpoints], concentrador IoT proporciona comentarios a través de un extremo de servicio orientación (**/messages/servicebound/feedback**) como mensajes. La semántica para recibir comentarios son los mismos que los mensajes de dispositivo de nube y tiene el mismo [mensaje de ciclo de vida del][lnk-lifecycle]. Siempre que sea posible, comentarios de mensaje es por lotes en un único mensaje con el siguiente formato:

| (Propiedad) | Descripción |
| -------- | ----------- |
| EnqueuedTime | Marca de tiempo que indica cuándo se creó el mensaje. |
| Identificador de usuario | `{iot hub name}` |
| ContentType | `application/vnd.microsoft.iothub.feedback.json` |

El cuerpo es una matriz JSON número de serie de registros, cada una con las siguientes propiedades:

| (Propiedad) | Descripción |
| -------- | ----------- |
| EnqueuedTimeUtc | Marca de tiempo que indica cuando ha pasado el resultado del mensaje. Por ejemplo, el dispositivo completado o el mensaje expirado. |
| OriginalMessageId | **Id. de mensaje** del mensaje de dispositivo de nube al que pertenece esta información de comentarios. |
| StatusCode | Integer requerido. Se usa en los mensajes de comentarios generados por IoT concentrador. <br/> 0 = correcto <br/> 1 = mensaje caducado <br/> 2 = excedido el número máximo de entrega <br/> 3 = mensaje rechazado |
| Descripción | Valores de cadena para **StatusCode**. |
| DeviceId | **DeviceId** del dispositivo de destino del dispositivo de nube mensaje al que pertenece este fragmento de comentarios. |
| DeviceGenerationId | **DeviceGenerationId** del dispositivo de destino del dispositivo de nube mensaje al que pertenece este fragmento de comentarios. |


>[AZURE.IMPORTANT] El servicio debe especificar un **Id. de mensaje** para el mensaje de dispositivo de nube poder relacionar sus comentarios con el mensaje original.

En el ejemplo siguiente se muestra el cuerpo de un mensaje de comentarios.

```
[
  {
    "OriginalMessageId": "0987654321",
    "EnqueuedTimeUtc": "2015-07-28T16:24:48.789Z",
    "StatusCode": 0
    "Description": "Success",
    "DeviceId": "123",
    "DeviceGenerationId": "abcdefghijklmnopqrstuvwxyz"
  },
  {
    ...
  },
  ...
]
```

### <a name="cloud-to-device-configuration-options"></a>Opciones de configuración de dispositivo de nube

Cada concentrador IoT expone las siguientes opciones de configuración de dispositivo de nube mensajería.

| (Propiedad) | Descripción | Rango y predeterminado |
| -------- | ----------- | ----------------- |
| defaultTtlAsIso8601 | TTL predeterminado para los mensajes de la nube al dispositivo. | Intervalo de ISO_8601 hasta 2D (1 minuto como mínimo). Valor predeterminado: 1 hora. |
| maxDeliveryCount | Recuento de entrega máximo para colas de dispositivo de nube por dispositivo. | 1 a 100. Valor predeterminado: 10. |
| feedback.ttlAsIso8601 | Retención para los mensajes de comentarios del límite de servicio. | Intervalo de ISO_8601 hasta 2D (1 minuto como mínimo). Valor predeterminado: 1 hora. |
| feedback.maxDeliveryCount | Número máximo de entrega de cola de comentarios. | 1 a 100. Valor predeterminado: 100. |

Para obtener más información, vea [crear IoT hubs][lnk-portal].

## <a name="read-device-to-cloud-messages"></a>Leer mensajes de la nube de dispositivo

Concentrador IoT expone un extremo de los servicios de back-end para leer los mensajes de la nube de dispositivo recibidos por su centro. El extremo es evento compatibles con el concentrador, que le permite utilizar cualquiera de los mecanismos el servicio de evento Hubs admite para leer mensajes.

Al utilizar el [SDK de Bus de servicio de Azure para .NET] [ lnk-servicebus-sdk] o el [Evento Hubs - evento procesador Host][lnk-eventprocessorhost], puede usar las cadenas de conexión de IoT Hub con los permisos correctos. A continuación, usar **mensajes y eventos** como el nombre de evento concentrador.

Cuándo usar SDK (o integraciones de producto) que desconocen IoT concentrador, debe recuperar un extremo compatible de concentrador de evento y un nombre de evento concentrador compatible desde la configuración de concentrador IoT en el [portal de Azure][lnk-management-portal]:

1. En el módulo de concentrador IoT, haga clic en **mensajería**.
2. En la sección **configuración de dispositivo a la nube** , encontrará los siguientes valores: **extremo compatible con evento concentrador**, **compatibles con el concentrador de eventos**y **particiones**.

    ![Configuración de dispositivo a la nube][img-eventhubcompatible]

> [AZURE.NOTE] Si el SDK requiere un valor de **nombre de host** o **Namespace** , quite la combinación desde el **extremo de concentrador-compatible con el evento**. Por ejemplo, si su extremo compatible con evento concentrador es **sb://iothub-ns-myiothub-1234.servicebus.windows.net/**, el **nombre de host** sería **iothub-ns-myiothub-1234.servicebus.windows.net**y **Namespace** podría ser **iothub-ns-myiothub-1234**.

A continuación, puede utilizar una directiva de seguridad de acceso compartido que tenga los permisos de **ServiceConnect** para conectar con el Hub de evento especificado.

Si necesita crear una cadena de conexión de Hub de evento con la información anterior, use el siguiente patrón:

```
Endpoint={Event Hub-compatible endpoint};SharedAccessKeyName={iot hub policy name};SharedAccessKey={iot hub policy key}
```

A continuación se muestra una lista de SDK y aplicaciones integradas que puede usar con los extremos de concentrador-compatible con el evento que expone IoT concentrador:

* [Cliente de Java evento Hubs](https://github.com/hdinsight/eventhubs-client)
* [Apache tormenta chorros](../hdinsight/hdinsight-storm-develop-csharp-event-hub-topology.md). Puede ver el [origen de chorros](https://github.com/apache/storm/tree/master/external/storm-eventhubs) en GitHub.
* [Integración del motor de Apache](../hdinsight/hdinsight-apache-spark-eventhub-streaming.md)

## <a name="reference-topics"></a>Temas de referencia:

Los temas de referencia siguientes le proporciona más información sobre el intercambio de mensajes con IoT concentrador.

## <a name="message-format"></a>Formato del mensaje

Los mensajes de concentrador IoT constan de:

* Un conjunto de *Propiedades del sistema*. Propiedades de concentrador IoT interpreta o conjuntos. Este conjunto es predeterminado.
* Un conjunto de *Propiedades de la aplicación*. Diccionario de propiedades de cadena que la aplicación puede definir y acceso, sin necesidad de deserializar el cuerpo del mensaje. Concentrador IoT nunca modifica estas propiedades.
* Un cuerpo binario opaco.

Para obtener más información sobre cómo el mensaje está codificado en diferentes protocolos, vea [IoT concentrador API y SDK][lnk-sdks].

La siguiente tabla muestra el conjunto de propiedades del sistema en los mensajes de concentrador IoT.

| (Propiedad) | Descripción |
| -------- | ----------- |
| Id. de mensaje | Un identificador establecidas por el usuario para el mensaje, se utiliza para tramas de respuesta de la solicitud. Formato: Una entre mayúsculas y minúsculas cadena (hasta 128 caracteres) de ASCII 7 bits caracteres alfanuméricos + `{'-', ':',’.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. |
| Número de secuencia | Un número (único por dispositivo cola) asignado por IoT concentrador a cada mensaje de nube al dispositivo. |
| Para | Un destino especificado en el [Dispositivo de nube] [ lnk-c2d] mensajes. |
| ExpiryTimeUtc | Fecha y hora de caducidad del mensaje. |
| EnqueuedTime | Fecha y hora que IOT concentrador recibió el mensaje. |
| CorrelationId | Propiedad de cadena en un mensaje de respuesta que normalmente contiene el identificador del mensaje de la solicitud, en los modelos de respuesta de la solicitud. |
| Identificador de usuario | ID usado para especificar el origen de los mensajes. Cuando se generan mensajes por IoT concentrador, se establece en `{iot hub name}`. |
| ACK | Un generador de mensaje de comentarios. Esta propiedad se usa en los mensajes de dispositivo de nube para solicitar IoT concentrador para generar mensajes de comentarios como resultado el consumo del mensaje por el dispositivo. Valores posibles: **Ninguno** (predeterminado): se genera ningún mensaje de comentarios, **positivo**: recibe un mensaje de comentarios si el mensaje se ha completado, **negativo**: recibe un mensaje de comentarios si el mensaje caducado (o se ha alcanzado el número máximo de entrega) sin que se complete el dispositivo o **completa**: positivos y negativos. Para obtener más información, vea [comentarios de mensaje][lnk-feedback]. |
| ConnectionDeviceId | Un identificador de establecer IoT concentrador en los mensajes de la nube de dispositivo. Contiene **deviceId** del dispositivo que envió el mensaje. |
| ConnectionDeviceGenerationId | Un identificador de establecer IoT concentrador en los mensajes de la nube de dispositivo. Contiene el **generationId** (según las [Propiedades de identidad de dispositivo][lnk-device-properties]) del dispositivo que envió el mensaje. |
| ConnectionAuthMethod | Método de autenticación establecer IoT concentrador en los mensajes de la nube de dispositivo. Esta propiedad contiene información sobre el método de autenticación utilizado para autenticar el dispositivo a enviar el mensaje. Para obtener más información, consulte [dispositivo contra la suplantación de nube][lnk-antispoofing].|

## <a name="communication-protocols"></a>Protocolos de comunicación

Concentrador IoT permite dispositivos usar [MQTT][lnk-mqtt], MQTT sobre WebSockets, [AMQP][lnk-amqp], AMQP WebSockets y HTTP protocolos para las comunicaciones del dispositivo. La tabla siguiente proporciona recomendaciones de alto nivel de su elección de protocolo:

| Protocolo | Cuándo debe elegir este protocolo |
| -------- | ------------------------------------ |
| MQTT <br> MQTT sobre WebSocket     | Utilizar en todos los dispositivos que no se requieren para conectar varios dispositivos (cada una con sus propias credenciales por dispositivo) con la misma conexión TLS. |
| AMQP <br> AMQP sobre WebSocket    | Use de puertas de enlace de campo y nube aprovechar multiplexión en todos los dispositivos de conexión. |
| HTTP    | Se usa para dispositivos que no es compatible con otros protocolos. |

Al elegir el protocolo de comunicaciones del lado del dispositivo, tenga en cuenta los siguientes puntos:

* **Trama de nube al dispositivo**. No tiene un modo eficaz de implementar la inserción de servidor HTTP. Por lo tanto, cuando se usa HTTP, dispositivos sondean IoT concentrador para mensajes de nube al dispositivo. Este enfoque es ineficaz para el dispositivo y el concentrador IoT. En las directrices HTTP actuales, cada dispositivo debe sondear para mensajes cada 25 minutos o más. Por otro lado, MQTT y AMQP admiten inserción del servidor al recibir mensajes de la nube al dispositivo. Habilitan inmediato Push de mensajes de concentrador IoT al dispositivo. Si le preocupa latencia de entrega, MQTT o AMQP son los mejores protocolos para usar. Para dispositivos conectados con poca frecuencia, HTTP funciona bien.
* **Las puertas de enlace de campo**. Cuando se usa MQTT y HTTP, no se puede conectar varios dispositivos (cada una con sus propias credenciales por dispositivo) con la misma conexión TLS. Por lo tanto, para [escenarios de puerta de enlace de campo][lnk-azure-gateway-guidance], estos protocolos son más apropiados porque requieren una conexión TLS entre la puerta de enlace de campo y IoT concentrador para cada dispositivo conectado a la puerta de enlace de campo.
* **Dispositivos de pocos recursos**. Las bibliotecas MQTT y HTTP tienen un diseño más pequeño que las bibliotecas AMQP. Por lo tanto, si el dispositivo tiene limitados recursos (por ejemplo, menos de 1 MB de RAM), estos protocolos podrían ser la única implementación de protocolo disponible.
* **Recorrido por la red**. El protocolo AMQP estándar utiliza el puerto 5671, mientras MQTT escucha en el puerto 8883, lo que podría causar problemas en las redes que están cerradas para los protocolos no HTTP. MQTT sobre WebSockets, AMQP WebSockets y HTTP están disponibles para utilizarse en este escenario.
* **Tamaño de carga**. MQTT y AMQP son protocolos binarios, que se producen cargas más compactas que HTTP.

> [AZURE.NOTE] Cuando se utiliza HTTP, cada dispositivo debe sondear para mensajes de dispositivo de nube cada 25 minutos o más. Sin embargo, durante el desarrollo, es aceptable sondeos con más frecuencia que cada 25 minutos.

## <a name="port-numbers"></a>Números de puerto

Dispositivos puedan comunicarse con concentrador IoT en Azure mediante varios protocolos. Normalmente, la elección de protocolo se basa en los requisitos específicos de la solución. La siguiente tabla enumeran los puertos de salida que deben estar abiertos para un dispositivo para que pueda usar un protocolo específico:

| Protocolo | Puertos |
| -------- | ------- |
| MQTT     | 8883    |
| MQTT sobre WebSockets | 443    |
| AMQP     | 5671    |
| AMQP sobre WebSockets | 443    |
| HTTP     | 443     |
| LWM2M (administración de dispositivos) | 5684 |

Una vez que haya creado un concentrador IoT en un área de Azure, el concentrador mantiene la misma dirección IP para la duración de ese concentrador. Sin embargo, para mantener la calidad del servicio, si Microsoft mueve el concentrador IoT a una unidad de escala diferente, a continuación, se asigna una nueva dirección IP.

## <a name="notes-on-mqtt-support"></a>Notas sobre la compatibilidad con MQTT

IoT concentrador implementa el protocolo de v3.1.1 MQTT con las siguientes limitaciones y comportamiento específico:

  * **2 QoS no es compatible**. Cuando un cliente de dispositivo publica un mensaje con **QoS 2**, concentrador IoT cierra la conexión de red. Cuando un cliente de dispositivo se suscribe a un tema con **QoS 2**, concentrador IoT concede nivel máximo QoS 1 en el paquete **SUBACK** .
  * **Conservar los mensajes no se conservan**. Si un cliente de dispositivo publica un mensaje con el indicador de conservar establecido en 1, concentrador IoT agrega la **x-participar-conservar** la propiedad de aplicación al mensaje. En este caso, IoT concentrador no se conserva el mensaje conservar, pero en su lugar se pasa a la aplicación de back-end.

Para obtener más información, vea [IoT concentrador MQTT admite][lnk-devguide-mqtt].

Una consideración final, debería revisar la [puerta de enlace de protocolo de Azure IoT] [ lnk-azure-protocol-gateway] que le permiten implementar una puerta de enlace de protocolo personalizado de alto rendimiento que interactúa directamente con IoT concentrador. La puerta de enlace de protocolo de Azure IoT le permite personalizar el protocolo de dispositivo para acomodar implementaciones de MQTT brownfield u otros protocolos personalizados. Sin embargo, este enfoque requiere que ejecute y trabajar con una puerta de enlace de protocolo personalizado.

## <a name="additional-reference-material"></a>Material de referencia adicional

Otros temas de referencia en la Guía del programador incluyen:

- [Extremos de concentrador IoT] [ lnk-endpoints] describe los diversos extremos que expone cada concentrador IoT para operaciones de administración y el tiempo de ejecución.
- [Cuotas y regulación] [ lnk-quotas] describe las cuotas que se aplican al servicio IoT concentrador y el comportamiento de limitación que va a pasar al usar el servicio.
- [SDK de dispositivos y servicios de concentrador IoT] [ lnk-sdks] listas el idioma diversos SDK que un uso al desarrollar aplicaciones dispositivos y servicios que interactúan con IoT concentrador.
- [Lenguaje de consulta para gemelos, métodos y trabajos] [ lnk-query] describe el lenguaje de consulta que se puede usar para recuperar información de concentrador IoT sobre sus twins de dispositivo, métodos y trabajos.
- [Soporte técnico de IoT concentrador MQTT] [ lnk-devguide-mqtt] proporciona más información acerca del soporte técnico de IoT concentrador para el protocolo MQTT.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido a enviar y recibir mensajes con IoT concentrador, puede estar interesado en los siguientes temas de la Guía del programador:

- [Cargar archivos desde un dispositivo][lnk-devguide-upload]
- [Administrar identidades de dispositivo en el IoT Hub][lnk-devguide-identities]
- [Controlar el acceso a IoT concentrador][lnk-devguide-security]
- [Utilizar a twins dispositivo para sincronizar estado y configuraciones][lnk-devguide-device-twins]
- [Llamar a un método directo en un dispositivo][lnk-devguide-directmethods]
- [Programar trabajos en varios dispositivos][lnk-devguide-jobs]

Si desea probar algunos de los conceptos descritos en este artículo, puede estar interesado en los siguientes tutoriales de concentrador IoT:

- [Introducción a Azure IoT concentrador][lnk-getstarted-tutorial]
- [Cómo enviar mensajes de dispositivo de nube con concentrador IoT][lnk-c2d-tutorial]
- [Cómo procesar los mensajes de dispositivo a la nube Concentrador IoT][lnk-d2c-tutorial]


[img-lifecycle]: ./media/iot-hub-devguide-messaging/lifecycle.png
[img-eventhubcompatible]: ./media/iot-hub-devguide-messaging/eventhubcompatible.png

[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-azure-gateway-guidance]: iot-hub-devguide-endpoints.md#field-gateways
[lnk-guidance-scale]: iot-hub-scaling.md
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md
[lnk-amqp]: http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf
[lnk-mqtt]: http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/mqtt-v3.1.1.pdf
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/
[lnk-event-hubs-consuming-events]: ../event-hubs/event-hubs-programming-guide.md#event-consumers
[lnk-management-portal]: https://portal.azure.com
[lnk-servicebus]: http://azure.microsoft.com/documentation/services/service-bus/
[lnk-eventhub-partitions]: ../event-hubs/event-hubs-overview.md#partitions
[lnk-portal]: iot-hub-create-through-portal.md

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-c2d]: iot-hub-devguide-messaging.md#cloud-to-device-messages
[lnk-compatible-endpoint]: iot-hub-devguide-messaging.md#read-device-to-cloud-messages
[lnk-protocols]: iot-hub-devguide-messaging.md#communication-protocols
[lnk-message-format]: iot-hub-devguide-messaging.md#message-format
[lnk-d2c-configuration]: iot-hub-devguide-messaging.md#device-to-cloud-configuration-options
[lnk-device-properties]: iot-hub-devguide-identity-registry.md#device-identity-properties
[lnk-ttl]: iot-hub-devguide-messaging.md#message-expiration-time-to-live
[lnk-c2d-configuration]: iot-hub-devguide-messaging.md#cloud-to-device-configuration-options
[lnk-lifecycle]: iot-hub-devguide-messaging.md#message-lifecycle
[lnk-feedback]: iot-hub-devguide-messaging.md#message-feedback
[lnk-antispoofing]: iot-hub-devguide-messaging.md#anti-spoofing-properties
[lnk-compare]: iot-hub-compare-event-hubs.md

[lnk-devguide-upload]: iot-hub-devguide-file-upload.md
[lnk-devguide-identities]: iot-hub-devguide-identity-registry.md
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-servicebus-sdk]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-eventprocessorhost]: http://blogs.msdn.com/b/servicebus/archive/2015/01/16/event-processor-host-best-practices-part-1.aspx


[lnk-getstarted-tutorial]: iot-hub-csharp-csharp-getstarted.md
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
