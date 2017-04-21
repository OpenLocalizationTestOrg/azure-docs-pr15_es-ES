<properties 
    pageTitle="Windows Phone Silverlight alcance SDK integración" 
    description="Cómo integrar alcance de Azure compromiso móvil con aplicaciones de Windows Phone Silverlight"                    
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-windows-phone" 
    ms.devlang="na" 
    ms.topic="article"
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="windows-phone-silverlight-reach-sdk-integration"></a>Windows Phone Silverlight alcance SDK integración

Debe seguir el procedimiento de integración descrito en la [Integración de Windows Phone Silverlight compromiso SDK](mobile-engagement-windows-phone-integrate-engagement.md) antes de seguir a esta guía.

##<a name="embed-the-engagement-reach-sdk-into-your-windows-phone-silverlight-project"></a>Integrar el SDK de alcance de contratación en el proyecto de Silverlight de Windows Phone

No tiene que agregar nada. `EngagementReach`referencias y recursos ya están en el proyecto.

> [AZURE.TIP]  Puede personalizar las imágenes que se encuentra en la `Resources` carpeta del proyecto, especialmente el icono de marca (ese valor predeterminado para el icono de contratación).

##<a name="add-the-capabilities"></a>Agregar las capacidades

El SDK de alcance de contratación necesita algunas funciones adicionales.

Abrir su `WMAppManifest.xml` de archivo y asegúrese de que se declaren las siguientes capacidades:

-   `ID_CAP_PUSH_NOTIFICATION`
-   `ID_CAP_WEBBROWSERCOMPONENT`

Primero se usa el servicio de MPNS para permitir la visualización de notificación. En el segundo se se usa para insertar una tarea del explorador en el SDK.

Editar la `WMAppManifest.xml` archivo y agregue dentro de la `<Capabilities />` etiqueta:

    <Capability Name="ID_CAP_PUSH_NOTIFICATION" />
    <Capability Name="ID_CAP_WEBBROWSERCOMPONENT" />

##<a name="enable-the-microsoft-push-notification-service"></a>Habilitar el servicio de notificación de inserción de Microsoft

Para utilizar el **Servicio de notificación de inserción de Microsoft** (Esto se conoce como MPNS) su `WMAppManifest.xml` archivo debe tener un `<App />` etiqueta con un `Publisher` atributo establecido en el nombre del proyecto.

##<a name="initialize-the-engagement-reach-sdk"></a>Inicializar el alcance de contratación SDK

### <a name="engagement-configuration"></a>Configuración de compromiso

La configuración de compromiso está centralizada en el `Resources\EngagementConfiguration.xml` archivo del proyecto.

Editar este archivo para especificar la configuración de acceso:

-   *Opcional*, indique si se activa la inserción nativa (MPNS) o si no está entre `<enableNativePush>` y `</enableNativePush>` etiquetas, (`true` de forma predeterminada).
-   *Opcional*, indique el nombre del canal de inserción entre `<channelName>` y `</channelName>` etiquetas, proporcione la misma que la aplicación puede usar o deje en blanco actualmente.

Si desea especificar en tiempo de ejecución, puede llamar al método siguiente antes de la inicialización del agente de compromiso:

    /* Engagement configuration. */
    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    
    engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
    
    engagementConfiguration.Reach.EnableNativePush = true;                  
    /* [Optional] whether the native push (MPNS) is activated or not. */
    
    engagementConfiguration.Reach.ChannelName = "YOUR_PUSH_CHANNEL_NAME";   
    /* [Optional] Provide the same channel name that your application may currently use. */
    
    /* Initialize Engagement agent with above configuration. */
    EngagementAgent.Instance.Init(engagementConfiguration);

> [AZURE.TIP] Puede especificar el nombre del canal de inserción MPNS de la aplicación. De forma predeterminada, contratación crea un nombre basado en el Id. No tiene para especificar el nombre usted mismo, excepto si planea usar el canal de inserción fuera de contratación.

### <a name="engagement-initialization"></a>Inicialización de contratación

Modificar el `App.xaml.cs`:

-   Agregar a su `using` instrucciones:

        using Microsoft.Azure.Engagement;

-   Insertar `EngagementReach.Instance.Init` inmediatamente posterior `EngagementAgent.Instance.Init` en `Application_Launching` :

        private void Application_Launching(object sender, LaunchingEventArgs e)
        {
           EngagementAgent.Instance.Init();
           EngagementReach.Instance.Init();
        }

-   Insertar `EngagementReach.Instance.OnActivated` en la `Application_Activated` método:

        private void Application_Activated(object sender, ActivatedEventArgs e)
        {
           EngagementAgent.Instance.OnActivated(e);
           EngagementReach.Instance.OnActivated(e);
        }

> [AZURE.IMPORTANT] La `EngagementReach.Instance.Init` se ejecuta en un subproceso dedicado. No tiene que hacerlo usted mismo.

##<a name="app-store-submission-considerations"></a>Consideraciones de envío de almacén de aplicación

Microsoft impone algunas reglas al usar las notificaciones de inserción:

En la documentación de Microsoft [Las directivas de aplicación] , sección 2,9:

1) Debe solicitar al usuario que acepte para recibir notificaciones de inserción. A continuación, en la configuración, agregue una forma para deshabilitar las notificaciones de inserción.

El objeto EngagementReach proporciona dos métodos para administrar la participación en/opcionales en salir, `EnableNativePush()` y `DisableNativePush()`. Por ejemplo, podría crear una opción en la configuración con una opción de alternancia para deshabilitar o habilitar MPNS.

También puede decidir desactivar MPNS mediante la configuración de compromiso\<windows phone sdk alcance configuración\>.

> 2.9.1) la aplicación en primer lugar debe describir las notificaciones para ofrecer y **obtener el permiso del usuario express (participar en la)**y **debe proporcionar un mecanismo por el que el usuario puede optar por no recibir notificaciones de inserción**. Todas las notificaciones mediante el servicio de notificación de inserción de Microsoft debe ser coherentes con la descripción proporcionada al usuario y debe cumplir con las aplicables todas las [Directivas de aplicación]  [ Content Policies] y [Requisitos adicionales para determinados tipos de aplicación].

2) No debe utilizar demasiados notificaciones de inserción. Compromiso controlará las notificaciones para usted.

> 2.9.2) la aplicación y su uso del servicio de notificación de inserción de Microsoft deben exceso no use la capacidad de red o ancho de banda del servicio de notificación de inserción de Microsoft, o en caso contrario, indebidamente sobrecarga un Windows Phone o dispositivo de Microsoft o servicio de notificaciones de inserción excesivo, según lo determinado por Microsoft en su discreción razonable y debe no dañar o interferir con las redes de Microsoft o servidores o los servidores de terceros o redes conectadas al servicio de notificación de inserción de Microsoft.

3) No confiar en MPNS para enviar información de criticals. Compromiso usa MPNS, por lo que también se aplica esta regla a las campañas creadas dentro de la contratación front-end.

> 2.9.3) el servicio de notificación de inserción de Microsoft no puede ser usado para enviar notificaciones de objetivos críticos o de lo contrario, podrían afectar asuntos de vida o muerte, incluyendo, sin las notificaciones críticas de limitación relacionados con un dispositivo médico o condición. MICROSOFT RENUNCIA A TODAS LAS GARANTÍAS QUE EL USO DEL SERVICIO DE NOTIFICACIÓN DE INSERCIÓN DE MICROSOFT O ENTREGA DE NOTIFICACIONES DE SERVICIO DE NOTIFICACIÓN DE INSERCIÓN DE MICROSOFT SERÁ SIN INTERRUPCIONES, SIN ERRORES O EN CASO CONTRARIO GARANTIZA QUE SE PRODUCEN EN TIEMPO REAL.

**No podemos garantizar que su aplicación iniciará el proceso de validación si no respeta estas recomendaciones.**

##<a name="handle-data-push-optional"></a>Controlar la inserción de datos (opcional)

Si desea que la aplicación para que pueda recibir alcance Push de datos, tiene que implementar dos eventos de la clase EngagementReach:

    EngagementReach.Instance.DataPushStringReceived += (body) =>
    {
       Debug.WriteLine("String data push message received: " + body);
       return true;
    };
    
    EngagementReach.Instance.DataPushBase64Received += (decodedBody, encodedBody) =>
    {
       Debug.WriteLine("Base64 data push message received: " + encodedBody);
       // Do something useful with decodedBody like updating an image view
       return true;
    };

Puede ver que la devolución de llamada de cada método devuelve un valor boolean. Compromiso envía comentarios a su back-end después de enviar la inserción de datos. Si la devolución de llamada devuelve false, la `exit` comentarios se estarán. De lo contrario, será `action`. Si no se establece ninguna devolución de llamada para los eventos, el `drop` comentarios se devolverá al compromiso.

> [AZURE.WARNING] Compromiso no es capaz de recibir comentarios de múltiplos para insertar datos. Si va a configurar varios controladores en un evento, tenga en cuenta que los comentarios se corresponden a la última uno enviado. En este caso, se recomienda siempre devuelve el mismo valor para evitar tener confusa comentarios en el front-end.

##<a name="customize-ui-optional"></a>Personalizar la interfaz de usuario (opcional)

### <a name="first-step"></a>Primer paso

Permitir personalizar el alcance de la interfaz de usuario.

Para ello, debe crear una subclase de la `EngagementReachHandler` clase.

**Código de ejemplo:**

    using Microsoft.Azure.Engagement;
    
    namespace Example
    {
       internal class ExampleReachHandler : EngagementReachHandler
       {
          // Override EngagementReachHandler methods depending on your needs
       }
    }

A continuación, establezca el contenido de la `EngagementReach.Instance.Handler` campo con el objeto personalizado en su `App.xaml.cs` clase dentro de la `Application_Launching` método.

**Código de ejemplo:**

    private void Application_Launching(object sender, LaunchingEventArgs e)
    {
       // your app initialization 
       EngagementReach.Instance.Handler = new ExampleReachHandler();
       // Engagement Agent and Reach initialization
    }

> [AZURE.NOTE] De forma predeterminada, contratación utiliza su propia implementación de `EngagementReachHandler`. No tiene que crear sus propios y si lo hace, no tiene reemplazar cada método. El comportamiento predeterminado es seleccionar el objeto de base de contratación.

### <a name="layouts"></a>Diseños

De forma predeterminada, alcance usará los recursos incrustados del archivo DLL para mostrar las notificaciones y las páginas.

Sin embargo, puede decidir usar sus propios recursos para reflejar su marca en estos componentes.

Puede reemplazar `EngagementReachHandler` métodos en su subclase para indicarle a compromiso para usar los diseños:

**Código de ejemplo:**

    // In your subclass of EngagementReachHandler
    
    public override string GetTextViewAnnouncementUri()
    {
       // return the path of your own xaml
    }
    
    public override string GetWebViewAnnouncementUri()
    {
       // return the path of your own xaml
    }
    
    public override string GetPollUri()
    {
       // return the path of your own xaml
    }
    
    public override EngagementNotificationView CreateNotification(EngagementNotificationViewModel viewModel)
    {
       // return a new instance of your own notification
    }

> [AZURE.TIP] La `CreateNotification` método puede devolver null. No se muestra la notificación y se quitará la campaña alcance.

Para simplificar la implementación de diseño, también se proporcionan nuestro propio xaml que puede servir como base para el código. Se encuentran en el archivo de contratación SDK (/ src/alcance /).

> [AZURE.WARNING] Las fuentes que proporcionamos son los mismos que usamos. Por lo que si desea modificar directamente, no olvide cambiar el espacio de nombres y el nombre.

### <a name="notification-position"></a>Posición de notificación

De forma predeterminada, se muestra una notificación de la aplicación en la parte inferior izquierda de la aplicación. Puede cambiar este comportamiento reemplazando la `GetNotificationPosition` método de la `EngagementReachHandler` objeto.

    // In your subclass of EngagementReachHandler
    
    public override EngagementReachHandler.NotificationPosition GetNotificationPosition(EngagementNotificationViewModel viewModel)
    {
       // return a value of the EngagementReachHandler.NotificationPosition enum (TOP or BOTTOM)
    }

Actualmente, puede elegir entre el `BOTTOM` (predeterminado) y `TOP` posiciones.

### <a name="launch-message"></a>Mensaje de inicio

Cuando un usuario hace clic en una notificación del sistema (rosas), contratación inicia la aplicación, cargar el contenido de los mensajes de inserción y mostrar la página de la campaña correspondiente.

Hay un retardo entre el inicio de la aplicación y la pantalla de la página (según la velocidad de la red).

Para indicar al usuario que algo esté cargando, debe proporcionar una información visual, como una barra de progreso o un indicador de progreso. Compromiso no puede controlar, pero proporciona algunos controladores para usted.

Para implementar la devolución de llamada, realice:

    /* The application has launched and the content is loading.
     * You should display an indicator here.
     */
    EngagementReach.Instance.RetrieveLaunchMessageStarted += () => { [...] };
    
    /* The application has finished loading the content and the page
     * is about to be displayed.
     * You should hide the indicator here.
     */
    EngagementReach.Instance.RetrieveLaunchMessageCompleted += () => { [...] };
    
    /* The content has been loaded, but an error has occurred.
     * You can provide an information to the user.
     * You should hide the indicator here.
     */
    EngagementReach.Instance.RetrieveLaunchMessageFailed += () => { [...] };

Puede establecer la devolución de llamada en su `Application_Launching` método de su `App.xaml.cs` de archivos, preferentemente antes el `EngagementReach.Instance.Init()` de llamadas.

> [AZURE.TIP] Cada controlador se denomina por el subproceso de interfaz de usuario. No tiene que preocuparse cuando se usa un cuadro de mensaje o algo relacionado con la interfaz de usuario.

[Directivas de aplicación]:http://msdn.microsoft.com/library/windows/apps/hh184841(v=vs.105).aspx
[Content Policies]:http://msdn.microsoft.com/library/windows/apps/hh184842(v=vs.105).aspx
[Requisitos adicionales para los tipos de aplicación específica]:http://msdn.microsoft.com/library/windows/apps/hh184838(v=vs.105).aspx
 
