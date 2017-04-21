<properties
    pageTitle="Dispositivo Azure IoT SDK para C - IoTHubClient | Microsoft Azure"
    description="Más información sobre el uso de la biblioteca de IoTHubClient en el dispositivo de Azure IoT SDK para C"
    services="iot-hub"
    documentationCenter=""
    authors="olivierbloch"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/06/2016"
     ms.author="obloch"/>

# <a name="microsoft-azure-iot-device-sdk-for-c--more-about-iothubclient"></a>Dispositivo de Microsoft Azure IoT SDK para C: más información acerca de IoTHubClient

El [primer artículo](iot-hub-device-sdk-c-intro.md) de esta serie presentó el **dispositivo de Microsoft Azure IoT SDK para C**. Este artículo explica que hay dos capas arquitectónicas en SDK. En la base es la biblioteca de **IoTHubClient** que administra la comunicación con IoT concentrador directamente. También es la biblioteca de **serializador** que se basa en el mismo para proporcionar servicios de serialización. En este artículo se explican los detalles adicionales en la biblioteca de **IoTHubClient** .

El artículo anterior describe cómo usar la biblioteca de **IoTHubClient** para enviar eventos a IoT concentrador y recibir mensajes. En este artículo, se extiende esa discusión por explicar cómo administrar con mayor precisión *al* enviar y recibir datos, presentación de las **API de nivel inferior**. También explicaremos cómo adjuntar propiedades a eventos (y recuperarlos de mensajes) mediante la propiedad funciones en la biblioteca de **IoTHubClient** de control. Por último, proporcionamos la explicación adicional de diferentes formas de tratar los mensajes recibidos IoT concentrador.

El artículo termina cubierta por un par de varios temas, incluidos más información sobre las credenciales del dispositivo y cómo cambiar el comportamiento de la **IoTHubClient** a través de las opciones de configuración.

Usaremos los ejemplos de SDK **IoTHubClient** para explicar estos temas. Si desea seguir a lo largo, consulte la **iothub\_cliente\_ejemplo\_http** y **iothub\_cliente\_ejemplo\_amqp** aplicaciones que se incluyen en el dispositivo de Azure IoT SDK para C. todo lo descrito en las secciones siguientes se muestra en estos ejemplos.

Puede encontrar el **dispositivo de Azure IoT SDK para C** en el repositorio de [Microsoft Azure IoT SDK](https://github.com/Azure/azure-iot-sdks) GitHub y ver los detalles de la API en la [referencia de la API de C](http://azure.github.io/azure-iot-sdks/c/api_reference/index.html).

## <a name="the-lower-level-apis"></a>Las API de nivel inferior

El artículo anterior describe el funcionamiento básico de la **IotHubClient** en el contexto de la **iothub\_cliente\_ejemplo\_amqp** aplicación. Por ejemplo, explican cómo iniciar la biblioteca mediante este código.

```
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

También se describe cómo enviar eventos mediante esta llamada a función.

```
IoTHubClient_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message);
```

El artículo también describe cómo recibir mensajes mediante el registro de una función de devolución de llamada.

```
int receiveContext = 0;
IoTHubClient_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext);
```

El artículo también mostramos cómo liberar los recursos mediante código como el siguiente.

```
IoTHubClient_Destroy(iotHubClientHandle);
```

Sin embargo, hay funciones companion a cada una de estas API:

-   IoTHubClient\_LL\_CreateFromConnectionString

-   IoTHubClient\_LL\_SendEventAsync

-   IoTHubClient\_LL\_SetMessageCallback

-   IoTHubClient\_LL\_Destroy


Estas funciones todos incluyen "L" en el nombre de la API. Aparte de eso, los parámetros de cada una de estas funciones son idénticos a sus homólogos no LL. Sin embargo, el comportamiento de estas funciones es diferente en un aspecto importante.

Al llamar a **IoTHubClient\_CreateFromConnectionString**, las bibliotecas subyacentes crean un nuevo subproceso que se ejecuta en segundo plano. Este subproceso envía eventos a y recibe mensajes de concentrador IoT. Ningún subproceso de este tipo se crea al trabajar con la API de "l". La creación de subproceso en segundo plano es una comodidad para el desarrollador. No tiene que preocuparse por explícitamente eventos de enviar y recibir mensajes de concentrador IoT--automáticamente sucede en segundo plano. En cambio, "L" API le ofrecen control explícito sobre comunicación con IoT concentrador, si lo necesita.

Para entender mejor, veamos un ejemplo:

Al llamar a **IoTHubClient\_SendEventAsync**, lo que realmente está haciendo es colocar el evento en un búfer. El subproceso de fondo que se creó cuando llame a **IoTHubClient\_CreateFromConnectionString** supervisa este búfer y envía los datos que contiene a concentrador IoT continuamente. Esto sucede en segundo plano al mismo tiempo que el subproceso principal está realizando otra tarea.

Del mismo modo, al registrar una función de devolución de llamada para mensajes con **IoTHubClient\_SetMessageCallback**, está indicando al SDK de tiene el subproceso en segundo plano invocar la función de devolución de llamada cuando un mensaje recibido, independientemente del subproceso principal.

"L" API no crea un subproceso de fondo. En su lugar, debe llamar a una nueva API para explícitamente enviar y recibir datos IoT concentrador. Esto se muestra en el ejemplo siguiente.

La **iothub\_cliente\_ejemplo\_http** aplicación que se incluye en el SDK muestra las API de nivel inferior. En este ejemplo, hemos enviar eventos a IoT concentrador con código como el siguiente:

```
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Message_%d_From_IoTHubClient_LL_Over_HTTP", i);
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));

IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

Las tres primeras líneas crean el mensaje y la última línea envía el evento. Sin embargo, como se mencionó anteriormente, "Enviar" el evento significa que los datos simplemente se colocan en un búfer. Nada se transmite en la red cuando llamamos **IoTHubClient\_LL\_SendEventAsync**. En orden de realmente entrada los datos a IoT concentrador, debe llamar **IoTHubClient\_LL\_DoWork**, como en este ejemplo:

```
while (1)
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(1000);
}
```

Este código (desde el **iothub\_cliente\_ejemplo\_http** aplicación) llama repetidamente **IoTHubClient\_LL\_DoWork**. Cada vez que **IoTHubClient\_LL\_DoWork** es llamado, envía algunos eventos del búfer a IoT concentrador y recupera un mensaje en cola que se envía al dispositivo. El último caso significa que si se registra una función de devolución de llamada para los mensajes, a continuación, la devolución de llamada se invoca (suponiendo que todos los mensajes en la cola). Esta función de devolución de llamada se habría registrado con código como el siguiente:

```
IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext)
```

El motivo por el que **IoTHubClient\_LL\_DoWork** a menudo se denomina un bucle es que cada vez que se llama, envía *algunos* intermedia eventos a IoT concentrador y recupera *el siguiente* mensaje en la cola para el dispositivo. No se garantiza que cada llamada enviar búfer todos los eventos o recuperar todos los mensajes en cola. Si desea enviar todos los eventos en el búfer y continúe con otro proceso puede reemplazar este bucle con código como el siguiente:

```
IOTHUB_CLIENT_STATUS status;

while ((IoTHubClient_LL_GetSendStatus(iotHubClientHandle, &status) == IOTHUB_CLIENT_OK) && (status == IOTHUB_CLIENT_SEND_STATUS_BUSY))
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(1000);
}
```

Este código llama **IoTHubClient\_LL\_DoWork** hasta que todos los eventos del búfer se han enviado a IoT concentrador. Tenga en cuenta que esto también implica que todos los mensajes en cola recibidos. Parte de la razón para esto es que buscando "todos" de los mensajes no determinista como una acción. ¿Qué sucede si recuperar "todos" de los mensajes, pero a continuación, otro se envía al dispositivo inmediatamente después de? Una manera mejor tratar con un tiempo de espera programada. Por ejemplo, la función de devolución de llamada de mensaje podría restablecer un temporizador cada vez que se invoca. A continuación, puede escribir lógica para continuar el proceso si, por ejemplo, no hay mensajes recibidos en los últimos segundos de *X* .

Cuando está terminado ingressing eventos y recibir mensajes, asegúrese de llamar a la función correspondiente para limpiar los recursos.

```
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

Básicamente, hay solo un conjunto de API para enviar y recibir datos con un subproceso en segundo plano y otro conjunto de API que hace lo mismo sin el subproceso en segundo plano. Una gran cantidad de programadores que prefiera la API no LL, pero las API de nivel inferior son útiles cuando el programador desea un control explícito sobre las transmisiones de red. Por ejemplo, algunos dispositivos recopilan datos en el tiempo y los eventos de entrada única a intervalos especificados (por ejemplo, una vez cada hora o una vez al día). Las API de nivel inferior proporcionan la capacidad para controlar explícitamente al enviar y recibir datos IoT concentrador de. Otros usuarios prefieren simplemente la simplificar que proporcionan las API de nivel inferior. Todo lo que ocurre en el subproceso principal en lugar de algunos ocurra de trabajo en segundo plano.

El modelo que elija, asegúrese de ser coherentes en qué API que utiliza. Si inicia llamando **IoTHubClient\_LL\_CreateFromConnectionString**, asegúrese de usar solo las API de nivel inferior correspondientes para cualquier trabajo de seguimiento:

-   IoTHubClient\_LL\_SendEventAsync

-   IoTHubClient\_LL\_SetMessageCallback

-   IoTHubClient\_LL\_Destroy

-   IoTHubClient\_LL\_DoWork

Lo contrario también es true. Si empieza con **IoTHubClient\_CreateFromConnectionString**, use la API no LL ningún procesamiento adicional.

En el dispositivo de Azure IoT SDK para C, consulte la **iothub\_cliente\_ejemplo\_http** aplicaciones para obtener un ejemplo completo de las API de nivel inferior. La **iothub\_cliente\_ejemplo\_amqp** aplicación se puede hacer referencia para obtener un ejemplo completo de la API LL.

## <a name="property-handling"></a>Control (propiedad)

Hasta ese momento cuando hemos descrito envío de datos, nos hemos se hace referencia al cuerpo del mensaje. Por ejemplo, considere este código:

```
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Hello World");
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));
IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

Este ejemplo envía un mensaje a IoT concentrador con el texto "Hola a todos". Sin embargo, concentrador IoT también permite propiedades que se asociarán a cada mensaje. Propiedades son pares de nombre/valor que se pueden adjuntar al mensaje. Por ejemplo, se puede modificar el código para adjuntar una propiedad al mensaje anterior:

```
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Empezaremos llamando **IoTHubMessage\_propiedades** y pasando el controlador del mensaje. ¿Qué obtenemos es un **mapa\_controlador** referencia que nos permite empezar a agregar propiedades. Se realiza llamando **mapa\_AddOrUpdate**, que tiene una referencia a un mapa\_controlador, el nombre de propiedad y valor de la propiedad. Con esta API podemos agregar tantas propiedades como se desee.

Cuando se lee el evento de **Hubs de evento**, el receptor puede enumerar las propiedades y recuperar sus valores correspondientes. Por ejemplo, en .NET debería realizarse mediante el acceso a la [colección de propiedades en el objeto EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.properties.aspx).

En el ejemplo anterior, nos estamos asociar propiedades a un evento que se envía a IoT concentrador. Propiedades también se asocian a los mensajes recibidos de concentrador IoT. Si queremos recuperar propiedades de un mensaje, podemos utilizar código como el siguiente en la función de devolución de llamada de mensaje:

```
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .

    // Retrieve properties from the message
    MAP_HANDLE mapProperties = IoTHubMessage_Properties(message);
    if (mapProperties != NULL)
    {
        const char*const* keys;
        const char*const* values;
        size_t propertyCount = 0;
        if (Map_GetInternals(mapProperties, &keys, &values, &propertyCount) == MAP_OK)
        {
            if (propertyCount > 0)
            {
                printf("Message Properties:\r\n");
                for (size_t index = 0; index < propertyCount; index++)
                {
                    printf("\tKey: %s Value: %s\r\n", keys[index], values[index]);
                }
                printf("\r\n");
            }
        }
    }

    . . .
}
```

La llamada a **IoTHubMessage\_propiedades** devuelve el **mapa\_controlador** referencia. Se pasa a continuación, que hacen referencia a **mapa\_GetInternals** para obtener una referencia a una matriz de pares de nombre/valor (así como un recuento de las propiedades). En ese momento es tan sencillo enumerar las propiedades para obtener acceso a los valores que deseamos.

No debe utilizar propiedades en la aplicación. Sin embargo, si necesita configurarlas en eventos o recuperarlos de mensajes, la biblioteca de **IoTHubClient** facilita.

## <a name="message-handling"></a>Control de mensajes

Como se indicó anteriormente, cuando llegan mensajes IoT concentrador de la biblioteca de **IoTHubClient** responde al invocar una función de devolución de llamada registrado. Hay un parámetro de retorno de esta función que merece una explicación adicional. Este es un extracto de la función de devolución de llamada en la **iothub\_cliente\_ejemplo\_http** aplicación de ejemplo:

```
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .
    return IOTHUBMESSAGE_ACCEPTED;
}
```

Tenga en cuenta que el tipo devuelto es **IOTHUBMESSAGE\_disposición\_resultado** y en este caso particular se devuelven **IOTHUBMESSAGE\_aceptado**. Existen otros valores podemos volver de esta función que cambiar cómo reacciona la biblioteca **IoTHubClient** a la devolución de llamada del mensaje. Estas son las opciones.

-   **IOTHUBMESSAGE\_aceptado** : el mensaje se ha procesado correctamente. La biblioteca **IoTHubClient** no llamará a la función de devolución de llamada nuevo con el mismo mensaje.

-   **IOTHUBMESSAGE\_rechazado** : no se ha procesado el mensaje y no hay ningún deseo de hacerlo en el futuro. La biblioteca **IoTHubClient** no debe invocar la función de devolución de llamada nuevo con el mismo mensaje.

-   **IOTHUBMESSAGE\_abandonado** : el mensaje no se ha procesado correctamente, pero la biblioteca de **IoTHubClient** debe invocar la función de devolución de llamada nuevo con el mismo mensaje.

Para los dos primeros códigos de retorno, la biblioteca de **IoTHubClient** envía un mensaje a IoT concentrador que indica que el mensaje se debe eliminado de la cola del dispositivo y no se han entregado nuevamente. El resultado es el mismo (el mensaje se ha eliminado de la cola de dispositivo), pero si el mensaje fue aceptado o rechazado aún está registrado.  Grabar esta distinción es útil a remitentes del mensaje que pueden escuchar comentarios y averiguar si un dispositivo ha aceptado o rechazado un mensaje concreto.

En el último caso también se envía un mensaje a IoT concentrador, pero indica que se debe entregar el mensaje. Normalmente deberá abandonar un mensaje si encuentra algún error pero desea volver a procesar el mensaje de nuevo. Por el contrario, rechazar un mensaje es adecuado cuando se produce un error no recuperable (o si simplemente decide que no desea procesar el mensaje).

En cualquier caso, tenga en cuenta de los códigos de retorno diferentes para que puede obtener el comportamiento que desee en la biblioteca de **IoTHubClient** .

## <a name="alternate-device-credentials"></a>Credenciales alternativas de dispositivo

Como se explica anteriormente, lo primero que hay que hacer cuando se trabaja con la biblioteca de **IoTHubClient** es obtener un **IOTHUB\_cliente\_controlador** con una llamada como la siguiente:

```
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Los argumentos de **IoTHubClient\_CreateFromConnectionString** son la cadena de conexión de nuestro dispositivo y un parámetro que indica el protocolo que usamos para comunicarse con IoT concentrador. La cadena de conexión tiene un formato que aparece como sigue:

```
HostName=IOTHUBNAME.IOTHUBSUFFIX;DeviceId=DEVICEID;SharedAccessKey=SHAREDACCESSKEY
```

Existen cuatro tipos de información de la cadena: nombre del concentrador de IoT, sufijo IoT concentrador, Id. de dispositivo y tecla de acceso compartido. Obtener el nombre de dominio completo (FQDN) de un concentrador IoT cuando se crea la instancia de concentrador IoT en el portal de Azure: Esto le da el nombre de concentrador IoT (la primera parte del FQDN) y el sufijo de concentrador IoT (el resto del FQDN). Obtener el identificador del dispositivo y la tecla de acceso compartido al registrar su dispositivo con concentrador IoT (como se describe en el [artículo anterior](iot-hub-device-sdk-c-intro.md)).

**IoTHubClient\_CreateFromConnectionString** le ofrece una manera de iniciar la biblioteca. Si lo prefiere, puede crear un nuevo **IOTHUB\_cliente\_controlador** mediante el uso de estos parámetros individuales en lugar de la cadena de conexión. Para ello, con el siguiente código:

```
IOTHUB_CLIENT_CONFIG iotHubClientConfig;
iotHubClientConfig.iotHubName = "";
iotHubClientConfig.deviceId = "";
iotHubClientConfig.deviceKey = "";
iotHubClientConfig.iotHubSuffix = "";
iotHubClientConfig.protocol = HTTP_Protocol;
IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_LL_Create(&iotHubClientConfig);
```

Esto realiza lo mismo que **IoTHubClient\_CreateFromConnectionString**.

Puede parecer obvio que quiera usar **IoTHubClient\_CreateFromConnectionString** en lugar de este método de inicialización más detallado. Tenga en cuenta, sin embargo, cuando se registre un dispositivo concentrador IoT lo que se obtiene es un identificador de dispositivo y la clave del dispositivo (no una cadena de conexión). La herramienta SDK del **Administrador de dispositivos** que introdujo en el [artículo anterior](iot-hub-device-sdk-c-intro.md) utiliza bibliotecas en el **servicio de Azure IoT SDK** para crear la cadena de conexión desde el Id. de dispositivo, la clave del dispositivo y el nombre de host de concentrador IoT. Para llamar a **IoTHubClient\_LL\_crear** puede ser preferible porque ahorra el paso de generar una cadena de conexión. Use el método que resulte práctico.

## <a name="configuration-options"></a>Opciones de configuración

Hasta el momento, todo lo descrito sobre el funcionamiento de la biblioteca de **IoTHubClient** refleja su comportamiento predeterminado. Sin embargo, hay varias opciones que puede configurar para cambiar el funcionamiento de la biblioteca. Esto se logra al aprovechar la **IoTHubClient\_LL\_SetOption** API. Considere este ejemplo:

```
unsigned int timeout = 30000;
IoTHubClient_LL_SetOption(iotHubClientHandle, "timeout", &timeout);
```

Hay un par de opciones que se usan normalmente:

-   **SetBatching** (booleano): si **es true**, los datos enviados a IoT concentrador se envía en lotes. Si **false**, a continuación, los mensajes se envían individualmente. El valor predeterminado es **false**. Observe que la opción **SetBatching** solo es válido para el protocolo HTTP y no para los protocolos MQTT o AMQP.

-   **Tiempo de espera** (unsigned int): este valor se representa en milisegundos. Si envía una solicitud HTTP o recibir una respuesta tarda más de este momento, se agota la conexión.

La opción por lotes es importante. De forma predeterminada, los eventos de ingresses biblioteca individualmente (un único evento es lo que se pase a **IoTHubClient\_LL\_SendEventAsync**). Si la opción por lotes es **Verdadero**, la biblioteca recopila tantos eventos como sea posible desde el búfer (hasta el tamaño máximo de los mensajes que aceptará IoT concentrador).  El lote de eventos se envía a IoT concentrador en una sola llamada HTTP (se incluyen los eventos individuales en una matriz JSON). Habilitar el procesamiento por lotes suele ser el resultado de gran rendimiento puesto que está reducción de ida y vuelta de red. También reduce considerablemente ancho de banda ya que va a enviar un conjunto de encabezados HTTP con un lote de eventos en lugar de un conjunto de encabezados para cada evento individual. A menos que tenga una razón específica para hacer lo contrario, normalmente que desee habilitar el procesamiento por lotes.

## <a name="next-steps"></a>Pasos siguientes

Este artículo describe detalladamente el comportamiento de la biblioteca de **IoTHubClient** que se encuentra en el **dispositivo de Azure IoT SDK para C**. Con esta información, debe tener una buena comprensión de las funciones de la biblioteca **IoTHubClient** . El [siguiente artículo](iot-hub-device-sdk-c-serializer.md) proporciona detalles similares en la biblioteca de **serializador** .

Para obtener más información sobre el desarrollo de concentrador IoT, consulte el [SDK de concentrador IoT][lnk-sdks].

Para explorar aún más las capacidades de concentrador IoT, consulte:

- [Simulación de un dispositivo con el SDK de puerta de enlace IoT][lnk-gateway]

[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
