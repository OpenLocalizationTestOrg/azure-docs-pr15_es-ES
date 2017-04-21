<properties
    pageTitle="Azure compromiso móvil iOS SDK integración | Microsoft Azure"
    description="Últimas actualizaciones y procedimientos para iOS SDK para Azure Mobile contratación"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="09/14/2016"
    ms.author="piyushjo" />

#<a name="how-to-integrate-engagement-on-ios"></a>Cómo integrar compromiso en iOS

> [AZURE.SELECTOR]
- [Universal de Windows](mobile-engagement-windows-store-integrate-engagement.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-integrate-engagement.md)

Este procedimiento describe la manera más sencilla de activar el análisis de contratación y la supervisión de funciones en la aplicación de iOS.

El SDK de contratación requiere iOS6 + y Xcode 8: el destino de implementación de la aplicación debe tener al menos iOS 6.

> [AZURE.NOTE]
> Si realmente depende XCode 7 puede usar [iOS compromiso SDK v3.2.4](https://aka.ms/r6oouh). Hay un error conocido en el módulo de alcance de esta versión anterior mientras se ejecuta en dispositivos iOS 10 vea [la integración de módulo alcance](mobile-engagement-ios-integrate-engagement-reach.md) para obtener más detalles. Si decide usar el SDK v3.2.4, a continuación, pasar por alto el `UserNotifications.framework` importar en el siguiente paso.

Los pasos siguientes son suficientes para activar el informe de registros necesarios para calcular todas las estadísticas acerca de los usuarios, sesiones, actividades, se bloquea y Technicals. El informe de registros necesarios para calcular otros datos estadísticos como eventos, errores y trabajos debe hacerse manualmente con la API de contratación (consulte [cómo usar el compromiso Mobile avanzadas etiquetado API en su aplicación iOS](mobile-engagement-ios-use-engagement-api.md) ya que estas estadísticas depende de la aplicación.

##<a name="embed-the-engagement-sdk-into-your-ios-project"></a>Integrar el SDK de contratación en su proyecto de iOS

- Descargar el SDK de iOS desde [aquí](http://aka.ms/qk2rnj).

- Agregue el SDK de contratación a su proyecto iOS: en Xcode, haga clic con el botón secundario en el proyecto y seleccione **"Agregar archivos a..."** y elija la `EngagementSDK` carpeta.

- Compromiso requiere marcos adicionales para trabajar: en el Explorador de proyectos, abra el panel de proyecto y seleccione el destino correcto. A continuación, abra la pestaña **"Crear fases"** y en el menú **"binario con bibliotecas de vínculos"** , agregue estos marcos:

    -   `UserNotifications.framework`-establecer el vínculo como`Optional`
    -   `AdSupport.framework`-establecer el vínculo como`Optional`
    -   `SystemConfiguration.framework`
    -   `CoreTelephony.framework`
    -   `CFNetwork.framework`
    -   `CoreLocation.framework`
    -   `libxml2.dylib`

> [AZURE.NOTE] Se puede quitar el marco AdSupport. Compromiso necesita este marco de trabajo para recopilar la IDFA. Sin embargo, puede deshabilitarse colección IDFA \<ios-sdk-contratación-idfa\> para cumplir con la nueva directiva de Apple sobre este Id.

##<a name="initialize-the-engagement-sdk"></a>Inicializar el compromiso SDK

Debe modificar al delegado de la aplicación:

-   En la parte superior de su archivo de implementación, importar al agente de compromiso:

        [...]
        #import "EngagementAgent.h"

-   Inicializar compromiso dentro del método '**applicationDidFinishLaunching:**'o'**aplicación: didFinishLaunchingWithOptions:**':

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
        {
          [...]
          [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
          [...]
        }

##<a name="basic-reporting"></a>Informes básicos

### <a name="recommended-method-overload-your-uiviewcontroller-classes"></a>Método recomendado: sobrecargue su `UIViewController` clases

Para activar el informe de todos los registros necesarios para compromiso para calcular los usuarios, sesiones, actividades, se bloquea y las estadísticas técnicas, puede crear todo su `UIViewController` clases subsitios que heredan de la `EngagementViewController` clases (misma regla para `UITableViewController`  - \> `EngagementTableViewController`).

**Sin compromiso:**

    #import <UIKit/UIKit.h>

    @interface Tab1ViewController : UIViewController<UITextFieldDelegate> {
      UITextField* myTextField1;
      UITextField* myTextField2;
    }

    @property (nonatomic, retain) IBOutlet UITextField* myTextField1;
    @property (nonatomic, retain) IBOutlet UITextField* myTextField2;

**Con compromiso:**

    #import <UIKit/UIKit.h>
    #import "EngagementViewController.h"

    @interface Tab1ViewController : EngagementViewController<UITextFieldDelegate> {
      UITextField* myTextField1;
      UITextField* myTextField2;
    }

    @property (nonatomic, retain) IBOutlet UITextField* myTextField1;
    @property (nonatomic, retain) IBOutlet UITextField* myTextField2;

### <a name="alternate-method-call-startactivity-manually"></a>Método alternativo: llamar `startActivity()` manualmente

Si no puede o no desea sobrecarga su `UIViewController` clases, en su lugar puede iniciar sus actividades llamando `EngagementAgent`de métodos directamente.

> [AZURE.IMPORTANT] IOS SDK llama automáticamente la `endActivity()` método cuando se cierra la aplicación. Por tanto, es *muy* recomendada llamar a la `startActivity` método siempre que la actividad del usuario cambia y *nunca* llamar la `endActivity` método, puesto que llamar a este método fuerza la sesión actual se va a finalizar.

##<a name="location-reporting"></a>Reporting de ubicación

Términos de Apple de servicio no permitir aplicaciones para usar solo con fines de estadísticas de seguimiento de ubicación. Por lo tanto, se recomienda habilitar informes de ubicación solo si la aplicación también utilizar la ubicación de seguimiento por otro motivo.

Comenzando con iOS 8, debe proporcionar una descripción para el modo en que la aplicación utiliza los servicios de ubicación configurando una cadena para la clave [NSLocationWhenInUseUsageDescription] o [NSLocationAlwaysUsageDescription] en el archivo de Info.plist de la aplicación. Si desea que la ubicación del informe en segundo plano con compromiso, agregue la clave NSLocationAlwaysUsageDescription. En los demás casos, agregue la clave NSLocationWhenInUseUsageDescription.

### <a name="lazy-area-location-reporting"></a>Reporting de ubicación de área diferida

Reporting de ubicación de área diferida permite para informar del país, región y ubicación asociadas a los dispositivos. Informes de la ubicación de este tipo solo usa ubicaciones de red (según el identificador de celda o Wi-Fi). El área del dispositivo se notifica al menos una vez por sesión. Nunca se usa el GPS y, por tanto, este tipo de informe de ubicación tiene muy pocos (no quiere decir que no) influencia sobre la batería.

Áreas notificadas se usan para calcular estadísticas geográficas acerca de los usuarios, sesiones, eventos y errores. También se puede utilizar como criterio en las campañas de alcance. Gracias a la [API del dispositivo], se puede recuperar la última área conocida notificada para un dispositivo.

Para habilitar el informe de ubicación de área diferida, agregue la línea siguiente después iniciando al agente de compromiso:

    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
    {
      [...]
      [[EngagementAgent shared] setLazyAreaLocationReport:YES];
      [...]
    }

### <a name="real-time-location-reporting"></a>Ubicación informes en tiempo real

Informes en tiempo real ubicación permiten informar la latitud y longitud asociados a los dispositivos. De forma predeterminada, informes de la ubicación de este tipo solo usa ubicaciones de red (según el identificador de celda o Wi-Fi) y los informes solo esté activo cuando la aplicación se ejecuta en primer plano (es decir, durante una sesión).

*No* utilizada para calcular estadísticas son ubicaciones en tiempo real. Su único propósito es permitir el uso de tiempo real geo-barrera \<geofencing de audiencia de alcance\> criterio en las campañas de alcance.

Para habilitar la ubicación informes en tiempo real, agregue la línea siguiente después iniciando al agente de compromiso:

    [[EngagementAgent shared] setRealtimeLocationReport:YES];

#### <a name="gps-based-reporting"></a>En función de GPS informes

De forma predeterminada, solo ubicación informes en tiempo real utiliza ubicaciones de red. Para habilitar el uso de ubicaciones de GPS según (que son mucho más preciso), agregue:

    [[EngagementAgent shared] setFineRealtimeLocationReport:YES];

#### <a name="background-reporting"></a>Informes de fondo

De forma predeterminada, el informes en tiempo real ubicación solo esté activa cuando la aplicación se ejecuta en primer plano (es decir, durante una sesión). Para habilitar la notificación también en segundo plano, agregue:

    [[EngagementAgent shared] setBackgroundRealtimeLocationReport:YES withLaunchOptions:launchOptions];

> [AZURE.NOTE] Cuando la aplicación se ejecuta en segundo plano, sólo ubicaciones basada en la red se notifican, incluso si ha habilitado el GPS.

Implementación de esta función llamará a [startMonitoringSignificantLocationChanges] cuando la aplicación entra en segundo plano. Tenga en cuenta que automáticamente reinicie la aplicación en el fondo si llega un nuevo evento de ubicación.

##<a name="advanced-reporting"></a>Informes avanzados

Opcionalmente, si desea informar eventos específicos de la aplicación, los errores y trabajos, debe usar la API de compromiso a través de los métodos de la `EngagementAgent` clase. Un objeto de esta clase se puede recuperar llamando el `[EngagementAgent shared]` método estático.

La API de contratación permite usar todas las capacidades avanzadas de contratación y se detallan cómo usar la API de contratación en iOS (así como en la documentación técnica de la `EngagementAgent` clase).

##<a name="disable-idfa-collection"></a>Deshabilitar la recopilación de IDFA

De forma predeterminada, contratación usará el [IDFA] para identificar un usuario. Pero, si no está utilizando publicidad en otra parte de la aplicación, puede ser rechazados por el proceso de revisión de la tienda de aplicaciones. Colección de IDFA puede deshabilitarse agregando la macro de preprocesador `ENGAGEMENT_DISABLE_IDFA` en el archivo pch (o en la `Build Settings` de la aplicación). Esto se asegurará de que no hay ninguna referencia a `ASIdentifierManager`, `advertisingIdentifier` o `isAdvertisingTrackingEnabled` en la compilación de la aplicación.

Integración en el archivo **prefix.pch** :

    #define ENGAGEMENT_DISABLE_IDFA
    ...

Puede comprobar que la colección de IDFA está deshabilitada correctamente en la aplicación comprobando los registros de prueba de compromiso. Vea la integración de pruebas\<ios sdk compromiso prueba idfa\> documentación para obtener más información.

##<a name="disable-log-reporting"></a>Deshabilitar los informes de registro

### <a name="using-a-method-call"></a>Mediante una llamada de método

Si desea que el compromiso para dejar de enviar los registros, puede llamar:

    [[EngagementAgent shared] setEnabled:NO];

Esta llamada es persistente: utiliza `NSUserDefaults` para almacenar la información.

Puede habilitar el registro nuevo informe mediante una llamada a la misma función con `YES`.

### <a name="integration-in-your-settings-bundle"></a>Integración en el paquete de configuración

En lugar de llamar a esta función, también puede integrar esta configuración directamente en el existente `Settings.bundle` archivo. La cadena `engagement_agent_enabled` debe utilizar como un identificador de la preferencia y deben estar asociados a un conmutador (`PSToggleSwitchSpecifier`).

El siguiente ejemplo de `Settings.bundle` muestra cómo implementarlo:

    <dict>
        <key>PreferenceSpecifiers</key>
        <array>
            <dict>
                <key>DefaultValue</key>
                <true/>
                <key>Key</key>
                <string>engagement_agent_enabled</string>
                <key>Title</key>
                <string>Log reporting enabled</string>
                <key>Type</key>
                <string>PSToggleSwitchSpecifier</string>
            </dict>
        </array>
        <key>StringsTable</key>
        <string>Root</string>
    </dict>

<!-- URLs. -->
[API de dispositivo]: http://go.microsoft.com/?linkid=9876094
[NSLocationWhenInUseUsageDescription]:https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26
[NSLocationAlwaysUsageDescription]:https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW18
[startMonitoringSignificantLocationChanges]:http://developer.apple.com/library/IOs/#documentation/CoreLocation/Reference/CLLocationManager_Class/CLLocationManager/CLLocationManager.html#//apple_ref/occ/instm/CLLocationManager/startMonitoringSignificantLocationChanges
[IDFA]:https://developer.apple.com/library/ios/documentation/AdSupport/Reference/ASIdentifierManager_Ref/ASIdentifierManager.html#//apple_ref/occ/instp/ASIdentifierManager/advertisingIdentifier
