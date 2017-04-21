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

Al final de este tutorial, tendrá dos aplicaciones de consola de Node.js:

* **AddTagsAndQuery.js**, una aplicación de Node.js diseñada para ejecutarse desde el back-end, que agrega etiquetas y consultas a twins del dispositivo.
* **TwinSimulatedDevice.js**, una aplicación de Node.js que simula un dispositivo que se conecta a su centro IoT con la identidad del dispositivo creada anteriormente, los informes y su estado de conectividad.

> [AZURE.NOTE] El artículo [IoT concentrador SDK] [ lnk-hub-sdks] proporciona información sobre los distintos SDK que puede usar para crear aplicaciones dispositivo y back-end.

Para completar este tutorial, se necesita lo siguiente:

+ Node.js versión 0.10.x o posterior.

+ Una cuenta de Azure active. (Si no tiene una cuenta, puede crear una [cuenta gratuita] de[ lnk-free-trial] de dos minutos.)

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-the-service-app"></a>Crear la aplicación de servicio

En esta sección, creará una aplicación de consola Node.js que agrega metadatos de ubicación a la doble asociada **myDeviceId**. A continuación, las consultas de la twins almacenados en el hub de selección de los dispositivos que encuentran en los Estados Unidos y, a continuación, los que informar de una conexión móvil.

1. Crear una nueva carpeta vacía denominada **addtagsandqueryapp**. En la carpeta **addtagsandqueryapp** , cree un nuevo archivo package.json mediante el comando siguiente en el símbolo. Aceptar todos los valores predeterminados:

    ```
    npm init
    ```

2. En la línea de comandos en la carpeta **addtagsandqueryapp** , ejecute el comando siguiente para instalar el paquete de **azure iothub** :

    ```
    npm install azure-iothub@dtpreview --save
    ```

3. Con un editor de texto, cree un nuevo archivo de **AddTagsAndQuery.js** en la carpeta **addtagsandqueryapp** .

4. Agregue el código siguiente al archivo **AddTagsAndQuery.js** y sustituya el marcador de posición de **{cadena de conexión de servicio}** con la cadena de conexión que copió al crear su centro:

        'use strict';
        var iothub = require('azure-iothub');
        var connectionString = '{service hub connection string}';
        var registry = iothub.Registry.fromConnectionString(connectionString);

        registry.getTwin('myDeviceId', function(err, twin){
            if (err) {
                console.error(err.constructor.name + ': ' + err.message);
            } else {
                var patch = {
                    tags: {
                        location: {
                            region: 'US',
                            plant: 'Redmond43'
                      }
                    }
                };
             
                twin.update(patch, function(err) {
                  if (err) {
                    console.error('Could not update twin: ' + err.constructor.name + ': ' + err.message);
                  } else {
                    console.log(twin.deviceId + ' twin updated successfully');
                    queryTwins();
                  }
                });
            }
        });

    El objeto de **registro** expone todos los métodos necesarios para interactuar con twins dispositivo del servicio. El código anterior inicializa primero el objeto de **registro** y, a continuación, recupera al doble para **myDeviceId**y finalmente actualiza sus etiquetas con la información de la ubicación deseada.

    Después de actualizar las etiquetas llama a la función **queryTwins** .

7. Agregue el código siguiente al final de **AddTagsAndQuery.js** para implementar la función **queryTwins** :

        var queryTwins = function() {
            var query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });
            
            query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43 using cellular network: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });
        };

    El código anterior ejecuta dos consultas: el primero selecciona solo la gemelos de dispositivo de dispositivos ubicados en la planta **Redmond43** , y el segundo refina la consulta para seleccionar sólo los dispositivos que están conectados a través de la red de telefonía móvil.

    Observe que el código anterior, cuando se crea el objeto de **consulta** , especifica un número máximo de documentos devueltos. El objeto de **consulta** contiene una propiedad booleana **hasMoreResults** que puede usar para invocar los métodos de **nextAsTwin** varias veces para recuperar todos los resultados. Un método denominado **siguiente** está disponible para obtener los resultados que no son twins dispositivo por ejemplo, los resultados de las consultas de agregación.

8. Ejecute la aplicación con:

        node AddTagsAndQuery.js

    Debería ver un dispositivo en los resultados de la consulta que se solicita todos los dispositivos que se encuentra en **Redmond43** y ninguno para la consulta que restringe los resultados a los dispositivos que utilizan una red de telefonía móvil.

    ![][1]

En la sección siguiente se crea una aplicación de dispositivo que informa de la información de conectividad y cambia el resultado de la consulta en la sección anterior.

## <a name="create-the-device-app"></a>Crear la aplicación de dispositivo

En esta sección, creará un Node.js aplicación de consola que se conecta a su centro como **myDeviceId**y, a continuación, actualiza a su doble notificadas propiedades que contienen la información que está conectado con una red de telefonía móvil.

> [AZURE.NOTE] En este momento, twins dispositivo sólo son accesibles desde dispositivos que se conectan a concentrador IoT mediante el protocolo MQTT. Consulte el [soporte técnico MQTT] [ lnk-devguide-mqtt] artículo para obtener instrucciones sobre cómo convertir la aplicación de dispositivo existente usar MQTT.

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

6. Ahora que el dispositivo ha detectado su información de conectividad, debería aparecer en ambas consultas. Ir hacia atrás en la carpeta **addtagsandqueryapp** y vuelva a ejecutar las consultas:

        node AddTagsAndQuery.js

    En este momento **myDeviceId** debería aparecer en los resultados de la consulta.

    ![][3]

## <a name="next-steps"></a>Pasos siguientes
En este tutorial, configurar un nuevo concentrador IoT en el portal de Azure y, a continuación, crea una identidad de dispositivo en registro de identidad del concentrador. Había agregado dispositivo metadatos como etiquetas desde una aplicación de back-end y escribir una aplicación de dispositivo simulado para notificar información de conectividad de dispositivo en el doble del dispositivo. Además, ha aprendido cómo consultar esta información mediante el lenguaje de consulta SQL como concentrador de IoT.

Use los siguientes recursos para obtener información sobre cómo:

- enviar telemetría desde dispositivos con la [Introducción a IoT concentrador] [ lnk-iothub-getstarted] tutorial,
- configurar dispositivos con propiedades que desee de doble con las [propiedades que desee para configurar dispositivos de usar] [ lnk-twin-how-to-configure] tutorial,
- controlar los dispositivos de forma interactiva (por ejemplo, si un ventilador desde una aplicación controlada por el usuario), con los [métodos de uso directos] [ lnk-methods-tutorial] tutorial.

<!-- images -->
[1]: media/iot-hub-node-node-twin-getstarted/service1.png
[3]: media/iot-hub-node-node-twin-getstarted/service2.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-identity]: iot-hub-devguide-identity-registry.md

[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/

[lnk-twin-how-to-configure]: iot-hub-node-node-twin-how-to-configure.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md

[lnk-methods-tutorial]: iot-hub-c2d-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md