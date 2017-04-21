<properties 
    pageTitle="Integración con Windows aplicaciones Universal compromiso SDK" 
    description="Cómo integrar Azure compromiso móvil con Windows Universal aplicaciones"                  
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

# <a name="windows-universal-apps-engagement-sdk-integration"></a>Integración con Windows aplicaciones Universal compromiso SDK

> [AZURE.SELECTOR] 
- [Windows universal](mobile-engagement-windows-store-integrate-engagement.md) 
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md) 
- [iOS](mobile-engagement-ios-integrate-engagement.md) 
- [Android](mobile-engagement-android-integrate-engagement.md) 

Este procedimiento describe la manera más sencilla de activar el análisis de contratación y la supervisión de funciones en la aplicación Universal de Windows.

Los pasos siguientes son suficientes para activar el informe de registros necesarios para calcular todas las estadísticas acerca de los usuarios, sesiones, actividades, se bloquea y Technicals. El informe de registros necesarios para calcular otros datos estadísticos como eventos, errores y trabajos debe hacerse manualmente con la API de contratación (consulte [cómo usar el compromiso Mobile avanzadas etiquetado API en la aplicación Windows Universal](mobile-engagement-windows-store-use-engagement-api.md) ya que estas estadísticas depende de la aplicación.

## <a name="supported-versions"></a>Versiones compatibles

Solo se puede integrar el compromiso SDK para Windows Universal aplicaciones Mobile en tiempo de ejecución de Windows y aplicaciones de la plataforma de Windows Universal identificación:

-   Windows 8
-   Windows 8.1
-   Windows Phone 8.1
-   Windows 10 (familias de escritorio y portátiles)

> [AZURE.NOTE] Si se dirige a Windows Phone Silverlight, consulte el [procedimiento de integración de Silverlight de Windows Phone](mobile-engagement-windows-phone-integrate-engagement.md).

## <a name="install-the-mobile-engagement-universal-apps-sdk"></a>Instalar el SDK de aplicaciones móvil compromiso Universal

### <a name="all-platforms"></a>Todas las plataformas

El compromiso SDK para Windows Universal aplicación Mobile está disponible como un paquete de Nuget denominado *MicrosoftAzure.MobileEngagement*. Puede instalar desde el Administrador de paquetes de Visual Studio Nuget.

### <a name="windows-8x-and-windows-phone-81"></a>Windows 8.x y Windows Phone 8.1

NuGet implementa automáticamente los recursos SDK en el `Resources` carpeta en la raíz de su proyecto de aplicación.

### <a name="windows-10-universal-windows-platform-applications"></a>Aplicaciones de la plataforma de Windows de Windows 10 Universal

NuGet implemente automáticamente los recursos SDK en su aplicación UWP todavía. Tiene que hacerlo manualmente hasta que la implementación de recursos se introducirse en NuGet:

1.  Abra el Explorador de archivos.
2.  Vaya a la siguiente ubicación (**x.x.x** es la versión de contratación está instalando): *% PERFILUSUARIO %\\.nuget\packages\MicrosoftAzure.MobileEngagement\\**x.x.x**\\content\win81*
3.  Arrastrar y colocar la carpeta de **recursos** desde el Explorador de archivos en la raíz de su proyecto de Visual Studio.
4.  En Visual Studio, seleccione el proyecto y activar el icono **Mostrar todos los archivos** en la parte superior del **Explorador de soluciones**.
5.  Algunos archivos no se incluyen en el proyecto. Para importar ellos a la vez derecho haga clic en la carpeta de **recursos** , **excluir del proyecto** , a continuación, otro clic en la carpeta de **recursos** , **incluir en el proyecto** para volver a incluir toda la carpeta. Ahora se incluyen todos los archivos de la carpeta de **recursos** del proyecto.

## <a name="add-the-capabilities"></a>Agregar las capacidades

El SDK de contratación necesita algunas de las capacidades de Windows SDK para que funcione correctamente.

Abrir su `Package.appxmanifest` de archivo y asegúrese de que se declaren las siguientes capacidades:

-   `Internet (Client)`

## <a name="initialize-the-engagement-sdk"></a>Inicializar el compromiso SDK

### <a name="engagement-configuration"></a>Configuración de compromiso

La configuración de compromiso está centralizada en el `Resources\EngagementConfiguration.xml` archivo del proyecto.

Editar este archivo para especificar:

-   La cadena de conexión de la aplicación entre etiquetas `<connectionString>` y `<\connectionString>`.

Si desea especificar en tiempo de ejecución, puede llamar al método siguiente antes de la inicialización del agente de compromiso:
          
          /* Engagement configuration. */
          EngagementConfiguration engagementConfiguration = new EngagementConfiguration();

          /* Set the Engagement connection string. */
          engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

          /* Initialize Engagement angent with above configuration. */
          EngagementAgent.Instance.Init(e, engagementConfiguration);

La cadena de conexión de la aplicación se muestra en el Portal de clásico de Azure.

### <a name="engagement-initialization"></a>Inicialización de contratación

Cuando se crea un nuevo proyecto, una `App.xaml.cs` genera el archivo. Esta clase hereda de `Application` y contiene varios métodos importantes. También se utilizará iniciar el SDK de contratación.

Modificar el `App.xaml.cs`:

-   Agregar a su `using` instrucciones:

        using Microsoft.Azure.Engagement;

-   Definir un método para compartir la inicialización de contratación una vez para todas las llamadas:

        private void InitEngagement(IActivatedEventArgs e)
        {
          EngagementAgent.Instance.Init(e);
        
          // or
        
          EngagementAgent.Instance.Init(e, engagementConfiguration);
        }
        
-   Llamar a `InitEngagement` en la `OnLaunched` método:

        protected override void OnLaunched(LaunchActivatedEventArgs e)
        {
          InitEngagement(e);
        }

-   Cuando se inicia la aplicación con un esquema personalizado, otra aplicación o la línea de comandos, a continuación, el `OnActivated` se denomina método. También debe iniciar el SDK de contratación cuando se activa la aplicación. Para ello, reemplace `OnActivated` método:

        protected override void OnActivated(IActivatedEventArgs args)
        {
          InitEngagement(args);
        }

> [AZURE.IMPORTANT] Se recomienda evitar la para agregar la inicialización de contratación en otro lugar de la aplicación.

## <a name="basic-reporting"></a>Informes básicos

### <a name="recommended-method-overload-your-page-classes"></a>Método recomendado: sobrecargue su `Page` clases

Para activar el informe de todos los registros necesarios para compromiso para calcular los usuarios, sesiones, actividades, se bloquea y las estadísticas técnicas, puede crear todo su `Page` clases subsitios que heredan de la `EngagementPage` clases.

Aquí es un ejemplo de cómo hacer esto para una página de la aplicación. Puede hacer lo mismo para todas las páginas de la aplicación.

#### <a name="c-source-file"></a>Archivo de origen C#

Modificar la página `.xaml.cs` archivo:

-   Agregar a su `using` instrucciones:

        using Microsoft.Azure.Engagement;

-   Reemplazar `Page` con `EngagementPage`:

**Sin compromiso:**
    
        namespace Example
        {
          public sealed partial class ExamplePage : Page
          {
            [...]
          }
        }

**Con compromiso:**

        using Microsoft.Azure.Engagement;
        
        namespace Example
        {
          public sealed partial class ExamplePage : EngagementPage 
          {
            [...]
          }
        }

> [AZURE.IMPORTANT] Si la página reemplaza el `OnNavigatedTo` método, asegúrese de llamar a `base.OnNavigatedTo(e)`. En caso contrario, no se produce la actividad (el `EngagementPage` llamadas `StartActivity` dentro de su `OnNavigatedTo` método).

#### <a name="xaml-file"></a>Archivo XAML

Modificar la página `.xaml` archivo:

-   Agregar a las declaraciones de espacios de nombres:

        xmlns:engagement="using:Microsoft.Azure.Engagement"

-   Reemplazar `Page` con `engagement:EngagementPage`:

**Sin compromiso:**

        <Page>
            <!-- layout -->
            ...
        </Page>

**Con compromiso:**

        <engagement:EngagementPage 
            xmlns:engagement="using:Microsoft.Azure.Engagement">
            <!-- layout -->
            ...
        </engagement:EngagementPage >

#### <a name="override-the-default-behaviour"></a>Invalidar el comportamiento predeterminado

De forma predeterminada, el nombre de la clase de la página se notifica como el nombre de la actividad con sin adicional. Si la clase utiliza el sufijo "Página", el compromiso quitará también.

Si desea reemplazar el comportamiento predeterminado para el nombre, simplemente lo agrega a su código:

        // in the .xaml.cs file
        protected override string GetEngagementPageName()
        {
          /* your code */
          return "new name";
        }

Si desea informar algunos datos adicionales con su actividad, puede agregar a su código:

        // in the .xaml.cs file
        protected override Dictionary<object,object> GetEngagementPageExtra()
        {
          /* your code */
          return extra;
        }

Estos métodos se denominan desde el `OnNavigatedTo` método de la página.

### <a name="alternate-method-call-startactivity-manually"></a>Método alternativo: llamar `StartActivity()` manualmente

Si no puede o no desea sobrecarga su `Page` clases, en su lugar puede iniciar sus actividades llamando `EngagementAgent` métodos directamente.

Se recomienda para llamar a `StartActivity` dentro de su `OnNavigatedTo` método de la página.

            protected override void OnNavigatedTo(NavigationEventArgs e)
            {
              base.OnNavigatedTo(e);
              EngagementAgent.Instance.StartActivity("MyPage");
            }

> [AZURE.IMPORTANT]  Asegúrese de que finalizar la sesión correctamente.
> 
> El SDK de Windows Universal llama automáticamente la `EndActivity` método cuando se cierra la aplicación. Por tanto, es **muy** recomendada llamar a la `StartActivity` método siempre que la actividad del usuario cambia y **nunca** llamar la `EndActivity` método, este método envía al servidor de contratación que usuario actual se deje la aplicación, esta afecta a todos los registros de la aplicación.

## <a name="advanced-reporting"></a>Informes avanzados

Si lo desea, es aconsejable eventos específicos de la aplicación de informe, errores y tareas, para ello, use los otros métodos que se encuentran en la `EngagementAgent` clase. La API de contratación permite usar todas las capacidades avanzadas de contratación.

Para obtener más información, vea [cómo usar el compromiso Mobile avanzadas etiquetado API en la aplicación Universal de Windows](mobile-engagement-windows-store-use-engagement-api.md).

##<a name="advanced-configuration"></a>Configuración avanzada

### <a name="disable-automatic-crash-reporting"></a>Deshabilitar los informes de bloqueo automático

Puede deshabilitar la característica de contratación de informes de bloqueos automática. A continuación, cuando se produzca una excepción no controlada, compromiso no hará nada.

> [AZURE.WARNING] Si piensa deshabilitar esta característica, debe tener en cuenta que cuando se produzca un bloqueo no controlado en la aplicación, compromiso no enviará que el bloqueo **y** no cierra la sesión y trabajos.

Para deshabilitar el informe de bloqueo automático, simplemente personalizar la configuración según la manera en que se ha declarado:

#### <a name="from-engagementconfigurationxml-file"></a>Desde `EngagementConfiguration.xml` archivo

Establecer el bloqueo de informe `false` entre `<reportCrash>` y `</reportCrash>` etiquetas.

#### <a name="from-engagementconfiguration-object-at-run-time"></a>Desde `EngagementConfiguration` objeto en tiempo de ejecución

Establecer el bloqueo de informe falso mediante el objeto EngagementConfiguration.

        /* Engagement configuration. */
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
        
        /* Disable Engagement crash reporting. */
        engagementConfiguration.Agent.ReportCrash = false;

### <a name="burst-mode"></a>Modo de ráfaga

De forma predeterminada, los informes de servicio de contratación registros en tiempo real. Si su aplicación informes registros con frecuencia, es mejor para los registros del búfer y para informar de a la vez en una base de tiempo regular (Esto se denomina "modo de ráfaga").

Para ello, llame al método:

        EngagementAgent.Instance.SetBurstThreshold(int everyMs);

El argumento es un valor en **milisegundos**. En cualquier momento, si desea volver a activar el registro en tiempo real, simplemente llame al método sin parámetros o con el valor 0.

El modo de ráfaga ligeramente aumentar la duración de la batería, pero tiene un gran impacto en el Monitor de compromiso: todas las sesiones y trabajos la duración se redondeará al umbral de ráfaga (por lo tanto, sesiones y trabajos menos tiempo que el umbral de ráfaga puede no ser visible). Se recomienda usar un umbral de ráfaga ya no que 30000 (30s). Debe tener en cuenta que los registros guardados se limitan a 300 elementos. Si el envío es demasiado larga puede perder algunos registros.

> [AZURE.WARNING] No se puede configurar el umbral de ráfaga en un período menor que 1s. Si intenta hacerlo, el SDK mostrará un seguimiento con el error y se restablecerá automáticamente al valor predeterminado, es decir, 0. Esto activará el SDK para informar de los registros en tiempo real.

[here]:http://www.nuget.org/packages/Capptain.WindowsCS
[NuGet website]:http://docs.nuget.org/docs/start-here/overview
 
