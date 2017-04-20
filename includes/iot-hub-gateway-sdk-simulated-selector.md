> [AZURE.SELECTOR]
- [Linux](../articles/iot-hub/iot-hub-linux-gateway-sdk-simulated-device.md)
- [Windows](../articles/iot-hub/iot-hub-windows-gateway-sdk-simulated-device.md)

Este tutorial de la [nube simulado de dispositivo cargar ejemplo] muestra cómo utilizar el [SDK de Azure IoT Gateway] [ lnk-sdk] para enviar telemetría de dispositivo a la nube a IoT concentrador de dispositivos simulados.

Este tutorial trata sobre:

1. **Arquitectura**: información importante de arquitectura acerca del ejemplo cargar nube de dispositivo simulado.

2. **Compilación y ejecución**: los pasos necesarios para generar y ejecutar el ejemplo.

## <a name="architecture"></a>Arquitectura

El simulado nube de dispositivo cargar muestra cómo utilizar el SDK para crear una puerta de enlace que envía telemetría desde dispositivos simulados a un concentrador de IoT. Los dispositivos simulados no se pueden conectar directamente al concentrador IoT porque:

- Los dispositivos utilizan un protocolo de comunicaciones que se entiende por IoT concentrador.
- Los dispositivos no son suficientemente inteligentes como para recordar la identidad asignada por IoT concentrador.

La puerta de enlace resuelve estos problemas para los dispositivos simulados de las maneras siguientes:

- La puerta de enlace comprende el protocolo utilizado por los dispositivos simulados, recibe telemetría nube de dispositivo de los dispositivos y reenvía los mensajes al concentrador IoT mediante un protocolo entendido por el concentrador.
- La puerta de enlace almacena las identidades IoT concentrador en nombre de los dispositivos simulados y actúa como un proxy cuando los dispositivos simulados envían mensajes al concentrador IoT.

El siguiente diagrama muestra los componentes principales de la muestra, incluidos los módulos de puerta de enlace:

![][1]


> [AZURE.NOTE] Los módulos no pasan mensajes directamente entre sí. Los módulos de publican mensajes en un agente interno que envía los mensajes a los demás módulos mediante un mecanismo de suscripción como se muestra en el diagrama siguiente. Para obtener más información, vea [Introducción con el SDK de puerta de enlace de IoT][lnk-gw-getstarted].

### <a name="protocol-ingestion-module"></a>Módulo de protocolo de recopilación

Este módulo es el punto de partida para obtener datos de los dispositivos, a través de la puerta de enlace y en la nube. En el ejemplo, el módulo realiza cuatro tareas:

1.  Crea datos de temperatura simulada.
    
    Nota: Si utilizas dispositivos reales, el módulo podría leer datos de los dispositivos físicos.

2.  Coloca los datos de temperatura simulado en el contenido de un mensaje.

3.  Agrega una propiedad con una dirección MAC falsa en el mensaje que contiene los datos de temperatura simulada.

4.  Hace disponible para el siguiente módulo el mensaje en la cadena.

> [AZURE.NOTE] El módulo denominado **ingestión de protocolo X** en el diagrama anterior se llama **simulado de dispositivo** en el código fuente.

### <a name="mac-lt-gt-iot-hub-id-module"></a>MAC &lt; - &gt; módulo IoT concentrador ID

Este módulo busca mensajes que incluyen una propiedad que contiene la dirección MAC, agregada por el módulo de protocolo de la ingestión del dispositivo simulado. Si el módulo detecta este tipo de propiedad, agrega otra propiedad con una clave de dispositivo concentrador IoT al mensaje y, a continuación, hace que el mensaje disponible para el módulo siguiente de la cadena. Se trata de cómo el ejemplo asocia identidades de dispositivo concentrador IoT dispositivos simulados. El desarrollador configura la asignación entre las direcciones MAC y las identidades de concentrador IoT manualmente como parte de la configuración del módulo. 

> [AZURE.NOTE]  Este ejemplo utiliza una dirección MAC como un identificador único de dispositivo y correlaciona con una identidad de dispositivo concentrador IoT. Sin embargo, puede escribir su propio módulo que utiliza un identificador único diferente. Por ejemplo, puede tener dispositivos con números de serie únicos o los datos de telemetría que tiene un nombre único dispositivo incrustado en lo que se podría utilizar para determinar la identidad del dispositivo concentrador IoT.

### <a name="iot-hub-communication-module"></a>Módulo de comunicación IoT concentrador

Este módulo toma los mensajes con un concentrador de IoT identidad de dispositivo asignado por el módulo anterior y envía el contenido del mensaje al concentrador IoT mediante HTTP. HTTP es uno de los tres protocolos entendidos por IoT concentrador.

En lugar de abrir una conexión al concentrador de IoT para cada dispositivo simulado, este módulo abre una única conexión de HTTP desde la puerta de enlace al concentrador IoT y Multiplexa las conexiones desde todos los dispositivos simulados a través de esa conexión. Esto permite que una sola puerta de enlace conectar muchos más dispositivos, simulados o de otro tipo, que se obtendría si abre una conexión única para cada dispositivo.

![][2]


<!-- Images -->
[1]: media/iot-hub-gateway-sdk-simulated-selector/image1.png
[2]: media/iot-hub-gateway-sdk-simulated-selector/image2.png

<!-- Links -->
[Ejemplo de nube de dispositivo cargar simulada]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/sample_simulated_device_cloud_upload.md
[lnk-sdk]: https://github.com/Azure/azure-iot-gateway-sdk
[lnk-gw-getstarted]: ../articles/iot-hub/iot-hub-linux-gateway-sdk-get-started.md