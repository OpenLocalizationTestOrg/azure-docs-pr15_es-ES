<properties
 pageTitle="Guía de programador - métodos directos | Microsoft Azure"
 description="Guía de programador de Azure IoT concentrador - usar métodos directos para llamar a código en sus dispositivos"
 services="iot-hub"
 documentationCenter=".net"
 authors="nberdy"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="nberdy"/>

# <a name="invoke-a-direct-method-on-a-device-preview"></a>Llamar a un método directo en un dispositivo (vista preliminar)

## <a name="overview"></a>Información general

Concentrador IoT permite invocar métodos en dispositivos de la nube. Métodos representan una interacción solicitud responder con un dispositivo similar a una llamada HTTP en que se realizan correctamente o un error inmediatamente (después de un tiempo de espera especificado por el usuario) para el usuario comunicar el estado de la llamada. Esto es útil para escenarios donde el curso de acción inmediata se diferentes dependiendo de si el dispositivo se puede responder, como enviar un SMS reactivación a un dispositivo si un dispositivo está sin conexión (SMS está más caro que una llamada al método).

Puede considerar que un método como una llamada a procedimiento remoto directamente al dispositivo. Pueden llamar a solo los métodos que se han implementado en un dispositivo de la nube. Si la nube intenta invocar un método en un dispositivo que no tiene ese método definido, se produce un error en la llamada al método.

Cada método de dispositivo dirige a un único dispositivo. [Trabajos] [ lnk-devguide-jobs] proporciona una manera de invocar métodos en varios dispositivos y cola llamada a un método para dispositivos desconectados.

Cualquier usuario con permisos de **servicio conectar** IoT concentrador puede llamar a un método en un dispositivo.

### <a name="when-to-use"></a>Cuándo usar

Métodos de dispositivo son similares a los [mensajes de la nube a dispositivo] [ lnk-devguide-messages] que permiten que el back-end de nube pasar información a un dispositivo, pero se diferencian en aspectos fundamentales. Conceptualmente, métodos son sincrónico y no resistentes, aunque mensajes de dispositivo de nube son asincrónicos con un máximo de 48 horas de duración.

Métodos siguen un patrón de respuesta de la solicitud y no son resistentes. La falta de duración proporciona dos ventajas inmediatos cuando se comandos dispositivos:

- **Comentarios inmediato en ejecución del método** significa que no es necesario para administrar la correlación entre la solicitud y responder.
- **Mayor rendimiento** significa que las operaciones pueden realizarse con mayor rapidez porque IoT concentrador no proporciona ninguna duración. Concentrador IoT permite más llamadas de método por unidad que los mensajes de la nube al dispositivo.

Dispositivo de nube mensajes no son necesariamente comandos en el dispositivo, sino que representan un servicio de nube pasar algunos bits de información en el dispositivo para que pueda recoger en su tiempo libre, y de que el dispositivo puede o no puede responder. Mensajes de dispositivo de nube tienen un mayor tiempo de espera (48 horas) mientras métodos expire mucho más rápidamente.

Utilizar métodos de dispositivo de invocación del comando inmediato en un dispositivo y trabajos de invocación programada de un comando en un dispositivo.

## <a name="method-lifecycle"></a>Ciclo de vida del método

Métodos se implementan en el dispositivo y pueden obligarle a cero o más entradas en la carga de método para crear instancias de correctamente. Se invoca un método directo a través de un URI de orientación de servicio (`{iot hub}/twins/{device id}/methods/`). Un dispositivo recibe métodos directas a través de un tema MQTT específica del dispositivo (`$iothub/methods/POST/{method name}/`). Nos podemos es compatible con métodos de protocolos de red del dispositivo adicionales en el futuro.

> [AZURE.NOTE] Cuando se invoca un método directo en un dispositivo, nombres y los valores sólo pueden contener EE imprimible alfanuméricos, excepto en el siguiente conjunto: ``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}``.

Métodos son sincrónicos y ya sea correcta o no después del período de tiempo de espera (predeterminado: 30 segundos, se puede configurar 3600 segundos). Métodos son útiles en escenarios interactivos donde desea que un dispositivo que actúe únicamente si el dispositivo está conectados y recibe comandos, como una luz desde un teléfono. En estos casos, que desea ver un error o éxito inmediato para que el servicio de nube puede actuar en el resultado tan pronto como sea posible. El dispositivo puede devolver algunos cuerpo del mensaje como resultado del método, pero no es necesario para el método hacerlo. No hay ninguna garantía en orden o cualquier semántica de simultaneidad en llamadas a métodos.

Llamadas de método de dispositivo son solo HTTP desde el lado de la nube y solo MQTT desde el lado del dispositivo.

## <a name="reference-topics"></a>Temas de referencia:

Los temas de referencia siguientes le proporciona más información sobre el uso de métodos directos.

## <a name="invoke-a-direct-method-from-a-back-end-app"></a>Llamar a un método directo desde una aplicación de back-end

### <a name="method-invocation"></a>Llamada a un método

Las llamadas de método directo en un dispositivo son llamadas HTTP que componen:

- La *URI* específico del dispositivo (`{iot hub}/twins/{device id}/methods/`)
- El *método* de entrada
- *Encabezados* que contienen la autorización solicitar ID, el tipo de contenido y la codificación de contenido
- Un JSON transparente *cuerpo* en el siguiente formato:

```
{
    "methodName": "reboot",
    "timeoutInSeconds": 200,
    "payload": {
        "input1": "someInput",
        "input2": "anotherInput"
    }
}
```

  Tiempo de espera es en segundos. Si no se establece el tiempo de espera, el valor predeterminado es 30 segundos.
  
### <a name="response"></a>Respuesta

El back-end recibe una respuesta que incluye:

- *Código de estado HTTP*, que se utiliza para errores procedentes de concentrador IoT, incluidos un error 404 para dispositivos que no están conectados
- *Encabezados* que contienen el valor de etag solicitar ID, el tipo de contenido y la codificación de contenido
- Un JSON *cuerpo* en el siguiente formato:

```
{
    "status" : 201,
    "payload" : {...}
}
```
  
   Ambos `status` y `body` son proporcionada por el dispositivo y se usa para responder con el código de estado del dispositivo o la descripción.

## <a name="handle-a-direct-method-on-a-devcie"></a>Controlador de un método directo de un devcie

### <a name="method-invocation"></a>Llamada a un método

Dispositivos reciben las solicitudes de método directo en el tema MQTT:`$iothub/methods/POST/{method name}/?$rid={request id}`

El cuerpo que recibe el dispositivo está en el siguiente formato:

```
{
    "input1": "someInput",
    "input2": "anotherInput"
}
```

Las solicitudes de método son QoS 0.

### <a name="response"></a>Respuesta

El dispositivo envía respuestas a `$iothub/methods/res/{status}/?$rid={request id}`, donde:

 - La `status` propiedad es el estado proporcionado por el dispositivo de ejecución del método.
 - La `$rid` propiedad es el identificador de solicitud de la invocación del método recibido IoT concentrador.

El cuerpo está establecido por el dispositivo y puede ser cualquier estado.

## <a name="additional-reference-material"></a>Material de referencia adicional

Otros temas de referencia en la Guía del programador incluyen:

- [Extremos de concentrador IoT] [ lnk-endpoints] describe los diversos extremos que expone cada concentrador IoT para operaciones de administración y el tiempo de ejecución.
- [Cuotas y regulación] [ lnk-quotas] describe las cuotas que se aplican al servicio IoT concentrador y el comportamiento de limitación que va a pasar al usar el servicio.
- [SDK de dispositivos y servicios de concentrador IoT] [ lnk-sdks] listas el idioma diversos SDK que un uso al desarrollar aplicaciones dispositivos y servicios que interactúan con IoT concentrador.
- [Lenguaje de consulta para gemelos, métodos y trabajos] [ lnk-query] describe el lenguaje de consulta que se puede usar para recuperar información de concentrador IoT sobre sus twins de dispositivo, métodos y trabajos.
- [Soporte técnico de IoT concentrador MQTT] [ lnk-devguide-mqtt] proporciona más información acerca del soporte técnico de IoT concentrador para el protocolo MQTT.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido a usar métodos directos, puede estar interesado en el tema siguiente de la Guía del programador:

- [Programar trabajos en varios dispositivos][lnk-devguide-jobs]

Si desea probar algunos de los conceptos descritos en este artículo, puede estar interesado en el tutorial de concentrador IoT siguiente:

- [Utilizar métodos de dispositivo de nube][lnk-methods-tutorial]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-methods-tutorial]: iot-hub-c2d-methods.md
[lnk-devguide-messages]: iot-hub-devguide-messaging.md
