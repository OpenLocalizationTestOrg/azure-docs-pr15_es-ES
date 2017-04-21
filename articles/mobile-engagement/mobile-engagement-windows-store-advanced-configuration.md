<properties
    pageTitle="Configuración de compromiso de aplicaciones Universal de Windows SDK avanzada"
    description="Opciones de configuración de compromiso de Azure Mobile con Windows Universal aplicaciones avanzadas"                    
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
    ms.date="10/04/2016"
    ms.author="piyushjo;ricksal" />

# <a name="advanced-configuration-for-windows-universal-apps-engagement-sdk"></a>Configuración de compromiso de aplicaciones Universal de Windows SDK avanzada

> [AZURE.SELECTOR]
- [Windows universal](mobile-engagement-windows-store-advanced-configuration.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-advanced-configuration.md)

Este procedimiento describe cómo configurar diversas opciones de configuración para las aplicaciones de Azure Mobile compromiso Android.

## <a name="prerequisites"></a>Requisitos previos

[AZURE.INCLUDE [Prereqs](../../includes/mobile-engagement-windows-store-prereqs.md)]

##<a name="advanced-configuration"></a>Configuración avanzada

### <a name="disable-automatic-crash-reporting"></a>Deshabilitar los informes de bloqueo automático

Puede deshabilitar la característica de contratación de informes de bloqueos automática. A continuación, cuando se produce una excepción no controlada, compromiso no hace nada.

> [AZURE.WARNING] Si deshabilita esta característica, a continuación, cuando se produce un error no controlado en la aplicación, compromiso no envía que el bloqueo **y** no cierra la sesión y trabajos.

Para deshabilitar el informe de bloqueo automático, personalizar la configuración según la manera en que se ha declarado:

#### <a name="from-engagementconfigurationxml-file"></a>Desde `EngagementConfiguration.xml` archivo

Establecer el bloqueo de informe `false` entre `<reportCrash>` y `</reportCrash>` etiquetas.

#### <a name="from-engagementconfiguration-object-at-run-time"></a>Desde `EngagementConfiguration` objeto en tiempo de ejecución

Establecer el bloqueo de informe falso mediante el objeto EngagementConfiguration.

        /* Engagement configuration. */
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

        /* Disable Engagement crash reporting. */
        engagementConfiguration.Agent.ReportCrash = false;

### <a name="disable-real-time-reporting"></a>Deshabilitar informes en tiempo real

De forma predeterminada, los informes de servicio de contratación registros en tiempo real. Si su aplicación informes registros con frecuencia, es mejor para los registros del búfer y para informar de todas a la vez en una base de tiempo regular. Esto se denomina "modo de ráfaga".

Para ello, llame al método:

        EngagementAgent.Instance.SetBurstThreshold(int everyMs);

El argumento es un valor en **milisegundos**. Siempre que desee volver a activar el registro en tiempo real, llame al método sin parámetros o con el valor 0.

Modo de ráfaga ligeramente aumenta la batería, pero tiene un gran impacto en el Monitor de compromiso: todas las sesiones y trabajos la duración se redondean el umbral de ráfaga (por lo tanto, sesiones y trabajos menos tiempo que el umbral de ráfaga puede no ser visible). Se recomienda usar un umbral de ráfaga ya no que 30000 (30s). Registros guardados se limitan a 300 elementos. Si envía es demasiado larga, puede perder algunos registros.

> [AZURE.WARNING] No se puede configurar el umbral de ráfaga en un período menor que un segundo. Si lo hace, el SDK muestra un seguimiento con el error y restablece automáticamente el valor predeterminado, cero segundos. Esto activa el SDK para informar de los registros en tiempo real.

[here]:http://www.nuget.org/packages/Capptain.WindowsCS
[NuGet website]:http://docs.nuget.org/docs/start-here/overview
