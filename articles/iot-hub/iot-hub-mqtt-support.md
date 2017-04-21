<properties
 pageTitle="Soporte técnico de IoT concentrador MQTT | Microsoft Azure"
 description="Descripción de MQTT de soporte técnico de nivel de concentrador IoT"
 services="iot-hub"
 documentationCenter=".net"
 authors="kdotchkoff"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/24/2016"
 ms.author="kdotchko"/>

# <a name="iot-hub-mqtt-support"></a>Soporte técnico de IoT concentrador MQTT

Concentrador IoT permite que los dispositivos para comunicarse con los extremos del dispositivo concentrador IoT utilizando la [MQTT v3.1.1] [ lnk-mqtt-org] protocolo en el puerto 8883 o MQTT v3.1.1 sobre Protocolo WebSocket en el puerto 443. Concentrador IoT requiere todas las comunicaciones de dispositivo esté protegido con SSL/TLS (por lo tanto, IoT concentrador no admite conexiones no seguras por el puerto 1883).

## <a name="connecting-to-iot-hub"></a>Conectarse a IoT concentrador

Un dispositivo puede usar el protocolo MQTT para conectarse a un concentrador IoT mediante las bibliotecas en el [SDK de Microsoft Azure IoT] [ lnk-device-sdks] o mediante el uso de la MQTT protocolo directamente.

## <a name="using-the-device-client-sdks"></a>Con el SDK de cliente de dispositivo

[Cliente de dispositivo SDK] [ lnk-device-sdks] que sea compatible con el protocolo MQTT están disponibles para Java, Node.js, C, C# y Python. El cliente de dispositivo SDK utilizar la cadena de conexión de IoT Hub estándar para establecer una conexión a un concentrador IoT. Para usar el protocolo MQTT, el parámetro de protocolo de cliente debe estar establecido en **MQTT**. De forma predeterminada, el cliente de dispositivo SDK que se conectan a un concentrador IoT con la **CleanSession** marcador establecido en **0** y usar **QoS 1** para intercambio de mensajes con el concentrador IoT.

Cuando un dispositivo está conectado a un concentrador IoT, el cliente de dispositivo SDK proporcionan métodos que habilita el dispositivo para enviar y recibir mensajes de un concentrador IoT.

En la tabla siguiente contiene vínculos a ejemplos de código para cada idioma admitido y se especifica el parámetro se utiliza para establecer una conexión a concentrador IoT mediante el protocolo MQTT.

| Idioma                   | Parámetros de protocolo        |
| -------------------------- | ------------------------- |
| [Node.js][lnk-sample-node] | Azure-iot-dispositivo-mqtt     |
| [Java][lnk-sample-java]    | IotHubClientProtocol.MQTT |
| [C][lnk-sample-c]          | MQTT_Protocol             |
| [C#][lnk-sample-csharp]    | TransportType.Mqtt        |
| [Python][lnk-sample-python] | IoTHubTransportProvider.MQTT |

### <a name="migrating-a-device-app-from-amqp-to-mqtt"></a>Migrar una aplicación de dispositivo de AMQP a MQTT
Si está utilizando el [cliente de dispositivo SDK][lnk-device-sdks], dejar de usar AMQP a MQTT requiere cambiar el parámetro de protocolo en la inicialización del cliente como se indicó anteriormente.

Al hacerlo, asegúrese de comprobar los elementos siguientes:

* AMQP devuelve los errores de muchas condiciones, mientras que MQTT finaliza la conexión. Como resultado, la lógica de excepciones pueden requerir algunos cambios.
* MQTT no admite las operaciones *Rechazar* al recibir [mensajes de C2D][lnk-messaging]. Si su back-end debe recibir una respuesta de la aplicación de dispositivo, piense en usar [métodos directos][lnk-methods].

## <a name="using-the-mqtt-protocol-directly"></a>Mediante el protocolo MQTT directamente

Si un dispositivo no puede utilizar al cliente de dispositivo SDK, aún puede conectarse a los extremos del dispositivo público mediante el protocolo MQTT. En el paquete de **Conectar** el dispositivo debe usar los siguientes valores:

- Para el campo **ClientId** , use la **deviceId**. 
- Para el campo **nombre de usuario** , use `{iothubhostname}/{device_id}`, donde {iothubhostname} es el registro CName completo del concentrador IoT.

    Por ejemplo, si el nombre de su centro IoT es **contoso.azure devices.net** y el nombre de su dispositivo es **MyDevice01**, debe contener el campo **nombre de usuario** completo `contoso.azure-devices.net/MyDevice01`.

- Para el campo de **contraseña** , utilice un símbolo de SA. El formato del token SA es el mismo que para los protocolos HTTP y AMQP:<br/>`SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`.

    Para obtener más información sobre cómo generar tokens SA, vea la sección de dispositivo usando IoT concentrador de [tokens]de seguridad[lnk-sas-tokens].
    
    Cuando las pruebas, también puede usar el [Explorador de dispositivo] [ lnk-device-explorer] herramienta para generar rápidamente un símbolo de SA que puede copiar y pegar en su propio código:
    
    1. Vaya a la pestaña **administración** en el Explorador de dispositivo.
    2. Haga clic en **El Token de SA** (superior derecha).
    3. En **SASTokenForm**, seleccione su dispositivo en **DeviceID** lista desplegable. Establecer el **TTL**.
    4. Haga clic en **Generar** para crear el token.
    
    El token de SA que se genera tiene esta estructura:   `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2fdevices%2fMyDevice01&sig=vSgHBMUG.....Ntg%3d&se=1456481802`.

    La parte de este token a utilizar como el campo de **contraseña** para conectarse con MQTT:   `SharedAccessSignature sr={your hub name}.azure-devices.net%2fdevices%2fyDevice01&sig=vSgHBMUG.....Ntg%3d&se=1456481802g%3d&se=1456481802`.

Para MQTT conectar y desconectar paquetes, concentrador IoT emite un evento en el canal de **Supervisión de operaciones** con información adicional que le puede ayudar a solucionar problemas de conectividad.

### <a name="sending-messages-to-iot-hub"></a>Enviar mensajes a IoT concentrador

Después de realizar una conexión correcta, un dispositivo puede enviar mensajes al concentrador IoT con `devices/{device_id}/messages/events/` o `devices/{device_id}/messages/events/{property_bag}` como un **Nombre de tema**. La `{property_bag}` elemento habilita el dispositivo enviar mensajes con propiedades adicionales en un formato de url codificada. Por ejemplo:

```
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [AZURE.NOTE] Esto `{property_bag}` elemento utiliza la misma codificación para cadenas de consulta en el protocolo HTTP.

También puede usar la aplicación cliente de dispositivo `devices/{device_id}/messages/events/{property_bag}` como la **tendrá el nombre del tema** al definir *los mensajes* se envíen como un mensaje de telemetría.

Concentrador IoT no admite QoS 2 mensajes. Si un cliente de dispositivo publica un mensaje con **QoS 2**, concentrador IoT cierra la conexión de red.
Concentrador IoT no persiste conservar mensajes. Si un dispositivo envía un mensaje con el indicador de **conservar** establecido en 1, concentrador IoT agrega la **x-participar-conservar** la propiedad de aplicación al mensaje. En este caso, en lugar de guardar el mensaje de conservar, concentrador IoT pasa a la aplicación de back-end.

### <a name="receiving-messages"></a>Recibir mensajes

Para recibir mensajes de concentrador IoT, deberá suscribirse un dispositivo con `devices/{device_id}/messages/devicebound/#` como un **Filtro de tema**. El carácter comodín multinivel **#** en el filtro de tema solo se usa para permitir que el dispositivo recibir propiedades adicionales en el nombre del tema. IoT concentrador no permite el uso de la **#** o **?** caracteres comodín para el filtrado de temas secundarios. Como concentrador IoT no es un agente de mensajería de pub sub de propósito general, solo es compatible con los filtros de tema y los nombres de tema documentado.

Por favor, tenga en cuenta que el dispositivo no recibirá todos los mensajes de concentrador IoT, antes de se ha suscrito correctamente a su extremo específico del dispositivo, representada por la `devices/{device_id}/messages/devicebound/#` filtro de tema. Después de que se estableció la suscripción correctamente, el dispositivo empezará a recibir sólo los mensajes de dispositivo de nube que se han enviado a él después de la hora de la suscripción. Si el dispositivo se conecta con marca **CleanSession** establecida en **0**, la suscripción se conservará entre sesiones diferentes. En este caso, la próxima vez que conecta con **CleanSession 0** el dispositivo recibirá mensajes pendientes que se han enviado a él mientras estaba desconectado. Si el dispositivo usa marca **CleanSession** establecido en **1** , aunque no recibirá todos los mensajes de concentrador IoT hasta que se suscribe a su dispositivo de extremo.

Concentrador IoT envía mensajes con el **Nombre del tema** `devices/{device_id}/messages/devicebound/`, o `devices/{device_id}/messages/devicebound/{property_bag}` si existen las propiedades del mensaje. `{property_bag}`contiene pares de clave y valor url codificada de propiedades del mensaje. Solo las propiedades de la aplicación y propiedades del sistema establecidas por el usuario (por ejemplo, el **identificador del mensaje** o **correlationId**) se incluyen en la bolsa de propiedades. Nombres de propiedades de sistema tienen el prefijo **$**, propiedades de la aplicación utilizan el nombre de propiedad original sin prefijo.

Cuando un cliente de dispositivo se suscribe a un tema con **QoS 2**, concentrador IoT concede nivel máximo QoS 1 en el paquete **SUBACK** . Después de eso, concentrador IoT entregar los mensajes en el dispositivo con QoS 1.

### <a name="additional-considerations"></a>Consideraciones adicionales

Una consideración final, si necesita personalizar el comportamiento de protocolo MQTT en el lado de la nube, debería revisar la [puerta de enlace de protocolo de Azure IoT] [ lnk-azure-protocol-gateway] que le permiten implementar una puerta de enlace de protocolo personalizado de alto rendimiento que interactúa directamente con IoT concentrador. La puerta de enlace de protocolo de Azure IoT le permite personalizar el protocolo de dispositivo para acomodar implementaciones de MQTT brownfield u otros protocolos personalizados. Sin embargo, este enfoque requiere que ejecute y trabajar con una puerta de enlace de protocolo personalizado.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información, consulte las [notas sobre MQTT admite] [ lnk-mqtt-devguide] la Guía del programador de Azure IoT concentrador.

Para obtener más información sobre el protocolo MQTT, consulte la [documentación de MQTT][lnk-mqtt-docs].

Para obtener más información sobre cómo planear la implementación de concentrador IoT, consulte:

- [Azure certificadas para el catálogo de dispositivo IoT][lnk-devices]
- [Admitir protocolos adicionales][lnk-protocols]
- [Comparar con Hubs de evento][lnk-compare]
- [Ajuste de escala, HA y DR][lnk-scaling]

Para explorar aún más las capacidades de concentrador IoT, consulte:

- [Guía para programadores][lnk-devguide]
- [Simulación de un dispositivo con el SDK de puerta de enlace IoT][lnk-gateway]

[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-mqtt-org]: http://mqtt.org/
[lnk-mqtt-docs]: http://mqtt.org/documentation
[lnk-sample-node]: https://github.com/Azure/azure-iot-sdks/blob/develop/node/device/samples/simple_sample_device.js
[lnk-sample-java]: https://github.com/Azure/azure-iot-sdks/blob/develop/java/device/samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/iothub/SendReceive.java
[lnk-sample-c]: https://github.com/Azure/azure-iot-sdks/tree/master/c/iothub_client/samples/iothub_client_sample_mqtt
[lnk-sample-csharp]: https://github.com/Azure/azure-iot-sdks/tree/master/csharp/device/samples
[lnk-sample-python]: https://github.com/Azure/azure-iot-sdks/tree/master/python/device/samples
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/readme.md
[lnk-sas-tokens]: iot-hub-devguide-security.md#using-sas-tokens-as-a-device
[lnk-mqtt-devguide]: iot-hub-devguide-messaging.md#notes-on-mqtt-support
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md

[lnk-devices]: https://catalog.azureiotsuite.com/
[lnk-protocols]: iot-hub-protocol-gateway.md
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md

[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-messaging]: iot-hub-devguide-messaging.md
