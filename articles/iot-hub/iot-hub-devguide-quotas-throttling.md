<properties
 pageTitle="Guía de programador - cuotas y limitación | Microsoft Azure"
 description="Guía de programador de Azure IoT concentrador - descripción de las cuotas que se aplican a IoT concentrador y comportamiento esperado de límite"
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="dobett"/>

# <a name="reference---quotas-and-throttling"></a>Referencia: las cuotas y límite

## <a name="quotas-and-throttling"></a>Cuotas y límite

Cada suscripción Azure puede tener como máximo de 10 IoT hubs.

Cada concentrador IoT se aprovisiona con un determinado número de unidades en un número SKU específico (para obtener más información, vea [Precios de Azure IoT concentrador][lnk-pricing]). El SKU y el número de unidades determinan la cuota máxima diaria de los mensajes que se pueden enviar.

El SKU también determina los límites de limitación IoT concentrador se aplica en todas las operaciones.

## <a name="operation-throttles"></a>Límites de operación

Límites de operación son las limitaciones de tasa que se aplican en los rangos minutos y están pensadas para evitar abuso. Concentrador IoT intenta evitar devolver errores siempre que sea posible, pero se inicia devolver excepciones si se ha infringido el Acelerador durante mucho tiempo.

La siguiente es la lista de límites de obligatoria. Valores se refieren a un concentrador individual.

| Acelerador | Valor por concentrador |
| -------- | ------------- |
| Las operaciones de registro de identidad (crear, recuperar, lista, actualizar, eliminar) | 5000/min/unidad (para S3) <br/> 100/min/unidad (para S1 y S2). |
| Conexiones de dispositivo | sec/6000/unidad (para S3), 120/sec/unidad (S2), s/12/unidad (para S1). <br/>Mínimo de 100/seg. <br/> Por ejemplo, dos unidades de S1 son 2\*12 = 24/seg, pero tiene al menos 100 por segundo en las unidades. Con nueve unidades S1, tiene 108/seg (9\*12) en todas las unidades. |
| Envía el dispositivo a la nube | sec/6000/unidad (para S3), 120/sec/unidad (S2), s/12/unidad (para S1). <br/>Mínimo de 100/seg. <br/> Por ejemplo, dos unidades de S1 son 2\*12 = 24/seg, pero tiene al menos 100 por segundo en las unidades. Con nueve unidades S1, tiene 108/seg (9\*12) en todas las unidades. |
| Dispositivo de nube envía | 5000/min/unidad (para S3), 100/min /: unidad (para S1 y S2). |
| Recibe el dispositivo de nube | 50000/min/unidad (para S3), 1000/min /: unidad (para S1 y S2). |
| Operaciones de carga de archivos | carga de archivos de 5000 min/notificaciones/unidad (para S3), carga de archivo 100 notificaciones/min/unidad (para S1 y S2). <br/> URI de SA 10000 puede ser por para una cuenta de Azure almacenamiento a la vez.<br/> 10 SA URI o dispositivo puede ser por a la vez. | 

Es importante tener en cuenta que el límite de *conexiones de dispositivo* controla la velocidad a la que se pueden establecer nuevas conexiones de dispositivo con un concentrador IoT y no el número máximo de dispositivos conectados al mismo tiempo. El límite depende del número de unidades que se haya creado en el hub.

Por ejemplo, si compra una sola unidad S1, obtendrá un límite de 100 conexiones por segundo. Esto significa que para conectar 100000 dispositivos, tiene al menos 1000 segundos (aproximadamente 16 minutos). Sin embargo, puede tener tantos dispositivos conectados simultáneamente como tiene dispositivos registrados en el registro de la identidad de dispositivo.

Para obtener información detallada de concentrador IoT limitación comportamiento, consulte la entrada de blog [concentrador IoT limitación y][lnk-throttle-blog].

>[AZURE.NOTE] En cualquier momento, es posible aumentar cuotas o acelerador límites aumentando el número de unidades aprovisionados en un concentrador IoT.

>[AZURE.IMPORTANT] Las operaciones de registro de identidad se refieren tiempo de ejecución en escenarios de aprovisionamiento y administración de dispositivos. Leer o actualizar una gran cantidad de identidades de dispositivo es compatible a través de [Importar y exportar trabajos][lnk-importexport].

## <a name="next-steps"></a>Pasos siguientes

Otros temas de referencia en esta guía para programadores de concentrador IoT incluyen:

- [Extremos de concentrador IoT][lnk-devguide-endpoints]
- [Lenguaje de consulta para gemelos, métodos y trabajos][lnk-devguide-query]
- [Soporte técnico de IoT concentrador MQTT][lnk-devguide-mqtt]

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[lnk-throttle-blog]: https://azure.microsoft.com/blog/iot-hub-throttling-and-you/
[lnk-importexport]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities

[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md