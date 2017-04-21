<properties
    pageTitle="Informes con compromiso MobileApps avanzado de Universal de Windows"
    description="Cómo integrar Azure compromiso móvil con Windows Universal aplicaciones"                  
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows-store"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/12/2016"
    ms.author="piyushjo;ricksal" />

# <a name="advanced-reporting-with-the-windows-universal-apps-engagement-sdk"></a>Informes con el compromiso de aplicaciones Universal de Windows SDK avanzados

> [AZURE.SELECTOR]
- [Windows universal](mobile-engagement-windows-store-advanced-reporting.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-advanced-reporting.md)

Este tema describen los escenarios de informes adicionales en la aplicación Universal de Windows. Estos escenarios incluyen opciones que puede aplicar a la aplicación que creó en el tutorial de [Introducción](mobile-engagement-windows-store-dotnet-get-started.md) .

## <a name="prerequisites"></a>Requisitos previos

[AZURE.INCLUDE [Prereqs](../../includes/mobile-engagement-windows-store-prereqs.md)]

Antes de comenzar este tutorial, primero debe completar el tutorial de [Introducción](mobile-engagement-windows-store-dotnet-get-started.md) , que es deliberadamente directo y simple. Este tutorial trata sobre opciones adicionales que puede elegir.

## <a name="specifying-engagement-configuration-at-runtime"></a>Especificar la configuración de compromiso en tiempo de ejecución

La configuración de compromiso está centralizada en el `Resources\EngagementConfiguration.xml` archivo de proyecto, que es donde se especificó en el tema de [Introducción](mobile-engagement-windows-store-dotnet-get-started.md) .

Pero también puede especificar en tiempo de ejecución: puede llamar al método siguiente antes de la inicialización del agente de compromiso:

          /* Engagement configuration. */
          EngagementConfiguration engagementConfiguration = new EngagementConfiguration();

          /* Set the Engagement connection string. */
          engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

          /* Initialize Engagement angent with above configuration. */
          EngagementAgent.Instance.Init(e, engagementConfiguration);



## <a name="recommended-method-overload-your-page-classes"></a>Método recomendado: sobrecargue su `Page` clases

Para activar la notificación de todos los registros necesarios para compromiso para calcular los usuarios, sesiones, actividades, se bloquea y las estadísticas técnicas, asegúrese de todo su `Page` clases subsitios que heredan de la `EngagementPage` clases.

Aquí es un ejemplo de una página de la aplicación. Puede hacer lo mismo para todas las páginas de la aplicación.

### <a name="c-source-file"></a>Archivo de origen C#

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

> [AZURE.IMPORTANT] Si la página reemplaza el `OnNavigatedTo` método, asegúrese de llamar a `base.OnNavigatedTo(e)`. En caso contrario, la actividad no se notificará (la `EngagementPage` llamadas `StartActivity` dentro de su `OnNavigatedTo` método).

### <a name="xaml-file"></a>Archivo XAML

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

### <a name="override-the-default-behaviour"></a>Invalidar el comportamiento predeterminado

De forma predeterminada, el nombre de la clase de la página se notifica como el nombre de la actividad con sin adicional. Si la clase utiliza el sufijo "Página", contratación quita.

Para reemplazar el comportamiento predeterminado para el nombre, agregue este código:

        // in the .xaml.cs file
        protected override string GetEngagementPageName()
        {
          /* your code */
          return "new name";
        }

Para notificar información adicional con su actividad, agregue este código:

        // in the .xaml.cs file
        protected override Dictionary<object,object> GetEngagementPageExtra()
        {
          /* your code */
          return extra;
        }

Estos métodos se denominan desde el `OnNavigatedTo` método de la página.

### <a name="alternate-method-call-startactivity-manually"></a>Método alternativo: llamar `StartActivity()` manualmente

Si no puede o no desea sobrecarga su `Page` clases, en su lugar puede iniciar sus actividades llamando `EngagementAgent` métodos directamente.

Le recomendamos que llama `StartActivity` dentro de su `OnNavigatedTo` método de la página.

            protected override void OnNavigatedTo(NavigationEventArgs e)
            {
              base.OnNavigatedTo(e);
              EngagementAgent.Instance.StartActivity("MyPage");
            }

> [AZURE.IMPORTANT]  Asegúrese de que finalizar la sesión correctamente.
>
> El SDK de Windows Universal llama automáticamente la `EndActivity` método cuando se cierra la aplicación. Por tanto, es **muy** recomendada llamar a la `StartActivity` método siempre que la actividad del usuario cambia y **nunca** llamar la `EndActivity` método. Este método notifica que el servidor de contratación que el usuario actual ha dejado la aplicación, que afectará a todos los registros de la aplicación.

## <a name="advanced-reporting"></a>Informes avanzados

Si lo desea, puede que desee informar eventos específicos de la aplicación, los errores y trabajos para hacerlo, usan los otros métodos que se encuentran en la `EngagementAgent` clase. La API de contratación permite el uso de las capacidades avanzadas de contratación todos.

Para obtener más información, vea [cómo usar el compromiso Mobile avanzadas etiquetado API en la aplicación Universal de Windows](mobile-engagement-windows-store-use-engagement-api.md).
