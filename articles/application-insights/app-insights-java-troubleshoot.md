<properties 
    pageTitle="Solucionar problemas de aplicación perspectivas en un proyecto de web Java" 
    description="Guía de solución de problemas: supervisión de aplicaciones de Java directo con recomendaciones de aplicación." 
    services="application-insights" 
    documentationCenter="java"
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="03/01/2016" 
    ms.author="awills"/>
 
# <a name="troubleshooting-and-q-and-a-for-application-insights-for-java"></a>Solución de problemas y preguntas y respuestas de perspectivas de aplicación de Java

¿Preguntas o problemas con [Los conocimientos de Visual Studio aplicación en Java][java]? Aquí encontrará algunas sugerencias.


## <a name="build-errors"></a>Errores de compilación

*En Eclipse, al agregar el SDK de perspectivas de aplicación a través de Maven o Gradle, recibo generar o suma de comprobación de errores de validación.*

* Si la dependencia <version> elemento es usar una trama con caracteres comodín (por ejemplo, (Maven) `<version>[1.0,)</version>` o (Gradle) `version:'1.0.+'`), pruebe a especificar una versión específica en su lugar como `1.0.2`. Vea las [notas de la versión](https://github.com/Microsoft/ApplicationInsights-Java#release-notes) de la versión más reciente.

## <a name="no-data"></a>Sin datos 

*He agregado aplicación perspectivas correctamente y ejecutó mi aplicación, pero nunca he visto datos en el portal.*

* Espere un minuto y haga clic en actualizar. Los gráficos propios actualización periódicamente, pero también puede actualizar manualmente. El intervalo de actualización depende del intervalo de tiempo del gráfico.
* Compruebe que tiene una clave de instrumentación definida en el archivo ApplicationInsights.xml (en la carpeta de recursos del proyecto)
* Compruebe que no hay ningún `<DisableTelemetry>true</DisableTelemetry>` nodo en el archivo xml.
* En el firewall, es posible que tenga que abra los puertos TCP 80 y 443 para el tráfico saliente para dc.services.visualstudio.com. Ver la [lista completa de excepciones de firewall](app-insights-ip-addresses.md)
* En Microsoft Azure iniciar panel, busque en el mapa de estado del servicio. Si hay algunas indicaciones alerta, espere hasta que ha vuelto a Aceptar y cierre y vuelva a abre el módulo de aplicación de la información de la aplicación.
* Activar el registro en la ventana de consola IDE agregando una `<SDKLogger />` elemento bajo el nodo raíz en el archivo ApplicationInsights.xml (en la carpeta de recursos en el proyecto) y comprobar si las entradas precedido [Error].
* Asegúrese de que el archivo ApplicationInsights.xml correcto se ha cargado correctamente el SDK de Java consultando mensajes de salida de la consola de una instrucción "archivo de configuración ha sido correctamente encontrado".
* Si no se encuentra el archivo de configuración, compruebe los mensajes de salida para ver dónde se busca el archivo de configuración de y asegúrese de que la ApplicationInsights.xml se encuentra en una de las ubicaciones de búsqueda. Como regla general, puede colocar el archivo de configuración cerca el SDK de aplicación perspectivas JAR. Por ejemplo: en Tomcat, esto significa la carpeta WEB-INF/biblioteca.



#### <a name="i-used-to-see-data-but-it-has-stopped"></a>Usa para ver datos, pero se ha detenido

* Compruebe el [blog de estado](http://blogs.msdn.com/b/applicationinsights-status/).
* ¿Haber alcanzado la cuota mensual de puntos de datos? Abrir configuración o cuota y precios para averiguar. Si es así, puede actualizar su plan o pagar capacidad adicional. Vea la [combinación de precios](https://azure.microsoft.com/pricing/details/application-insights/).

#### <a name="i-dont-see-all-the-data-im-expecting"></a>No puedo ver todos los datos que estoy esperando

* Abra las cuotas y precios placa y comprobar si [muestreo](app-insights-sampling.md) esté en funcionamiento. (100% de transmisión significa que muestreo no está en operación). El servicio de información de la aplicación se puede configurar para aceptar sólo una parte de la telemetría que llega desde la aplicación. Esto le ayuda a mantener dentro de la cuota mensual de telemetría. 

## <a name="no-usage-data"></a>No hay datos de uso

*Puedo ver datos sobre solicitudes y tiempos de respuesta, pero no la vista de página, explorador o datos de usuario.*

Correctamente configurar su aplicación para enviar telemetría desde el servidor. Ahora el siguiente paso es configurar [sus páginas web para enviar telemetría desde el explorador web][usage].

Como alternativa, si su cliente es una aplicación en un [teléfono u otro dispositivo][platforms], puede enviar telemetría desde allí. 

Usar la misma clave instrumentación para configurar su cliente y el servidor de telemetría. Los datos aparecerán en el mismo recurso de información de la aplicación y, a continuación, podrá correlación de eventos de cliente y el servidor.



## <a name="disabling-telemetry"></a>Deshabilitación de telemetría

*¿Cómo puedo deshabilitar la colección de telemetría?*

En el código:

    TelemetryConfiguration config = TelemetryConfiguration.getActive();
    config.setTrackingIsDisabled(true);


**O** 

Actualizar ApplicationInsights.xml (en la carpeta de recursos de su proyecto). Agregue lo siguiente bajo el nodo raíz:

    <DisableTelemetry>true</DisableTelemetry>

Con el método XML, debe reiniciar la aplicación cuando cambie el valor.

## <a name="changing-the-target"></a>Cambiar el destino

*¿Cómo se puede cambiar qué recursos Azure mi proyecto envía los datos?*

* [Obtener la clave de instrumentación del nuevo recurso.][java]
* Si agrega perspectivas de aplicación para el proyecto mediante el Kit de herramientas de Azure para Eclipse, derecho en su proyecto web, seleccione **Azure**, **Configurar perspectivas de aplicación**y cambiar la clave.
* En caso contrario, actualice la clave de ApplicationInsights.xml en la carpeta de recursos del proyecto.


## <a name="the-azure-start-screen"></a>La pantalla de inicio de Azure

*Debo buscar en [el portal de Azure](https://portal.azure.com). ¿El mapa me muestra algo sobre mi aplicación?*

No, se muestra el estado de Azure servidores todo el mundo.

*Desde el panel de inicio de Azure (pantalla principal), ¿cómo puedo encontrar datos sobre mi aplicación?*

Suponiendo que [Configurar la aplicación de la aplicación perspectivas][java], haga clic en Examinar, seleccione la información de la aplicación y seleccione el recurso de aplicación de la aplicación que ha creado. Para obtener hay más rápido en el futuro, puede anclar la aplicación a los paneles de inicio.

## <a name="intranet-servers"></a>Servidores de la intranet

*¿Supervisar un servidor en la intranet?*

Sí, siempre y cuando que el servidor puede enviar telemetría en el portal de información de la aplicación a través de internet público. 

En el firewall, es posible que tenga que abra los puertos TCP 80 y 443 para el tráfico saliente para dc.services.visualstudio.com y f5.services.visualstudio.com.

## <a name="data-retention"></a>Retención de datos 

*¿Cuánto tiempo se conservan los datos en el portal? ¿Es segura?*

Consulte la [declaración de privacidad y retención de datos][data].

## <a name="next-steps"></a>Pasos siguientes

*Aplicación perspectivas puedo configurar mi aplicación de servidor de Java. ¿Qué más puedo hacer?*

* [Supervisar la disponibilidad de las páginas web][availability]
* [Supervisar el uso de la página web][usage]
* [Realizar un seguimiento de uso y diagnóstico de problemas en las aplicaciones de dispositivo][platforms]
* [Escribir el código para realizar un seguimiento de uso de la aplicación][track]
* [Capturar registros de diagnóstico][javalogs]


## <a name="get-help"></a>Obtener ayuda

* [Desbordamiento de pila](http://stackoverflow.com/questions/tagged/ms-application-insights)

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[data]: app-insights-data-retention-privacy.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[platforms]: app-insights-platforms.md
[track]: app-insights-api-custom-events-metrics.md
[usage]: app-insights-web-track-usage.md

 