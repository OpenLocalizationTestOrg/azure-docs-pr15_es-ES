<properties
     pageTitle="Usar el portal de Azure para configurar la carga de archivos | Microsoft Azure"
     description="Información general sobre cómo configurar la carga de archivos con el portal de Azure"
     services="iot-hub"
     documentationCenter=""
     authors="dominicbetts"
     manager="timlt"
     editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/30/2016"
     ms.author="dobett"/>

# <a name="configure-file-uploads-using-the-azure-portal"></a>Configurar las cargas de archivos con el portal de Azure

## <a name="file-upload"></a>Carga de archivos

Usar la [funcionalidad de IoT concentrador de carga de archivos][lnk-upload], primero debe asociar una cuenta de Azure almacenamiento de su centro. Seleccione la configuración de **carga de archivos** para mostrar una lista de propiedades de carga de archivo para el concentrador IoT que se está modificando.

![][13]

**Contenedor de almacenamiento**: usar el portal de Azure para seleccionar un contenedor de blobs de una cuenta de Azure almacenamiento de su suscripción actual de Azure asociar a su centro IoT. Si es necesario, puede crear una cuenta de almacenamiento de Azure en el contenedor de módulo y blobs de **cuentas de almacenamiento** en el módulo de **contenedores** . Concentrador IoT genera automáticamente SA URI con permisos de escritura a este contenedor de blobs para dispositivos para usar al cargar los archivos.

![][14]

**Las notificaciones de recepción de archivos cargados**: habilitar o deshabilitar las notificaciones de carga de archivo mediante el botón de alternancia.

**Sa TTL**: esta opción es el tiempo de vida de los URI de SA IoT concentrador devuelve al dispositivo. Establecer una hora de forma predeterminada, pero puede personalizarse para otros valores con el control deslizante.

**Notificación de archivo configuración predeterminada TTL**: el tiempo de vida de un archivo de notificación de carga antes de que se ha expirado. Establecer un día de forma predeterminada pero puede personalizarse para otros valores con el control deslizante.

**Número de entrega máximo de notificación de archivos**: notificación de carga el número de veces que el concentrador IoT intenta enviar un archivo. Establecer a 10 de forma predeterminada, pero se pueden personalizar otros valores con el control deslizante.

![][15]

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información acerca de las capacidades de carga de archivo de concentrador IoT, vea [cargar archivos desde un dispositivo] [ lnk-upload] la Guía del programador.

Siga estos vínculos para obtener más información sobre la administración de Azure IoT concentrador:

- [Masa administrar dispositivos IoT][lnk-bulk]
- [Métrica de uso][lnk-metrics]
- [Operaciones de supervisión][lnk-monitor]

Para explorar aún más las capacidades de concentrador IoT, consulte:

- [Guía para programadores][lnk-devguide]
- [Simulación de un dispositivo con el SDK de puerta de enlace IoT][lnk-gateway]
- [Proteger la solución IoT desde el principio hacia arriba][lnk-securing]


  [13]: ./media/iot-hub-configure-file-upload/file-upload-settings.png
  [14]: ./media/iot-hub-configure-file-upload/file-upload-container-selection.png
  [15]: ./media/iot-hub-configure-file-upload/file-upload-selected-container.png

[lnk-upload]: iot-hub-devguide-file-upload.md

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-securing]: iot-hub-security-ground-up.md