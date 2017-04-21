<properties 
   pageTitle="Guías de solución de problemas de Azure compromiso móvil" 
   description="Guía de solución de problemas para Azure compromiso móvil" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="08/19/2016"
   ms.author="piyushjo"/>

# <a name="azure-mobile-engagement---troubleshooting-guide"></a>Azure compromiso móvil - Guía de solución de problemas

## <a name="introduction"></a>Introducción
La Guía de solución de problemas siguiente le ayudará a comprender las causas raíz de algunos problemas comunes y le permiten solucionar sus propias. 

## <a name="general"></a>General

En general, debe siempre asegurarse de lo siguiente:

1. Asegúrese de que ha leído todos los pasos necesarios para integración, como se describe en los [tutoriales de introducción](mobile-engagement-windows-store-dotnet-get-started.md)
2. Está usando la versión más reciente de la plataforma SDK. 
3. Probar en un dispositivo real y un emulador porque algunos problemas son específicos de emulador solo. 
4. No se alcance cualquier límites y límites de contratación Mobile que se describen [aquí](../azure-subscription-service-limits.md)
5. Si no consigue conectar con el servicio de contratación Mobile back-end o ver datos no se carguen continuamente, a continuación, no compruebe que no incidencias de servicio en curso activando [aquí](https://azure.microsoft.com/status/)

## <a name="monitor-issues"></a>Problemas de 'Monitor'

### <a name="i-am-not-seeing-my-device-showing-up-on-the-monitor-tab"></a>No puedo ver mi dispositivo aparece en la pestaña Monitor
Ficha Monitor muestra los dispositivos conectados a su plataforma móvil compromiso en tiempo real. Si va a depurar en un emulador y el dispositivo, debería ver al menos una sesión aquí. Si se ha distribuido la aplicación, a continuación, verá el indicador de sesiones activas reflejar los dispositivos que están conectados a la plataforma en tiempo real. 

Si no ve el dispositivo en la pestaña Monitor, probablemente es un problema de integración de SDK. Algunos pasos a seguir para solucionar problemas comunes son las siguientes:

1. Asegúrese de que está utilizando la cadena de conexión correcta en la aplicación móvil y procede de la sección de claves SDK y no la sección de claves API. La cadena de conexión que conecta la aplicación móvil a la instancia de la aplicación móvil contratación en el que se ve el dispositivo en la pestaña Monitor. 
2. Para la plataforma Windows - si reemplaza la página de la `OnNavigatedTo` método, asegúrese de llamar a `base.OnNavigatedTo(e)`.
3. Si va a integrar compromiso de móvil en una aplicación móvil existente, a continuación, también puede asegurarse de que no falta ningún paso consultando los pasos de integración avanzada [aquí](mobile-engagement-windows-store-integrate-engagement.md)
4. Asegúrese de que va a enviar al menos una pantalla o actividad reemplazando la página con EngagementActivity dependiendo de la plataforma que está trabajando como se describe en los [tutoriales de introducción](mobile-engagement-windows-store-dotnet-get-started.md).

### <a name="i-am-seeing-the-monitor-tab-showing-a-session-even-when-i-have-disconnected-or-closed-my-app-emulator"></a>Veo la pestaña Monitor que muestra una sesión incluso al he desconectado o cerrado mi aplicación o emulador. 
Si usted es el único conectado a la plataforma en este momento y que está usando un emulador para abrir la aplicación esto es probable debido a modo de interpretación emulador. En general, debe asegurarse de que vuelva a la pantalla de inicio en el emulador para la sesión de la aplicación para desconectarse correctamente. Además, en la plataforma de Windows, al depurar con Visual Studio, debe asegurarse de que en Visual Studio, vaya a la barra de menús de **Eventos de ciclo de Vida** y haga clic en **Suspender** realmente cerrar la sesión. Para obtener información detallada, consulte [el tutorial de Windows](mobile-engagement-windows-store-dotnet-get-started.md) . 

## <a name="analytics-issues"></a>Problemas de 'análisis de '

### <a name="i-am-not-seeing-any-data-refreshed-data-on-analytics-tab"></a>No ver ningún dato o actualizar datos en la pestaña análisis 
Se vuelve a calcular los datos de análisis de forma regular y podría tardar hasta 24 horas para esta actualización. Estos datos no están en tiempo real y verá que actualice dentro de este período de 24 horas.
Asegúrese de no obstante que va a enviar al menos una pantalla o actividad a la plataforma de back-end cualquier reemplazo al menos una página con `EngagementActivity` o llamar a `SendActivity` forma explícita. 

### <a name="i-am-seeing-incorrectly-captured-datetime-for-a-device-on-the-analytics-tab"></a>Veo incorrectamente capturada fecha y hora para un dispositivo en la pestaña análisis
El período de tiempo para análisis se basa en la fecha de la configuración de dispositivo de los usuarios. Así que asegúrese de que el dispositivo tiene la fecha establecida correctamente. 

## <a name="segment-issues"></a>Problemas de 'Segmento'

### <a name="i-created-a-segment-and-it-is-showing-up-as-greyed-out-or-not-showing-any-data"></a>Se ha creado un segmento y se muestran como atenuado o no muestra ningún dato
Creación de segmento no está en tiempo real en el momento. Se calcula al mismo tiempo que se agregan los datos de análisis y para que podría tardar hasta 24 horas. Debe comprobar volver más adelante, pero mientras tanto también debe asegurarse de que las aplicaciones móviles en realidad envían los datos a partir del cual se forman los segmentos. Por ejemplo Si un evento dice 'foo' no enviado por cualquier dispositivo móvil, a continuación, sería cualquier dato de segmento para un segmento creado con EventName = foo como criterio. Compruebe también la integración del SDK para garantizar la aplicación móvil envía los datos correctamente. 

## <a name="reach-or-push-notifications-issues"></a>Problemas 'Alcance' o notificaciones Push

### <a name="my-push-messages-are-not-being-delivered"></a>No se van a entregar los mensajes de inserción 

1. Intente enviar notificaciones a un dispositivo de prueba en primer lugar para asegurarse de que todos los componentes - aplicación móvil, SDK y el servicio están conectadas correctamente y puede ofrecer notificaciones de inserción. 
2. Enviar 'de la aplicación notificación' más sencilla primero a través de una campaña que no está programada y ni tiene cualquier criterio audiencia especificado. Esta es otra vez para demostrar que la conectividad de notificación funciona correctamente. 
3. Si está teniendo problemas de entrega de notificaciones de la aplicación, a continuación, también es un buen primer paso para intentar enviar una notificación de la aplicación en primer lugar. 
4. Asegúrese de que la 'Insertar nativo' está configurada correctamente para la aplicación móvil. Dependiendo de la plataforma se bien implican claves (Android, Windows) o certificados (iOS). Ver [la interfaz de usuario - configuración](mobile-engagement-user-interface-settings.md)
5. En la aplicación de notificaciones también podrían estar bloqueadas por el usuario mediante el sistema operativo móvil así que asegúrese de que no es el caso. 
6. Asegúrese de que no están estableciendo la opción *Omitir audiencia, inserción se enviarán a los usuarios a través de la API* en la sección de **campaña** de una campaña de alcance porque esto se asegurará de que solo se pueden enviar notificaciones de inserción a través de la API. 
7. Asegúrese de que está probando su campaña de inserción con ambos un dispositivo conectado a través de la red Wi-Fi y teléfono del operador para eliminar la conexión de red como un origen de posible problemas.
8. Asegúrese de que el sistema de fecha y hora en su dispositivo o emulador es correcta porque cualquier dispositivo sincronizado también interfieran con la capacidad de inserción notificación del servicio para entregar notificaciones. 

Más específico de la plataforma instrucciones de solución de problemas:

1. **iOS** 

    - Asegúrese de que los certificados son válidos y vigente para iOS notificaciones Push. 
    - Asegúrese de que está configurando correctamente un certificado de *producción* en la aplicación móvil compromiso. 
    - Asegúrese de que está probando en un *dispositivo real, física.* El simulador iOS no puede procesar los mensajes de inserción.
    - Asegúrese de que el identificador de paquete está configurado correctamente en la aplicación móvil. Vea las instrucciones [aquí](https://developer.apple.com/library/prerelease/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html#//apple_ref/doc/uid/TP40012582-CH26-SW6)
    - Cuando se prueba, use distribución "Ad Hoc" en el perfil de aprovisionamiento móvil. No podrá recibir una notificación si la aplicación se compila con "Depurar"

2. **Android**

    - Asegúrese de que ha especificado el número correcto de proyecto en archivo de AndroidManifest.xml de la aplicación móvil seguido por carácter \n. 
    
            <meta-data android:name="engagement:gcm:sender" android:value="************\n" />
        
    - Asegúrese de que no faltan o mal configurados los permisos en el archivo de manifiestos Android 
    - Asegúrese de que es el número de proyecto que se va a agregar a la aplicación cliente desde la misma cuenta donde obtuvo la clave del servidor GCM. Cualquier no coinciden entre los dos impedirá que su Push saliente. 
    - Si recibe las notificaciones del sistema pero no en la aplicación, a continuación, revisar la [especificar un icono de la sección de notificaciones](mobile-engagement-android-get-started.md) como es muy probable que no se especifica el icono correcto en el archivo de manifiestos Android. 
    - Si va a enviar una notificación de BigPicture y garantizar que, si tiene servidores de imágenes externas, a continuación, que necesitan poder soporte HTTP "GET" y "Encabezado".

3. **Windows**
    
    - Asegúrese de que se ha asociado la aplicación a una aplicación de la tienda Windows válida. En Visual Studio - tendrá que haga clic con el botón secundario del mouse en el proyecto y seleccione la opción "Aplicación asociar con Store", seleccione la aplicación que creó en la tienda de Windows. Esta aplicación de la tienda de Windows debe ser el mismo desde donde obtuvo las credenciales de inserción nativa para configurar en el portal de compromiso de móvil.
    - Si recibe las notificaciones de inserción de la aplicación, pero no de la aplicación notificaciones con `EngagementOverlay` integración, a continuación, asegúrese de que haya un elemento de la cuadrícula de raíz en la página. EngagementOverlay utiliza el primer elemento de "Cuadrícula" que se encuentra en el archivo xaml para agregar web dos vistas de la página. Si desea buscar donde se establecerá vistas web, puede definir una cuadrícula denominada "EngagementGrid" como esto no obstante debe asegurarse de que hay suficiente alto y ancho para la web de las siguientes dos vistas que mostrará la notificación y el siguiente anuncio como notificación de la aplicación:
        
            <Grid x:Name="EngagementGrid"></Grid>

### <a name="i-created-a-push-notificationannouncement-campaign-and-even-after-it-sent-me-the-notification-it-is-showing-as-active-what-does-it-mean"></a>He creado un notificación de inserción/anuncio de campaña y después de que me envió la notificación, se muestra como 'Active'. ¿Qué significa? 
La **campaña** que creó en el compromiso de Mobile se denomina modo porque es un significado de notificación de inserción larga como nuevos dispositivos conectan a su plataforma de contratación móvil, se enviará automáticamente la notificación que configure aquí, siempre que cumplen el criterio establecido en la campaña. Esto es no una captura notificación único programa de instalación. Debe manualmente, haga clic en el botón **Finalizar** para finalizar la campaña para que no envía más notificaciones. 

### <a name="i-created-a-push-campaign-and-i-am-receiving-notifications-successfully-however-whenever-i-open-up-the-app-i-get-the-same-notification-even-when-i-had-actioned-it-before"></a>¿Se ha creado una campaña de inserción y recibo notificaciones correctamente pero siempre que se abre la aplicación, puedo obtener la misma notificación incluso cuando tenía ejecutados antes? 
Esto es probable que ocurrirá durante las pruebas y si está utilizando emuladores o algunas probar marco como TestFlight. ¿Qué sucede aquí es que en cada aplicación ejecutar instancia, es para adquirir una nueva DeviceID y enviarlo a nuestra back-end que está causando la plataforma Mobile compromiso tratar como un nuevo dispositivo y enviar la notificación. 

## <a name="getting-support"></a>Obtener soporte técnico

Si no puede resolver el problema usted mismo, a continuación, se puede:

1. Busque el problema en los subprocesos existentes en el [foro MSDN](https://social.msdn.microsoft.com/Forums/windows/en-US/home?forum=azuremobileengagement) y StackOverflow foro y si no es así, escriba una pregunta allí. 
2. Si encuentra una característica que faltan, a continuación, agregar/voto para la solicitud en nuestro [foro UserVoice](https://feedback.azure.com/forums/285737-mobile-engagement/)
3. Si tiene abierto de soporte técnico de Microsoft un incidente de soporte técnico proporcionando los siguientes detalles: 
    - Id. de suscripción de Azure
    - Plataforma (por ejemplo, iOS, Android etcetera)
    - Identificador de aplicación
    - Identificador de campaña (si hay problemas de notificación de inserción)
    - Id. de dispositivo
    - Versión de SDK de contratación móvil (por ejemplo, Android SDK v2.1.0)
    - Detalles del error con mensaje de error exacto y escenario
