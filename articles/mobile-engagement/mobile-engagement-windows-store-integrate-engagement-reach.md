<properties 
    pageTitle="Integración de SDK Universal alcance de aplicaciones de Windows" 
    description="Cómo integrar alcance de Azure compromiso móviles con Windows Universal aplicaciones"
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-windows-store" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

# <a name="windows-universal-apps-reach-sdk-integration"></a>Integración de SDK Universal alcance de aplicaciones de Windows

Debe seguir el procedimiento de integración descrito en la [Integración de Windows Universal compromiso SDK](mobile-engagement-windows-store-integrate-engagement.md) antes de seguir a esta guía.

## <a name="embed-the-engagement-reach-sdk-into-your-windows-universal-project"></a>Integrar el SDK de contratación alcance en su proyecto Universal de Windows

No tiene que agregar nada. `EngagementReach`referencias y recursos ya están en el proyecto.

> [AZURE.TIP] Puede personalizar las imágenes que se encuentra en la `Resources` carpeta del proyecto, especialmente el icono de marca (ese valor predeterminado para el icono de contratación). En aplicaciones Universal también puede mover el `Resources` carpeta en su proyecto compartido para compartir su contenido entre aplicaciones, pero tendrá que mantener la `Resources\EngagementConfiguration.xml` sea depende de la plataforma de archivo en su ubicación predeterminada.

## <a name="enable-the-windows-notification-service"></a>Habilitar el servicio de notificación de Windows

### <a name="windows-8x-and-windows-phone-81-only"></a>Windows 8.x y Windows Phone 8.1 solo

Para utilizar el **Servicio de notificación de Windows** (Esto se conoce como WNS) en su `Package.appxmanifest` de archivos `Application UI` haga clic en `All Image Assets` en el cuadro izquierdo robots. A la derecha del cuadro de `Notifications`, cambiar `toast capable` de `(not set)` a `(Yes)`.

### <a name="all-platforms"></a>Todas las plataformas

Debe sincronizar la aplicación a su cuenta de Microsoft y a la plataforma de contratación. Para ello debe crear una cuenta o iniciar sesión en [el centro de desarrollo de windows](https://dev.windows.com). Después de cree una nueva aplicación y busque la clave secreta SID. En el front-end de contratación, vaya en la configuración de la aplicación en `native push` y pegar sus credenciales. Después, haga clic en su proyecto, seleccione `store` y `Associate App with the Store...`. ¿Necesita seleccionar la aplicación que se crea antes para sincronizarlo.

## <a name="initialize-the-engagement-reach-sdk"></a>Inicializar el alcance de contratación SDK

Modificar el `App.xaml.cs`:

-   Insertar `EngagementReach.Instance.Init` inmediatamente posterior `EngagementAgent.Instance.Init` en su `InitEngagement` método:

        private void InitEngagement(IActivatedEventArgs e)
        {
          EngagementAgent.Instance.Init(e);
          EngagementReach.Instance.Init(e);
        }

    La `EngagementReach.Instance.Init` se ejecuta en un subproceso dedicado. No tiene que hacerlo usted mismo.

> [AZURE.NOTE] Si está utilizando las notificaciones de inserción en otra parte de la aplicación debe [compartir su canal de inserción](#push-channel-sharing) con alcance de contratación.

## <a name="integration"></a>Integración

Compromiso proporciona dos formas de agregar las cintas y alcance de la aplicación y las vistas intersticiales para anuncios y sondeos en la aplicación: la integración de superposición y la integración manual de vistas de web. No debe combinar ambos enfoque en la misma página.

La elección entre la integración de dos podría resumirse de esta manera:

-   Puede elegir la integración de superposición si ya hereda las páginas desde el agente de `EngagementPage`, es simplemente cuestión de reemplazar `EngagementPage` por `EngagementPageOverlay` y `xmlns:engagement="using:Microsoft.Azure.Engagement"` por `xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"` en las páginas.
-   Puede elegir las vistas web integración manual si desea colocar la interfaz de usuario alcance dentro de las páginas de forma precisa o si no desea agregar otro nivel de herencia a las páginas. 

### <a name="overlay-integration"></a>Integración de superposición

Superposición de contratación agrega dinámicamente los elementos de interfaz de usuario que se usa para mostrar las campañas de alcance de la página. Si la superposición no se adaptan a su diseño debe considerar las vistas web integración manual en su lugar.

En el cambio de archivo XAML `EngagementPage` hacer referencia a`EngagementPageOverlay`

-   Agregar a las declaraciones de espacios de nombres:

        xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"

-   Reemplazar `engagement:EngagementPage` con `engagement:EngagementPageOverlay`:

**Con EngagementPage:**

        <engagement:EngagementPage 
            xmlns:engagement="using:Microsoft.Azure.Engagement">
        
            <!-- Your layout -->
        </engagement:EngagementPage>

**Con EngagementPageOverlay:**

        <engagement:EngagementPageOverlay 
            xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay">
        
            <!-- Your layout -->
        </engagement:EngagementPageOverlay>

A continuación, en el archivo. cs etiquetar su página en `EngagementPageOverlay` en lugar de `EngagementPage` e importar `Microsoft.Azure.Engagement.Overlay`.

            using Microsoft.Azure.Engagement.Overlay;

-   Reemplazar `EngagementPage` con `EngagementPageOverlay`:

**Con EngagementPage:**

            using Microsoft.Azure.Engagement;
            
            namespace Example
            {
              public sealed partial class ExamplePage : EngagementPage
              {
                [...]
              }
            }

**Con EngagementPageOverlay:**

            using Microsoft.Azure.Engagement.Overlay;
            
            namespace Example
            {
              public sealed partial class ExamplePage : EngagementPageOverlay 
              {
                [...]
              }
            }


Superposición de contratación agrega un `Grid` elemento encima de la página se compone de los dos y su diseño `WebView` elementos uno para el título y la otra para la vista intersticial.

Puede personalizar los elementos de gráfico superpuesto directamente en el `EngagementPageOverlay.cs` archivo.

### <a name="web-views-manual-integration"></a>Integración de Web vistas manual

Alcance buscan las páginas para los dos `WebView` elementos responsables de mostrar el título y la vista intersticial. Lo único que tiene que hacer es agregar esas dos `WebView` elementos en algún lugar en las páginas, aquí se muestra un ejemplo:

    <Grid x:Name="engagementGrid">

      <!-- Your layout -->

      <WebView x:Name="engagement_notification_content" Visibility="Collapsed" Height="80" HorizontalAlignment="Stretch" VerticalAlignment="Top"/>
      <WebView x:Name="engagement_announcement_content" Visibility="Collapsed"  HorizontalAlignment="Stretch"  VerticalAlignment="Stretch"/>
    </Grid>


En este ejemplo la `WebView` elementos se ajustan para ajustar su contenedor que automáticamente cambia el tamaño de ellos en caso de cambio de tamaño de ventana o rotación de pantalla.

> [AZURE.WARNING] Es importante mantener la misma nomenclatura `engagement_notification_content` y `engagement_announcement_content` para el `WebView` elementos. Alcance identifica por su nombre. 

## <a name="handle-datapush-optional"></a>Controlador datapush (opcional)

Si desea que la aplicación para que pueda recibir alcance Push de datos, tiene que implementar dos eventos de la clase EngagementReach:

En App.xaml.cs en el constructor App() agregar:

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

## <a name="customize-ui-optional"></a>Personalizar la interfaz de usuario (opcional)

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

A continuación, establezca el contenido de la `EngagementReach.Instance.Handler` campo con el objeto personalizado en su `App.xaml.cs` clase dentro de la `App()` método.

**Código de ejemplo:**

            protected override void OnLaunched(LaunchActivatedEventArgs args)
            {
              // your app initialization 
              EngagementReach.Instance.Handler = new ExampleReachHandler();
              // Engagement Agent and Reach initialization
            }

> [AZURE.NOTE]De forma predeterminada, contratación utiliza su propia implementación de `EngagementReachHandler`.
> No tiene que crear sus propios y si lo hace, no tiene reemplazar cada método. El comportamiento predeterminado es seleccionar el objeto de base de contratación.

### <a name="web-view"></a>Vista Web

De forma predeterminada, alcance usará los recursos incrustados del archivo DLL para mostrar las notificaciones y las páginas.

Para proporcionar una personalización completa posibilidades usamos solo vista web. Si desea personalizar los diseños, reemplazar directamente los archivos de recursos `EngagementAnnouncement.html` y `EngagementNotification.html`. Compromiso necesita todo el código de `<body></body>` para que se ejecute correctamente. Pero puede agregar etiqueta exterior `engagement_webview_area`.

Sin embargo, puede decidir usar sus propios recursos.

Puede reemplazar `EngagementReachHandler` métodos en su subclase para indicarle a compromiso a usar los diseños, pero tenga cuidado incrustados mecanismo de compromiso:

**Código de ejemplo:**
            
            // In your subclass of EngagementReachHandler
            
            public override string GetAnnouncementHTML()
            {
              return base.GetAnnouncementHTML();
            }
            public override string GetAnnouncementName()
            {
              return base.GetAnnouncementName();
            }
            public override string GetNotfificationHTML()
            {
              return base.GetNotfificationHTML();
            }
            public override string GetNotfificationName()
            {
              return base.GetNotfificationName();
            }


De forma predeterminada, es AnnouncementHTML `ms-appx-web:///Resources/EngagementAnnouncement.html`. Representa el archivo html que diseñar el contenido de un mensaje de inserción (anuncio de texto, anoucement Web y anuncio de sondeo). AnnouncementName es `engagement_announcement_content`. Es el nombre del diseño de vista Web en la página xaml.

NotfificationHTML es `ms-appx-web:///Resources/EngagementNotification.html`. Representa el archivo html que la notificación de un mensaje de inserción de diseño. NotfificationName es `engagement_notification_content`. Es el nombre del diseño de vista Web en la página xaml.

### <a name="customization"></a>Personalización

Puede personalizar notificación y anuncio vista web tiene desea si mantener el objeto de contratación. Tenga cuidado de esa vista Web objeto se describe tres veces: la primera vez en el xaml, la segunda vez en el archivo. cs en el método "setwebview()" y la tercera vez en el archivo html.

-   En el xaml que describe el componente de vista Web diseño gráfico actual.
-   En el archivo. cs puede definir "setwebview()" en el que se establece la dimensión de la vista Web dos (notificación, anuncio). Es muy eficaces al cambiar el tamaño de la aplicación.
-   En el archivo html de contratación describimos el contenido de la vista Web, el diseño y las posiciones de elementos entre sí.

### <a name="launch-message"></a>Mensaje de inicio

Cuando un usuario hace clic en una notificación del sistema (rosas), contratación inicia la aplicación, cargar el contenido de los mensajes de inserción y mostrar la página de la campaña correspondiente.

Hay un retardo entre el inicio de la aplicación y la pantalla de la página (según la velocidad de la red).

Para indicar al usuario que algo esté cargando, debe proporcionar una información visual, como una barra de progreso o un indicador de progreso. Compromiso no puede controlar, pero proporciona algunos controladores para usted.

Para implementar la devolución de llamada, en App.xaml.cs en "App() público {}" Agregar:

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

Puede establecer la devolución de llamada en el método "App() público {}" de la `App.xaml.cs` de archivos, preferentemente antes el `EngagementReach.Instance.Init()` llamar.

> [AZURE.TIP] Cada controlador se denomina por el subproceso de interfaz de usuario. No tiene que preocuparse cuando se usa un cuadro de mensaje o algo relacionado con la interfaz de usuario.

##<a id="push-channel-sharing"></a>Uso compartido de canal de inserción

Si está utilizando las notificaciones de inserción para otro propósito de la aplicación debe utilizar el canal de inserción característica del SDK compromiso de uso compartido. Esto es para evitar la inserción perdida.

- Puede proporcionar su propio canal de inserción a la inicialización alcance de contratación. El SDK utiliza en lugar de solicitar una nueva.

Actualizar la inicialización compromiso contactar con su canal de inserción en la `InitEngagement` método desde la `App.xaml.cs` archivo:
    
    /* Your own push channel logic... */
    var pushChannel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
    
    /*...Engagement initialization */
    EngagementAgent.Instance.Init(e);
    EngagementReach.Instance.Init(e,pushChannel);

- Como alternativa, si desea utilizar el canal de inserción después de la inicialización del alcance, a continuación, se puede establecer una devolución de llamada en compromiso llegar a obtener el canal de inserción una vez creado por el SDK.

Establecer la devolución de llamada en cualquier lugar **después de** la inicialización del alcance:

    /* Set action on the SDK push channel. */
    EngagementReach.Instance.SetActionOnPushChannel((PushNotificationChannel channel) => 
    {
      /* The forwarded channel can be null if its creation fails for any reason. */
      if (channel != null)
      {
        /* Your own push channel logic... */
      });
    }

## <a name="custom-scheme-tip"></a>Sugerencia de combinación personalizada

Le proporcionamos el uso de la combinación personalizada. Puede enviar otro tipo de URI de contratación front-end que se utilizará en la aplicación de contratación. Por defecto como `http, ftp, ...` son administrar Windows, una obtendrá de la ventana símbolo del sistema si no están ninguna aplicación predeterminada instalada en el dispositivo. También puede crear su propia combinación personalizada para la aplicación.

Es una forma sencilla de establecer una combinación personalizada en la aplicación abrir su `Package.appxmanifest` vaya en `Declarations` panel. Seleccione `Protocol` en las declaraciones disponibles desplácese cuadro y agregarlo. Editar la `Name` campo con el protocolo de nuevo el nombre deseado.

Ahora para usar este protocolo, modifique la `App.xaml.cs` con el `OnActivated` método y no olvide iniciar compromiso aquí también:

            /// <summary>
            /// Enter point when app his called by another way than user click
            /// </summary>
            /// <param name="args">Activation args</param>
            protected override void OnActivated(IActivatedEventArgs args)
            {
              /* Init engagement like it was launch by a custom uri scheme */
              EngagementAgent.Instance.Init(args);
              EngagementReach.Instance.Init(args);
            
              //TODO design action to do when app is launch
            
              #region Custom scheme use
              if (args.Kind == ActivationKind.Protocol)
              {
                ProtocolActivatedEventArgs myProtocol = (ProtocolActivatedEventArgs)args;
            
                if (myProtocol.Uri.Scheme.Equals("protocolName"))
                {
                  string path = myProtocol.Uri.AbsolutePath;
                }
              }
              #endregion
 
