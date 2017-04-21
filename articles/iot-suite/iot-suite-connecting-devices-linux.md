<properties
   pageTitle="Conectar un dispositivo con C en Linux | Microsoft Azure"
   description="Describe cómo conectar un dispositivo con la solución de supervisión de Azure IoT Suite preconfigurado remota mediante una aplicación escrita en C que se ejecuta en Linux."
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


# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-linux"></a>Conectar el dispositivo a la solución de preconfigurados supervisión (Linux)

[AZURE.INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## <a name="build-and-run-a-sample-c-client-linux"></a>Crear y ejecutar a un cliente de ejemplo C Linux

Los procedimientos siguientes muestran cómo crear una aplicación de cliente, escrito en C e integrado y ejecutar en Ubuntu Linux, que se comunica con la solución de preconfigurados supervisión. Para completar estos pasos, necesita un dispositivo que ejecuta Ubuntu versión 15.04 o 15.10. Antes de continuar, instale los paquetes de requisitos previos en su dispositivo de Ubuntu mediante el siguiente comando:

```
sudo apt-get install cmake gcc g++
```

## <a name="install-the-client-libraries-on-your-device"></a>Instalar las bibliotecas de cliente en el dispositivo

Las bibliotecas de cliente de Azure IoT concentrador están disponibles como un paquete que se puede instalar en el dispositivo de Ubuntu utilizando el comando **obtener apt** . Complete los pasos siguientes para instalar el paquete que contiene los archivos de encabezado y de biblioteca de cliente de concentrador IoT en su equipo Ubuntu:

1. Agregue el repositorio de AzureIoT al equipo:

    ```
    sudo add-apt-repository ppa:aziotsdklinux/ppa-azureiot
    sudo apt-get update
    ```

2. Instalar el paquete de azure iot sdk c desarrollo

    ```
    sudo apt-get install -y azure-iot-sdk-c-dev
    ```

## <a name="add-code-to-specify-the-behavior-of-the-device"></a>Agregar código para especificar el comportamiento del dispositivo

En el equipo Ubuntu, cree una carpeta denominada **remoto\_supervisión**. En la **remoto\_supervisión** carpeta crear los cuatro archivos **main.c**, **remoto\_monitoring.c**, **remoto\_monitoring.h**y **CMakeLists.txt**.

Las bibliotecas de cliente de concentrador IoT serializador usar un modelo para especificar el formato de los mensajes que envía el dispositivo IoT concentrador y los comandos que recibe de concentrador IoT.

1. En un editor de texto, abra el **remoto\_monitoring.c** archivo. Agregue las siguientes `#include` instrucciones:

    ```
    #include "iothubtransportamqp.h"
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
    static const char* hubName = "IoTHub Name]";
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

### <a name="add-code-to-implement-the-behavior-of-the-device"></a>Agregar código para implementar el comportamiento del dispositivo

Agregue las funciones para ejecutar cuando el dispositivo recibe un comando de concentrador y el código para enviar telemetría simulada al concentrador.

1. Agregue las siguientes funciones que se ejecutan cuando el dispositivo recibe los comandos de **SetTemperature** y **SetHumidity** definidos en el modelo:

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
      if (platform_init() != 0)
      {
        printf("Failed to initialize the platform.\r\n");
      }
      else
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
          config.deviceSasToken = NULL;
          config.iotHubName = hubName;
          config.iotHubSuffix = hubSuffix;
          config.protocol = AMQP_Protocol;
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
        platform_deinit();
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

### <a name="add-code-to-invoke-the-remotemonitoringrun-function"></a>Agregar código para llamar a la función remote_monitoring_run

En un editor de texto, abra el archivo **remote_monitoring.h** . Agregue el código siguiente:

```
void remote_monitoring_run(void);
```

En un editor de texto, abra el archivo **main.c** . Agregue el código siguiente:

```
#include "remote_monitoring.h"

int main(void)
{
    remote_monitoring_run();

    return 0;
}
```

## <a name="use-cmake-to-build-the-client-application"></a>Usar CMake para generar la aplicación de cliente

Los pasos siguientes describen cómo usar *CMake* para generar la aplicación de cliente.

1. En un editor de texto, abra el archivo **CMakeLists.txt** en la carpeta **remote_monitoring** .

2. Agregue las siguientes instrucciones para definir cómo crear la aplicación cliente:

    ```
    cmake_minimum_required(VERSION 2.8.11)

    set(AZUREIOT_INC_FOLDER ".." "/usr/include/azureiot")

    include_directories(${AZUREIOT_INC_FOLDER})

    set(sample_application_c_files
        ./remote_monitoring.c
        ./main.c
    )

    set(sample_application_h_files
        ./remote_monitoring.h
    )

    add_executable(sample_app ${sample_application_c_files} ${sample_application_h_files})

    target_link_libraries(sample_app
        serializer
        iothub_client
        iothub_client_amqp_transport
        aziotsharedutil
        uamqp
        pthread
        curl
        ssl
        crypto
    )
    ```

3. En la carpeta **remote_monitoring** , cree una carpeta para almacenar los archivos *Asegúrese de* que genera CMake y, a continuación, ejecute los comandos **cmake** y **realizar** como sigue:

    ```
    mkdir cmake
    cd cmake
    cmake ../
    make
    ```

4. Ejecutar la aplicación cliente y enviar telemetría a IoT concentrador:

    ```
    ./sample_app
    ```

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

