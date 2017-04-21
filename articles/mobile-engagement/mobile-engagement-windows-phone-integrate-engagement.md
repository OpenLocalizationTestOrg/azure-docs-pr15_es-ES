<properties 
    pageTitle="Integración de Windows Phone Silverlight compromiso SDK" 
    description="Cómo integrar compromiso móvil Azure con aplicaciones de Windows Phone Silverlight"                  
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-windows-phone" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="windows-phone-silverlight-engagement-sdk-integration"></a>Integración de Windows Phone Silverlight compromiso SDK

> [AZURE.SELECTOR] 
- [Universal de Windows](mobile-engagement-windows-store-integrate-engagement.md) 
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md) 
- [iOS](mobile-engagement-ios-integrate-engagement.md) 
- [Android](mobile-engagement-android-integrate-engagement.md) 

Este procedimiento describe la manera más sencilla de activar análisis Azure Mobile compromiso y la supervisión de funciones en la aplicación de Windows Phone Silverlight.

Los pasos siguientes son suficientes para activar el informe de registros necesarios para calcular todas las estadísticas acerca de los usuarios, sesiones, actividades, se bloquea y Technicals. El informe de registros necesarios para calcular otros datos estadísticos como eventos, errores y trabajos debe hacerse manualmente con la API de contratación (consulte [cómo usar el compromiso Mobile avanzadas etiquetado API en la aplicación de Windows Phone Silverlight](mobile-engagement-windows-phone-use-engagement-api.md) ) puesto que estas estadísticas dependen de la aplicación.

##<a name="supported-versions"></a>Versiones compatibles

Solo se puede integrar el SDK de compromiso de Mobile para Windows Silverlight en aplicaciones de destino:

-   Windows Phone 8.0
-   Windows Phone 8.1 Silverlight

> [AZURE.NOTE] Si se dirige a Windows Phone 8.1 (no de Silverlight), consulte el [procedimiento de integración Universal de Windows](mobile-engagement-windows-store-integrate-engagement.md).

##<a name="install-the-mobile-engagement-silverlight-sdk"></a>Instalar el SDK de Silverlight de contratación móvil

El SDK de compromiso de Mobile para Windows Silverlight está disponible como un paquete de Nuget denominado *MicrosoftAzure.MobileEngagement*. Puede instalar desde el Administrador de paquetes de Visual Studio Nuget. 

##<a name="add-the-capabilities"></a>Agregar las capacidades

El SDK de contratación necesita algunas de las capacidades de Windows Phone Silverlight SDK para que funcione correctamente.

Abrir su `WMAppManifest.xml` de archivo y asegúrese de que las siguientes capacidades declaradas en el `Capabilities` panel:

-   `ID_CAP_NETWORKING`
-   `ID_CAP_IDENTITY_DEVICE`

##<a name="initialize-the-engagement-sdk"></a>Inicializar el compromiso SDK

### <a name="engagement-configuration"></a>Configuración de compromiso

La configuración de compromiso está centralizada en el `Resources\EngagementConfiguration.xml` archivo del proyecto.

Editar este archivo para especificar:

-   La cadena de conexión de la aplicación entre etiquetas `<connectionString>` y `<\connectionString>`.

Si desea especificar en tiempo de ejecución, puede llamar al método siguiente antes de la inicialización del agente de compromiso:

    /* Engagement configuration. */
    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

    /* Initialize Engagement agent with above configuration. */
    EngagementAgent.Instance.Init(engagementConfiguration);

La cadena de conexión de la aplicación se muestra en el Portal de clásico de Azure.

### <a name="engagement-initialization"></a>Inicialización de contratación

Cuando se crea un nuevo proyecto, una `App.xaml.cs` genera el archivo. Esta clase hereda de `Application` y contiene varios métodos importantes. También se utilizará iniciar el SDK de contratación.

Modificar el `App.xaml.cs`:

-   Agregar a su `using` instrucciones:

        using Microsoft.Azure.Engagement;

-   Insertar `EngagementAgent.Instance.Init` en la `Application_Launching` método:

        private void Application_Launching(object sender, LaunchingEventArgs e)
        {
          EngagementAgent.Instance.Init();
        }

-   Insertar `EngagementAgent.Instance.OnActivated` en la `Application_Activated` método:

        private void Application_Activated(object sender, ActivatedEventArgs e)
        {
           EngagementAgent.Instance.OnActivated(e);
        }

> [AZURE.WARNING] Se recomienda evitar la para agregar la inicialización de contratación en otro lugar de la aplicación. Sin embargo, tenga en cuenta que el `EngagementAgent.Instance.Init` método se ejecuta en un subproceso dedicado y no en el subproceso de interfaz de usuario.

##<a name="basic-reporting"></a>Informes básicos

### <a name="recommended-method--overload-your-phoneapplicationpage-classes"></a>Método recomendado: sobrecargue su `PhoneApplicationPage` clases

Para activar el informe de todos los registros necesarios para compromiso para calcular los usuarios, sesiones, actividades, se bloquea y las estadísticas técnicas, puede crear todo su `PhoneApplicationPage` clases subsitios que heredan de la `EngagementPage` clases.

Aquí es un ejemplo de cómo hacer esto para una página de la aplicación. Puede hacer lo mismo para todas las páginas de la aplicación.

#### <a name="c-source-file"></a>Archivo de origen C#

Modificar la página `.xaml.cs` archivo:

-   Agregar a su `using` instrucciones:

        using Microsoft.Azure.Engagement;

-   Reemplazar `PhoneApplicationPage` con `EngagementPage` :

**Sin compromiso:**

        namespace Example
        {
          public partial class ExamplePage : PhoneApplicationPage
          {
            [...]
          }
        }

**Con compromiso:**

        using Microsoft.Azure.Engagement;
        
        namespace Example
        {
          public partial class ExamplePage : EngagementPage 
          {
            [...]
          }
        }

> [AZURE.WARNING] Si la página hereda de la `OnNavigatedTo` método, tenga cuidado de permitir que el `base.OnNavigatedTo(e)` de llamadas. En caso contrario, no se mostrarán la actividad. De hecho, la `EngagementPage` llama `StartActivity` dentro de la `OnNavigatedTo` método.

#### <a name="xaml-file"></a>Archivo XAML

Modificar la página `.xaml` archivo:

-   Agregar a las declaraciones de espacios de nombres:

        xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"

-   Reemplazar `phone:PhoneApplicationPage` con `engagement:EngagementPage` :

**Sin compromiso:**

        <phone:PhoneApplicationPage>
            <!-- layout -->
        </phone:PhoneApplicationPage>

**Con compromiso:**

        <engagement:EngagementPage 
            xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP">
        
            <!-- layout -->
        </engagement:EngagementPage >

#### <a name="override-the-default-behavior"></a>Invalidar el comportamiento predeterminado

De forma predeterminada, el nombre de la clase de la página se notifica como el nombre de la actividad con sin adicional. Si la clase utiliza el sufijo "Página", el compromiso quitará también.

Si desea reemplazar el comportamiento predeterminado para el nombre, simplemente lo agrega a su código:

        // in the .xaml.cs file
        protected override string GetEngagementPageName()
        {
           /* your code */
           return "new name";
        }

Si desea información adicional con la actividad de informe, puede agregar a su código:

        // in the .xaml.cs file
        protected override Dictionary<object,object> GetEngagementPageExtra()
        {
           /* your code */
           return extra;
        }

Estos métodos se denominan desde el `OnNavigatedTo` método de la página.

### <a name="alternate-method-call-startactivity-manually"></a>Método alternativo: llamar `StartActivity()` manualmente

Si no puede o no desea sobrecarga su `PhoneApplicationPage` clases, en su lugar puede iniciar sus actividades llamando `EngagementAgent` métodos directamente.

Le recomendamos que llama `StartActivity` dentro de su `OnNavigatedTo` método de su PhoneApplicationPage.

        protected override void OnNavigatedTo(NavigationEventArgs e)
        {
           base.OnNavigatedTo(e);
           EngagementAgent.Instance.StartActivity("MyPage");
        }

> [AZURE.IMPORTANT] Asegúrese de que finalizar la sesión correctamente.
>
> El SDK llama automáticamente la `EndActivity` método cuando se cierra la aplicación. Por tanto, es **muy** recomendada llamar a la `StartActivity` método siempre que la actividad del usuario cambia y **nunca** llamar la `EndActivity` método. Este método envía un mensaje en el servidor de contratación que el usuario actual ha dejado la aplicación y afecta a todos los registros de la aplicación.

##<a name="advanced-reporting"></a>Informes avanzados

Si lo desea, es aconsejable eventos específicos de la aplicación de informe, errores y tareas, para ello, use los otros métodos que se encuentran en la `EngagementAgent` clase. La API de contratación permite usar todas las capacidades avanzadas de contratación.

Para obtener más información, vea [cómo usar el compromiso Mobile avanzadas etiquetado API en la aplicación de Windows Phone Silverlight](mobile-engagement-windows-phone-use-engagement-api.md).

##<a name="advanced-configuration"></a>Configuración avanzada

### <a name="disable-automatic-crash-reporting"></a>Deshabilitar los informes de bloqueo automático

Puede deshabilitar la característica de contratación de informes de bloqueos automática. A continuación, cuando se produzca una excepción no controlada, compromiso no hará nada.

> [AZURE.WARNING] Si piensa deshabilitar esta característica, debe tener en cuenta que cuando se produzca un bloqueo no controlado en la aplicación, compromiso no enviará el bloqueo **y** no se cerrará la sesión y trabajos.

Para deshabilitar el informe de bloqueo automático, simplemente personalizar la configuración según la manera en que se ha declarado:

#### <a name="from-engagementconfigurationxml-file"></a>Desde `EngagementConfiguration.xml` archivo

Establecer el bloqueo de informe `false` entre `<reportCrash>` y `</reportCrash>` etiquetas.

#### <a name="from-engagementconfiguration-object-at-run-time"></a>Desde `EngagementConfiguration` objeto en tiempo de ejecución

Establecer el bloqueo de informe falso mediante el objeto EngagementConfiguration.

        /* Engagement configuration. */

        EngagementConfiguration engagementConfiguration = new EngagementConfiguration(); engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
        /\* Disable Engagement crash reporting. \*/ engagementConfiguration.Agent.ReportCrash = false;

### <a name="burst-mode"></a>Modo de ráfaga

De forma predeterminada, los informes de servicio de contratación registros en tiempo real. Si su aplicación informes registros con frecuencia, es mejor para los registros del búfer y para informar de a la vez en una base de tiempo regular (Esto se denomina "modo de ráfaga").

Para ello, llame al método:

        EngagementAgent.Instance.SetBurstThreshold(int everyMs);

El argumento es un valor en **milisegundos**. En cualquier momento, si desea volver a activar el registro en tiempo real, simplemente llame al método sin parámetros o con el valor 0.

El modo de ráfaga ligeramente aumentar la duración de la batería, pero tiene un gran impacto en el Monitor de compromiso: todas las sesiones y trabajos la duración se redondeará al umbral de ráfaga (por lo tanto, sesiones y trabajos menos tiempo que el umbral de ráfaga puede no ser visible). Se recomienda usar un umbral de ráfaga ya no que 30000 (30s). Debe tener en cuenta que los registros guardados se limitan a 300 elementos. Si el envío es demasiado larga puede perder algunos registros.

> [AZURE.WARNING] No se puede configurar el umbral de ráfaga a un período inferior a un segundo. Si intenta hacerlo, el SDK mostrará un seguimiento con el error y restablecer automáticamente el valor predeterminado, es decir, cero segundos. Esto activará el SDK para informar de los registros en tiempo real.
 
