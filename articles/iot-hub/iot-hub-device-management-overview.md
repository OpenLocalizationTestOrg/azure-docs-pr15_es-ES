<properties
 pageTitle="Información general sobre la administración de dispositivos de concentrador IoT | Microsoft Azure"
 description="Este artículo proporciona una descripción general de administración de dispositivos en Azure IoT concentrador: ciclo de vida del dispositivo de empresa, reinicie, restablecimiento de fábrica, actualización de firmware, configuración, twins dispositivo, consultas, trabajos"
 services="iot-hub"
 documentationCenter=""
 authors="bzurcher"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/03/2016"
 ms.author="bzurcher"/>

# <a name="overview-of-azure-iot-hub-device-management-preview"></a>Información general de administración de dispositivos de Azure IoT concentrador (vista preliminar)

## <a name="introduction"></a>Introducción

Hub de Azure IoT proporciona las características y un modelo de capacidad de ampliación que Habilitar dispositivo y back-end a los desarrolladores crear soluciones de administración de dispositivo IoT sólidas. IoT dispositivos incluyen sensores restringidos y Microcontroladores único propósito, puertas de enlace eficaces que enrutar comunicaciones para grupos de dispositivos.  Además, los casos de uso y los requisitos para los operadores IoT varían considerablemente en industrias.  A pesar de esta variante, administración de dispositivos de Azure IoT concentrador proporciona las capacidades, tramas y bibliotecas de código para satisfacer una amplia variedad de dispositivos y usuarios finales.

Una parte crucial de la creación de una empresa exitosa solución IoT para proporcionar una estrategia de cómo operadores controlan la administración continua de su colección de dispositivos. Operadores IoT requieren simples y confiables herramientas y aplicaciones que les permiten centrarse en los aspectos más estratégicos de su trabajo. Este artículo proporciona:

- Una breve introducción de enfoque de Azure IoT concentrador para la administración de dispositivos.
- Descripción de principios comunes de administración de dispositivos.
- Descripción del ciclo de vida del dispositivo.
- Información general sobre los modelos de administración de dispositivos comunes.

## <a name="iot-device-management-principles"></a>Principios de administración de dispositivos IoT

IoT incorpora un conjunto único de desafíos de administración de dispositivos y cada solución empresarial debe tratar los principios siguientes:

![Gráfico de principios de administración de dispositivo de concentrador IoT Azure][img-dm_principles]

- **Escala y automatización**: IoT soluciones requieren simples herramientas que pueden automatizar tareas habituales y habilitar un personal de operaciones relativamente pequeño administrar millones de dispositivos. Día a día, operadores esperan controlar las operaciones de dispositivo de forma remota, en masa y que solo se le avise cuando surgen problemas que requieren su atención directa.

- **Apertura y compatibilidad**: entorno de dispositivo de la IoT es extraordinariamente diverso. Herramientas de administración deben personalizarse para adaptarse a un gran número de clases de dispositivos, plataformas y los protocolos. Operadores deben ser compatible con muchos tipos de dispositivos, desde los más restringidos chips único proceso incrustados, en los equipos eficaces y totalmente funcionales.

- **Reconocimiento de contexto**: IoT entornos son dinámicas y cambiantes. Confiabilidad de servicio es fundamental. Operaciones de administración de dispositivo deben tener en ventanas de mantenimiento, Estados de red y power, condiciones de uso y ubicación geográfica del dispositivo para asegurarse de que el tiempo de inactividad de mantenimiento no afectan a las operaciones de negocios críticos o crear condiciones peligrosas SLA.

- **Muchas funciones de servicio**: soporte técnico para los flujos de trabajo únicos y procesos de las funciones de operaciones de IoT es fundamental. El personal de operaciones debe trabajar en armonía con las restricciones de los departamentos de TI internos determinadas.  También deben buscar formas sostenible de información de operaciones de dispositivo de superficie en tiempo real para los supervisores y otras funciones de gestión de empresa.

## <a name="iot-device-lifecycle"></a>Ciclo de vida del dispositivo de IoT

Hay un conjunto de fases de administración de dispositivo general que sean comunes a todos los proyectos de empresa IoT. En Azure IoT, hay cinco etapas del ciclo de vida del dispositivo IoT:

![Las cinco fases del ciclo de vida de dispositivo de Azure IoT: planear, aprovisionar, configurar, supervisar, retirar][img-device_lifecycle]

Dentro de cada uno de estos cinco etapas, hay varios requisitos de operador de dispositivo que deben cumplirse para proporcionar una solución completa:

- **Planear**: habilitar operadores crear un esquema de metadatos de dispositivo que les permite fácil y precisa para la consulta y un grupo de dispositivos para operaciones de administración de masa de destino. Puede usar al doble de dispositivo para almacenar los metadatos de este dispositivo en el formulario de etiquetas y propiedades.

    *Lecturas adicionales*: [Introducción a twins dispositivo][lnk-twins-getstarted], [gemelos de dispositivo de comprender][lnk-twins-devguide], [cómo utilizar las propiedades de doble][lnk-twin-properties]

- **Aprovisionar**: aprovisionar dispositivos nuevos a IoT concentrador y habilitar operadores inmediatamente descubrir las capacidades del dispositivo de forma segura.  Usar el registro de dispositivo de IoT concentrador para crear las credenciales e identidades de dispositivo flexible y realizar esta operación de forma masiva mediante una tarea. Crear dispositivos para informar de sus capacidades y sus condiciones a través de propiedades de dispositivos en los dos dispositivos.

    *Lecturas adicionales*: [Administrar identidades de dispositivo][lnk-identity-registry], [administración de masa de identidades de dispositivo][lnk-bulk-identity], [cómo utilizar las propiedades de doble][lnk-twin-properties]

- **Configurar**: facilitar los cambios de configuración de forma masiva y actualizaciones de firmware dispositivos conservando salud y seguridad. Realizar estas operaciones de administración de dispositivos de forma masiva mediante propiedades que desee o con métodos directos y trabajos de difusión.

    *Lecturas adicionales*: [usar métodos directos][lnk-c2d-methods], [invocar un método directo en un dispositivo][lnk-methods-devguide], [cómo utilizar las propiedades de doble][lnk-twin-properties], [programación y trabajos difusión][lnk-jobs], [programar trabajos en varios dispositivos][lnk-jobs-devguide]

- **Monitor**: supervisar el mantenimiento de colección de dispositivo general, el estado de las operaciones en curso, y avisa a los operadores a los problemas que pueden requerir su atención.  Aplicar el doble de dispositivo para permitir que los dispositivos de condiciones de funcionamiento de informe en tiempo real y el estado de las operaciones de actualización. Generar informes de paneles eficaces que la superficie de los problemas más inmediatos mediante dispositivo dos consultas.

    *Lecturas adicionales*: [cómo usar las propiedades de doble][lnk-twin-properties], [lenguaje de consulta para twins y trabajos][lnk-query-language]

- **Retirada**: reemplazar o retirar dispositivos después de un error, actualice ciclo, o al final de la duración del servicio.  Usar el doble de dispositivo para mantener la información de dispositivo si el dispositivo físico se está reemplazado o archivarla si se retira. Usar el registro de dispositivo IoT concentrador para revocar de forma segura identidades de dispositivo y las credenciales.

    *Lecturas adicionales*: [cómo usar las propiedades de doble][lnk-twin-properties], [Administrar identidades de dispositivo][lnk-identity-registry]

## <a name="iot-hub-device-management-patterns"></a>Patrones de administración de dispositivo concentrador IoT

Concentrador IoT permite el siguiente conjunto de patrones de administración de dispositivos.  Los [tutoriales de administración de dispositivo] [ lnk-get-started] muestran con más detalle cómo ampliar estos patrones para ajustar su situación exacta y cómo diseñar nuevos patrones basados en estas plantillas principales.

- **Reinicie** - la aplicación de back-end informa el dispositivo a través de un método directo que inició reiniciar el equipo.  El dispositivo usa el dispositivo doble notificadas propiedades para actualizar el estado de reinicio del dispositivo.

    ![Administración de dispositivos de Azure IoT concentrador reinicie gráfico de trama][img-reboot_pattern]

- **Restablecer factory** - la aplicación de back-end informa el dispositivo a través de un método directo que ha iniciado el restablecimiento de fábrica.  El dispositivo utiliza el doble de dispositivo notificados propiedades para actualizar el generador de restablecen el estado del dispositivo.

    ![Generador de administración de dispositivos de Azure IoT concentrador restablece gráfico de trama][img-facreset_pattern]

- **Configuración** - la aplicación de back-end utiliza las propiedades de doble deseado del dispositivo para configurar el software que se ejecuta en el dispositivo.  El dispositivo usa el dispositivo doble notificadas propiedades para actualizar el estado de la configuración del dispositivo.

    ![Gráfico de trama de Azure IoT concentrador dispositivo administración configuración][img-config_pattern]

- **Actualizar el firmware** - la aplicación de back-end informa el dispositivo a través de un método directo que ha iniciado una actualización del firmware.  El dispositivo iniciará un proceso de varios pasos para descargar el paquete de firmware, aplique el paquete de firmware y finalmente volver a conectar con el servicio de concentrador IoT.  Durante el proceso paso mult, el dispositivo utiliza el dispositivo doble notificadas propiedades para actualizar el progreso y el estado del dispositivo.

    ![Actualización de firmware de administración de dispositivos de Azure IoT concentrador gráfico de trama][img-fwupdate_pattern]

- **Informes de estado y progreso** - el back-end de la aplicación se ejecuta consultas de doble del dispositivo, en un conjunto de dispositivos para informar del estado y el progreso de las acciones que se ejecuten en los dispositivos.

    ![Administración de dispositivos de concentrador IoT Azure informes de estado y progreso de gráfico de trama][img-report_progress_pattern]

## <a name="next-steps"></a>Pasos siguientes

Puede usar las capacidades, tramas y las bibliotecas de código que proporciona administración de dispositivos de Azure IoT concentrador, para crear aplicaciones IoT que cumplen con los requisitos de operador de enterprise IoT dentro de cada fase de ciclo de vida del dispositivo.

Para continuar aprendiendo sobre las características de administración de dispositivos de Azure IoT concentrador, consulte la [Introducción a la administración de dispositivos de Azure IoT concentrador] [ lnk-get-started] tutorial.

<!-- Images and links -->
[img-dm_principles]: media/iot-hub-device-management-overview/image4.png
[img-device_lifecycle]: media/iot-hub-device-management-overview/image5.png
[img-config_pattern]: media/iot-hub-device-management-overview/configuration-pattern.png
[img-facreset_pattern]: media/iot-hub-device-management-overview/facreset-pattern.png
[img-fwupdate_pattern]: media/iot-hub-device-management-overview/fwupdate-pattern.png
[img-reboot_pattern]: media/iot-hub-device-management-overview/reboot-pattern.png
[img-report_progress_pattern]: media/iot-hub-device-management-overview/report-progress-pattern.png

[lnk-twins-devguide]: iot-hub-devguide-device-twins.md
[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-twins-getstarted]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-properties]: iot-hub-node-node-twin-how-to-configure.md
[lnk-hub-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-identity-registry]: iot-hub-devguide-identity-registry.md
[lnk-bulk-identity]: iot-hub-bulk-identity-mgmt.md
[lnk-query-language]: iot-hub-devguide-query-language.md
[lnk-c2d-methods]: iot-hub-c2d-methods.md
[lnk-methods-devguide]: iot-hub-devguide-direct-methods.md
[lnk-jobs]: iot-hub-schedule-jobs.md
[lnk-jobs-devguide]: iot-hub-devguide-jobs.md