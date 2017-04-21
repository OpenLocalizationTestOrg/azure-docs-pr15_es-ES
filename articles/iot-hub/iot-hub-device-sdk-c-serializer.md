<properties
    pageTitle="Dispositivo Azure IoT SDK para C - serializador | Microsoft Azure"
    description="Más información sobre el uso de la biblioteca de serializador en el dispositivo de Azure IoT SDK para C"
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

# <a name="microsoft-azure-iot-device-sdk-for-c--more-about-serializer"></a>Dispositivo de Microsoft Azure IoT SDK para C: más información acerca de serializador

El [primer artículo](iot-hub-device-sdk-c-intro.md) de esta serie presentó el **dispositivo de Azure IoT SDK para C**. El siguiente artículo proporciona una descripción más detallada de la [**IoTHubClient**](iot-hub-device-sdk-c-iothubclient.md). En este artículo se completa cobertura del SDK al proporcionar una descripción más detallada del componente restante: la biblioteca de **serializador** .

El artículo Introducción describe cómo usar la biblioteca **serializador** para enviar eventos a y recibir mensajes de concentrador IoT. En este artículo, ampliamos esa discusión proporcionando una explicación más completa de cómo del modelo de datos con el lenguaje de macros de **serializador** . El artículo también incluye información más detallada sobre cómo la biblioteca serializa mensajes (y en algunos casos, cómo puede controlar el comportamiento de serialización). También describiremos algunos parámetros que se pueden modificar que determinan el tamaño de los modelos de que crear.

Por último, el artículo repasa algunos temas de artículos anterior como mensaje y uso de la propiedad. Como se va a calcular, estas características funcionen de la misma manera mediante la biblioteca de **serializador** como lo hacen con la biblioteca **IoTHubClient** .

Todo lo descrito en este artículo se basa en los ejemplos de SDK **serializador** . Si desea seguir a lo largo, consulte la **simplesample\_amqp** y **simplesample\_http** aplicaciones incluidas en el dispositivo de Azure IoT SDK para C.

Puede encontrar el **dispositivo de Azure IoT SDK para C** en el repositorio de [Microsoft Azure IoT SDK](https://github.com/Azure/azure-iot-sdks) GitHub y ver los detalles de la API en la [referencia de la API de C](http://azure.github.io/azure-iot-sdks/c/api_reference/index.html).

## <a name="the-modeling-language"></a>El lenguaje de modelado

El [artículo de introducción](iot-hub-device-sdk-c-intro.md) de esta serie presentó el **dispositivo de Azure IoT SDK para C** modelado del lenguaje el ejemplo que se proporcionan en la **simplesample\_amqp** aplicación:

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

Como puede ver, se basa en el idioma de modelado C macros. Siempre comienzan con la definición de **inicial\_espacio de nombres** y siempre terminan con **final\_espacio de nombres**. Es común para su empresa, o bien, como en este ejemplo, el proyecto que está trabajando en el nombre del espacio de nombres.

¿Qué pasa dentro del espacio de nombres son las definiciones del modelo. En este caso, hay un único modelo para una anemometer. Una vez más, el modelo se puede denominar, pero normalmente se denomina para el dispositivo o el tipo de datos que desea intercambiar con IoT concentrador.  

Modelos contienen una definición de los eventos que puede entrada a IoT concentrador (los *datos*), así como los mensajes que puede recibir IoT concentrador ( *acciones*). Como puede ver en el ejemplo, los eventos tienen un tipo y un nombre. acciones de tienen un nombre y parámetros opcionales (cada uno con un tipo).

No se muestra en este ejemplo son los tipos de datos adicionales que son compatibles con el SDK. Trataremos esa siguiente.

> [AZURE.NOTE] Concentrador IoT hace referencia a los datos en que un dispositivo envía a él como *eventos*, mientras que el lenguaje de modelado hace referencia a él como *datos* (definido mediante **WITH_DATA**). Del mismo modo, concentrador IoT hace referencia a los datos que se envía a los dispositivos como *mensajes*, mientras que el lenguaje de modelado hace referencia a él como *acciones* (definido mediante **WITH_ACTION**). Tenga en cuenta que estos términos pueden utilizarse indistintamente en este artículo.

### <a name="supported-data-types"></a>Tipos de datos compatibles

Los siguientes tipos de datos son compatibles con modelos creados con la biblioteca de **serializador** :

| Tipo                    | Descripción                            |
|-------------------------|----------------------------------------|
| doble                  | doble precisión número de punto flotante |
| int                     | entero de 32 bits                         |
| flotante                   | número de punto flotante de precisión simple |
| largo                    | entero largo                           |
| int8\_t                 | entero de 8 bits                          |
| Int16\_t                | entero de 16 bits                         |
| Int32\_t                | entero de 32 bits                         |
| Int64\_t                | entero de 64 bits                         |
| BOOL                    | valor booleano                                |
| ASCII\_char\_ptr        | Cadena ASCII                           |
| EDM\_fecha\_tiempo\_desplazamiento | desplazamiento de fecha y hora                       |
| EDM\_GUID               | GUID                                   |
| EDM\_binario             | binario.                                 |
| DECLARAR\_estructura         | tipo de datos complejos                      |

Comencemos con el último tipo de datos. La **DECLARE\_estructura** le permite definir tipos de datos complejos, que son agrupaciones de los otros tipos simples. Estas agrupaciones nos permiten definir un modelo de es similar a esta:

```
DECLARE_STRUCT(TestType,
double, aDouble,
int, aInt,
float, aFloat,
long, aLong,
int8_t, aInt8,
uint8_t, auInt8,
int16_t, aInt16,
int32_t, aInt32,
int64_t, aInt64,
bool, aBool,
ascii_char_ptr, aAsciiCharPtr,
EDM_DATE_TIME_OFFSET, aDateTimeOffset,
EDM_GUID, aGuid,
EDM_BINARY, aBinary
);

DECLARE_MODEL(TestModel,
WITH_DATA(TestType, Test)
);
```

Nuestro modelo contiene un evento de datos único de tipo **TestType**. **TestType** es un tipo complejo que incluye a varios miembros, que colectivamente muestran los tipos simples admitidos por el **serializador** modelado del lenguaje.

Con un modelo así, podemos escribir código para enviar datos a concentrador IoT que aparece como sigue:

```
TestModel* testModel = CREATE_MODEL_INSTANCE(MyThermostat, TestModel);

testModel->Test.aDouble = 1.1;
testModel->Test.aInt = 2;
testModel->Test.aFloat = 3.0f;
testModel->Test.aLong = 4;
testModel->Test.aInt8 = 5;
testModel->Test.auInt8 = 6;
testModel->Test.aInt16 = 7;
testModel->Test.aInt32 = 8;
testModel->Test.aInt64 = 9;
testModel->Test.aBool = true;
testModel->Test.aAsciiCharPtr = "ascii string 1";

time_t now;
time(&now);
testModel->Test.aDateTimeOffset = GetDateTimeOffset(now);

EDM_GUID guid = { { 0x00, 0x01, 0x02, 0x03, 0x04, 0x05, 0x06, 0x07, 0x08, 0x09, 0x0A, 0x0B, 0x0C, 0x0D, 0x0E, 0x0F } };
testModel->Test.aGuid = guid;

unsigned char binaryArray[3] = { 0x01, 0x02, 0x03 };
EDM_BINARY binaryData = { sizeof(binaryArray), &binaryArray };
testModel->Test.aBinary = binaryData;

SendAsync(iotHubClientHandle, (const void*)&(testModel->Test));
```

Básicamente, estamos asignar un valor a todos los miembros de la estructura de **prueba** y, a continuación, llamar a **SendAsync** para enviar el evento de datos de **prueba** a la nube. **SendAsync** es una función auxiliar que envía un evento de datos único a IoT concentrador:

```
void SendAsync(IOTHUB_CLIENT_LL_HANDLE iotHubClientHandle, const void *dataEvent)
{
    unsigned char* destination;
    size_t destinationSize;
    if (SERIALIZE(&destination, &destinationSize, *(const unsigned char*)dataEvent) ==
    {
        // null terminate the string
        char* destinationAsString = (char*)malloc(destinationSize + 1);
        if (destinationAsString != NULL)
        {
            memcpy(destinationAsString, destination, destinationSize);
            destinationAsString[destinationSize] = '\0';
            IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromString(destinationAsString);
            if (messageHandle != NULL)
            {
                IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)0);

                IoTHubMessage_Destroy(messageHandle);
            }
            free(destinationAsString);
        }
        free(destination);
    }
}
```

Esta función serializa el evento de datos determinado y lo envía a concentrador IoT con **IoTHubClient\_SendEventAsync**. Este es el mismo código analizado en artículos anteriores (**SendAsync** encapsula la lógica en una función cómoda).

Una otra función auxiliar que se utiliza en el código anterior es **GetDateTimeOffset**. Esta función transforma el tiempo especificado en un valor de tipo **EDM\_fecha\_tiempo\_desplazamiento**:

```
EDM_DATE_TIME_OFFSET GetDateTimeOffset(time_t time)
{
    struct tm newTime;
    gmtime_s(&newTime, &time);
    EDM_DATE_TIME_OFFSET dateTimeOffset;
    dateTimeOffset.dateTime = newTime;
    dateTimeOffset.fractionalSecond = 0;
    dateTimeOffset.hasFractionalSecond = 0;
    dateTimeOffset.hasTimeZone = 0;
    dateTimeOffset.timeZoneHour = 0;
    dateTimeOffset.timeZoneMinute = 0;
    return dateTimeOffset;
}
```

Si ejecuta este código, el siguiente mensaje se envía a IoT concentrador:

```
{"aDouble":1.100000000000000, "aInt":2, "aFloat":3.000000, "aLong":4, "aInt8":5, "auInt8":6, "aInt16":7, "aInt32":8, "aInt64":9, "aBool":true, "aAsciiCharPtr":"ascii string 1", "aDateTimeOffset":"2015-09-14T21:18:21Z", "aGuid":"00010203-0405-0607-0809-0A0B0C0D0E0F", "aBinary":"AQID"}
```

Tenga en cuenta que la serialización es JSON, que es el formato generado por la biblioteca de **serializador** . Tenga en cuenta que cada miembro del objeto JSON serie coincide con los miembros de **TestType** definido en nuestro modelo. Los valores también coincidir exactamente con los utilizados en el código. Sin embargo, tenga en cuenta que los datos binarios codificado base 64: "AQID" es la base de 64 codificación de {0 x 01, 0 x 02, 0 x 03}.

Este ejemplo muestra la ventaja de usar la biblioteca de **serializador** : que nos permite enviar JSON a la nube, sin tener que tratar explícitamente serialización en nuestra aplicación. Simplemente hay que preocuparse es establecer los valores de los eventos de datos en nuestro modelo y, a continuación, llamar a la API simple para enviar los eventos a la nube.

Con esta información, podemos definir modelos que incluyen el intervalo de tipos de datos compatibles, incluidos los tipos complejos (¿podríamos incluso incluimos tipos complejos dentro de otros tipos complejos). Sin embargo, serie JSON generado por el ejemplo anterior se obtiene un punto importante. *¿Cómo* enviar datos con la biblioteca de **serializador** determina exactamente cómo se forma el JSON. Ese punto determinado es lo que trataremos a continuación.

## <a name="more-about-serialization"></a>Más información sobre serialización

La sección anterior resalta un ejemplo de los resultados generados por la biblioteca de **serializador** . En esta sección, explicaremos cómo la biblioteca serializa datos y cómo puede controlar ese comportamiento con la API de serialización.

Para avanzar la discusión sobre la serialización, se deberá trabajar con un modelo nuevo en función de un termostato. En primer lugar, vamos a proporcionar algunas fondo en el escenario que estamos intentando.

Queremos termostato que las medidas de temperatura y humedad del modelo. Cada elemento de datos se va a enviar a IoT concentrador diferente. De forma predeterminada, la ingresses termostato un evento de temperatura cada 2 minutos; un evento de humedad es ingressed una vez cada 15 minutos. Una vez ingressed cualquier evento, debe incluir una marca de tiempo que muestra el tiempo que se mide la temperatura correspondiente o humedad.

Dado que este escenario, mostraremos dos maneras diferentes de los datos del modelo y explicaremos el efecto que tiene modelado en los resultados de la serie.

### <a name="model-1"></a>Modelo 1

Aquí es la primera versión de un modelo que admite el escenario anterior:

```
BEGIN_NAMESPACE(Contoso);

DECLARE_STRUCT(TemperatureEvent,
int, Temperature,
EDM_DATE_TIME_OFFSET, Time);

DECLARE_STRUCT(HumidityEvent,
int, Humidity,
EDM_DATE_TIME_OFFSET, Time);

DECLARE_MODEL(Thermostat,
WITH_DATA(TemperatureEvent, Temperature),
WITH_DATA(HumidityEvent, Humidity)
);

END_NAMESPACE(Contoso);
```

Observe que el modelo incluye dos eventos de datos: **temperatura** y **humedad**. A diferencia de los ejemplos anteriores, el tipo de cada evento es una estructura definida con **DECLARE\_estructura**. **TemperatureEvent** incluye una medida de temperatura y una marca de hora; **HumidityEvent** contiene una medida de humedad y una marca de tiempo. En este modelo nos proporciona una manera natural modelar los datos para el escenario descrito anteriormente. Cuando se envía un evento en la nube, ya sea le enviaremos un temperatura de fecha o un par de humedad o marca de tiempo.

Podemos enviar un evento de temperatura en la nube mediante código como el siguiente:

```
time_t now;
time(&now);
thermostat->Temperature.Temperature = 75;
thermostat->Temperature.Time = GetDateTimeOffset(now);

unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Se podrá usar valores codificados de temperatura y humedad en el código de ejemplo, pero imagine que realmente estamos recuperando estos valores por muestreo los sensores correspondientes en el termostato.

El código anterior usa la aplicación auxiliar **GetDateTimeOffset** que se introdujo previamente. Por motivos de que se convertirá en Borrar posteriores, este código explícitamente separa la tarea de serializar y enviar el evento. El código anterior serializa el evento de temperatura en un búfer. A continuación, **sendMessage** es una función auxiliar (incluidos en **simplesample\_amqp**) que envía el evento a IoT concentrador:

```
static void sendMessage(IOTHUB_CLIENT_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
{
    static unsigned int messageTrackingId;
    IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
    if (messageHandle != NULL)
    {
        IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)(uintptr_t)messageTrackingId);

        IoTHubMessage_Destroy(messageHandle);
    }
    free((void*)buffer);
}
```

Este código es un subconjunto del Ayudante **SendAsync** descrito en la sección anterior, de modo que no vuelve a sobre él aquí.

Cuando se ejecuta el código anterior para enviar el evento de temperatura, este número de serie formulario del evento se envía a IoT concentrador:

```
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

Nos estamos enviar una temperatura que es de tipo **TemperatureEvent** y estructura contiene a un miembro de **temperatura** y la **hora** . Esto se refleja directamente en el número de serie de datos.

Asimismo, podemos enviarle un evento de humedad con este código:

```
thermostat->Humidity.Humidity = 45;
thermostat->Humidity.Time = GetDateTimeOffset(now);
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

El formulario de número de serie que se envía a IoT concentrador aparece como sigue:

```
{"Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

De nuevo, esto es como se esperaba.

Con este modelo, puede imaginar eventos adicionales puede agregar fácilmente. Definir más estructuras utilizando **DECLARE\_estructura**e incluir el evento correspondiente en el modelo mediante **con\_datos**.

Ahora, vamos a modificar el modelo para que incluya los mismos datos, pero con una estructura diferente.

### <a name="model-2"></a>Modelo 2

Tenga en cuenta este modelo alternativo a la anterior:

```
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

En este caso hemos eliminado la **DECLARE\_estructura** macros y simplemente definir los elementos de datos de nuestro escenario con tipos simples del idioma de modelado.

Simplemente por el momento vamos a pasar por alto el evento de **tiempo** . Con ese margen, éste es el código para la **temperatura**de entrada:

```
time_t now;
time(&now);
thermostat->Temperature = 75;

unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Este código envía el siguiente evento serie a IoT concentrador:

```
{"Temperature":75}
```

Y el código para enviar el evento humedad aparece como sigue:

```
thermostat->Humidity = 45;
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Este código envía a IoT concentrador:

```
{"Humidity":45}
```

Hasta ahora no hay sorpresas. Ahora vamos a cambiar cómo se utiliza la macro SERIALIZE.

La macro **SERIALIZE** puede tardar varios eventos de datos como argumentos. Esto nos permite serializar el evento de **temperatura** y **humedad** juntos y enviarlos a IoT concentrador en una llamada:

```
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Se puede suponer que el resultado de este código es que se envían los eventos de datos de dos IoT concentrador:

[

{"Temperatura": 75},

{"Humedad": 45}

]

En otras palabras, cabría esperar que este código es el mismo que enviar **temperatura** y **humedad** por separado. Es simplemente una comodidad para pasar ambos eventos a **SERIALIZE** en la misma llamada. Sin embargo, no es el caso. En su lugar, el código anterior envía este evento de datos único a IoT concentrador:

{"Temperatura": 75, "humedad": 45}

Esto puede parecer extraño porque nuestro modelo define **temperatura** y **humedad** como dos eventos *independiente* :

```
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Más hasta el momento, no modelar estos eventos donde la **temperatura** y **humedad** están en la misma estructura:

```
DECLARE_STRUCT(TemperatureAndHumidityEvent,
int, Temperature,
int, Humidity,
);

DECLARE_MODEL(Thermostat,
WITH_DATA(TemperatureAndHumidityEvent, TemperatureAndHumidity),
);
```

Si se utiliza este modelo, es más fácil de entender cómo **temperatura** y **humedad** se pueden enviar el mismo mensaje de número de serie. Sin embargo no puede borrar por qué funciona de esta forma cuando se pasan a los eventos de datos a **SERIALIZE** con modelo 2.

Este comportamiento es más fácil de entender si sabe lo que está realizando la biblioteca **serializador** . Para poder interpretar esto volvamos a nuestro modelo:

```
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Piense en este modelo en términos orientado a objetos. En este caso, estamos proyectando un dispositivo físico (termostato) y ese dispositivo incluye atributos como **temperatura** y **humedad**.

Podemos enviar todo el estado de nuestro modelo con código como el siguiente:

```
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity, thermostat->Time) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Suponiendo que los valores de temperatura, humedad y hora, veremos un evento así enviado a IoT concentrador:

```
{"Temperature":75, "Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

A veces solo desea enviar *algunas* de las propiedades del modelo a la nube (Esto es cierto si el modelo contiene un gran número de eventos de datos). Es útil enviar solo un subconjunto de eventos de datos, como en el ejemplo anterior:

```
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

Esto genera exactamente el mismo evento serie como si hubiera definidos por un **TemperatureEvent** con un miembro de **temperatura** y la **hora** , como se hizo con modelo 1. En este caso, pudimos generar exactamente el mismo evento serie mediante un modelo diferente (modelo 2) porque llamamos **SERIALIZE** de manera diferente.

El punto importante es si pasa varios eventos de datos a **SERIALIZE,** se supone que cada evento es una propiedad en un único objeto JSON.

El mejor método depende de y cómo piensa en el modelo. Si va a enviar "eventos" en la nube y cada evento contiene un conjunto de propiedades, el primer método tiene mucho sentido. En ese caso usaría **DECLARE\_estructura** para definir la estructura de cada evento y, a continuación, incluirlos en el modelo mediante la **con\_datos** macro. A continuación, enviar cada evento igual que en el primer ejemplo anterior. En este enfoque sólo se debería pasar un evento de datos único al **SERIALIZADOR**.

Si piensa en el modelo de un modo orientado a objetos, a continuación, el segundo enfoque posible más le convenga. En este caso, los elementos definidos mediante **con\_datos** son las propiedades de"" del objeto. Pasar cualquier subconjunto de eventos a **SERIALIZE** que le gusta, dependiendo de la cantidad de estado de su "del objeto" que desea enviar a la nube.

Enfoque ninguno de estos método es correcta o incorrecta. Solo debe tener en cuenta sobre el funcionamiento de la biblioteca de **serializador** y elija el enfoque de modelado que mejor se adapte a sus necesidades.

## <a name="message-handling"></a>Control de mensajes

Hasta el momento en este artículo solo se han tratado envío eventos a IoT concentrador y no se ha tratado de recibir mensajes. El motivo es que necesitamos saber sobre la recepción de mensajes se en gran medida ha cubierto en un [artículo anterior](iot-hub-device-sdk-c-intro.md). Recuperar de dicho artículo procesar los mensajes mediante el registro de una función de devolución de llamada de mensaje:

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

Esta implementación de **IoTHubMessage** llama a la función específica para cada acción del modelo. Por ejemplo, si el modelo define esta acción:

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

A continuación, se denomina **SetAirResistance** cuando el mensaje se envía a su dispositivo.

¿Qué no hemos explicamos todavía es el aspecto de la versión de serie de mensaje. ¿En otras palabras, si desea enviar un mensaje de **SetAirResistance** en el dispositivo, lo aspecto tiene?

Si va a enviar un mensaje a un dispositivo, lo haría a través del servicio de Azure IoT SDK. Necesitará saber qué cadena para enviar a invocar una acción determinada. El formato general para enviar un mensaje aparece como sigue:

```
{"Name" : "", "Parameters" : "" }
```

Va a enviar un objeto JSON serie con dos propiedades: **nombre** es el nombre de la acción (mensaje) y **parámetros** contiene los parámetros de acción.

Por ejemplo, para invocar **SetAirResistance** puede enviar este mensaje a un dispositivo:

```
{"Name" : "SetAirResistance", "Parameters" : { "Position" : 5 }}
```

El nombre de la acción debe coincidir exactamente con una acción que se definen en el modelo. Los nombres de parámetro deben coincidir con también. Tenga en cuenta también entre mayúsculas y minúsculas. **Nombre** y los **parámetros** son siempre en mayúsculas. Asegúrese de que coincida con el nombre de su nombre de acción y los parámetros del modelo. En este ejemplo, el nombre de la acción es "SetAirResistance" y no "setairresistance".

Esta sección describe todo lo que necesita saber cuando los eventos de enviarlos y recibir mensajes con la biblioteca de **serializador** . Antes de continuar, vamos a cubrir algunos parámetros puede configurar que controlan el tamaño del modelo.

## <a name="macro-configuration"></a>Configuración de macros

Si está usando la biblioteca de **serializador** una parte importante del SDK que debería considerar se encuentra en la biblioteca de azure-c-compartido-utilidad.
Si clonar el repositorio de SDK de iot de Azure desde GitHub con la opción--recursiva, encontrará esta biblioteca compartida utilidad aquí:

```
.\\c\\azure-c-shared-utility
```

Si no dispone de clonar la biblioteca, puede encontrar [aquí](https://github.com/Azure/azure-c-shared-utility).

Dentro de la biblioteca de utilidades compartida, encontrará la siguiente carpeta:

```
azure-c-shared-utility\\macro\_utils\_h\_generator.
```

Esta carpeta contiene una solución de Visual Studio denominada **macro\_utilidades\_h\_generator.sln**:

  ![](media/iot-hub-device-sdk-c-serializer/01-macro_utils_h_generator.PNG)

El programa en esta solución genera la **macro\_utils.h** archivo. Hay una macro predeterminado\_archivo utils.h incluido con el SDK. Esta solución le permite modificar algunos parámetros y, a continuación, vuelva a crear el archivo de encabezado en función de estos parámetros.

Los dos parámetros claves que preocuparse por son **nArithmetic** y **nMacroParameters** que se definen en estas dos líneas que se encuentra en la macro\_utils.tt:

```
<#int nArithmetic=1024;#>
<#int nMacroParameters=124;/*127 parameters in one macro deﬁnition in C99 in chapter 5.2.4.1 Translation limits*/#>

```

Estos valores son los parámetros predeterminados incluidos con el SDK. Cada parámetro tiene el siguiente significado:

-   nMacroParameters – controla cuántos parámetros puede tener en una DECLARE\_definición de macro del modelo.

-   nArithmetic: controla el número total de miembros permitido en un modelo.

El motivo de que estos parámetros son importantes es porque controlan cómo grande puede ser el modelo. Por ejemplo, tenga en cuenta esta definición de modelo:

```
DECLARE_MODEL(MyModel,
WITH_DATA(int, MyData)
);
```

Como se mencionó anteriormente, **DECLARE\_modelo** es una macro de C. Los nombres del modelo y la **con\_datos** instrucción (aún otra macro) es parámetros de **DECLARE\_modelo**. **nMacroParameters** define cuántos parámetros pueden incluirse en **DECLARE\_modelo**. De hecho, define cuántos declaraciones de evento y una acción de datos puede tener. Por lo tanto, con el límite predeterminado de 124 Esto significa que puede definir un modelo con una combinación de aproximadamente 60 acciones y eventos de datos. Si intenta supera este límite, recibirá errores de compilador que tengan un aspecto parecidos a este:

  ![](media/iot-hub-device-sdk-c-serializer/02-nMacroParametersCompilerErrors.PNG)

El parámetro **nArithmetic** es más acerca del funcionamiento interno del lenguaje de macro de la aplicación.  Controlar el número total de los miembros que puede hacer en el modelo, incluidas las macros **DECLARE_STRUCT** . Si empieza a ver errores de compilador como esta, debe intente aumentar **nArithmetic**:

   ![](media/iot-hub-device-sdk-c-serializer/03-nArithmeticCompilerErrors.PNG)

Si desea cambiar estos parámetros, modifique los valores de la macro\_utils.tt de archivos, vuelva a compilar la macro\_utilidades\_h\_generator.sln solución y ejecute el programa compilado. Cuando lo haga, una nueva macro\_utils.h archivo generado y lo coloca en la. \\comunes\\directorio Inc..

Para poder usar la nueva versión de macro\_utils.h, quite el paquete de NuGet **serializador** de su solución y en su lugar, incluya el proyecto de Visual Studio **serializador** . Esto permite que el código compilar contra el código fuente de la biblioteca de serializador. Esto incluye la macro actualizada\_utils.h. Si desea hacer esto para **simplesample\_amqp**, empiece eliminando el paquete NuGet para la biblioteca de serializador de la solución:

   ![](media/iot-hub-device-sdk-c-serializer/04-serializer-github-package.PNG)

A continuación, agregue este proyecto a la solución de Visual Studio:

> . \\c\\serializador\\generar\\windows\\serializer.vcxproj

Cuando haya terminado, la solución debería tener este aspecto:

   ![](media/iot-hub-device-sdk-c-serializer/05-serializer-project.PNG)

Ahora cuando se compila la solución, la macro actualizada\_utils.h se incluye en binario.

Observe que aumenta estos valores suficientemente altos puede superar los límites de compilador. En este punto, el **nMacroParameters** es el parámetro principal con la que preocuparse. La especificación de C99 especifica que se permite un mínimo de 127 parámetros en una definición de macro. El compilador Microsoft sigue la especificación de exactamente (y tiene un límite de 127), por lo que no podrá aumentar **nMacroParameters** más allá de la predeterminada. Otros compiladores posible que le permita hacerlo (por ejemplo, el compilador GNU admite un límite superior).

Hasta ahora hemos tratado todo lo que debe saber sobre cómo escribir código con la biblioteca de **serializador** . Antes de concluir, vamos a revisar algunos temas de artículos anterior que puede que se pregunte sobre.

## <a name="the-lower-level-apis"></a>Las API de nivel inferior

La aplicación de ejemplo en el que se centra en este artículo es **simplesample\_amqp**. Este ejemplo se utiliza el nivel superior (el no "l") las API de eventos de enviar y recibir mensajes. Si utiliza estas API, ejecuta un subproceso de fondo que se encarga de envío de eventos y recibir mensajes. Sin embargo, puede usar las API de nivel inferior (LL) para eliminar este subproceso en segundo plano y tomar el control explícito al enviar eventos o recibir mensajes de la nube.

Como se describe en un [artículo anterior](iot-hub-device-sdk-c-iothubclient.md), hay un conjunto de funciones que consta de las API de nivel superior:

-   IoTHubClient\_CreateFromConnectionString

-   IoTHubClient\_SendEventAsync

-   IoTHubClient\_SetMessageCallback

-   IoTHubClient\_Destroy

Estas API se muestran en **simplesample\_amqp**.

También hay una análoga conjunto de API de nivel inferior.

-   IoTHubClient\_LL\_CreateFromConnectionString

-   IoTHubClient\_LL\_SendEventAsync

-   IoTHubClient\_LL\_SetMessageCallback

-   IoTHubClient\_LL\_Destroy

Tenga en cuenta que las API de nivel inferior funcionan exactamente del mismo modo, como se describe en los artículos anteriores. Puede usar el primer conjunto de API si desea controlar eventos enviarlos y recibir mensajes de un subproceso en segundo plano. Utilice el segundo conjunto de API si desea que el control explícito al enviar y recibir datos IoT concentrador de. Cada conjunto de trabajo de la API igualmente con la biblioteca de **serializador** .

Para obtener un ejemplo de cómo se usan las API de nivel inferior con la biblioteca de **serializador** , consulte la **simplesample\_http** aplicación.

## <a name="additional-topics"></a>Temas adicionales

Otros temas mencionar nuevo son propiedad tratamiento, con las credenciales del dispositivo alternativo y opciones de configuración. Estos son todos los temas de un [artículo anterior](iot-hub-device-sdk-c-iothubclient.md). El punto principal es todas estas características funcionan de la misma forma con la biblioteca de **serializador de** manera que con la biblioteca **IoTHubClient** . Por ejemplo, si desea adjuntar propiedades para un evento de su modelo, use **IoTHubMessage\_propiedades** y **mapa**\_**AddorUpdate**, la misma forma en que se ha descrito anteriormente:

```
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Si el evento generado desde la biblioteca de **serializador** o creado manualmente con la biblioteca de **IoTHubClient** no es relevante.

Para las credenciales de dispositivo alternativo mediante **IoTHubClient\_LL\_crear** funciona bien como **IoTHubClient\_CreateFromConnectionString** para asignar un **IOTHUB\_cliente\_controlador**.

Por último, si está usando la biblioteca de **serializador** , puede establecer opciones de configuración con **IoTHubClient\_LL\_SetOption** al igual que hizo al utilizar la biblioteca **IoTHubClient** .

Una característica que sea única a la biblioteca de **serializador** son la API de inicialización. Antes de empezar a trabajar con la biblioteca, debe llamar **serializador\_inicialización**:

```
serializer_init(NULL);
```

Esto se realiza antes de llamar a **IoTHubClient\_CreateFromConnectionString**.

Del mismo modo, cuando haya terminado trabajar con la biblioteca, la última llamada realizará es **serializador\_deinit**:

```
serializer_deinit();
```

En caso contrario, todas las otras características mencionadas funcionan de la misma en la biblioteca de **serializador** como lo hacen en la biblioteca de **IoTHubClient** . Para obtener más información acerca de cualquiera de estos temas, vea el [artículo anterior](iot-hub-device-sdk-c-iothubclient.md) de esta serie.

## <a name="next-steps"></a>Pasos siguientes

En este artículo se describe en detalle los aspectos únicos de la biblioteca de **serializador** contenidas en el **dispositivo de Azure IoT SDK para C**. Con la información proporcionada que debería tener una buena comprensión de cómo usar modelos de eventos de enviar y recibir mensajes de concentrador IoT.

Esto también concluye la serie de tres partes sobre cómo desarrollar aplicaciones con el **dispositivo de Azure IoT SDK para C**. Debe ser suficiente información no sólo empezar pero dar a comprender cómo funcionan las API. Para obtener información adicional, hay algunos ejemplos en el SDK no incluye aquí. En caso contrario, la [documentación del SDK](https://github.com/Azure/azure-iot-sdks) es un buen recurso para obtener información adicional.


Para obtener más información sobre el desarrollo de concentrador IoT, consulte el [SDK de concentrador IoT][lnk-sdks].

Para explorar aún más las capacidades de concentrador IoT, consulte:

- [Simulación de un dispositivo con el SDK de puerta de enlace IoT][lnk-gateway]

[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
