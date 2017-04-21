<properties
    pageTitle="Con el dispositivo de Azure IoT SDK para C | Microsoft Azure"
    description="Obtenga más información sobre y empezar a trabajar con el código de ejemplo en el dispositivo de Azure IoT SDK para C."
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

# <a name="introducing-the-azure-iot-device-sdk-for-c"></a>Introducción a Azure IoT dispositivo SDK para C

El **dispositivo de Azure IoT SDK** es un conjunto de bibliotecas diseñadas para simplificar el proceso de eventos de enviarlos y recibir mensajes del servicio de **Azure IoT concentrador** . Hay distintas variaciones del SDK, cada dirige a una plataforma concreta, pero este artículo describe el **dispositivo de Azure IoT SDK para C**.

El dispositivo de Azure IoT SDK para C se escribe en ANSI C (C99) para maximizar la portabilidad. De esta forma adecuada para que funcione en varias plataformas y dispositivos, especialmente cuando minimizar el disco y memoria es una prioridad.  

Hay una amplia gama de plataformas en el que ha probado el SDK (consulte la [Azure certificados para el catálogo de dispositivo IoT](https://catalog.azureiotsuite.com/) para obtener información detallada). Aunque en este artículo se incluye tutoriales de código de ejemplo que se ejecuta en la plataforma de Windows, tenga en cuenta que el código que se describe en este artículo es exactamente la misma por el rango de plataformas compatibles.

En este artículo se presentarán a la arquitectura del dispositivo IoT Azure SDK para C. Mostraremos cómo iniciar la biblioteca de dispositivo, enviar eventos a IoT concentrador así como recibir mensajes de él. La información en este artículo es suficiente para empezar a usar el SDK, pero también proporciona punteros a información adicional acerca de las bibliotecas.

## <a name="sdk-architecture"></a>Arquitectura SDK

Puede encontrar el **dispositivo de Azure IoT SDK para C** en el repositorio de [Microsoft Azure IoT SDK](https://github.com/Azure/azure-iot-sdks) GitHub y ver los detalles de la API en la [referencia de la API de C](http://azure.github.io/azure-iot-sdks/c/api_reference/index.html).

La última versión de las bibliotecas se puede encontrar en la rama **principal** del repositorio:

  ![](media/iot-hub-device-sdk-c-intro/01-MasterBranch.PNG)

Este repositorio contiene toda la familia de Azure IoT dispositivo SDK. Sin embargo, este artículo trata sobre el dispositivo de Azure IoT SDK *para C* que se encuentra en la carpeta **c** .

  ![](media/iot-hub-device-sdk-c-intro/02-CFolder.PNG)

* Puede encontrar la implementación principal del SDK en el **iothub\_cliente** carpeta que contiene la implementación de la capa de API inferior en el SDK: la biblioteca **IoTHubClient** . La biblioteca de **IoTHubClient** contiene las API de implementación de la mensajería sin formato para enviar mensajes a IoT concentrador, así como para recibir mensajes de él. Cuando se usa esta biblioteca, usted es responsable de la implementación de la serialización del mensaje (finalmente usando el ejemplo serializador se describe a continuación), pero se administran otros detalles de la comunicación con IoT concentrador para usted.
* La carpeta de **serializador** contiene funciones auxiliares y ejemplos que muestran cómo serializar datos antes de enviar a Azure IoT concentrador mediante la biblioteca de cliente. Observe que el uso del serializador no es obligatorio y solo proporciona para mayor comodidad. Si utiliza la biblioteca de **serializador** , iniciar mediante la definición de un modelo que especifica los eventos que desea enviar a IoT concentrador, así como los mensajes que se espera recibir de ella. Una vez que se define el modelo, el SDK proporciona una superficie de API que le permite trabajar fácilmente con los eventos y mensajes sin tener que preocuparse de los detalles de serialización.
La biblioteca depende de otras bibliotecas de código abierto que implementan transporte con varios protocolos (MQTT, AMQP).
* La biblioteca **IoTHubClient** depende de otras bibliotecas de código abierto:
   * La biblioteca de [Azure C compartida utilidad](https://github.com/Azure/azure-c-shared-utility) que proporciona la funcionalidad común para las tareas básicas (como cadena, manipulación de lista, IO, etc....) necesario a través de varios SDK de C relacionados de Azure
   * La biblioteca de [Azure uAMQP](https://github.com/Azure/azure-uamqp-c) que es la implementación del lado cliente de AMQP optimizado para dispositivos de restricción de recursos.
   * La biblioteca de [Azure uMQTT](https://github.com/Azure/azure-umqtt-c) que es una biblioteca general implementar el protocolo MQTT y optimizados para dispositivos de restricción de recursos.

Todo esto es más fácil de entender mediante el código de ejemplo. Las secciones siguientes le guiarán a través de un par de las aplicaciones de ejemplo que se incluyen en el SDK. Esto debe dar una buena idea de las distintas capacidades de las capas arquitectónicas del SDK, así como una introducción sobre cómo funcionan las API.

## <a name="before-running-the-samples"></a>Antes de ejecutar los ejemplos

Antes de poder ejecutar los ejemplos en el dispositivo de Azure IoT SDK para C debe crear una instancia del servicio en la suscripción de Azure si todavía no tiene una y completar 2 tareas:
* Preparar el entorno de desarrollo
* obtener las credenciales del dispositivo.

Si necesita crear una instancia de Azure IoT concentrador de su suscripción de Azure, siga las instrucciones [aquí](https://github.com/Azure/azure-iot-sdks/blob/master/doc/setup_iothub.md).

El [archivo Léame](https://github.com/Azure/azure-iot-sdks/tree/master/c) que se incluye con el SDK proporciona instrucciones para preparar su entorno de desarrollo y obtener las credenciales del dispositivo.
Las secciones siguientes incluyen algunos comentarios adicionales en las instrucciones.

### <a name="preparing-your-development-environment"></a>Preparar el entorno de desarrollo

Mientras se proporcionan paquetes para algunas plataformas (como NuGet para Windows o apt_get para Debian y Ubuntu) y los ejemplos de utilizan estos paquetes cuando esté disponible, las siguientes instrucciones explican cómo crear la biblioteca y las muestras directamente desde el código.

En primer lugar, necesitará obtener una copia del SDK de GitHub y, a continuación, crear el origen. Debe obtener una copia del origen de la rama **principal** del [repositorio de GitHub](https://github.com/Azure/azure-iot-sdks).

Cuando haya descargado una copia de la fuente, debe completar los pasos descritos en el artículo SDK ["Preparar el entorno de desarrollo"](https://github.com/Azure/azure-iot-sdks/blob/master/c/doc/devbox_setup.md).


Las siguientes son algunas sugerencias para ayudarle a completar el procedimiento descrito en la Guía de preparación:

-   Cuando se instala la utilidad **CMake** , elija la opción Agregar **CMake** a la ruta de acceso del sistema para **todos los usuarios** (agregando a también funciona **del usuario actual** ):

  ![](media/iot-hub-device-sdk-c-intro/08-CMake.PNG)


-   Antes de abrir el **símbolo del programador para VS2015**, instale las herramientas de línea de comandos de Git. Para instalar estas herramientas, realice los pasos siguientes:

    1. Inicie el programa de instalación de **2015 de Visual Studio** (o elija **Microsoft Visual Studio 2015** desde el panel de control de **programas y características** y seleccione **cambiar**).
    
    2. Asegúrese de que está activada la característica **Git para Windows** en Windows installer, pero también puede activar la opción de **GitHub extensión para Visual Studio** para proporcionar integración IDE:

        ![](media/iot-hub-device-sdk-c-intro/10-GitTools.PNG)

    3. Complete el Asistente de instalación para instalar las herramientas.

    4. Agregue el directorio de **bin** Git herramientas a la variable de entorno de **ruta de acceso** de sistema. En Windows, esto es similar a la siguiente:

        ![](media/iot-hub-device-sdk-c-intro/11-GitToolsPath.PNG)


Cuando haya completado los pasos descritos en la página ["Preparar el entorno de desarrollo"](https://github.com/Azure/azure-iot-sdks/blob/master/c/doc/devbox_setup.md) , está listo para compilar las aplicaciones de ejemplo.

### <a name="obtaining-device-credentials"></a>Obtener las credenciales del dispositivo

Ahora que está configurado el entorno de desarrollo, es lo siguiente obtener un conjunto de credenciales del dispositivo.  Para que un dispositivo puedan acceder a un concentrador IoT, primero debe agregar el registro del dispositivo de concentrador IoT el dispositivo. Al agregar el dispositivo recibirá un conjunto de credenciales del dispositivo que necesitará para el dispositivo para que pueda conectarse a un concentrador IoT. Las aplicaciones de ejemplo que veremos en la siguiente sección esperan estas credenciales en el formulario de una **cadena de conexión del dispositivo**.

Hay un par herramientas proporcionadas en el repositorio de Abrir origen SDK para ayudar a administrar el concentrador IoT. Una es una aplicación denominada Explorador de dispositivo, en el segundo se es un node.js herramienta CLI de distintas plataformas denominada iothub Explorador de Windows. Puede obtener más información sobre estas herramientas [aquí](https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md).

Como vamos a través de ejecutar los ejemplos de Windows en este artículo, estamos usando la herramienta de explorador de dispositivos. Pero también puede usar el Explorador de iothub si prefiere herramientas CLI.

La herramienta de [Explorador de dispositivo](https://github.com/Azure/azure-iot-sdks/tree/master/tools/DeviceExplorer) usa las bibliotecas de servicio de Azure IoT para realizar varias funciones de concentrador IoT, incluida la adición de dispositivos. Si utiliza el Explorador de dispositivo para agregar un dispositivo, recibirá una cadena de conexión correspondiente. Necesita esta cadena de conexión para hacer que el ejemplo ejecutarán aplicaciones.

En caso de que no ya está familiarizado con el proceso, el siguiente procedimiento describe cómo utilizar el Explorador de dispositivo para agregar un dispositivo y obtener una cadena de conexión del dispositivo.

Puede encontrar a un Windows installer para la herramienta de explorador de dispositivo en el [SDK de soltar la página](https://github.com/Azure/azure-iot-sdks/releases). Pero también puede ejecutar la herramienta directamente desde su código abrir **[DeviceExplorer.sln](https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/DeviceExplorer.sln)** en **Visual Studio de 2015** y la creación de la solución.

Cuando se ejecuta el programa verá esta interfaz:

  ![](media/iot-hub-device-sdk-c-intro/03-DeviceExplorer.PNG)

Escriba la **Cadena de conexión de Hub IoT** en el primer campo y haga clic en **Actualizar**. Esto configura la herramienta que se puede comunicar con IoT concentrador.

Una vez configurada la cadena de conexión de IoT Hub haga clic en la pestaña **administración** :

  ![](media/iot-hub-device-sdk-c-intro/04-ManagementTab.PNG)

Esto es donde podrá administrar los dispositivos que se registra en su centro IoT.

Puede crear un dispositivo haciendo clic en el botón **crear** . Se muestra un cuadro de diálogo con un conjunto de claves rellenarán (primario y secundarios). Todo lo que tiene que hacer es especificar un **Identificador de dispositivo** y, a continuación, haga clic en **crear**.

  ![](media/iot-hub-device-sdk-c-intro/05-CreateDevice.PNG)

Una vez creado el dispositivo, se actualiza la lista de dispositivos con todos los dispositivos registrados, incluyendo el que acaba de crear. Si haga el nuevo dispositivo, verá este menú:

  ![](media/iot-hub-device-sdk-c-intro/06-RightClickDevice.PNG)

Si elige la opción de **copiar la cadena de conexión para el dispositivo seleccionado** , la cadena de conexión de su dispositivo se copia en el Portapapeles. Mantener una copia de la cadena de conexión. Necesitará cuando se ejecuta las aplicaciones de ejemplo que se describe en las siguientes secciones.

Una vez haya completado los pasos anteriores, está listo para iniciar la ejecución de código. Dos ejemplos de tienen una constante en la parte superior del archivo de origen principal que permite escribir una cadena de conexión. Por ejemplo, la línea correspondiente de la **iothub\_cliente\_ejemplo\_amqp** aplicación aparece como sigue.

```
static const char* connectionString = "[device connection string]";
```

Si desea seguir a lo largo de, escriba la cadena de conexión de dispositivo, compilar la solución y podrá ejecutar el ejemplo.

## <a name="iothubclient"></a>IoTHubClient

Dentro de la **iothub\_cliente** carpeta en el repositorio de azure SDK de iot, hay una carpeta de **ejemplos** que contiene una aplicación denominada **iothub\_cliente\_ejemplo\_amqp**.

La versión de Windows de la **iothub\_cliente\_ejemplo\_ampq** aplicación incluye la solución de Visual Studio siguiente:

  ![](media/iot-hub-device-sdk-c-intro/12-iothub-client-sample-amqp.PNG)

Esta solución contiene un proyecto único. Es importante recordar que hay cuatro paquetes de NuGet instalados en esta solución:

- Microsoft.Azure.C.SharedUtility
- Microsoft.Azure.IoTHub.AmqpTransport
- Microsoft.Azure.IoTHub.IoTHubClient
- Microsoft.Azure.uamqp

Siempre debe el paquete de **Microsoft.Azure.C.SharedUtility** cuando se trabaja con el SDK. Dado que este ejemplo se basa en AMQP, también debe incluir los paquetes **Microsoft.Azure.uamqp** y **Microsoft.Azure.IoTHub.AmqpTransport** (no hay equivalentes paquetes para MQTT y HTTP). Como el ejemplo utiliza la biblioteca **IoTHubClient** , también debe incluir el paquete **Microsoft.Azure.IoTHub.IoTHubClient** en la solución.

Puede encontrar la implementación de la aplicación de ejemplo en el **iothub\_cliente\_ejemplo\_amqp.c** archivo de origen.

Usaremos esta aplicación de ejemplo para mostrarle lo necesario para usar la biblioteca **IoTHubClient** .

### <a name="initializing-the-library"></a>Inicialización de la biblioteca

> [AZURE.NOTE] Antes de empezar a trabajar con las bibliotecas, tendrá que realizar algunos inicialización específica de la plataforma. Por ejemplo, si tiene previsto usar AMQP en Linux debe inicializar la biblioteca OpenSSL. Los ejemplos en el [repositorio de GitHub](https://github.com/Azure/azure-iot-sdks) , llame a la función de utilidad **platform_init** cuando el cliente se inicia y llamar a la función **platform_deinit** antes de salir. Estas funciones están declaradas en el archivo de encabezado "platform.h". Debe examinar las definiciones de estas funciones de la plataforma de destino en el [repositorio](https://github.com/Azure/azure-iot-sdks) para determinar si debe incluir cualquier código de inicialización de plataforma en el cliente.

Para empezar a trabajar con las bibliotecas en primer lugar debe asignar un identificador de cliente IoT concentrador:

```
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Tenga en cuenta que estamos pasando una copia de la cadena de conexión de dispositivo a esta función (la que una se obtiene desde el Explorador de dispositivo). También se designa el protocolo que desea utilizar. Este ejemplo usa AMQP, pero MQTT y HTTP también son una opción.

Cuando tenga una válida **IOTHUB\_cliente\_controlador**, puede comenzar a llamar a las API para eventos de enviar y recibir mensajes de concentrador IoT. Veremos ese siguiente.

### <a name="sending-events"></a>Envío de eventos

Enviar eventos IoT concentrador requiere que complete los pasos siguientes:

Primero, cree un mensaje:

```
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Message_%d_From_IoTHubClient_Over_AMQP", i);
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText);
```

A continuación, envíe el mensaje:

```
IoTHubClient_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message);
```

Cada vez que envía un mensaje, especifique una referencia a una función de devolución de llamada que se invoca cuando se envían datos:

```
static void SendConfirmationCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    EVENT_INSTANCE* eventInstance = (EVENT_INSTANCE*)userContextCallback;
    (void)printf("Confirmation[%d] received for message tracking id = %d with result = %s\r\n", callbackCounter, eventInstance->messageTrackingId, ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
    /* Some device specific action code goes here... */
    callbackCounter++;
    IoTHubMessage_Destroy(eventInstance->messageHandle);
}
```

Observe la llamada a la **IoTHubMessage\_Destroy** cuando haya terminado con el mensaje de la función. Debe realizar esta llamada para liberar los recursos asignados cuando creó el mensaje.

### <a name="receiving-messages"></a>Recibir mensajes

Recibir un mensaje es una operación asincrónica. En primer lugar, registrar una devolución de llamada se invoca cuando el dispositivo recibe un mensaje:

```
int receiveContext = 0;
IoTHubClient_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext);
```

El último parámetro es un puntero nulo a cualquier cosa que desee. En la muestra es un puntero a un entero pero podría ser un puntero a una estructura de datos más complejo. Esto permite la función de devolución de llamada para que funcione en estado compartido con el llamador de esta función.

Cuando el dispositivo recibe un mensaje, se invoca la función de devolución de llamada registrado:

```
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    int* counter = (int*)userContextCallback;
    const char* buffer;
    size_t size;
    if (IoTHubMessage_GetByteArray(message, (const unsigned char**)&buffer, &size) == IOTHUB_MESSAGE_OK)
    {
        (void)printf("Received Message [%d] with Data: <<<%.*s>>> & Size=%d\r\n", *counter, (int)size, buffer, (int)size);
    }

    /* Some device specific action code goes here... */
    (*counter)++;
    return IOTHUBMESSAGE_ACCEPTED;
}
```

Tenga en cuenta que usa el **IoTHubMessage\_GetByteArray** función para recuperar el mensaje, que en este ejemplo es una cadena.

### <a name="uninitializing-the-library"></a>Cancelar la inicialización la biblioteca

Cuando haya terminado de eventos envío y recepción de mensajes, puede cancelar la inicialización de la biblioteca IoT. Para ello, emitir la llamada de función siguiente:

```
IoTHubClient_Destroy(iotHubClientHandle);
```

Esto permite liberar los recursos asignados previamente por la **IoTHubClient\_CreateFromConnectionString** función.

Como puede ver, es fácil enviar eventos y recibir mensajes con la biblioteca **IoTHubClient** . La biblioteca controla los detalles de la comunicación con IoT concentrador, incluido el protocolo que desea usar (desde la perspectiva del desarrollador, es una opción de configuración simple).

La biblioteca de **IoTHubClient** también proporciona un control preciso sobre cómo serializar los eventos que se envía el dispositivo a IoT concentrador. En algunos casos, esta es una ventaja, pero en otros casos esto es un detalle de implementación que no desea que preocuparse. Si es así, considere la posibilidad de usar la biblioteca **serializador** , que se describen en la siguiente sección.

## <a name="serializer"></a>Serializador

Conceptualmente la biblioteca **serializador** se encuentra encima de la biblioteca de **IoTHubClient** en el SDK. Utiliza la biblioteca de **IoTHubClient** para la comunicación subyacente con IoT concentrador, pero agrega capacidades de modelado que quitar la carga de trabajar con serialización de mensajes del desarrollador. ¿Cómo funciona de esta biblioteca es la mejor muestran un ejemplo.

En el **serializador** en el repositorio de azure SDK de iot es una carpeta de **ejemplos** que contiene una aplicación denominada **simplesample\_amqp**. La versión de Windows de este ejemplo incluye la solución de Visual Studio siguiente:

  ![](media/iot-hub-device-sdk-c-intro/14-simplesample_amqp.PNG)

Al igual que en el ejemplo anterior, éste incluye varios paquetes de NuGet:

- Microsoft.Azure.C.SharedUtility
- Microsoft.Azure.IoTHub.AmqpTransport
- Microsoft.Azure.IoTHub.IoTHubClient
- Microsoft.Azure.IoTHub.Serializer
- Microsoft.Azure.uamqp

Hemos visto la mayoría de ellos en el ejemplo anterior, pero **Microsoft.Azure.IoTHub.Serializer** es nuevo. Esto es necesario cuando se utiliza la biblioteca de **serializador** .

Puede encontrar la implementación de la aplicación de ejemplo en el **simplesample\_amqp.c** archivo.

Las secciones siguientes le guiarán a través de las partes principales de este ejemplo.

### <a name="initializing-the-library"></a>Inicialización de la biblioteca

Para empezar a trabajar con la biblioteca de **serializador** , debe llamar a la API de inicialización:

```
serializer_init(NULL);

IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);

ContosoAnemometer* myWeather = CREATE_MODEL_INSTANCE(WeatherStation, ContosoAnemometer);
```

La llamada a la **serializador\_inicialización** función es una llamada de una sola vez y se utiliza para iniciar la biblioteca subyacente. A continuación, llame a la **IoTHubClient\_CreateFromConnectionString** función, que es la misma API como en el ejemplo **IoTHubClient** . Esta llamada establece la cadena de conexión del dispositivo (esta es también donde elegir el protocolo que desea utilizar). Tenga en cuenta que este ejemplo usa AMQP como transporte, pero que podría haber utilizado HTTP.

Por último, llame a la **crear\_modelo\_instancia** función. Tenga en cuenta que **WeatherStation** es el espacio de nombres del modelo y **ContosoAnemometer** es el nombre del modelo. Una vez creada la instancia de modelo, puede usarlo para iniciar eventos de enviar y recibir mensajes. Sin embargo, es importante conocer qué un modelo es.

### <a name="defining-the-model"></a>Definir el modelo

Un modelo de la biblioteca de **serializador** define los eventos que puede enviar IoT concentrador y los mensajes, denominados *acciones* en el lenguaje de modelado, puede recibir el dispositivo. Definir un modelo con un conjunto de macros de C como en la **simplesample\_amqp** aplicación de ejemplo:

```
BEGIN_NAMESPACE(WeatherStation);

DECLARE_MODEL(ContosoAnemometer,
WITH_DATA(ascii_char_ptr, DeviceId),
WITH_DATA(double, WindSpeed),
WITH_ACTION(TurnFanOn),
WITH_ACTION(TurnFanOff),
WITH_ACTION(SetAirResistance, int, Position)
);

END_NAMESPACE(WeatherStation);
```

La **comenzar\_espacio de nombres** y **final\_espacio de nombres** macros ambos tomar el espacio de nombres del modelo como un argumento. Esperamos que nada entre estas macros es la definición de los modelos y las estructuras de datos que usan los modelos.

En este ejemplo, hay un solo modelo denominado **ContosoAnemometer**. En este modelo define dos eventos que puede enviar el dispositivo a IoT concentrador: **DeviceId** y **velocidad del viento**. También se define tres acciones (mensajes) que pueda recibir el dispositivo: **TurnFanOn**, **TurnFanOff**y **SetAirResistance**. Cada evento tiene un tipo y, a continuación, cada acción tiene un nombre (y, opcionalmente, un conjunto de parámetros).

Los eventos y las acciones que se definen en el modelo de definen una superficie de API que puede usar para enviar eventos a IoT concentrador, así como responder a mensajes que se envían al dispositivo. Se comprende mejor mediante un ejemplo.

### <a name="sending-events"></a>Envío de eventos

El modelo define los eventos que se pueden enviar a IoT concentrador. En este ejemplo, lo que significa que uno de los dos eventos definidos mediante la macro **WITH_DATA** . Por ejemplo, si desea enviar un evento de **velocidad del viento** a un concentrador IoT, hay algunos pasos implicados en lograr esto. La primera es establecer los datos que desea enviar:

```
myWeather->WindSpeed = 15;
```

El modelo que se definió anteriormente nos permite hacer esto mediante la configuración de un miembro de una **estructura**. A continuación, nos serializar el evento que desea enviar:

```
unsigned char* destination;
size_t destinationSize;

SERIALIZE(&destination, &destinationSize, myWeather->WindSpeed);
```

Este código serializa el evento a un búfer (al que hace referencia el **destino**). Por último, le enviaremos el evento al concentrador IoT con este código:

```
sendMessage(iotHubClientHandle, destination, destinationSize);
```

Esta es una función de ayuda en la aplicación de ejemplo que envía nuestro evento serie a IoT concentrador:

```
static void sendMessage(IOTHUB_CLIENT_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
{
    static unsigned int messageTrackingId;
    IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
    if (messageHandle != NULL)
    {
        if (IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)(uintptr_t)messageTrackingId) != IOTHUB_CLIENT_OK)
        {
            printf("failed to hand over the message to IoTHubClient");
        }
        else
        {
            printf("IoTHubClient accepted the message for delivery\r\n");
        }

        IoTHubMessage_Destroy(messageHandle);
    }
    free((void*)buffer);
    messageTrackingId++;
}
```

Este código es muy similar a lo veía en la **iothub\_cliente\_ejemplo\_amqp** aplicación, en las que crea un mensaje de una matriz de bytes y, a continuación, se utiliza **IoTHubClient\_SendEventAsync** para enviar a IoT concentrador. Después de que solo se debe liberar el identificador de mensaje y serie búfer de datos que se asignan una versión anterior.

El segundo al último parámetro de **IoTHubClient\_SendEventAsync** es una referencia a una función de devolución de llamada se llama cuando los datos se envían correctamente. Aquí tiene un ejemplo de una función de devolución de llamada:

```
void sendCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    int messageTrackingId = (intptr_t)userContextCallback;

    (void)printf("Message Id: %d Received.\r\n", messageTrackingId);

    (void)printf("Result Call Back Called! Result is: %s \r\n", ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
}
```

El segundo parámetro es un puntero al contexto de usuario; el mismo puntero pasado a **IoTHubClient\_SendEventAsync**. En este caso, el contexto es un contador simple, pero puede ser cualquier cosa que desee.

Eso es todo para enviar eventos. La única izquierda para cubrir es cómo recibir mensajes.

### <a name="receiving-messages"></a>Recibir mensajes

Recibir un mensaje funciona de forma similar a los mensajes de manera trabaja en la biblioteca de **IoTHubClient** . En primer lugar, registrar una función de devolución de llamada de mensaje:

```
IoTHubClient_SetMessageCallback(iotHubClientHandle, IoTHubMessage, myWeather)
```

A continuación, escribir la función de devolución de llamada que se invoca cuando se recibe un mensaje:

```
static IOTHUBMESSAGE_DISPOSITION_RESULT IoTHubMessage(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    IOTHUBMESSAGE_DISPOSITION_RESULT result;
    const unsigned char* buffer;
    size_t size;
    if (IoTHubMessage_GetByteArray(message, &buffer, &size) != IOTHUB_MESSAGE_OK)
    {
        printf("unable to IoTHubMessage_GetByteArray\r\n");
        result = EXECUTE_COMMAND_ERROR;
    }
    else
    {
        /*buffer is not zero terminated*/
        char* temp = malloc(size + 1);
        if (temp == NULL)
        {
            printf("failed to malloc\r\n");
            result = EXECUTE_COMMAND_ERROR;
        }
        else
        {
            memcpy(temp, buffer, size);
            temp[size] = '\0';
            EXECUTE_COMMAND_RESULT executeCommandResult = EXECUTE_COMMAND(userContextCallback, temp);
            result =
                (executeCommandResult == EXECUTE_COMMAND_ERROR) ? IOTHUBMESSAGE_ABANDONED :
                (executeCommandResult == EXECUTE_COMMAND_SUCCESS) ? IOTHUBMESSAGE_ACCEPTED :
                IOTHUBMESSAGE_REJECTED;
            free(temp);
        }
    }
    return result;
}
```

Este código repetitivo: es el mismo para cualquier solución. Esta función recibe el mensaje y se encarga de enrutamiento a la función adecuada a través de la llamada a **EXECUTE\_comando**. La función llamada en este momento depende de la definición de las acciones en nuestro modelo.

Al definir una acción del modelo, es necesaria para implementar una función que se llama cuando el dispositivo recibe el mensaje correspondiente. Por ejemplo, si el modelo define esta acción:

```
WITH_ACTION(SetAirResistance, int, Position)
```

Debe definir una función con esta firma:

```
EXECUTE_COMMAND_RESULT SetAirResistance(ContosoAnemometer* device, int Position)
{
    (void)device;
    (void)printf("Setting Air Resistance Position to %d.\r\n", Position);
    return EXECUTE_COMMAND_SUCCESS;
}
```

Tenga en cuenta que el nombre de la función coincide con el nombre de la acción en el modelo y que los parámetros de la función coincidir los parámetros de la acción. El primer parámetro siempre es necesario y contiene un puntero a la instancia de nuestro modelo.

Cuando el dispositivo recibe un mensaje que coincide con esta firma, se denomina la función correspondiente. Por lo tanto, aparte de tener que incluir el código reutilizable de **IoTHubMessage**, recibir mensajes sólo se trata de la definición de una función sencilla por cada acción definido en el modelo.

### <a name="uninitializing-the-library"></a>Cancelar la inicialización la biblioteca

Cuando haya terminado de enviar y recibir mensajes, puede cancelar la inicialización de la biblioteca IoT:

```
        DESTROY_MODEL_INSTANCE(myWeather);
    }
    IoTHubClient_Destroy(iotHubClientHandle);
}
serializer_deinit();
```

Cada una de estas tres funciones se alinea con las tres funciones de inicialización que se ha descrito anteriormente. Llamar a estas API garantiza que liberar los recursos asignados previamente.

## <a name="next-steps"></a>Pasos siguientes

En este artículo se abordan los conceptos básicos del uso de las bibliotecas en el **dispositivo de Azure IoT SDK para C**. Proporcionado suficiente información para comprender qué se incluye en el SDK, su arquitectura y cómo empezar a trabajar con los ejemplos de Windows. El siguiente artículo continúa la descripción del SDK por explicar [más acerca de la biblioteca IoTHubClient](iot-hub-device-sdk-c-iothubclient.md).

Para obtener más información sobre el desarrollo de concentrador IoT, consulte el [SDK de concentrador IoT][lnk-sdks].

Para explorar aún más las capacidades de concentrador IoT, consulte:

- [Simulación de un dispositivo con el SDK de puerta de enlace IoT][lnk-gateway]


[lnk-file upload]: iot-hub-csharp-csharp-file-upload.md
[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
