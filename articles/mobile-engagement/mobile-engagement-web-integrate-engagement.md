<properties
    pageTitle="Integración de Mobile compromiso Web SDK Azure | Microsoft Azure"
    description="Las últimas actualizaciones y procedimientos para el SDK de Azure Mobile compromiso Web"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="web"
    ms.devlang="js"
    ms.topic="article"
    ms.date="02/29/2016"
    ms.author="piyushjo" />

#<a name="integrate-azure-mobile-engagement-in-a-web-application"></a>Integrar el compromiso de Azure Mobile en una aplicación web

> [AZURE.SELECTOR]
- [Universal de Windows](mobile-engagement-windows-store-integrate-engagement.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-integrate-engagement.md)

Los procedimientos descritos en este artículo describen la manera más sencilla de activar las funciones de supervisión en Azure Mobile compromiso en la aplicación web y el análisis.

Siga los pasos para activar los informes de registro que son necesarios para calcular todas las estadísticas acerca de los usuarios, sesiones, actividades, se bloquea y technicals. Estadísticas de dependientes de la aplicación, como eventos, errores y tareas, debe activar informes de registro de manualmente mediante la API de compromiso de Azure Mobile. Para obtener más información, aprenda [a usar el compromiso Mobile avanzadas etiquetado API en una aplicación web](mobile-engagement-web-use-engagement-api.md).

## <a name="introduction"></a>Introducción

[Descargar el SDK de Azure compromiso móvil Web](http://aka.ms/P7b453).
El SDK de Web compromiso Mobile se incluye como un único archivo de JavaScript, azure engagement.js, que se deben incluir en cada página de su sitio o una aplicación web.

> [AZURE.IMPORTANT] Antes de ejecutar esta secuencia de comandos, debe ejecutar un fragmento de código o script que escribe para configurar Mobile compromiso de la aplicación.

## <a name="browser-compatibility"></a>Compatibilidad de explorador

El SDK de Web Mobile compromiso usa JSON nativa codificar y descodificar, además de las solicitudes de AJAX de dominios (depender la especificación de W3C CORS). Es compatible con los siguientes exploradores:

* Microsoft Edge 12 +
* Internet Explorer 10 +
* Firefox 3.5 +
* Chrome 4 +
* Safari 6 +
* Opera 12 +

## <a name="configure-mobile-engagement"></a>Configurar el compromiso de móvil

Escribir un script que crea global `azureEngagement` objeto de JavaScript, como en el ejemplo siguiente. Dado que el sitio puede tener páginas de múltiplos, se supone que esta secuencia de comandos se incluye en cada página. En este ejemplo, el objeto de JavaScript se denomina `azure-engagement-conf.js`.

    window.azureEngagement = {
      connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
      appVersionName: '1.0.0',
      appVersionCode: 1
    };

La `connectionString` el valor de la aplicación se muestra en el portal de Azure.

> [AZURE.NOTE] `appVersionName`y `appVersionCode` son opcionales. Sin embargo, se recomienda configurarlos para que el análisis pueda procesar la información de versión.

## <a name="include-mobile-engagement-scripts-in-your-pages"></a>Incluir secuencias de comandos de contratación Mobile en las páginas
Agregar secuencias de comandos de contratación Mobile a las páginas de una de las siguientes maneras:

    <head>
      ...
      <script type="text/javascript" src="azure-engagement-conf.js"></script>
      <script type="text/javascript" src="azure-engagement.js"></script>
      ...
    </head>

O bien:

    <body>
      ...
      <script type="text/javascript" src="azure-engagement-conf.js"></script>
      <script type="text/javascript" src="azure-engagement.js"></script>
      ...
    </body>

## <a name="alias"></a>Alias

Una vez cargado el script Mobile compromiso Web SDK, crea el alias de **contratación** para tener acceso a las API de SDK. No puede usar este alias para definir la configuración de SDK. Este alias se utiliza como una referencia en esta documentación.

Tenga en cuenta que si el alias predeterminado entra en conflicto con otra variable global de la página, puede redefinir en la configuración de manera antes de cargar el SDK de Web Mobile compromiso:

    window.azureEngagement = {
      connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
      appVersionName: '1.0.0',
      appVersionCode: 1
      alias:'anotherAlias'
    };

## <a name="basic-reporting"></a>Informes básicos

Informes básicos en Mobile contratación cubre estadísticas de nivel de sesión, como estadísticas acerca de los usuarios, sesiones, las actividades y se bloquea.

### <a name="session-tracking"></a>Seguimiento de sesión

Una sesión de contratación Mobile está dividida en una secuencia de actividades, cada una identificada por un nombre.

En un sitio Web clásico, le recomendamos que declarar una actividad diferente en cada página del sitio. Para una sitio Web o una aplicación web en el que nunca cambia la página actual, podría desea realizar un seguimiento de las actividades en una escala menor, como dentro de la página.

En ambos casos, para iniciar o cambiar la actividad de usuario actual, llame a la `engagement.agent.startActivity` función. Por ejemplo:

    <body onload="yourOnload()">

    <!-- -->

    yourOnload = function() {
      [...]
      engagement.agent.startActivity('welcome');
    };

El servidor de Mobile compromiso finaliza automáticamente una sesión abierta en tres minutos después de cerrar la página de la aplicación.

Como alternativa, puede finalizar una sesión manualmente llamando `engagement.agent.endActivity`. Esto establece la actividad de usuario actual a 'Inactivo'.  La sesión finalizará 10 segundos más adelante a menos que una nueva llamada al `engagement.agent.startActivity` reanuda la sesión.

Puede configurar el tiempo de retraso de 10 segundos en el objeto de compromiso global, como sigue:

    engagement.sessionTimeout = 2000; // 2 seconds
    // or
    engagement.sessionTimeout = 0; // end the session as soon as endActivity is called

> [AZURE.NOTE] No puede usar `engagement.agent.endActivity` en la `onunload` devolución de llamada porque no se puede realizar llamadas de AJAX en esta fase.

## <a name="advanced-reporting"></a>Informes avanzados

Opcionalmente, si desea informar de los trabajos, los errores y eventos específicos de la aplicación, debe usar la API de contratación Mobile. Puede obtener acceso a la API de contratación móvil a través de la `engagement.agent` objeto.

Puede obtener acceso a todas las capacidades avanzadas de compromiso de móvil en la API de contratación Mobile. La API se detalla en el artículo [cómo usar el compromiso Mobile avanzadas etiquetado API en una aplicación web](mobile-engagement-web-use-engagement-api.md).

## <a name="customize-the-urls-used-for-ajax-calls"></a>Personalizar las direcciones URL usadas para las llamadas de AJAX

Puede personalizar las direcciones URL que usa el SDK de Web Mobile compromiso. Por ejemplo, para volver a definir la dirección URL de registro (el extremo SDK para el registro), puede omitir la configuración similar a esta:

    window.azureEngagement = {
      ...
      urls: {
        ...        
        getLoggerUrl: function() {
        return 'someProxy/log';
        }
      }
    };

Si las funciones de la dirección URL devuelven una cadena que comienza con `/`, `//`, `http://`, o `https://`, no se utiliza el esquema de forma predeterminada. De forma predeterminada, la `https://` esquema se utiliza para las direcciones URL. Si desea personalizar el esquema de forma predeterminada, invalidar la configuración, similar a esta:

    window.azureEngagement = {
      ...
      urls: {
        ...      
        scheme: '//'
      }
    };
