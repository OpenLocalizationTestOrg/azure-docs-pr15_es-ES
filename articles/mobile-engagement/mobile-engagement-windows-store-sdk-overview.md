<properties
    pageTitle="Integración de Windows SDK Universal"
    description="Integración Universal SDK de Windows para Azure contratación móvil"                                     
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
    ms.date="08/12/2016"
    ms.author="piyushjo;ricksal" />

#<a name="windows-universal-sdk-integration-for-azure-mobile-engagement"></a>Integración de Windows SDK Universal para Azure contratación móvil

Este documento describe todas las integración y configuración de opciones disponibles para el compromiso Windows Azure Mobile Universal SDK.

## <a name="prerequisites"></a>Requisitos previos

Antes de comenzar este tutorial, primero debe completar nuestro [tutorial de 15 minutos](mobile-engagement-windows-store-dotnet-get-started.md).

## <a name="advanced-features"></a>Características avanzadas

### <a name="reporting-features"></a>Características de los informes
Puede agregar estas características:

1. [Creación de informes opciones avanzadas](mobile-engagement-windows-store-advanced-reporting.md)

2. [Opciones de configuración avanzada](mobile-engagement-windows-store-advanced-configuration.md)

### <a name="notifications"></a>Notificaciones

[Cómo integrar alcance (notificaciones) en la aplicación Universal de Windows](mobile-engagement-windows-store-integrate-engagement-reach.md)

### <a name="tag-plan-implementation"></a>Implementación del plan de etiqueta:

[Cómo usar el compromiso Mobile avanzadas etiquetas temáticas API de la aplicación Windows Universal](mobile-engagement-windows-store-use-engagement-api.md)

##<a name="release-notes"></a>Notas de la versión

###<a name="340-04192016"></a>3.4.0 (19/04/2016)

-   Llegar a mejoras en la superposición.
-   Registros de consola de agregado "TestLogLevel" API para habilitar o deshabilitar o filtrar emitidos por el SDK.
-   Iniciar fijas notificaciones de en la actividad de la primera actividad que no se muestra en la aplicación de destino.

Para las versiones anteriores, vea las [notas de la versión completa](mobile-engagement-windows-store-release-notes.md)

##<a name="upgrade-procedures"></a>Procedimientos de actualización

Si ya ha integrado una versión anterior de contratación en la aplicación, debe tener en cuenta los siguientes puntos al actualizar el SDK.

Si hay varias versiones del SDK, deberá seguir diversos procedimientos. Consulte los [Procedimientos de actualización](mobile-engagement-windows-store-upgrade-procedure.md). Por ejemplo, si se migra de 0.10.1 a 0.11.0 que debe primero siga el procedimiento "de 0.9.0 a 0.10.1", a continuación, el procedimiento "de 0.10.1 a 0.11.0".

###<a name="from-330-to-340"></a>Desde 3.3.0 a 3.4.0

####<a name="test-logs"></a>Registros de pruebas

Registros de consola generados por el SDK ahora pueden ser habilitado, deshabilitado o filtrada. Para personalizar, actualizar la propiedad `EngagementAgent.Instance.TestLogEnabled` a uno de los valores de la `EngagementTestLogLevel` enumeración, por ejemplo:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

####<a name="resources"></a>Recursos

Se ha mejorado la superposición de alcance. Es parte de los recursos del paquete NuGet SDK.

Al actualizar a la nueva versión del SDK, puede elegir si desea conservar los archivos existentes de la carpeta de superposición de los recursos o no:

* Si la superposición anterior está trabajando para usted, o que integra la `WebView` elementos manualmente, a continuación, puede decidir mantener su salir de archivos, aún funcionará.
* Para actualizar a la nueva superposición, reemplazar todo el `overlay` carpeta desde los recursos con el nuevo desde el paquete del SDK (UWP aplicaciones: tras la actualización, puede obtener la nueva carpeta de superposición de % PERFILUSUARIO %\\.nuget\packages\MicrosoftAzure.MobileEngagement\3.4.0\content\win81\Resources).

> [AZURE.WARNING] Usando la superposición nueva sobrescribe todas las personalizaciones realizadas en la versión anterior.

### <a name="upgrade-from-older-versions"></a>Actualizar desde versiones anteriores

Consulte [la actualización de procedimientos](mobile-engagement-windows-store-upgrade-procedure.md)
