<properties
    pageTitle="Disminuir el rendimiento de aplicación web en la aplicación de servicio | Microsoft Azure"
    description="Este artículo le ayudará a solucionar problemas de rendimiento de aplicación web lenta en la aplicación de servicio de Azure."
    services="app-service\web"
    documentationCenter=""
    authors="cephalin"
    manager="wpickett"
    editor=""
    tags="top-support-issue"
    keywords="rendimiento de aplicación en la Web app lenta, aplicación lenta"/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/06/2016"
    ms.author="cephalin"/>

# <a name="troubleshoot-slow-web-app-performance-issues-in-azure-app-service"></a>Solucionar problemas de rendimiento de aplicación web lenta en la aplicación de servicio de Azure

Este artículo le ayudará a solucionar problemas de rendimiento de aplicación web lenta en la [Aplicación de servicio de Azure](http://go.microsoft.com/fwlink/?LinkId=529714).

Si necesita más ayuda en cualquier punto de este artículo, póngase en contacto con los expertos de Azure en [Azure de MSDN y los foros de desbordamiento de pila](https://azure.microsoft.com/support/forums/). Como alternativa, también puede abrir un incidente de soporte de Azure. Vaya al [sitio de soporte técnico de Azure](https://azure.microsoft.com/support/options/) y haga clic en **Obtener soporte**.

## <a name="symptom"></a>Síntoma

Al explorar la aplicación web, la carga de páginas lentamente y a veces tiempo de espera.

## <a name="cause"></a>Causa

A menudo, este problema se debe a problemas de nivel de aplicación, como:

-   solicitudes tarda mucho tiempo
-   aplicación uso alto de CPU/memoria
-   aplicación se bloquea debido a una excepción.

## <a name="troubleshooting-steps"></a>Pasos de solución de problemas

Solución de problemas se puede dividir en tres tareas diferentes, en orden secuencial:

1.  [Observar y supervisar el comportamiento de la aplicación](#observe)
2.  [Recopilar datos](#collect)
3.  [Mitigar el problema](#mitigate)

[Aplicación de servicio Web Apps](/services/app-service/web/) le ofrece diversas opciones en cada paso.

<a name="observe" />
### <a name="1-observe-and-monitor-application-behavior"></a>1. observar y supervisar el comportamiento de la aplicación

#### <a name="track-service-health"></a>Realizar un seguimiento de estado del servicio

Microsoft Azure publicizes cada vez que hay una degradación del rendimiento o interrupción de servicio. Puede realizar un seguimiento del estado del servicio en el [Portal de Azure](https://portal.azure.com/). Para obtener más información, vea [el estado del servicio de control](../monitoring-and-diagnostics/insights-service-health.md).

#### <a name="monitor-your-web-app"></a>Supervisar la aplicación web

Esta opción permite averiguar si la aplicación está teniendo problemas. En el módulo de la aplicación web, haga clic en el mosaico de **errores y solicitudes** . El módulo de **métrica** mostrará todas las medidas que puede agregar.

Algunas de las mediciones que puede desear supervisar para la aplicación web

-   Memoria promedio espacio de trabajo
-   Tiempo medio de respuesta
-   Tiempo de la CPU
-   Trabajo de la memoria
-   Solicitudes

![supervisar el rendimiento de aplicación web](./media/app-service-web-troubleshoot-performance-degradation/1-monitor-metrics.png)

Para obtener más información, consulte:

-   [Supervisar aplicaciones de Web en el servicio de aplicación de Azure](web-sites-monitor.md)
-   [Recibir notificaciones de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

#### <a name="monitor-web-endpoint-status"></a>Supervisar el estado del extremo de web

Si está ejecutando la aplicación web en el **estándar** precios nivel, aplicaciones Web le permite supervisar 2 extremos de 3 ubicaciones geográficas.

Extremo supervisión configura pruebas web desde ubicaciones distribuidas geo que probar el tiempo de respuesta y el tiempo de actividad de direcciones URL de web. La prueba realiza una operación de HTTP GET en la dirección URL de web para determinar el tiempo de respuesta y la actividad de cada ubicación. Cada ubicación configurada ejecuta una prueba cada cinco minutos.

Tiempo de actividad se supervisa mediante códigos de respuesta HTTP y tiempo de respuesta se mide en milisegundos. Una prueba de supervisión se produce un error si el código de respuesta HTTP es mayor o igual a 400 o si la respuesta tarda más de 30 segundos. Se considera que un punto final está disponible si se realiza correctamente las pruebas de supervisión de todas las ubicaciones especificadas.

Para configurarlo, vea [Cómo: controlar el estado del extremo de web](web-sites-monitor.md#webendpointstatus).

Consulte también [supervisión extremo - con Stefan Schackow además de mantener los sitios Web de Azure hacia arriba](/documentation/videos/azure-web-sites-endpoint-monitoring-and-staying-up/) para ver un vídeo sobre la supervisión de extremo.

#### <a name="application-performance-monitoring-using-extensions"></a>Con las extensiones de supervisión de rendimiento de aplicación

También puede supervisar el rendimiento de la aplicación al aprovechar _las extensiones de sitio_.

Cada aplicación de servicio de aplicación web proporciona un punto final de administración extensible que le permite aprovechar un conjunto de eficaces herramientas implementado como extensiones de sitio. Estas herramientas abarcan desde editores de código fuente como [Visual Studio Team Services](https://www.visualstudio.com/products/what-is-visual-studio-online-vs.aspx) a las herramientas de administración de recursos conectadas como una base de datos MySQL conectado a una aplicación web.

[Información de la aplicación de Azure](/services/application-insights/) y [Relic nuevo](/marketplace/partners/newrelic/newrelic/) son dos extensiones de sitio disponibles de supervisión de rendimiento. Para usar el nuevo Relic, instale a un agente en tiempo de ejecución. Para usar la información de la aplicación de Azure, volver a generar el código con un SDK y también puede instalar una extensión que proporciona acceso a datos adicionales. El SDK le permite escribir código para supervisar el uso y el rendimiento de la aplicación con más detalle.

Para usar la información de la aplicación, consulte [supervisar el rendimiento en aplicaciones web](../application-insights/app-insights-web-monitor-performance.md).

Para usar el nuevo Relic, consulte [Nueva administración de rendimiento de aplicaciones de Relic en Azure](../store-new-relic-cloud-services-dotnet-application-performance-management.md).

<a name="collect" />
### <a name="2-collect-data"></a>2. recopilar datos

####    <a name="enable-diagnostics-logging-for-your-web-app"></a>Habilitar el registro de diagnóstico para la aplicación web

El entorno de aplicaciones Web proporciona la funcionalidad de diagnóstico para registrar información desde el servidor web y la aplicación web. Se divide lógicamente en diagnósticos de servidor web y diagnósticos de aplicación.

##### <a name="web-server-diagnostics"></a>Diagnósticos de servidor Web

Puede habilitar o deshabilitar a los siguientes tipos de registros:

-   **Registro de errores detallado** - información de error detallada de los códigos de estado HTTP que indique un error (código de estado 400 o mayor). Esto puede contener información que puede ayudar a determinar por qué el servidor devolvió el código de error.
-   **Seguimiento de solicitud de error** - información detallada sobre solicitudes de error, como un seguimiento de los componentes IIS utilizado para procesar la solicitud y el tiempo necesario en cada componente. Esto puede ser útil si está intentando mejorar el rendimiento de web app o aislar la causa de un error específico de HTTP.
-   **Registro de servidor web** : información sobre las transacciones de HTTP con el formato de archivo de registro extendido W3C. Esto es útil cuando determinación de los indicadores de aplicación web generales, como el número de solicitudes de administran o cuántas peticiones son de una dirección IP específica.

##### <a name="application-diagnostics"></a>Diagnósticos de aplicación

Diagnósticos de la aplicación le permiten capturar información creado mediante una aplicación web. Aplicaciones ASP.NET pueden utilizar la `System.Diagnostics.Trace` clase para registrar información en el registro de diagnósticos de aplicación.

Para obtener instrucciones detalladas sobre cómo configurar la aplicación para iniciar sesión, vea [Habilitar registro de diagnósticos para aplicaciones web de servicio de aplicaciones de Azure](web-sites-enable-diagnostic-log.md).

#### <a name="use-remote-profiling"></a>Usar la generación remota de perfiles

En el servicio de aplicación de Azure, aplicaciones Web, aplicaciones de API y WebJobs puede remotamente generar perfiles. Si el proceso se ejecuta más despacio de lo esperado, o la latencia de las solicitudes HTTP es mayor de lo normal y el uso de CPU del proceso también es alto, puede remotamente el proceso de perfil y obtener las pilas de llamadas de muestreo de la CPU para analizar la actividad del proceso y rutas de acceso rápido de código.

Para obtener más información, vea [soporte técnico generación remota de perfiles de servicio de aplicaciones de Azure](/blog/remote-profiling-support-in-azure-app-service).


#### <a name="use-the-azure-app-service-support-portal"></a>Usar el Portal de soporte técnico de Azure de aplicación de servicio

Web Apps le ofrece la capacidad para solucionar problemas relacionados con la aplicación web de consultando registros, registros de eventos, proceso volcados HTTP y mucho más. Puede acceder a toda esta información con nuestro portal de soporte técnico en **http://&lt;el nombre de la aplicación >.scm.azurewebsites.net/Support**

El portal de servicio compatible de aplicación de Azure ofrece tres pestañas independientes para admitir los tres pasos de un escenario de solución de problemas comunes:

1.  Observar el comportamiento actual
2.  Analizar recopilar información de diagnóstico y ejecutando los analizadores integrados
3.  Mitigar

Si el problema está sucediendo ahora, haga clic en **analizar** > **Diagnósticos** > **Diagnosticar ahora** para crear una sesión de diagnóstico, que recopilará HTTP registros, registros del Visor de sucesos, memoria volcados, registros de errores PHP y PHP procesan el informe.

Una vez que se recopilan los datos, también ejecutar un análisis de los datos y proporcionarle un informe HTML.

En caso de que desea descargar los datos de forma predeterminada, ¿se almacena en la carpeta D:\home\data\DaaS.

Para obtener más información sobre el portal de soporte técnico de servicio de aplicación de Azure, consulte [Nuevas actualizaciones a la extensión del sitio de soporte técnico para sitios Web de Azure](/blog/new-updates-to-support-site-extension-for-azure-websites).

#### <a name="use-the-kudu-debug-console"></a>Usar la consola de depuración Kudu

Aplicaciones de Web incluye una consola de depuración que puede usar para depurar, explorar y cargar archivos, así como los extremos JSON para obtener información acerca de su entorno. Esto se denomina la _Consola Kudu_ o el _Panel de SCM_ para la aplicación web.

Puede acceder a este panel, vaya al vínculo **https://&lt;el nombre de la aplicación >.scm.azurewebsites.net/**.

Algunas de las ventajas que ofrece Kudu son:

-   configuración del entorno de la aplicación
-   secuencia de registro
-   captura de diagnóstico
-   depurar consola en la que puede ejecutar los cmdlets de Powershell y los comandos básicos de DOS.


Otra característica útil de Kudu es que, en caso de que la aplicación es iniciar excepciones de primera oportunidad, puede usar Kudu y la herramienta SysInternals Procdump crear memoria vuelca. Estos volcados de memoria son instantáneas del proceso y pueden a menudo ayudar a solucionar los problemas más complicados con la aplicación web.

Para obtener más información sobre las características disponibles en Kudu, vea [Herramientas de Azure sitios Web Team Services que debe conocer](/blog/windows-azure-websites-online-tools-you-should-know-about/).

<a name="mitigate" />
### <a name="3-mitigate-the-issue"></a>3. mitigar el problema

####    <a name="scale-the-web-app"></a>Ajustar el tamaño de la aplicación web

En el servicio de aplicación de Azure, para mejorar el rendimiento y el rendimiento, puede ajustar la escala en el que se está ejecutando la aplicación. Escalado de una aplicación web implica dos acciones relacionadas: cambiar su plan de servicios de aplicación a un nivel superior de precios y configurar algunas configuraciones después de que haya cambiado al nivel superior de precio.

Para obtener más información sobre la escala, consulte [escala una aplicación web de servicio de aplicaciones de Azure](web-sites-scale.md).

Además, puede ejecutar la aplicación en más de una instancia. Esto le ofrece más capacidad de procesamiento y también proporciona cierta tolerancia a errores. Si el proceso se interrumpe en una instancia, la otra instancia seguirá servir solicitudes.

Puede establecer la escala de ser Manual o automática.

####    <a name="use-autoheal"></a>Usar AutoHeal

AutoHeal recicla el proceso de trabajo de la aplicación en función de la configuración que elija (por ejemplo, los cambios de configuración, las solicitudes, límites de memoria o el tiempo necesario para ejecutar una solicitud). La mayoría de los casos, el proceso de reciclaje es la manera más rápida de recuperar de un problema. Aunque siempre puede reiniciar la aplicación web de directamente en el Portal de Azure, AutoHeal hará automáticamente para usted. Todo lo que debe hacer es agregar algunos desencadenadores en web.config raíz para la aplicación web. Tenga en cuenta que esta configuración funcionarán de la misma manera incluso si su aplicación no es un .net uno.

Para obtener más información, vea [sitios Web de Azure resolución de problemas](/blog/auto-healing-windows-azure-web-sites/).

####    <a name="restart-the-web-app"></a>Reinicie la aplicación web

Suele ser la manera más sencilla de recuperar de problemas de una sola vez. En el [Portal de Azure](https://portal.azure.com/), en el módulo de la aplicación web, tiene las opciones para detener o reiniciar la aplicación.

 ![Reinicie la aplicación web para solucionar problemas de rendimiento](./media/app-service-web-troubleshoot-performance-degradation/2-restart.png)

También puede administrar la aplicación web de Powershell de Azure. Para obtener más información, vea [Usar PowerShell de Azure con el Administrador de recursos de Azure](../powershell-azure-resource-manager.md).
