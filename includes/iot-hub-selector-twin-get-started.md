> [AZURE.SELECTOR]
- [Node.js](../articles/iot-hub/iot-hub-node-node-twin-getstarted.md)
- [C#](../articles/iot-hub/iot-hub-csharp-node-twin-getstarted.md)

## <a name="introduction"></a>Introducción

Twins dispositivo son documentos JSON que almacenan información de estado del dispositivo (metadatos, las configuraciones y condiciones). Concentrador IoT continúa a un doble dispositivo para cada dispositivo que se conecta a IoT concentrador.

Use twins dispositivo para:

* Almacenar metadatos de dispositivo de back-end.
* Informe información de estado actual como capacidades disponibles y condiciones (por ejemplo, el método de conectividad usado) desde la aplicación del dispositivo.
* Sincronizar el estado de flujos de trabajo de ejecución larga (por ejemplo, las actualizaciones de firmware y configuración) entre la aplicación de dispositivo y back-end.
* Los metadatos del dispositivo, la configuración o el estado de la consulta.

> [AZURE.NOTE] Twins dispositivo están diseñados para la sincronización y para consultar condiciones y configuraciones de dispositivos. Use [los mensajes de nube de dispositivo] [ lnk-d2c] secuencias de eventos de marca (como secuencias de telemetría de datos de sensor tiempo) y [métodos de dispositivo de nube] [ lnk-methods] para el control interactivo de dispositivos, como activar un ventilador desde una aplicación controlada por el usuario.

Gemelos de dispositivo se almacenan en un concentrador IoT y contienen:

* *etiquetas*, dispositivo metadatos accesible solamente por el back-end;
* *propiedades que desee*, objetos JSON modificables por el back-end y pueden observar la aplicación de dispositivo; y
* *Propiedades notificados*, JSON objetos modificables por la aplicación de dispositivo y legibles con la copia final. Etiquetas y propiedades no pueden contener matrices, pero es posible anidar objetos.

![][img-twin]

Además, el fondo de la aplicación puede consultar twins dispositivo basados en todos los datos anteriores.
Hacer referencia a [comprender twins de dispositivo] [ lnk-twins] para obtener más información acerca de twins del dispositivo y el [lenguaje de consulta de concentrador IoT] [ lnk-query] referencia para la consulta.

> [AZURE.NOTE] En este momento, twins dispositivo sólo son accesibles desde dispositivos que se conectan a concentrador IoT mediante el protocolo MQTT. Consulte el [soporte técnico MQTT] [ lnk-devguide-mqtt] artículo para obtener instrucciones sobre cómo convertir la aplicación de dispositivo existente usar MQTT.

En este tutorial se muestra cómo para:

- Crear una aplicación de back-end que agrega *las etiquetas* a doble de un dispositivo y un dispositivo simulado que informa de su canal de conectividad como un *notificadas propiedad* en el doble de dispositivo.
- Consultar dispositivos desde la aplicación de back-end con los filtros en las etiquetas y propiedades que creó anteriormente.


<!-- images -->
[img-twin]: media/iot-hub-selector-twin-get-started/twin.png

<!-- links -->
[lnk-query]: ../articles/iot-hub/iot-hub-devguide-query-language.md
[lnk-twins]: ../articles/iot-hub/iot-hub-devguide-device-twins.md
[lnk-d2c]: ../articles/iot-hub/iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: ../articles/iot-hub/iot-hub-mqtt-support.md