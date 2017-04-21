<properties
    pageTitle="Integración de Azure compromiso móviles Android SDK"
    description="Últimas actualizaciones y procedimientos para Android SDK para Azure Mobile contratación"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="piyushjo" />

# <a name="release-notes"></a>Notas de la versión

## <a name="423-08102016"></a>4.2.3 (08/10/2016)

- Sin bloquear más de Wi-Fi.
- Corregir un bloqueo al llamar a getDeviceId antes de inicialización (error introducida en 4.2.0).

## <a name="422-05172016"></a>4.2.2 (17/05/2016)

- Mejoras en estabilidad.

## <a name="421-05102016"></a>4.2.1 (10/05/2016)

- Seguridad: deshabilitar el acceso al archivo local de vista web.
- Seguridad: quitar `EngagementPreferenceActivity` clase que extiende segura y obsoleto `PreferenceActivity` clase.
- Seguridad: alcance ahora se documentación actividades para usar `exported="false"`, este indicador también puede usarse en versiones anteriores de SDK.

## <a name="420-03112016"></a>4.2.0 (11/03/2016)

- Ahora el SDK tiene licencia MIT.
- Permite especificar un identificador de dispositivo personalizado en la inicialización SDK.

## <a name="415-02012016"></a>4.1.5 (01/02/2016)

- Mejoras en estabilidad.

## <a name="414-01262016"></a>4.1.4 (26/01/2016)

- Mejoras en estabilidad.

## <a name="413-1292015"></a>4.1.3 (12/9/2015)

- Mejoras en estabilidad.

## <a name="412-11252015"></a>4.1.2 (25/11/2015)

- Mejoras en estabilidad.

## <a name="411-11042015"></a>4.1.1 (04/11/2015)

- Mejoras en estabilidad.

## <a name="410-08252015"></a>4.1.0 (25/08/2015)

- Controlar el nuevo modelo de permisos para M Android.
- Ahora puede configurar las características de ubicación en tiempo de ejecución en lugar de usar `AndroidManifest.xml`.
- Corregir un error de permiso: si usa `ACCESS_FINE_LOCATION`, a continuación, `ACCESS_COARSE_LOCATION` no es necesario ya.
- Mejoras en estabilidad.

## <a name="400-07062015"></a>4.0.0 (06/07/2015)

-   Cambios de protocolo interno para realizar análisis e inserción más fiable.
-   Inserción nativa (GCM/ADM) ahora también se usa para en notificaciones de la aplicación por lo que debe configurar las credenciales de inserción nativa para cualquier tipo de campaña de inserción.
-   Corregir notificación panorama general: estaban mostrado 10s solo después de que se inserta.
-   Corregir un error en la vista web: haga clic en un vínculo también se ejecuta la URL de la acción predeterminada.
-   Corregir un error poco comunes relacionados con la administración de almacenamiento local.
-   Corregir la administración de cadenas de configuración dinámica.
-   Actualizar a CLUF.

## <a name="300-02172015"></a>3.0.0 (17/02/2015)

-   Versión inicial de Azure compromiso móvil
-   configuración de ID se reemplaza por una configuración de la cadena de conexión.
-   Quitar API para enviar y recibir mensajes XMPP arbitrarios de entidades XMPP arbitrarias.
-   Quitar API para enviar y recibir mensajes entre dispositivos.
-   Mejoras de seguridad.
-   Quita el seguimiento de Google Play y SmartAd.
