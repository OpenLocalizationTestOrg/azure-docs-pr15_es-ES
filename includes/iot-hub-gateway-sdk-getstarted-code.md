## <a name="typical-output"></a>Salida típica

A continuación es un ejemplo de la salida al archivo de registro escrita el ejemplo Hello World. Se han agregado saltos de línea y caracteres de tabulación para la legibilidad:

```
[{
    "time": "Mon Apr 11 13:48:07 2016",
    "content": "Log started"
}, {
    "time": "Mon Apr 11 13:48:48 2016",
    "properties": {
        "helloWorld": "from Azure IoT Gateway SDK simple sample!"
    },
    "content": "aGVsbG8gd29ybGQ="
}, {
    "time": "Mon Apr 11 13:48:55 2016",
    "properties": {
        "helloWorld": "from Azure IoT Gateway SDK simple sample!"
    },
    "content": "aGVsbG8gd29ybGQ="
}, {
    "time": "Mon Apr 11 13:49:01 2016",
    "properties": {
        "helloWorld": "from Azure IoT Gateway SDK simple sample!"
    },
    "content": "aGVsbG8gd29ybGQ="
}, {
    "time": "Mon Apr 11 13:49:04 2016",
    "content": "Log stopped"
}]
```

## <a name="code-snippets"></a>Fragmentos de código

Esta sección describe algunas partes clave del código en el ejemplo Hello World.

### <a name="gateway-creation"></a>Creación de puerta de enlace

El desarrollador debe escribir el *proceso de puerta de enlace*. Este programa crea la infraestructura interna (el agente), carga los módulos y todo lo configura para que funcione correctamente. El SDK proporciona la función **Gateway_Create_From_JSON** para que pueda iniciar la ejecución desde un archivo JSON en una puerta de enlace. Para utilizar la función **Gateway_Create_From_JSON** que debe pasarle la ruta a un archivo JSON que especifica los módulos para cargar. 

Puede encontrar el código para el proceso de puerta de enlace en el ejemplo Hello World en el [main.c] [ lnk-main-c] archivo. Para mayor legibilidad, el fragmento de código siguiente muestra una versión abreviada del código del proceso de puerta de enlace. Este programa crea una puerta de enlace y, a continuación, espera a que el usuario presione la tecla **ENTRAR** antes de que se destruye la puerta de enlace. 

```
int main(int argc, char** argv)
{
    GATEWAY_HANDLE gateway;
    if ((gateway = Gateway_Create_From_JSON(argv[1])) == NULL)
    {
        printf("failed to create the gateway from JSON\n");
    }
    else
    {
        printf("gateway successfully created from JSON\n");
        printf("gateway shall run until ENTER is pressed\n");
        (void)getchar();
        Gateway_LL_Destroy(gateway);
    }
    return 0;
}
```

El archivo de configuración de JSON contiene una lista de módulos que desee cargar. Cada módulo debe especificar a:

- **Nombre_Del_Módulo**: un nombre único para el módulo.
- **module_path**: la ruta de acceso a la biblioteca que contiene el módulo. Para Linux es un archivo. SO, en Windows es un archivo .dll.
- **args**: cualquier información de configuración que necesita el módulo.

El archivo JSON también contiene los vínculos entre los módulos que se pasarán al intermediario. Un vínculo tiene dos propiedades:
- **origen**: el nombre de un módulo de la `modules` sección, o "\*".
- **receptor**: el nombre de un módulo de la `modules` sección

Cada vínculo define una ruta de mensaje y una dirección. Mensajes del módulo `source` se entregan en el módulo `sink`. El `source` se puede establecer en "\*", que indica que se reciban los mensajes desde cualquier módulo por `sink`.

En el siguiente ejemplo se muestra el archivo de configuración de JSON utilizado para configurar el ejemplo Hello World en Linux. Todos los mensajes producidos por módulo `hello_world` serán utilizados por el módulo `logger`. Si un módulo requiere que un argumento depende del diseño del módulo. En este ejemplo, el módulo registrador toma un argumento que es la ruta de acceso al archivo de salida y el módulo Hello World no toma ningún argumento:

```
{
    "modules" :
    [ 
        {
            "module name" : "logger",
            "module path" : "./modules/logger/liblogger_hl.so",
            "args" : {"filename":"log.txt"}
        },
        {
            "module name" : "hello_world",
            "module path" : "./modules/hello_world/libhello_world_hl.so",
            "args" : null
        }
    ],
    "links" :
    [
        {
            "source" : "hello_world",
            "sink" : "logger"
        }
    ]
}
```

### <a name="hello-world-module-message-publishing"></a>Publicación de mensajes en módulo Hello World

Puede encontrar el código utilizado por el módulo de "hello world" para publicar mensajes en el ['hello_world.c'] [ lnk-helloworld-c] archivo. El fragmento de código siguiente muestra una versión modificada con comentarios adicionales y algunos quitado para la legibilidad del código de control de errores:

```
int helloWorldThread(void *param)
{
    // create data structures used in function.
    HELLOWORLD_HANDLE_DATA* handleData = param;
    MESSAGE_CONFIG msgConfig;
    MAP_HANDLE propertiesMap = Map_Create(NULL);
    
    // add a property named "helloWorld" with a value of "from Azure IoT
    // Gateway SDK simple sample!" to a set of message properties that
    // will be appended to the message before publishing it. 
    Map_AddOrUpdate(propertiesMap, "helloWorld", "from Azure IoT Gateway SDK simple sample!")

    // set the content for the message
    msgConfig.size = strlen(HELLOWORLD_MESSAGE);
    msgConfig.source = HELLOWORLD_MESSAGE;

    // set the properties for the message
    msgConfig.sourceProperties = propertiesMap;
    
    // create a message based on the msgConfig structure
    MESSAGE_HANDLE helloWorldMessage = Message_Create(&msgConfig);

    while (1)
    {
        if (handleData->stopThread)
        {
            (void)Unlock(handleData->lockHandle);
            break; /*gets out of the thread*/
        }
        else
        {
            // publish the message to the broker
            (void)Broker_Publish(handleData->brokerHandle, helloWorldMessage);
            (void)Unlock(handleData->lockHandle);
        }

        (void)ThreadAPI_Sleep(5000); /*every 5 seconds*/
    }

    Message_Destroy(helloWorldMessage);

    return 0;
}
```

### <a name="hello-world-module-message-processing"></a>Procesamiento de mensajes de módulo Hello World

El módulo Hello World nunca debe procesar los mensajes que otros módulos publican al intermediario. Realiza la implementación de la devolución de llamada de mensaje en el módulo Hello World una función inefectiva.

```
static void HelloWorld_Receive(MODULE_HANDLE moduleHandle, MESSAGE_HANDLE messageHandle)
{
    /* No action, HelloWorld is not interested in any messages. */
}
```

### <a name="logger-module-message-publishing-and-processing"></a>Mensaje de módulo registrador de publicación y procesamiento

El módulo registrador recibe los mensajes de los agentes y los escribe en un archivo. Nunca se publica todos los mensajes. Por lo tanto, el código del módulo registrador nunca llama a la función **Broker_Publish** .

La función **Logger_Recieve** en la [logger.c] [ lnk-logger-c] archivo es la devolución de llamada que se invoca el agente para entregar mensajes en el módulo del registrador. El fragmento de código siguiente muestra una versión modificada con comentarios adicionales y algunos quitado para la legibilidad del código de control de errores:

```
static void Logger_Receive(MODULE_HANDLE moduleHandle, MESSAGE_HANDLE messageHandle)
{

    time_t temp = time(NULL);
    struct tm* t = localtime(&temp);
    char timetemp[80] = { 0 };

    // Get the message properties from the message
    CONSTMAP_HANDLE originalProperties = Message_GetProperties(messageHandle); 
    MAP_HANDLE propertiesAsMap = ConstMap_CloneWriteable(originalProperties);

    // Convert the collection of properties into a JSON string
    STRING_HANDLE jsonProperties = Map_ToJSON(propertiesAsMap);

    //  base64 encode the message content
    const CONSTBUFFER * content = Message_GetContent(messageHandle);
    STRING_HANDLE contentAsJSON = Base64_Encode_Bytes(content->buffer, content->size);

    // Start the construction of the final string to be logged by adding
    // the timestamp
    STRING_HANDLE jsonToBeAppended = STRING_construct(",{\"time\":\"");
    STRING_concat(jsonToBeAppended, timetemp);

    // Add the message properties
    STRING_concat(jsonToBeAppended, "\",\"properties\":"); 
    STRING_concat_with_STRING(jsonToBeAppended, jsonProperties);

    // Add the content
    STRING_concat(jsonToBeAppended, ",\"content\":\"");
    STRING_concat_with_STRING(jsonToBeAppended, contentAsJSON);
    STRING_concat(jsonToBeAppended, "\"}]");

    // Write the formatted string
    LOGGER_HANDLE_DATA *handleData = (LOGGER_HANDLE_DATA *)moduleHandle;
    addJSONString(handleData->fout, STRING_c_str(jsonToBeAppended);
}
```

## <a name="next-steps"></a>Próximos pasos

Para obtener información sobre cómo utilizar el SDK de puerta de enlace de IoT, consulte lo siguiente:

- [Puerta de enlace de IoT SDK: enviar mensajes de dispositivo a la nube con un dispositivo simulado utilizando Linux][lnk-gateway-simulated].
- [Puerta de enlace de IoT Azure SDK] [ lnk-gateway-sdk] en GitHub.

<!-- Links -->
[lnk-main-c]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/samples/hello_world/src/main.c
[lnk-helloworld-c]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/modules/hello_world/src/hello_world.c
[lnk-logger-c]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/modules/logger/src/logger.c
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/
[lnk-gateway-simulated]: ../articles/iot-hub/iot-hub-linux-gateway-sdk-simulated-device.md