<properties 
    pageTitle="Notas de la versión de perspectivas de aplicación para Windows" 
    description="Las actualizaciones más recientes de la tienda de Windows SDK." 
    services="application-insights" 
    documentationCenter=""
    authors="alancameronwills" 
    manager="douge"/>
<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="02/12/2016" 
    ms.author="joshweb"/>
 
# <a name="release-notes-for-application-insights-sdk-for-windows-phone-and-store"></a>Notas de la versión de aplicación perspectivas SDK para Windows Phone y almacenar

El SDK de perspectivas de aplicación envía telemetría sobre la aplicación de live [Perspectivas de aplicación](https://azure.microsoft.com/services/application-insights/), donde puede analizar su uso y rendimiento.


## <a name="version-111"></a>Versión 1.1.1

### <a name="windows-sdk"></a>Windows SDK

- Corregir un bloqueo durante bloqueo cuando se usa el SDK de Silverlight de de Windows Phone. Después de este cambio, se conservará cualquier bloqueo que ocurre más adelante ~ 2 segundos después de la llamada a WindowsAppInitialier.InitializeAsync(...) en disco y se enviará la próxima vez que se inicie la aplicación. Si se produce un bloqueo antes ~ 2 segundos después de la llamada, se omitirá.  
- Establecer dependencias de NuGet a una versión específica del principal y Microsoft.ApplicationInsights.PersistenceChannel (v1.2.3).   

### <a name="core-sdk"></a>SDK principal

- Principales es administrado en github. Notas de la versión futura del SDK básico se encuentra [en github](http://github.com/Microsoft/ApplicationInsights-dotnet/releases)

## <a name="version-11"></a>Versión 1.1

### <a name="core-sdk"></a>SDK principal

- SDK ahora presenta el nuevo tipo de telemetría ```DependencyTelemetry``` que contiene información sobre la llamada de dependencia de aplicación
- Nuevo método ```TelemetryClient.TrackDependency``` permite enviar información sobre las llamadas de dependencia de aplicación

## <a name="version-100"></a>Versión 1.0.0

### <a name="windows-app-sdk"></a>Aplicación de Windows SDK

- Inicialización de nueva para las aplicaciones de Windows. Nuevo `WindowsAppInitializer` de clase con `InitializeAsync()` método permite arranque inicialización de colección de SDK. Este cambio permite un control más preciso y mejoras de rendimiento de inicialización de aplicación significativa sobre técnica de ApplicationInsights.config anterior.
- DeveloperMode automáticamente ya no está configurado. Para cambiar el comportamiento de DeveloperMode que debe especificar en código.
- Paquete NuGet ya no inserta ApplicationInsights.config. Se recomienda usar el nuevo WindowsAppInitializer cuando agregue manualmente el paquete NuGet.
- Sólo lee ApplicationInsights.config `<InstrumentationKey>`, todos los demás valores se pasan por alto en Preferencias de configuración de WindowsAppInitializer.
- Tienda mercado será automática recopilado por SDK.
- Gran cantidad de correcciones de errores, estabilidad y mejoras de rendimiento.

### <a name="core-sdk"></a>SDK principal

- Archivo de ApplicationInsights.config ya no está requiered. Y no se agregó el paquete NuGet. Configuración puede especificarse totalmente en código.
- Paquete NuGet ya no agregará un archivo de destinos para la solución. Esto quita la configuración automática de DeveloperMode durante una versión de depuración. DeveloperMode se debe establecer manualmente en el código.

## <a name="version-017"></a>Versión 0,17

### <a name="windows-app-sdk"></a>Aplicación de Windows SDK

- Aplicación de Windows SDK admite ahora creados con Windows 10 technical preview y con VS 2015 RC Universal aplicaciones de Windows.

### <a name="core-sdk"></a>SDK principal

- Valores predeterminados de TelemetryClient iniciar el InMemoryChannel.
- Nueva API agregado, `TelemetryClient.Flush()`. Este método de vaciado activará una carga de bloqueo de inmediata de telemetría todos conectado a ese cliente. Esto permite la activación manual de carga antes de cierre del proceso.
- Paquete NuGet agrega un destino de .net 4.5. Este destino no tiene dependencias externas (dependencias quiten BCL y origen de eventos).

## <a name="version-016"></a>Versión 0,16 

vista previa de 2015-04-28

- SDK ahora es compatible con la plataforma de destino DNX para habilitar la supervisión de aplicaciones [principales de .NET framework](http://www.dotnetfoundation.org/NETCore5) .
- Instancias de ```TelemetryClient``` no clave de caché instrumentación ya. Ahora si no se estableció la clave de instrumentación en ```TelemetryClient``` explícitamente ```InstrumentationKey``` devolverá el valor null. Corrige un problema al establecer ```TelemetryConfiguration.Active.InstrumentationKey``` después de que ya se ha recopilado algunos telemetría, módulos de telemetría como selector de dependencia, web solicitudes datos de rendimiento y recopilación de contadores selector usará la nueva clave de instrumentación.

## <a name="version-015"></a>Versión 0,15

- Nueva propiedad ```Operation.SyntheticSource``` ahora disponible en ```TelemetryContext```. Ahora puede marcar los elementos de telemetría como "no el tráfico de un usuario real" y especificar cómo se generó este tráfico. Como ejemplo estableciendo esta propiedad puede distinguir el tráfico de su automatización de prueba de tráfico de prueba de carga.
- Lógica de canal se ha movido a la NuGet independiente denominado Microsoft.ApplicationInsights.PersistenceChannel. Canal predeterminado ahora se llama InMemoryChannel
- Nuevo método ```TelemetryClient.Flush``` permite vaciar los elementos de telemetría del búfer sincrónicamente

## <a name="version-013"></a>Versión 0,13

Sin notas de la versión para las versiones anteriores disponibles. 
