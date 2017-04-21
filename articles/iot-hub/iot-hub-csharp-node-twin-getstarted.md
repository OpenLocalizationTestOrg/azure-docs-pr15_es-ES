<properties
    pageTitle="Introducción a twins | Microsoft Azure"
    description="Este tutorial muestra cómo usar twins"
    services="iot-hub"
    documentationCenter="node"
    authors="fsautomata"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="node"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/13/2016"
     ms.author="elioda"/>

# <a name="tutorial-get-started-with-device-twins-preview"></a>Tutorial: Introducción a twins dispositivo (vista preliminar)

[AZURE.INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Al final de este tutorial, tendrá un .NET y una aplicación de consola Node.js:

* **AddTagsAndQuery.sln**, una aplicación de .NET pensada para ejecutarse desde el back-end, que agrega etiquetas y consultas a twins del dispositivo.
* **TwinSimulatedDevice.js**, una aplicación de Node.js que simula un dispositivo que se conecta a su centro IoT con la identidad del dispositivo creada anteriormente, los informes y su estado de conectividad.

> [AZURE.NOTE] El artículo [IoT concentrador SDK] [ lnk-hub-sdks] proporciona información sobre los distintos SDK que puede usar para crear aplicaciones dispositivo y back-end.

Para completar este tutorial, se necesita lo siguiente:

+ Microsoft Visual Studio 2015.

+ Node.js versión 0.10.x o posterior.

+ Una cuenta de Azure active. (Si no tiene una cuenta, puede crear una [cuenta gratuita] de[ lnk-free-trial] de dos minutos.)

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-the-service-app"></a>Crear la aplicación de servicio

En esta sección, creará una aplicación de consola Node.js que agrega metadatos de ubicación a la doble asociada **myDeviceId**. A continuación, las consultas de la twins almacenados en el hub de selección de los dispositivos que encuentran en los Estados Unidos y, a continuación, los que informar de una conexión móvil.

1. En Visual Studio, agregue un proyecto de Visual C# escritorio clásico de Windows a la solución actual con la plantilla de proyecto de **Aplicación de consola** . Nombre del proyecto **AddTagsAndQuery**.

    ![Nuevo proyecto de Visual C# escritorio clásico de Windows][img-createapp]

2. En el Explorador de soluciones, haga clic en el proyecto **AddTagsAndQuery** y, a continuación, haga clic en **Administrar paquetes de Nuget**.

3. En la ventana **Administrador de paquetes de Nuget** , asegúrese de que la **versión preliminar de incluir** está activada, busque **microsoft.azure.devices**, seleccione **instalar** para instalar el la versión *preliminar* de la **Microsoft.Azure.Devices** empaquetar y acepte los términos de uso. Este procedimiento descargas, instalaciones y agrega una referencia en el [SDK de servicio de Microsoft Azure IoT] [ lnk-nuget-service-sdk] Nuget paquete y sus dependencias.

    ![Ventana del Administrador de paquetes de NuGet][img-servicenuget]

4. Agregue las siguientes `using` instrucciones en la parte superior del archivo **Program.cs** :

        using Microsoft.Azure.Devices;

5. Agregue los campos siguientes a la clase **Program** . Reemplace el valor de marcador de posición con la cadena de conexión para el concentrador IoT que creó en la sección anterior.

        static RegistryManager registryManager;
        static string connectionString = "{iot hub connection string}";

6. Agregue el método siguiente a la clase **Program** :

        public static async Task AddTagsAndQuery()
        {
            var twin = await registryManager.GetTwinAsync("myDeviceId");
            var patch =
                @"{
                    tags: {
                        location: {
                            region: 'US',
                            plant: 'Redmond43'
                        }
                    }
                }";
            await registryManager.UpdateTwinAsync(twin.DeviceId, patch, twin.ETag);

            var query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
            var twinsInRedmond43 = await query.GetNextAsTwinAsync();
            Console.WriteLine("Devices in Redmond43: {0}", string.Join(", ", twinsInRedmond43.Select(t => t.DeviceId)));

            query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
            var twinsInRedmond43UsingCellular = await query.GetNextAsTwinAsync();
            Console.WriteLine("Devices in Redmond43 using cellular network: {0}", string.Join(", ", twinsInRedmond43UsingCellular.Select(t => t.DeviceId)));
        }

    La clase **RegistryManager** expone todos los métodos necesarios para interactuar con twins dispositivo del servicio. El código anterior inicializa primero el objeto **registryManager** y, a continuación, recupera al doble para **myDeviceId**y finalmente actualiza sus etiquetas con la información de la ubicación deseada.

    Después de actualizar, ejecuta dos consultas: el primero selecciona solo la gemelos de dispositivo de dispositivos ubicados en la planta **Redmond43** , y el segundo refina la consulta para seleccionar sólo los dispositivos que están conectados a través de la red de telefonía móvil.

    Observe que el código anterior, cuando se crea el objeto de **consulta** , especifica un número máximo de documentos devueltos. El objeto de **consulta** contiene una propiedad booleana **HasMoreResults** que puede usar para invocar los métodos de **GetNextAsTwinAsync** varias veces para recuperar todos los resultados. Un método denominado **GetNextAsJson** está disponible para obtener los resultados que no son twins dispositivo por ejemplo, los resultados de las consultas de agregación.

7. Por último, agregue las siguientes líneas al método **Main** :

        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddTagsAndQuery().Wait();
        Console.WriteLine("Press Enter to exit.");
        Console.ReadLine();

8. Ejecutar esta aplicación y verá un dispositivo en los resultados de la consulta que se solicita todos los dispositivos que se encuentra en **Redmond43** y ninguno para la consulta que restringe los resultados a los dispositivos que utilizan una red de telefonía móvil.

    ![Resultados de la consulta en la ventana][img-addtagapp]

En la sección siguiente se crea una aplicación de dispositivo que informa de la información de conectividad y cambia el resultado de la consulta en la sección anterior.

## <a name="create-the-device-app"></a>Crear la aplicación de dispositivo

En esta sección, creará un Node.js aplicación de consola que se conecta a su centro como **myDeviceId**y, a continuación, actualiza a su doble notificadas propiedades que contienen la información que está conectado con una red de telefonía móvil.

1. Crear una nueva carpeta vacía denominada **reportconnectivity**. En la carpeta **reportconnectivity** , cree un nuevo archivo package.json mediante el comando siguiente en el símbolo. Aceptar todos los valores predeterminados:

    ```
    npm init
    ```

2. En la línea de comandos en la carpeta **reportconnectivity** , ejecute el comando siguiente para instalar el paquete de **azure-iot-dispositivo-mqtt** y **dispositivo de azure iot**:

    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
    ```

3. Con un editor de texto, cree un nuevo archivo de **ReportConnectivity.js** en la carpeta **reportconnectivity** .

4. Agregue el código siguiente al archivo **ReportConnectivity.js** y sustituya el marcador de posición de **{cadena de conexión de dispositivo}** con la cadena de conexión que ha copiado al crear la identidad del dispositivo de **myDeviceId** :

        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;

        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);

        client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');

            client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                var patch = {
                    connectivity: {
                        type: 'cellular'
                    }
                };

                twin.properties.reported.update(patch, function(err) {
                    if (err) {
                        console.error('could not update twin');
                    } else {
                        console.log('twin state reported');
                        process.exit();
                    }
                });
            }
            });
        }
        });

    El objeto de **cliente** expone todos los métodos que se requieren para interactuar con twins dispositivo desde el dispositivo. El código anterior, una vez inicializa el objeto de **cliente** recupera al doble para **myDeviceId** y actualiza su propiedad notificado con la información de conectividad.

5. Ejecute la aplicación de dispositivo

        node ReportConnectivity.js

    Verá el mensaje `twin state reported`.

6. Ahora que el dispositivo ha detectado su información de conectividad, debería aparecer en ambas consultas. Ejecute la aplicación .NET **AddTagsAndQuery** para volver a ejecutar las consultas. En este momento **myDeviceId** debería aparecer en los resultados de la consulta.

    ![][img-addtagapp2]

## <a name="next-steps"></a>Pasos siguientes
En este tutorial, configurar un nuevo concentrador IoT en el portal de Azure y, a continuación, crea una identidad de dispositivo en registro de identidad del concentrador. Había agregado dispositivo metadatos como etiquetas desde una aplicación de back-end y escribir una aplicación de dispositivo simulado para notificar información de conectividad de dispositivo en el doble del dispositivo. Además, ha aprendido cómo consultar esta información mediante el lenguaje de consulta SQL como concentrador de IoT.

Use los siguientes recursos para obtener información sobre cómo:

- enviar telemetría desde dispositivos con la [Introducción a IoT concentrador] [ lnk-iothub-getstarted] tutorial,
- configurar dispositivos con propiedades que desee de doble con las [propiedades que desee para configurar dispositivos de usar] [ lnk-twin-how-to-configure] tutorial,
- controlar los dispositivos de forma interactiva (por ejemplo, si un ventilador desde una aplicación controlada por el usuario) con los [métodos de uso directos] [ lnk-methods-tutorial] tutorial.

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-node-twin-getstarted/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-twin-getstarted/createnetapp.png
[img-addtagapp]: media/iot-hub-csharp-node-twin-getstarted/addtagapp.png
[img-addtagapp2]: media/iot-hub-csharp-node-twin-getstarted/addtagapp2.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/1.1.0-preview-004

[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-identity]: iot-hub-devguide-identity-registry.md

[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-methods-tutorial]: iot-hub-c2d-methods.md
[lnk-twin-how-to-configure]: iot-hub-csharp-node-twin-how-to-configure.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md

