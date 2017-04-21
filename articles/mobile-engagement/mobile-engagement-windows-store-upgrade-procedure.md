<properties 
    pageTitle="Procedimientos de actualización de Windows SDK de aplicaciones Universal" 
    description="Procedimientos de actualización de Windows SDK Universal de aplicaciones para Azure contratación móvil"                     
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

#<a name="windows-universal-apps-sdk-upgrade-procedures"></a>Procedimientos de actualización de Windows SDK de aplicaciones Universal

Si ya ha integrado una versión anterior de contratación en la aplicación, debe tener en cuenta los siguientes puntos al actualizar el SDK.

Deberá seguir diversos procedimientos si hay varias versiones del SDK. Por ejemplo, si se migra de 0.10.1 a 0.11.0 que debe primero siga el procedimiento "de 0.9.0 a 0.10.1", a continuación, el procedimiento "de 0.10.1 a 0.11.0".

##<a name="from-330-to-340"></a>Desde 3.3.0 a 3.4.0

### <a name="test-logs"></a>Registros de pruebas

Registros de consola generados por el SDK ahora pueden ser habilitada, deshabilitada o filtrado. Para personalizar esto, actualice la propiedad `EngagementAgent.Instance.TestLogEnabled` a uno de los valores de la `EngagementTestLogLevel` enumeración, por ejemplo:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

### <a name="resources"></a>Recursos

Se ha mejorado la superposición de alcance. Es parte de los recursos del paquete NuGet SDK.

Mientras actualizar a la nueva versión del SDK puede elegir si desea conservar los archivos existentes de la carpeta de superposición de los recursos o no:

* Si la superposición anterior está trabajando para usted o que integra la `WebView` elementos manualmente, a continuación, puede decidir mantener los archivos existentes, aún funcionará. 
* Si desea actualizar a la superposición nueva, simplemente reemplazar todo el `overlay` carpeta desde los recursos con el nuevo desde el paquete del SDK (UWP aplicaciones: tras la actualización, puede obtener la nueva carpeta de superposición de % PERFILUSUARIO %\\.nuget\packages\MicrosoftAzure.MobileEngagement\3.4.0\content\win81\Resources).

> [AZURE.WARNING] Usar la superposición nueva sobrescribirá todas las personalizaciones realizadas en la versión anterior.

##<a name="from-320-to-330"></a>Desde 3.2.0 a 3.3.0

### <a name="resources"></a>Recursos
Este paso se refiere a solo los recursos personalizados. Si ha personalizado los recursos proporcionados por el SDK (html, imágenes, superposición) tiene copia de seguridad de ellos antes de actualizar y volver a aplicar la personalización de recursos actualizados.

##<a name="from-310-to-320"></a>Desde 3.1.0 a 3.2.0

### <a name="resources"></a>Recursos
Este paso se refiere a solo los recursos personalizados. Si ha personalizado los recursos proporcionados por el SDK (html, imágenes, superposición) tiene copia de seguridad de ellos antes de actualizar y volver a aplicar la personalización de recursos actualizados.

### <a name="webview-integration"></a>Integración de vista Web
Algunas mejoras para que coincida con los factores de forma de dispositivo diferente introducidas en esta versión. Asegúrese de que la integración de la vista Web coincide con la siguiente:

En la página XAML ():

            <WebView x:Name="engagement_notification_content" Visibility="Collapsed" Height="80" HorizontalAlignment="Right" VerticalAlignment="Top"/>
            <WebView x:Name="engagement_announcement_content" Visibility="Collapsed" HorizontalAlignment="Right" VerticalAlignment="Top"/> 

En el archivo. cs asociado:

    using Microsoft.Azure.Engagement;
    using System;
    using Windows.ApplicationModel.Core;
    using Windows.UI.ViewManagement;
    using Windows.UI.Xaml;
    using Windows.UI.Xaml.Navigation;

    namespace My.Namespace.Example
    {
            /// <summary>
            /// An empty page that can be used on its own or navigated to within a Frame.
            /// </summary>
            public sealed partial class ExampleEngagementReachPage : EngagementPage
            {
              public ExampleEngagementReachPage()
              {
                this.InitializeComponent();
            
                /* Set your webview elements to the correct size. */
                SetWebView(width, height);
              }
            
              #region to implement
              /* Attach events when page is navigated. */
              protected override void OnNavigatedTo(NavigationEventArgs e)
              {
                /* Update the webview when the app window is resized. */
                Window.Current.SizeChanged += DisplayProperties_OrientationChanged;

                /* Update the webview when the app/status bar is resized. */
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
                ApplicationView.GetForCurrentView().VisibleBoundsChanged += DisplayProperties_VisibleBoundsChanged; 
    #endif
                base.OnNavigatedTo(e);
              }

              /* When page is left ensure to detach SizeChanged handler. */
              protected override void OnNavigatedFrom(NavigationEventArgs e)
              {
                Window.Current.SizeChanged -= DisplayProperties_OrientationChanged;
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
                ApplicationView.GetForCurrentView().VisibleBoundsChanged -= DisplayProperties_VisibleBoundsChanged;
    #endif
                base.OnNavigatedFrom(e);
              }
              
              /* "width" and "height" are the current size of your application display. */
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
              double width = ApplicationView.GetForCurrentView().VisibleBounds.Width;
              double height = ApplicationView.GetForCurrentView().VisibleBounds.Height;
    #else
              double width =  Window.Current.Bounds.Width;
              double height =  Window.Current.Bounds.Height;
    #endif
            
              /// <summary>
              /// Set your webview elements to the correct size.
              /// </summary>
              /// <param name="width">The width of your current display.</param>
              /// <param name="height">The height of your current display.</param>
              private void SetWebView(double width, double height)
              {
                #pragma warning disable 4014
                CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal,
                        () =>
                        {
                          this.engagement_notification_content.Width = width;
                          this.engagement_announcement_content.Width = width;
                          this.engagement_announcement_content.Height = height;
                        });
              }
            
              /// <summary>
              /// Handler that takes the Windows.Current.SizeChanged and indicates that webviews have to be resized.
              /// </summary>
              /// <param name="sender">Original event trigger.</param>
              /// <param name="e">Window Size Changed Event arguments.</param>
              private void DisplayProperties_OrientationChanged(object sender, Windows.UI.Core.WindowSizeChangedEventArgs e)
              {
                double width = e.Size.Width;
                double height = e.Size.Height;
            
                /* Set your webview elements to the correct size. */
                SetWebView(width, height);
              }

    #if WINDOWS_PHONE_APP || WINDOWS_UWP              
              /// <summary>
              /// Handler that takes the ApplicationView.VisibleBoundsChanged and indicates that webviews have to be resized
              /// </summary>
              /// <param name="sender">The related application view.</param>
              /// <param name="e">Related event arguments.</param>
              private void DisplayProperties_VisibleBoundsChanged(ApplicationView sender, Object e)
              {
                double width = sender.VisibleBounds.Width;
                double height = sender.VisibleBounds.Height;
            
                /* Set your webview elements to the correct size. */
                SetWebView(width, height);
              }
    #endif
              #endregion
            }
    }

##<a name="from-200-to-300"></a>Desde 2.0.0 a 3.0.0

### <a name="resources"></a>Recursos
Este paso se refiere a solo los recursos personalizados. Si ha personalizado los recursos proporcionados por el SDK (html, imágenes, superposición) tiene copia de seguridad de ellos antes de actualizar y volver a aplicar la personalización de recursos actualizados.

##<a name="from-111-to-200"></a>Desde 1.1.1 a 2.0.0

La siguiente describe cómo migrar una integración SDK desde el servicio de Capptain ofrecido por Capptain SAS a una aplicación con tecnología de Azure Mobile compromiso. 

> [Azure.IMPORTANT] Capptain y Mobile compromiso no son los mismos servicios y el procedimiento siguiente sólo resalta cómo migrar la aplicación de cliente. Migrar el SDK en la aplicación no se migrarán los datos de los servidores de Capptain a los servidores de compromiso de móvil

Si va a migrar desde una versión anterior, consulte el sitio web de Capptain para migrar a 1.1.1 en primer lugar, a continuación, aplique el siguiente procedimiento

### <a name="nuget-package"></a>Paquete NuGet

Reemplace **Capptain.WindowsPhone** **MicrosoftAzure.MobileEngagement** Nuget paquete.

### <a name="applying-mobile-engagement"></a>Aplicar compromiso móvil

El SDK utiliza el término `Engagement`. Deberá actualizar el proyecto para que coincida con este cambio.

Debe desinstalar el paquete de nuget Capptain actual. Tenga en cuenta que se eliminarán todos los cambios en la carpeta Capptain recursos. Si desea conservar los archivos, a continuación, hacer una copia de ellos.

Después de eso, instale el paquete de nuget de Microsoft Azure compromiso nuevo en su proyecto. Puede encontrarlo directamente en [nuget sitio Web]. o índice aquí. Esta acción reemplaza todos los archivos de recursos utilizados en contratación y agrega la nueva DLL de contratación a las referencias del proyecto.

Tendrá que limpiar las referencias del proyecto eliminando las referencias DLL Capptain. Si no hace esto, la versión de Capptain entrarán en conflicto y sucederá errores.

Si ha personalizado Capptain recursos, copie el contenido de archivos antiguos y péguelas en los nuevos archivos de contratación. Tenga en cuenta que los archivos xaml y cs deben actualizarse.

Cuando se completan estos pasos solo tiene que reemplazar antiguo Capptain las referencias a las nuevas referencias de contratación.

1. Todos los espacios de nombres de Capptain deben actualizarse.

    Antes de la migración:
    
        using Capptain.Agent;
        using Capptain.Reach;
    
    Después de la migración:
    
        using Microsoft.Azure.Engagement;

2. Todas las clases de Capptain que contienen "Capptain" deben contener "Compromiso".

    Antes de la migración:
    
        public sealed partial class MainPage : CapptainPage
        {
          protected override string GetCapptainPageName()
          {
            return "Capptain Demo";
          }
          ...
        }
    
    Después de la migración:
    
        public sealed partial class MainPage : EngagementPage
        {
          protected override string GetEngagementPageName()
          {
            return "Engagement Demo";
          }
          ...
        }

3. Para los archivos xaml atributos y espacio de nombres de Capptain también cambiarán.

    Antes de la migración:
    
        <capptain:CapptainPage
        ...
        xmlns:capptain="clr-namespace:Capptain.Agent;assembly=Capptain.Agent.WP"
        ...
        </capptain:CapptainPage>
    
    Después de la migración:
    
        <engagement:EngagementPage
        ...
        xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
        ...
        </engagement:EngagementPage>

4. Cambios de la página de superposición
    > [AZURE.IMPORTANT] Superposición también cambia. Su nuevo espacio de nombres es `Microsoft.Azure.Engagement.Overlay`. Debe usarse en archivos xaml y cs. Además `CapptainGrid` es denominarse `EngagementGrid`, `capptain_notification_content` y `capptain_announcement_content` se denominan `engagement_notification_content` y `engagement_announcement_content`.
    
    Superposición:
    
        <capptain:CapptainPageOverlay
          xmlns:capptain="using:Capptain.Overlay"
          ...
        </capptain:CapptainPageOverlay>
    
    Se convierte en:
    
        <EngagementPageOverlay
          engagement="using:Microsoft.Azure.Engagement.Overlay"
          ...
        </engagement:EngagementPageOverlay>

5. Para los demás recursos como Capptain imágenes y archivos HTML, tenga en cuenta que también cambiaron para usar "Compromiso".

### <a name="project-declaration"></a>Declaración de proyecto

En Package.appxmanifest `File Type Associations` se ha actualizado desde:

 -   capptain\_alcance\_contenido de contratación\_alcance\_contenido
 -   capptain\_registro\_archivo de contratación\_registro\_archivo

### <a name="application-id--sdk-key"></a>Id. de aplicación / SDK clave

Compromiso usa una cadena de conexión. No tiene que especificar un identificador de la aplicación y una tecla de SDK con compromiso de móvil, solo tiene que especificar una cadena de conexión. Puede configurarlo en el archivo EngagementConfiguration.

Puede establecer la configuración de compromiso en su `Resources\EngagementConfiguration.xml` archivo del proyecto.

Editar este archivo para especificar:

-   La cadena de conexión de la aplicación entre etiquetas `<connectionString>` y `<\connectionString>`.

Si desea especificar en tiempo de ejecución, puede llamar al método siguiente antes de la inicialización del agente de compromiso:

    /* Engagement configuration. */
    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
    
    /* Initialize Engagement agent with above configuration. */
    EngagementAgent.Instance.Init(args, engagementConfiguration);

La cadena de conexión de la aplicación se muestra en el Portal de clásico de Azure.

### <a name="items-name-change"></a>Cambio de nombre de elementos

Todos los elementos con nombre *capptain* han denominado *compromiso*. Del mismo modo para *Capptain* a *compromiso*.

Ejemplos de elementos de Capptain utilizados con frecuencia:

-   CapptainConfiguration ahora denominado EngagementConfiguration
-   CapptainAgent ahora denominado EngagementAgent
-   CapptainReach ahora denominado EngagementReach
-   CapptainHttpConfig ahora denominado EngagementHttpConfig
-   GetCapptainPageName ahora denominado GetEngagementPageName

Tenga en cuenta que cambiar de nombre también afecta a los métodos reemplazados.

 
