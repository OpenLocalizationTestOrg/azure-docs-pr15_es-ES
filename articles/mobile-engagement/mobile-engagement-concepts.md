<properties
    pageTitle="Conceptos de contratación móvil | Microsoft Azure"
    description="Azure conceptos de compromiso de móvil"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />

# <a name="azure-mobile-engagement-concepts"></a>Azure conceptos de compromiso de móvil

Compromiso de Mobile define algunos conceptos comunes a todas las plataformas compatibles. En este artículo se describen brevemente los conceptos.

En este artículo es un buen comienzo si no conoce el compromiso de móvil. Además, asegúrese de leer la documentación específica de la plataforma que está utilizando, como perfeccionar los los conceptos descritos en este artículo con más detalles y ejemplos, así como posibles limitaciones.

## <a name="devices-and-users"></a>Usuarios y dispositivos
Compromiso de Mobile identifica usuarios por generar un identificador único para cada dispositivo. Este identificador se denomina el identificador del dispositivo (o `deviceid`). Se genera de manera que la ejecución de todas las aplicaciones del mismo dispositivo comparten el mismo identificador de dispositivo.

Implícitamente, significa que Mobile contratación considera un dispositivo al que asignará exactamente un usuario y, por lo tanto, los usuarios y dispositivos son conceptos equivalentes.

## <a name="sessions-and-activities"></a>Las sesiones y actividades
Una sesión es un uso de la aplicación realizado por un usuario, desde el momento en que el usuario inicia usarlo, hasta que el usuario se detendrá.

Una actividad es un uso de una parte determinada de subcarpetas de la aplicación realizada por un usuario (suele ser una pantalla, pero puede ser cualquier cosa adecuado para la aplicación).

Un usuario sólo puede realizar una actividad a la vez.

Una actividad se identifica mediante un nombre (limitado a 64 caracteres) y, opcionalmente, puede insertar algunos datos adicionales (en el límite de 1024 bytes).

Sesiones se calculan automáticamente de la secuencia de actividades realizadas por los usuarios. Se inicia una sesión cuando el usuario inicia su primera actividad y se detiene cuando acabe su última actividad. Esto significa que no necesita una sesión de iniciar o detener explícitamente. En su lugar, actividades explícitamente se inicia o se detiene. Si no se notifica ninguna actividad, no se notifica ninguna sesión.

## <a name="events"></a>Eventos
Eventos se utilizan para informar de instantáneas acciones (como botón presionado o artículos leer los usuarios).

Un evento puede estar relacionado con la sesión actual, a un trabajo en ejecución, o puede ser un evento independiente.

Un evento se identifica mediante un nombre (limitado a 64 caracteres) y, opcionalmente, puede insertar algunos datos adicionales (en el límite de 1024 bytes).

## <a name="error"></a>Error
Errores se usan para informar de problemas detectados correctamente por la aplicación (por ejemplo, acciones de usuario incorrectos o errores de llamada de API).

Un error puede deberse a la sesión actual a un trabajo en ejecución, o puede ser un error independiente.

Un error se identifica mediante un nombre (limitado a 64 caracteres) y, opcionalmente, puede insertar algunos datos adicionales (en el límite de 1024 bytes).

## <a name="job"></a>Trabajo
Trabajos se usan para informar de acciones de una duración (como la duración de las llamadas de API, mostrar la hora de anuncios, la duración de las tareas de fondo o de acciones de usuario).

Un trabajo no está relacionado con una sesión, porque puede llevar a cabo una tarea en segundo plano, sin intervención del usuario.

Un trabajo se identifica mediante un nombre (limitado a 64 caracteres) y, opcionalmente, puede insertar algunos datos adicionales (en el límite de 1024 bytes).

## <a name="crash"></a>Bloqueo
El SDK de contratación Mobile para informar de errores de aplicación donde problemas no detectados por la aplicación que sea bloqueo emite automáticamente bloqueos.

## <a name="application-information"></a>Información de la aplicación
Información de la aplicación (o información de la aplicación) se usa para etiquetar los usuarios, es decir, asociar algunos datos para los usuarios de una aplicación (Esto es similar a las cookies de web, excepto en que se almacena información de la aplicación en el servidor en la plataforma de Azure Mobile compromiso).

Información de la aplicación se puede registrar mediante la API de SDK de contratación móvil o mediante el uso de la API de dispositivo de la plataforma de compromiso de móvil.

Información de la aplicación es un par de clave y valor asociado a un dispositivo. La clave es el nombre de la información de la aplicación (limitado a 64 letras ASCII [a-zA-Z], [0-9] de números y caracteres de subrayado [_]). El valor (limitado a 1024 caracteres) puede ser cualquier cadena, entero, fecha (MM / dd/aaaa) o booleanos (true o false).

Cualquier número de información de la aplicación se puede asociar a un dispositivo, dentro de los límites definidos por las condiciones de precios de compromiso de móvil. Para una clave determinada, Mobile compromiso solo realiza un seguimiento de la última conjunto de valores (sin historial). Establecer o cambiar el valor de una información de la aplicación fuerza compromiso Mobile para volver a evaluar audiencia criterios establecidos en esta información de la aplicación (si existe) lo que significa que esa información de la aplicación pueden usarse para desencadenar Push en tiempo real.

## <a name="extra-data"></a>Datos adicionales
Datos adicionales (o adicionales) están algunos datos arbitrarios que se pueden vincular a tareas, errores, las actividades y eventos.

Extras están estructuradas de forma similar a los objetos JSON: se realizan de un árbol de pares clave/valor. Teclas están limitadas a 64 letras ASCII [a-zA-Z], [0-9] de números y caracteres de subrayado [_]) y el tamaño total de extras se limita a 1024 caracteres (una vez codificados en JSON el SDK de contratación móvil).

Todo el árbol de pares clave/valor se almacena como un objeto JSON. No obstante, solo el primer nivel de claves/valores es descompuesto sean directamente accesibles para algunas funciones avanzadas como segmentos (por ejemplo, puede fácilmente definir un segmento llamado "SciFi ventiladores" que consta de todos los usuarios que ha enviado al menos 10 veces el evento, denominado "content_viewed" con la clave "content_type" establecido el valor "scifi" en el último mes). Así es muy recomendable para enviar solo extras realizan de listas simples de pares de clave/valor con valores escalares (por ejemplo, cadenas, fechas, enteros o booleano).

## <a name="next-steps"></a>Pasos siguientes

- [Información general de Windows SDK Universal para Azure Mobile contratación](mobile-engagement-windows-store-sdk-overview.md)
- [Información general de Windows Phone Silverlight SDK para Azure Mobile contratación](mobile-engagement-windows-phone-sdk-overview.md)
- [iOS SDK para Azure Mobile contratación](mobile-engagement-ios-sdk-overview.md)
- [Android SDK para Azure compromiso móvil](mobile-engagement-android-sdk-overview.md)
