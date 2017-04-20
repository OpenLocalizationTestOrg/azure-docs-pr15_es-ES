<properties 
    pageTitle="Solución de problemas sin datos - perspectivas de aplicación para .NET" 
    description="¿No hay datos en perspectivas de aplicación de Visual Studio? Pruebe aquí." 
    services="application-insights" 
    documentationCenter=".net"
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/24/2016" 
    ms.author="awills"/>
 
# <a name="troubleshooting-no-data---application-insights-for-net"></a>Solución de problemas sin datos - perspectivas de aplicación para .NET

## <a name="some-of-my-telemetry-is-missing"></a>Algunos de mi telemetría falta

*En información de la aplicación, solo ver una fracción de los eventos generados por la aplicación.*

* Si está viendo la misma fracción constantemente, probablemente es debido a adaptación [muestreo](app-insights-sampling.md). Para confirmarlo, abrir búsqueda (desde el módulo de introducción) y observe una instancia de una solicitud de u otro evento. En la parte inferior de la sección Propiedades haga clic en "..." para obtener detalles de la propiedad completa. Si solicitar Count > 1, a continuación, muestreo esté en funcionamiento. 
* En caso contrario, es posible que se llega a un [límite de velocidad de datos](app-insights-pricing.md#limits-summary) para su plan de precio. Estos límites se aplican por minuto.

## <a name="no-data-from-my-server"></a>Sin datos desde el servidor

*He instalado mi aplicación en el servidor web y ahora no veo ninguna telemetría de ella. Ha funcionado Aceptar en mi equipo de desarrollo.*

* Probablemente es un problema de firewall. [Establecer excepciones de firewall para impresiones de aplicación enviar los datos](app-insights-ip-addresses.md).

*He [instalado el Monitor de estado](app-insights-monitor-performance-live-website-now.md) en el servidor web para supervisar las aplicaciones existentes. No puedo ver los resultados.*

* Vea [solución de problemas de Monitor de estado](app-insights-monitor-performance-live-website-now.md#troubleshooting). 


## <a name="q01"></a>Opción sin 'Agregar perspectivas de aplicación' en Visual Studio

*Al crear un nuevo proyecto de Visual Studio, o cuando haga un proyecto existente en el Explorador de soluciones, no veo las opciones de aplicación perspectivas.*

+ No todos los tipos de proyecto .NET son compatibles con las herramientas. Proyectos de WCF y Web son compatibles. Para otros tipos de proyecto, como las aplicaciones de escritorio o un servicios, aún puede [Agregar de forma manual un SDK de perspectivas de aplicación a su proyecto](app-insights-windows-desktop.md).
+ Asegúrese de que tiene la [actualización de Visual Studio 2013 3 o posterior](http://go.microsoft.com/fwlink/?LinkId=397827). Viene preinstalado con herramientas de perspectivas de aplicación.
+ Seleccione **Herramientas**y **extensiones y actualizaciones** y compruebe que la **Aplicación perspectivas herramientas** está instalado y habilitado. Si es así, haga clic en **actualizaciones** para ver si hay una actualización disponible.
+ Abra el cuadro de diálogo nuevo proyecto y elija la aplicación Web de ASP.NET. Si ve la opción de aplicación perspectivas allí, se instalan las herramientas. Si no es así, pruebe a desinstalar y, a continuación, volver a instalar las herramientas de perspectivas de aplicación.


## <a name="q02"></a>Error al agregar la aplicación perspectivas

*Al crear un nuevo proyecto web, o al intentar agregar perspectivas de aplicación a un proyecto existente, aparece un mensaje de error.*

Causas posibles:

* Error de comunicación con el portal de información de la aplicación; o
* Hay algún problema con su cuenta de Azure
* Solo tiene [acceso de lectura a la suscripción o el grupo donde estaba intentando crear el nuevo recurso](app-insights-resources-roles-access-control.md).

Corregir:

+ Compruebe que ha proporcionado credenciales de inicio de sesión de la cuenta de Azure hacia la derecha. 
+ En el explorador, compruebe que tiene acceso al [portal de Azure](https://portal.azure.com). Abre configuración y vea si no hay ninguna restricción.
+ [Agregar perspectivas de aplicación a un proyecto existente](app-insights-asp-net.md): en el Explorador de soluciones, haga clic con el botón secundario del mouse en el proyecto y elija "Agregar perspectivas de aplicación".
+ Si aún no funciona, siga el [procedimiento manual](app-insights-windows-services.md) para agregar un recurso en el portal y, a continuación, agregue el SDK a su proyecto. 

## <a name="emptykey"></a>Recibo un error "clave de instrumentación no puede estar vacía"

Parece hubo un problema cuando instaló perspectivas de aplicación o tal vez un adaptador de registro.

En el Explorador de soluciones, haga clic con el botón `ApplicationInsights.config` y elija **Configurar perspectivas de aplicación**. Obtendrá un cuadro de diálogo que le invita a iniciar sesión en Azure y cree un recurso de información de la aplicación, o volver a utilizar uno existente.


##<a name="NuGetBuild"></a>"Paquetes de NuGet faltan" en mi servidor de generación

*Aceptar todo lo que crea cuando estoy depuración en mi equipo de desarrollo, pero recibo un mensaje de error de NuGet en el servidor de compilación.*

Consulte [Restaurar de paquete NuGet](http://docs.nuget.org/Consume/Package-Restore) y [Restaurar automática de paquete](http://docs.nuget.org/Consume/package-restore/migrating-to-automatic-package-restore).

## <a name="missing-menu-command-to-open-application-insights-from-visual-studio"></a>Falta el comando de menú para abrir la información de la aplicación de Visual Studio

*Cuando haga clic con botón de mi proyecto del explorador de soluciones, no se ven comandos perspectivas de aplicación o no veo un comando Abrir perspectivas de aplicación.*

Causas posibles:

* Si ha creado el recurso de aplicación perspectivas manualmente, o si el proyecto es de un tipo que no es compatible con las herramientas de la información de la aplicación.
* Las herramientas de la aplicación perspectivas están deshabilitadas en su Visual Studio.
* Su Visual Studio es anterior a la actualización de 2013 3.

Corregir:

* Asegúrese de que su versión de Visual Studio es 2013 actualización 3 o posterior.
* Seleccione **Herramientas**y **extensiones y actualizaciones** y compruebe que la **Aplicación perspectivas herramientas** está instalado y habilitado. Si es así, haga clic en **actualizaciones** para ver si hay una actualización disponible.
* Haga clic en el proyecto en el Explorador de soluciones. Si ve el comando **Configurar perspectivas de aplicación**, utilice para conectarse a su proyecto al recurso en el servicio de información de la aplicación.


En caso contrario, el tipo de proyecto directamente no es compatible con las herramientas de perspectivas de aplicación. Para ver la telemetría, inicie sesión en el [portal de Azure](https://portal.azure.com), elija información de la aplicación en la barra de navegación izquierda y seleccione la aplicación.

## <a name="access-denied-on-opening-application-insights-from-visual-studio"></a>'Acceso denegado' al abrir aplicación perspectivas de Visual Studio

*El comando de menú 'Abrir aplicación perspectivas', abre el portal de Azure, pero recibo un error "acceso denegado".*

![](./media/app-insights-asp-net-troubleshoot-no-data/access-denied.png)

El inicio de sesión de Microsoft que utilizó por última vez en el explorador predeterminado no tiene acceso al [recurso que se creó cuando se agregó perspectivas de aplicación para esta aplicación](app-insights-asp-net.md). Hay dos motivos más probables: 

* ¿Tiene más de una cuenta de Microsoft - quizás un trabajo y una cuenta personal de Microsoft? En el inicio de sesión que utilizada por última vez en el explorador predeterminado era para una cuenta diferente a la que tiene acceso a [agregar perspectivas de aplicación al proyecto](app-insights-asp-net.md). 

 * Corregir: Haga clic en su nombre en esquina superior derecha de la ventana del explorador y cerrar sesión. Inicie sesión con la cuenta que tiene acceso. A continuación, en la barra de navegación izquierdo, haga clic en información de la aplicación y seleccione la aplicación.

* Otra persona agregó perspectivas de aplicación al proyecto y se ha olvidado de conceder [acceso al grupo de recursos](app-insights-resources-roles-access-control.md) en el que se creó. 

 * Corregir: Si utiliza una cuenta de la organización, pueden agregar se al equipo; o bien puede conceder acceso individuales al grupo de recursos.



## <a name="asset-not-found-on-opening-application-insights-from-visual-studio"></a>'Activo no se encontró' al abrir aplicación perspectivas de Visual Studio

*El comando de menú 'Abrir aplicación perspectivas', abre el portal de Azure, pero recibo un error 'no se encontró el activo'.*

Causas posibles:

* Se ha eliminado el recurso de información de la aplicación de la aplicación; o
* La clave de instrumentación se ha establecido o modificado en ApplicationInsights.config editando directamente, sin actualizar el archivo de proyecto. 

La clave de instrumentación en controles de ApplicationInsights.config donde se envía la telemetría. Una línea en el archivo de proyecto controla el recurso que se abre al utilizar el comando de Visual Studio. 

Corregir:

* En el Explorador de soluciones, haga clic en el proyecto y elija aplicación perspectivas, configurar perspectivas de aplicación. En el cuadro de diálogo, puede elegir enviar telemetría a un recurso existente o cree uno nuevo. O:
* Abra el recurso directamente. Inicie sesión en [el portal de Azure](https://portal.azure.com), haga clic en información de la aplicación en la barra de navegación izquierdo y, a continuación, seleccione la aplicación.



## <a name="where-do-i-find-my-telemetry"></a>¿Dónde puedo encontrar mi telemetría?

*Iniciado sesión en el [portal de Microsoft Azure](https://portal.azure.com)y buscar en el panel de inicio de Azure. Así que ¿Dónde encuentro mis datos de la información de la aplicación?*

* En la barra de navegación izquierdo, haga clic en información de la aplicación y, a continuación, el nombre de la aplicación. Si no tiene ningún proyecto allí, debe [Agregar o configurar perspectivas de aplicación en el proyecto web](app-insights-asp-net.md).

    Aquí verá algunos gráficos de resumen. Puede hacer clic en ellos para ver más detalles.

* En Visual Studio, mientras está depurar la aplicación, haga clic en el botón de información de la aplicación.


## <a name="q03"></a>Sin datos del servidor (o sin datos)

*Ha encontrado mi aplicación y abre el servicio de aplicación perspectivas en Microsoft Azure, pero mostrar de todos los gráficos 'Aprenda a recopilar...' o 'No está configurado'.* O bien, *sólo datos de usuario y la vista de página, pero sin datos de servidor.*

+ Ejecute la aplicación en modo de depuración en Visual Studio (F5). Use la aplicación con el fin de generar algunos telemetría. Compruebe que puede ver los eventos registrados en la ventana de resultados de Visual Studio. 

    ![](./media/app-insights-asp-net-troubleshoot-no-data/output-window.png)

+ En el portal de información de la aplicación, abrir [Búsqueda de diagnóstico](app-insights-diagnostic-search.md). Datos normalmente aparecen aquí primero.
+ Haga clic en el botón Actualizar. El módulo propio actualiza periódicamente, pero también puede hacerlo manualmente. El intervalo de actualización es más largo de intervalos de tiempo mayores.
+ Compruebe que las claves de instrumentación coinciden. En el módulo principal de la aplicación en el portal de información de la aplicación, en la lista desplegable **Essentials** , mire **clave instrumentación**. A continuación, en el proyecto de Visual Studio, abra ApplicationInsights.config y busque el `<instrumentationkey>`. Compruebe que las dos claves son iguales. Si no es así:
 + En el portal, haga clic en información de la aplicación y busque el recurso de la aplicación con la tecla derecha; o
 + En el Explorador de soluciones de Visual Studio, haga clic en el proyecto y elija aplicación perspectivas, configurar. Restablecer la aplicación para enviar telemetría para el recurso de la derecha.
 + Si no puede encontrar las claves coincidentes, compruebe que está usando las mismas credenciales de inicio de sesión en Visual Studio como en el portal.

    ![](./media/app-insights-asp-net-troubleshoot-no-data/ikey-check.png)
    
+ En el [panel de inicio de Microsoft Azure](https://portal.azure.com), mire el mapa de estado del servicio. Si hay algunas indicaciones alerta, espere hasta que ha vuelto a Aceptar y cierre y vuelva a abre el módulo de aplicación de la información de la aplicación.
+ Compruebe también [nuestro blog de estado](http://blogs.msdn.com/b/applicationinsights-status/).
+ ¿Escribir código para el [SDK de servidor](app-insights-api-custom-events-metrics.md) que pueden cambiar la clave de instrumentación en `TelemetryClient` instancias o en `TelemetryContext`? ¿Escribir una [configuración de filtro o muestreos](app-insights-api-filtering-sampling.md) que podrían ser filtrado fuera demasiado?
+ Si edita ApplicationInsights.config, cuidadosamente Compruebe la configuración de [TelemetryInitializers y TelemetryProcessors](app-insights-api-filtering-sampling.md). Un parámetro o tipo denominado incorrectamente puede provocar el SDK no enviar datos.

## <a name="q04"></a>Sin datos en las vistas de la página, los exploradores, uso

*Puedo ver datos en gráficos de solicitudes de servidor y tiempo de respuesta del servidor, pero no los datos en tiempo de carga de la vista de página o en el explorador o uso módulos.*

Los datos proceden de secuencias de comandos en las páginas web. 

+ Si ha agregado perspectivas de aplicación a un proyecto web existente, [que tiene que agregar las secuencias de comandos a mano](app-insights-javascript.md).
+ Asegúrese de que Internet Explorer no muestra el sitio en modo de compatibilidad.
+ Usar la característica de depuración del explorador (F12 en algunos exploradores, a continuación, elija red) para comprobar que se está enviando datos a `dc.services.visualstudio.com`.

## <a name="no-dependency-or-exception-data"></a>No hay datos de dependencia o excepción

Consulte [telemetría de dependencia](app-insights-asp-net-dependencies.md) y de [telemetría de excepción](app-insights-asp-net-exceptions.md).

## <a name="no-performance-data"></a>No hay datos de rendimiento

Datos de rendimiento (CPU, tasa IO y así sucesivamente) está disponible para [los servicios web Java](app-insights-java-collectd.md), [aplicaciones de escritorio de Windows](app-insights-windows-desktop.md), [aplicaciones y si instala el monitor de estado de servicios web de IIS](app-insights-monitor-performance-live-website-now.md)y [Servicios de nube de Azure](app-insights-azure.md). la encontrará en configuración, servidores.

No está disponible para los sitios Web de Azure.

## <a name="no-server-data-since-i-published-the-app-to-my-server"></a>Sin datos (servidor), puesto que se ha publicado la aplicación con el servidor

+ Compruebe que ha copiado el de Microsoft. DLL de ApplicationInsights en el servidor, junto con Microsoft.Diagnostics.Instrumentation.Extensions.Intercept.dll
+ En el firewall, es posible que tenga que [abrir algunos puertos TCP](app-insights-ip-addresses.md#data-access-api).
+ Si tiene que utilizar a un servidor proxy para enviar fuera de su red corporativa, establezca [defaultProxy](https://msdn.microsoft.com/library/aa903360.aspx) en Web.config
+ Windows Server 2008: Asegúrese de que ha instalado las siguientes actualizaciones: [KB2468871](https://support.microsoft.com/kb/2468871), [KB2533523](https://support.microsoft.com/kb/2533523), [KB2600217](https://support.microsoft.com/kb/2600217).


## <a name="i-used-to-see-data-but-it-has-stopped"></a>Usa para ver datos, pero se ha detenido

* Compruebe el [blog de estado](http://blogs.msdn.com/b/applicationinsights-status/).
* ¿Haber alcanzado la cuota mensual de puntos de datos? Abra la configuración o cuota y precios para averiguar. Si es así, puede actualizar su plan o pagar capacidad adicional. Vea la [combinación de precios](https://azure.microsoft.com/pricing/details/application-insights/).


## <a name="i-dont-see-all-the-data-im-expecting"></a>No puedo ver todos los datos que estoy esperando

Si la aplicación envía una gran cantidad de datos y que está usando el SDK de perspectivas de aplicación para ASP.NET versión 2.0.0-beta3 o posterior, la característica de [muestreo adaptación](app-insights-sampling.md) puede operar y enviar sólo un porcentaje de su telemetría. 

Se puede deshabilitar, pero no se recomienda. Muestreo está diseñado para que telemetría relacionado se transmite correctamente con fines de diagnóstico. 

## <a name="wrong-geographical-data-in-user-telemetry"></a>Datos geográficos incorrectos de telemetría de usuario

La ciudad, región y las dimensiones de país derivan de direcciones IP y no siempre están precisas.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>Excepción "método no encontrado" en que se ejecuta en servicios de nube de Azure

¿Se genera para .NET 4.6? 4.6 no es compatible automáticamente en las funciones de servicios de nube de Azure. [Instalar 4.6 en cada rol](../cloud-services/cloud-services-dotnet-install-dotnet.md) antes de ejecutar la aplicación.

## <a name="still-not-working"></a>Sigue sin funcionar...

* [Foro de perspectivas de aplicación](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)

