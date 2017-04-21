<properties 
    pageTitle="Windows Phone Silverlight SDK procedimientos de actualización" 
    description="Windows Phone Silverlight SDK procedimientos de actualización para Azure contratación móvil"                  
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

#<a name="windows-phone-silverlight-sdk-upgrade-procedures"></a>Windows Phone Silverlight SDK procedimientos de actualización

Si ya ha integrado una versión anterior de nuestro SDK en la aplicación, debe tener en cuenta los siguientes puntos al actualizar el SDK.

Deberá seguir diversos procedimientos si hay varias versiones del SDK. Por ejemplo, si se migra de 0.10.1 a 0.11.0 que debe primero siga el procedimiento "de 0.9.0 a 0.10.1", a continuación, el procedimiento "de 0.10.1 a 0.11.0".

##<a name="from-200-to-330"></a>Desde 2.0.0 a 3.3.0

### <a name="test-logs"></a>Registros de pruebas

Registros de consola generados por el SDK ahora pueden ser habilitado, deshabilitado o filtrada. Para personalizar esto, actualice la propiedad `EngagementAgent.Instance.TestLogEnabled` a uno de los valores de la `EngagementTestLogLevel` enumeración, por ejemplo:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

##<a name="from-111-to-200"></a>Desde 1.1.1 a 2.0.0

La siguiente describe cómo migrar una integración SDK desde el servicio de Capptain ofrecido por Capptain SAS a una aplicación con tecnología de Azure Mobile compromiso. 

> [Azure.IMPORTANT] Capptain y Mobile compromiso no son los mismos servicios y el procedimiento siguiente sólo resalta cómo migrar la aplicación de cliente. Migrar el SDK en la aplicación no se migrarán los datos de los servidores de Capptain a los servidores de compromiso de móvil

Si va a migrar desde una versión anterior, consulte el sitio web de Capptain para migrar a 1.1.1 en primer lugar, a continuación, aplique el siguiente procedimiento

### <a name="nuget-package"></a>Paquete NuGet

Reemplace **Capptain.WindowsPhone** **MicrosoftAzure.MobileEngagement** Nuget paquete.

### <a name="applying-mobile-engagement"></a>Aplicar compromiso móvil

El SDK utiliza el término `Engagement`. Deberá actualizar el proyecto para que coincida con este cambio.

Debe desinstalar el paquete de nuget Capptain actual. Tenga en cuenta que se eliminarán todos los cambios en la carpeta Capptain recursos. Si desea conservar los archivos, a continuación, hacer una copia de ellos.

Después de eso, instale el paquete de nuget de Microsoft Azure compromiso nuevo en su proyecto. Puede encontrarlo directamente en [Nuget](http://www.nuget.org/packages/MicrosoftAzure.MobileEngagement). Esta acción reemplaza todos los archivos de recursos utilizados en contratación y agrega la nueva DLL de contratación a las referencias del proyecto.

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

3. Para los archivos xaml atributos y espacio de nombres de Capptain también cambian.

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

4. Para los demás recursos como Capptain imágenes, tenga en cuenta que también cambiaron para usar "Compromiso".

### <a name="application-id--sdk-key"></a>Id. de aplicación / SDK clave

Compromiso usa una cadena de conexión. No tiene que especificar un identificador de la aplicación y una tecla de SDK con compromiso de móvil, solo tiene que especificar una cadena de conexión. Puede configurarlo en el archivo EngagementConfiguration.

Puede establecer la configuración de compromiso en su `Resources\EngagementConfiguration.xml` archivo del proyecto.

Editar este archivo para especificar:

-   La cadena de conexión de la aplicación entre etiquetas `<connectionString>` y `<\connectionString>`.

Si desea especificar en tiempo de ejecución, puede llamar al método siguiente antes de la inicialización del agente de compromiso:

        /* Engagement configuration. */
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
        
        /* Initialize Engagement angent with above configuration. */
        EngagementAgent.Instance.Init(engagementConfiguration);

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



 
