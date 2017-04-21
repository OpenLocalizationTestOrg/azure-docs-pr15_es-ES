<properties
   pageTitle="Internet cosas prácticas recomendadas de seguridad | Microsoft Azure"
   description="El artículo proporciona una lista de Microsoft Internet de cosas prácticas recomendadas y recomendaciones generales curated."
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="StevenPo"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/25/2016"
   ms.author="yurid"/>

# <a name="internet-of-things-security-best-practices"></a>Internet cosas prácticas recomendadas de seguridad

Proteger la infraestructura de Internet de cosas (IoT) es una tarea crítica para cualquier persona que implica IoT soluciones. Debido a la cantidad de dispositivos implicados y la naturaleza distribuida de estos dispositivos, el impacto de un evento de seguridad relacionados con peligro de millones de dispositivos IoT no es sencillo y puede tener un impacto masivo.

Por este motivo, seguridad IoT necesita un enfoque de seguridad en profundidad. Datos deben estar seguro en la nube y cuando se mueve sobre redes públicas y privadas. Métodos deben estar en lugar de forma segura proporcionando los propios dispositivos IoT. Cada capa del dispositivo, red back-end de nube debe garantías de alta seguridad.

Prácticas recomendadas de IoT se pueden dividir de la siguiente manera:

- IoT fabricante o integración
- Programador de solución IoT
- Implementador de solución IoT
- Operador de solución IoT

Este artículo resume [Internet de cosas mejores prácticas de seguridad](../iot-suite/iot-security-best-practices.md). Consulte el artículo para obtener información detallada.

## <a name="iot-hardware-manufacturer-or-integrator"></a>IoT fabricante o integración

Si es un fabricante de hardware IoT o una integración de hardware, siga los procedimientos recomendados siguientes:

- **Requisitos mínimos de hardware de ámbito**: el diseño de hardware debe incluir características mínimas necesarias para que funcione el hardware y nada más. 
- **Asegúrese de hardware alteraciones**: crear en mecanismos para detectar la manipulación física de hardware, como abrir la tapa del dispositivo, quitar una parte del dispositivo, etcetera. 
- **Generar alrededor de hardware seguro**: si permitir [CV](https://en.wikipedia.org/wiki/Cost_of_goods_sold) , generar características de seguridad como almacenamiento seguro y cifrado y la funcionalidad de inicio basado en el módulo de plataforma de confianza TPM.
- **Realizar actualizaciones seguro**: actualizar el firmware durante la duración del dispositivo es inevitable.

## <a name="iot-solution-developer"></a>Programador de solución IoT

Si es un desarrollador de solución IoT, siga los procedimientos recomendados siguientes:

- **Seguir metodología de desarrollo de software seguro**: el desarrollo de software seguro requiere pensamiento de los conceptos básicos sobre la seguridad de inicio del proyecto hasta su implementación, prueba e implementación.
- **Elija software de código abierto con cuidado**: software de código abierto proporciona una oportunidad para desarrollar rápidamente soluciones.
- **Integrar con cuidado**: existen muchos de los errores de software de seguridad en el límite de bibliotecas y API. 

## <a name="iot-solution-deployer"></a>Implementador de solución IoT

Si es un implementador de solución IoT, siga los procedimientos recomendados siguientes:

- **Implementar hardware de forma segura**: IoT implementaciones pueden requerir hardware para su implementación en ubicaciones no seguras, como en espacios públicos o configuraciones regionales sin supervisión.
- **Proteger claves de autenticación**: durante la implementación, cada dispositivo requiere ID y asociados generadas por el servicio de nube de claves de autenticación. Mantener estas teclas física seguros incluso después de la implementación. Cualquier clave comprometida puede usarse un dispositivo malintencionado haga pasar por un dispositivo existente.

## <a name="iot-solution-operator"></a>Operador de solución IoT

Si es un operador de solución IoT, siga los procedimientos recomendados siguientes:

- **Mantener los sistemas actualizados**: asegúrese de que todos los controladores de dispositivos y sistemas operativos de dispositivos se actualizan a las últimas versiones. 
- **Protección contra actividad malintencionada**: si el sistema operativo lo permite, coloque las últimas capacidades antivirus y antimalware en cada sistema operativo del dispositivo. 
- **Auditoría con frecuencia**: IoT infraestructura de auditoría para seguridad relacionadas con problemas es clave al responder a incidencias de seguridad.
- **Física proteger la infraestructura de IoT**: se inician los peores ataques de seguridad contra IoT infraestructura con acceso físico a los dispositivos.
- **Credenciales de nube de proteger**: las credenciales de autenticación de nube utilizadas para configurar y utilizar una implementación IoT posiblemente son la manera más sencilla de obtener acceso y comprometa un sistema IoT. 
