<properties 
    pageTitle="Windows Phone Silverlight SDK Introducción" 
    description="Información general sobre el SDK de Silverlight de Windows Phone para Azure contratación móvil"                     
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

#<a name="windows-phone-silverlight-sdk-overview-for-azure-mobile-engagement"></a>Windows Phone Silverlight SDK Introducción para Azure contratación móvil

Empiece aquí para obtener detalles sobre cómo integrar compromiso de Azure Mobile en un Windows Phone Silverlight App. Si le gustaría pruébelo en primer lugar, asegúrese de que completar nuestro [tutorial de 15 minutos](mobile-engagement-windows-phone-get-started.md).

Haga clic para ver el [Contenido de SDK](mobile-engagement-windows-phone-sdk-content.md)

##<a name="integration-procedures"></a>Procedimientos de integración

1. Empiece aquí: [cómo integrar compromiso Mobile en su aplicación de Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)

2. Para las notificaciones: [cómo integrar alcance (notificaciones) en la aplicación de Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement-reach.md)

3. Implementación del plan de etiqueta: [cómo usar el compromiso Mobile avanzadas etiquetado API en la aplicación de Windows Phone Silverlight](mobile-engagement-windows-phone-use-engagement-api.md)

##<a name="release-notes"></a>Notas de la versión

###<a name="330-04192016"></a>3.3.0 (19/04/2016)
Parte de la nuget *MicrosoftAzure.MobileEngagement* empaquetar **v3.4.0**

-   Registros de consola de agregado "TestLogLevel" API para habilitar o deshabilitar o filtrar emitidos por el SDK.

Para una versión anterior, vea las [notas de la versión completa](mobile-engagement-windows-phone-release-notes.md)

##<a name="upgrade-procedures"></a>Procedimientos de actualización

Si ya ha integrado una versión anterior de nuestro SDK en la aplicación, debe tener en cuenta los siguientes puntos al actualizar el SDK.

Deberá seguir diversos procedimientos si hay varias versiones del SDK. Consulte los [Procedimientos de actualización](mobile-engagement-windows-phone-upgrade-procedure.md). Por ejemplo, si se migra de 0.10.1 a 0.11.0 que debe primero siga el procedimiento "de 0.9.0 a 0.10.1", a continuación, el procedimiento "de 0.10.1 a 0.11.0".

###<a name="from-200-to-330"></a>Desde 2.0.0 a 3.3.0

####<a name="test-logs"></a>Registros de pruebas

Registros de consola generados por el SDK ahora pueden ser habilitada, deshabilitada o filtrado. Para personalizar esto, actualice la propiedad `EngagementAgent.Instance.TestLogEnabled` a uno de los valores de la `EngagementTestLogLevel` enumeración, por ejemplo:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

### <a name="upgrade-from-older-versions"></a>Actualizar desde versiones anteriores

Consulte [la actualización de procedimientos](mobile-engagement-windows-phone-upgrade-procedure.md)
 
