<properties
    pageTitle="Información general del conjunto de programas de Microsoft Azure IoT | Microsoft Azure"
    description="Información general sobre cómo el conjunto de aplicaciones de Azure IoT ofrece internet de soluciones preconfigurados de cosas para recopilar, analizar y almacenar datos, proporcionar visualizaciones e integrar con otros sistemas."
    services=""
    suite="iot-suite"
    documentationCenter=""
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="na"
     ms.topic="get-started-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/09/2016"
     ms.author="dobett"/>

# <a name="what-is-azure-iot-suite"></a>¿Qué es el conjunto de aplicaciones de Azure IoT?

Azure internet de servicios de cosas (IoT) ofrecen una amplia gama de capacidades. Estos servicios de calificación de enterprise le permiten:

- Recopilar datos de dispositivos
- Analizar secuencias de datos en movimiento
- Almacenar y grandes conjuntos de datos de la consulta
- Visualizar datos históricos y en tiempo real
- Integrar con sistemas internos

Ofrecer estas capacidades, los paquetes de Azure IoT Suite juntos varios servicios Azure con extensiones personalizadas como *soluciones preconfiguradas*. Estas soluciones preconfiguradas son implementaciones bases de los modelos de solución IoT comunes que ayudan a reducir el tiempo que seguir para ofrecer su soluciones IoT. Utilizar el [Kit de desarrollo de software de IoT][lnk-sdks], puede personalizar y ampliar estas soluciones para satisfacer sus propios requisitos. También puede usar estas soluciones como ejemplos o plantillas al desarrollar soluciones de IoT nuevas.

El siguiente vídeo proporciona una introducción a Azure IoT Suite:

> [AZURE.VIDEO azurecon-2015-introducing-the-microsoft-azure-iot-suite]

## <a name="azure-iot-services-in-azure-iot-suite"></a>Servicios de Azure IoT en Azure IoT Suite

Las soluciones preconfiguradas suelen usar los siguientes servicios:

- Núcleo al conjunto de aplicaciones de Azure IoT es el [Concentrador de Azure IoT] [ lnk-iot-hub] servicio. Este servicio proporciona las capacidades de mensajería de dispositivo a la nube y dispositivo de nube y actúa como la puerta de enlace a la nube y los servicios IoT Suite clave. El servicio permite recibir mensajes desde sus dispositivos en escala y enviar comandos a sus dispositivos.

- [Análisis de secuencia de Azure] [ lnk-asa] proporciona análisis de datos en movimiento. Serie de IoT aprovecha este servicio para procesar telemetría entrante, realizar la agregación y detectar eventos. Las soluciones preconfiguradas también usar el análisis de secuencia para procesar los mensajes informativos que contienen datos como metadatos o comando respuestas desde dispositivos. Las soluciones de usar el análisis de secuencia para procesar los mensajes desde sus dispositivos y entregar los mensajes a otros servicios.

- [Almacenamiento de Azure] [ lnk-azure-storage] y [DocumentDB de Azure] [ lnk-document-db] proporcionar las capacidades de almacenamiento de datos. Las soluciones preconfiguradas utilizan almacenamiento de blobs para almacenar telemetría y para que esté disponible para el análisis. Las soluciones use DocumentDB para almacenar los metadatos de dispositivo y habilitar las capacidades de administración de dispositivos de las soluciones.

- [Aplicaciones Web de Azure] [ lnk-web-apps] y [Microsoft Power BI] [ lnk-power-bi] proporcionar las capacidades de visualización de datos. La flexibilidad de Power BI le permite crear rápidamente sus propio paneles interactivos que usan datos IoT Suite.

Para obtener información general de la arquitectura de una solución IoT típica, consulte [Microsoft Azure y Internet de cosas (IoT)][iot-suite-what-is-azure-iot].

## <a name="preconfigured-solutions"></a>Soluciones preconfiguradas

IoT Suite incluye soluciones preconfiguradas que le permiten comenzar rápidamente con y explorar los escenarios IoT comunes, como la *supervisión remota* y *mantenimiento predictivo*, que posibilita el conjunto de aplicaciones de Azure IoT. Puede implementar estas soluciones a su suscripción de Azure y luego ejecutar un escenario de IoT completado y llevar a cabo.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ya tiene una descripción general de lo que puede hacer IoT Suite y cuáles son sus componentes principales, puede obtener más información sobre las soluciones preconfiguradas en IoT Suite, consulte [¿Cuáles son los IoT Azure soluciones preconfiguradas?][lnk-what-are-preconfig]

[lnk-sdks]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/
[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-azure-storage]: https://azure.microsoft.com/documentation/services/storage/
[lnk-document-db]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-power-bi]: https://powerbi.microsoft.com/
[lnk-web-apps]: https://azure.microsoft.com/documentation/services/app-service/web/
[iot-suite-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-what-are-preconfig]: iot-suite-what-are-preconfigured-solutions.md
