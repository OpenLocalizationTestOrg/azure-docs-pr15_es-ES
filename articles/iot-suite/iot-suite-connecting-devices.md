<properties
   pageTitle="Conectar un dispositivo con C en Windows | Microsoft Azure"
   description="Describe cómo conectar un dispositivo con la solución de supervisión de Azure IoT Suite preconfigurado remota mediante una aplicación escrita en C que ejecute Windows."
   services=""
   suite="iot-suite"
   documentationCenter="na"
   authors="dominicbetts"
   manager="timlt"
   editor=""/>

<tags
   ms.service="iot-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/05/2016"
   ms.author="dobett"/>


# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-windows"></a>Conectar el dispositivo a la solución preconfigurada supervisión remota (Windows)

[AZURE.INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## <a name="create-a-c-sample-solution-on-windows"></a>Crear una solución de ejemplo de C en Windows

Los pasos siguientes muestran cómo utilizar Visual Studio para crear una aplicación de cliente escrita en C que se comunica con la solución de supervisión remota preconfigurado.

Crear un proyecto de inicio en Visual Studio de 2015 y agregar los paquetes de NuGet IoT concentrador dispositivo cliente:

1. En Visual Studio de 2015, cree una aplicación de consola de C con la plantilla de **Aplicación de consola Win32** de Visual C++. Nombre del proyecto **RMDevice**.

2. En la página de **Configuración de aplicaciones** en el **Asistente para aplicaciones Win32**, asegúrese de que está seleccionada la opción **aplicación de consola** y desactive el **encabezado precompilado** y **comprueba el ciclo de vida de desarrollo de seguridad (SDL)**.

3. En el **Explorador de soluciones**, elimine los archivos stdafx.h, targetver.h y stdafx.cpp.

4. En el **Explorador de soluciones**, cambie el nombre del archivo RMDevice.cpp RMDevice.c.

5. En el **Explorador de soluciones**, haga clic en el proyecto **RMDevice** y, a continuación, haga clic en **Administrar NuGet paquetes**. Haga clic en **Examinar**, a continuación, buscar e instalar los siguientes paquetes de NuGet en el proyecto:

    - Microsoft.Azure.IoTHub.Serializer
    - Microsoft.Azure.IoTHub.IoTHubClient
    - Microsoft.Azure.IoTHub.HttpTransport

6. En el **Explorador de soluciones**, haga clic en el proyecto **RMDevice** y, a continuación, haga clic en **Propiedades** para abrir el cuadro de diálogo de **Páginas de propiedades** del proyecto. Para obtener más información, vea [Establecer las propiedades de un proyecto de Visual C++][lnk-c-project-properties]. 

7. Haga clic en la carpeta **vinculador** y luego haga clic en la página de propiedades de **entrada** .

8. Agregar **crypt32.lib** a la propiedad **Dependencias adicionales** . Haga clic en **Aceptar** y luego en **Aceptar** nuevamente para guardar el proyecto los valores de propiedad.

## <a name="specify-the-behavior-of-the-iot-hub-device"></a>Especificar el comportamiento del dispositivo concentrador IoT

Las bibliotecas de cliente de concentrador IoT usar un modelo para especificar el formato de los mensajes que envía el dispositivo IoT concentrador y los comandos que recibe de concentrador IoT.

1. En Visual Studio, abra el archivo RMDevice.c. Reemplazar el existente `#include` instrucciones con el siguiente código:

    ```
    #include "iothubtransporthttp.h"
    #include "schemalib.h"
    #include "iothub_client.h"
    #include "serializer.h"
    #include "schemaserializer.h"
    #include "azure_c_shared_utility/threadapi.h"
    #include "azure_c_shared_utility/platform.h"
    ```

2. Agregue las siguientes declaraciones de variable después de la `#include` instrucciones. Reemplace los valores de marcador de posición [Id. de dispositivo] y [dispositivo clave] con valores para el dispositivo desde el panel de solución de supervisión remota. Use el nombre de host de concentrador IoT desde el panel para reemplazar [nombre del IoTHub]. Por ejemplo, si el nombre de host de concentrador IoT es **contoso.azure devices.net**, reemplazar [nombre del IoTHub] con **contoso**:

    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "azure-devices.net";
    ```

3. Agregue el código siguiente para definir el modelo que permite comunicarse con concentrador IoT el dispositivo. En este modelo especifica que el dispositivo envía temperatura, externo temperatura, humedad y un identificador de dispositivo como telemetría. El dispositivo también envía metadatos acerca del dispositivo a IoT concentrador, incluida una lista de comandos que admite el dispositivo. Este dispositivo responde a los comandos **SetTemperature** y **SetHumidity**:

    ```
    // Define the Model
    BEGIN_NAMESPACE(Contoso);

    DECLARE_STRUCT(SystemProperties,
    ascii_char_ptr, DeviceID,
    _Bool, Enabled
    );

    DECLARE_STRUCT(DeviceProperties,
    ascii_char_ptr, DeviceID,
    _Bool, HubEnabledState
    );

    DECLARE_MODEL(Thermostat,

    /* Event data (temperature, external temperature and humidity) */
    WITH_DATA(int, Temperature),
    WITH_DATA(int, ExternalTemperature),
    WITH_DATA(int, Humidity),
    WITH_DATA(ascii_char_ptr, DeviceId),

    /* Device Info - This is command metadata + some extra fields */
    WITH_DATA(ascii_char_ptr, ObjectType),
    WITH_DATA(_Bool, IsSimulatedDevice),
    WITH_DATA(ascii_char_ptr, Version),
    WITH_DATA(DeviceProperties, DeviceProperties),
    WITH_DATA(ascii_char_ptr_no_quotes, Commands),

    /* Commands implemented by the device */
    WITH_ACTION(SetTemperature, int, temperature),
    WITH_ACTION(SetHumidity, int, humidity)
    );

    END_NAMESPACE(Contoso);
    ```

## <a name="implement-the-behavior-of-the-device"></a>Implementar el comportamiento del dispositivo

Ahora agregar código que implementa el comportamiento definido en el modelo.

1. Agregue las siguientes funciones que se ejecutan cuando el dispositivo recibe los comandos **SetTemperature** y **SetHumidity** IoT concentrador de:

    ```
    EXECUTE_COMMAND_RESULT SetTemperature(Thermostat* thermostat, int temperature)
    {
      (void)printf("Received temperature %d\r\n", temperature);
      thermostat->Temperature = temperature;
      return EXECUTE_COMMAND_SUCCESS;
    }

    EXECUTE_COMMAND_RESULT SetHumidity(Thermostat* thermostat, int humidity)
    {
      (void)printf("Received humidity %d\r\n", humidity);
      thermostat->Humidity = humidity;
      return EXECUTE_COMMAND_SUCCESS;
    }
    ```

2. Agregue la siguiente función que envía un mensaje a IoT concentrador:

    ```
    static void sendMessage(IOTHUB_CLIENT_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
    {
      IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
      if (messageHandle == NULL)
      {
        printf("unable to create a new IoTHubMessage\r\n");
      }
      else
      {
        if (IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, NULL, NULL) != IOTHUB_CLIENT_OK)
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
    }
    ```

3. Agregue la siguiente función que enlaza la biblioteca de serialización en el SDK de:

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

4. Agregue la siguiente función para conectarse a IoT concentrador, enviar y recibir mensajes y desconecte el concentrador. Observe cómo el dispositivo envía metadatos acerca de sí misma, incluidos los comandos que admite a IoT concentrador cuando se conecta. Estos metadatos permite la solución actualizar el estado del dispositivo para **Ejecutar** en el panel:

    ```
    void remote_monitoring_run(void)
    {
      if (serializer_init(NULL) != SERIALIZER_OK)
      {
        printf("Failed on serializer_init\r\n");
      }
      else
      {
        IOTHUB_CLIENT_CONFIG config;
        IOTHUB_CLIENT_HANDLE iotHubClientHandle;

        config.deviceId = deviceId;
        config.deviceKey = deviceKey;
        config.iotHubName = hubName;
        config.iotHubSuffix = hubSuffix;
        config.protocol = HTTP_Protocol;
        config.deviceSasToken = NULL;
        config.protocolGatewayHostName = NULL;
        iotHubClientHandle = IoTHubClient_Create(&config);
        if (iotHubClientHandle == NULL)
        {
          (void)printf("Failed on IoTHubClient_CreateFromConnectionString\r\n");
        }
        else
        {
          Thermostat* thermostat = CREATE_MODEL_INSTANCE(Contoso, Thermostat);
          if (thermostat == NULL)
          {
            (void)printf("Failed on CREATE_MODEL_INSTANCE\r\n");
          }
          else
          {
            STRING_HANDLE commandsMetadata;

            if (IoTHubClient_SetMessageCallback(iotHubClientHandle, IoTHubMessage, thermostat) != IOTHUB_CLIENT_OK)
            {
              printf("unable to IoTHubClient_SetMessageCallback\r\n");
            }
            else
            {

              /* send the device info upon startup so that the cloud app knows
              what commands are available and the fact that the device is up */
              thermostat->ObjectType = "DeviceInfo";
              thermostat->IsSimulatedDevice = false;
              thermostat->Version = "1.0";
              thermostat->DeviceProperties.HubEnabledState = true;
              thermostat->DeviceProperties.DeviceID = (char*)deviceId;

              commandsMetadata = STRING_new();
              if (commandsMetadata == NULL)
              {
                (void)printf("Failed on creating string for commands metadata\r\n");
              }
              else
              {
                /* Serialize the commands metadata as a JSON string before sending */
                if (SchemaSerializer_SerializeCommandMetadata(GET_MODEL_HANDLE(Contoso, Thermostat), commandsMetadata) != SCHEMA_SERIALIZER_OK)
                {
                  (void)printf("Failed serializing commands metadata\r\n");
                }
                else
                {
                  unsigned char* buffer;
                  size_t bufferSize;
                  thermostat->Commands = (char*)STRING_c_str(commandsMetadata);

                  /* Here is the actual send of the Device Info */
                  if (SERIALIZE(&buffer, &bufferSize, thermostat->ObjectType, thermostat->Version, thermostat->IsSimulatedDevice, thermostat->DeviceProperties, thermostat->Commands) != IOT_AGENT_OK)
                  {
                    (void)printf("Failed serializing\r\n");
                  }
                  else
                  {
                    sendMessage(iotHubClientHandle, buffer, bufferSize);
                  }

                }

                STRING_delete(commandsMetadata);
              }

              thermostat->Temperature = 50;
              thermostat->ExternalTemperature = 55;
              thermostat->Humidity = 50;
              thermostat->DeviceId = (char*)deviceId;

              while (1)
              {
                unsigned char*buffer;
                size_t bufferSize;

                (void)printf("Sending sensor value Temperature = %d, Humidity = %d\r\n", thermostat->Temperature, thermostat->Humidity);

                if (SERIALIZE(&buffer, &bufferSize, thermostat->DeviceId, thermostat->Temperature, thermostat->Humidity, thermostat->ExternalTemperature) != IOT_AGENT_OK)
                {
                  (void)printf("Failed sending sensor value\r\n");
                }
                else
                {
                  sendMessage(iotHubClientHandle, buffer, bufferSize);
                }

                ThreadAPI_Sleep(1000);
              }
            }

            DESTROY_MODEL_INSTANCE(thermostat);
          }
          IoTHubClient_Destroy(iotHubClientHandle);
        }
        serializer_deinit();
      }
    }
    ```
    
    Para referencia, le presentamos un mensaje de **DeviceInfo** de ejemplo enviado a concentrador IoT inicial:

    ```
    {
      "ObjectType":"DeviceInfo",
      "Version":"1.0",
      "IsSimulatedDevice":false,
      "DeviceProperties":
      {
        "DeviceID":"mydevice01", "HubEnabledState":true
      }, 
      "Commands":
      [
        {"Name":"SetHumidity", "Parameters":[{"Name":"humidity","Type":"double"}]},
        { "Name":"SetTemperature", "Parameters":[{"Name":"temperature","Type":"double"}]}
      ]
    }
    ```
    
    Para referencia, le presentamos un mensaje de **telemetría** de ejemplo enviado a IoT concentrador:

    ```
    {"DeviceId":"mydevice01", "Temperature":50, "Humidity":50, "ExternalTemperature":55}
    ```
    
    Para referencia, este es un ejemplo que se recibió el **comando** IoT concentrador de:
    
    ```
    {
      "Name":"SetHumidity",
      "MessageId":"2f3d3c75-3b77-4832-80ed-a5bb3e233391",
      "CreatedTime":"2016-03-11T15:09:44.2231295Z",
      "Parameters":{"humidity":23}
    }
    ```

5. Reemplazar la función **principal** con el siguiente código para invocar la función **remote_monitoring_run** :

    ```
    int main()
    {
      remote_monitoring_run();
      return 0;
    }
    ```

6. Haga clic en **Generar** y, a continuación, **Generar solución** para generar la aplicación del dispositivo.

7. En el **Explorador de soluciones**, haga clic en el proyecto **RMDevice** , haga clic en **Depurar**y, a continuación, haga clic en **Iniciar nueva instancia** para ejecutar el ejemplo. La consola muestra los mensajes cuando la aplicación envía telemetría muestra IoT concentrador y recibe comandos.

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]


[lnk-c-project-properties]: https://msdn.microsoft.com/library/669zx6zc.aspx