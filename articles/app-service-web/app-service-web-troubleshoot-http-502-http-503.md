<properties
    pageTitle="Solucionar 502 puerta de enlace incorrecta, 503 Servicio no disponibles errores | Microsoft Azure"
    description="Solucionar problemas 502 puerta de enlace incorrecta y 503 Servicio no disponible errores en la aplicación web alojado en el servicio de aplicación de Azure."
    services="app-service\web"
    documentationCenter=""
    authors="cephalin"
    manager="wpickett"
    editor=""
    tags="top-support-issue"
    keywords="502 puerta de enlace incorrecta, 503 Servicio no disponible, error 503, error 502"/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/06/2016"
    ms.author="cephalin"/>

# <a name="troubleshoot-http-errors-of-502-bad-gateway-and-503-service-unavailable-in-your-azure-web-apps"></a>Solucionar errores HTTP de "502 puerta de enlace incorrecta" y "503 Servicio no disponible" en las aplicaciones web de Azure

"502 puerta de enlace incorrecta" y "503 Servicio no disponible" son los errores comunes en la aplicación web alojado en el [Servicio de aplicación de Azure](http://go.microsoft.com/fwlink/?LinkId=529714). Este artículo le ayudará a solucionar estos errores.

Si necesita más ayuda en cualquier punto de este artículo, póngase en contacto con los expertos de Azure en [Azure de MSDN y los foros de desbordamiento de pila](https://azure.microsoft.com/support/forums/). Como alternativa, también puede abrir un incidente de soporte de Azure. Vaya al [sitio de soporte técnico de Azure](https://azure.microsoft.com/support/options/) y haga clic en **Obtener soporte**.

## <a name="symptom"></a>Síntoma

Cuando examine la aplicación web, devuelve una HTTP error "502 puerta de enlace incorrecta" o una HTTP error "503 Servicio no disponible".

## <a name="cause"></a>Causa

A menudo, este problema se debe a problemas de nivel de aplicación, como:

-   solicitudes tarda mucho tiempo
-   aplicación uso alto de CPU/memoria
-   aplicación se bloquea debido a una excepción.

## <a name="troubleshooting-steps-to-solve-502-bad-gateway-and-503-service-unavailable-errors"></a>Pasos de solución de problemas para solucionar errores de "503 Servicio no disponible" y "502 puerta de enlace incorrecta"

Solución de problemas se puede dividir en tres tareas diferentes, en orden secuencial:

1.  [Observar y supervisar el comportamiento de la aplicación](#observe)
2.  [Recopilar datos](#collect)
3.  [Mitigar el problema](#mitigate)

[Aplicación de servicio Web Apps](/services/app-service/web/) le ofrece diversas opciones en cada paso.

<a name="observe" />
### <a name="1-observe-and-monitor-application-behavior"></a>1. observar y supervisar el comportamiento de la aplicación

####    <a name="track-service-health"></a>Realizar un seguimiento de estado del servicio

Microsoft Azure publicizes cada vez que hay una degradación del rendimiento o interrupción de servicio. Puede realizar un seguimiento del estado del servicio en el [Portal de Azure](https://portal.azure.com/). Para obtener más información, vea [el estado del servicio de control](../monitoring-and-diagnostics/insights-service-health.md).

####    <a name="monitor-your-web-app"></a>Supervisar la aplicación web

Esta opción permite averiguar si la aplicación está teniendo problemas. En el módulo de la aplicación web, haga clic en el mosaico de **errores y solicitudes** . El módulo de **métrica** mostrará todas las medidas que puede agregar.

Algunas de las mediciones que puede desear supervisar para la aplicación web

-   Memoria promedio espacio de trabajo
-   Tiempo medio de respuesta
-   Tiempo de la CPU
-   Trabajo de la memoria
-   Solicitudes

![supervisar la aplicación web resolver errores HTTP 502 puerta de enlace incorrecta y 503 Servicio no disponible](./media/app-service-web-troubleshoot-HTTP-502-503/1-monitor-metrics.png)

Para obtener más información, consulte:

-   [Supervisar aplicaciones de Web en el servicio de aplicación de Azure](web-sites-monitor.md)
-   [Recibir notificaciones de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

<a name="collect" />
### <a name="2-collect-data"></a>2. recopilar datos

####    <a name="use-the-azure-app-service-support-portal"></a>Usar el Portal de soporte técnico de Azure de aplicación de servicio

Web Apps le ofrece la capacidad para solucionar problemas relacionados con la aplicación web de consultando registros, registros de eventos, proceso volcados HTTP y mucho más. Puede acceder a toda esta información con nuestro portal de soporte técnico en **http://&lt;el nombre de la aplicación >.scm.azurewebsites.net/Support**

El portal de servicio compatible de aplicación de Azure ofrece tres pestañas independientes para admitir los tres pasos de un escenario de solución de problemas comunes:

1.  Observar el comportamiento actual
2.  Analizar recopilar información de diagnóstico y ejecutando los analizadores integrados
3.  Mitigar

Si el problema está sucediendo ahora, haga clic en **analizar** > **Diagnósticos** > **Diagnosticar ahora** para crear una sesión de diagnóstico, que recopilará HTTP registros, registros del Visor de sucesos, memoria volcados, registros de errores PHP y PHP procesan el informe.

Una vez que se recopilan los datos, también ejecutar un análisis de los datos y proporcionarle un informe HTML.

En caso de que desea descargar los datos de forma predeterminada, ¿se almacena en la carpeta D:\home\data\DaaS.

Para obtener más información sobre el portal de soporte técnico de servicio de aplicación de Azure, consulte [Nuevas actualizaciones a la extensión del sitio de soporte técnico para sitios Web de Azure](/blog/new-updates-to-support-site-extension-for-azure-websites).

####    <a name="use-the-kudu-debug-console"></a>Usar la consola de depuración Kudu

Aplicaciones de Web incluye una consola de depuración que puede usar para depurar, explorar y cargar archivos, así como los extremos JSON para obtener información acerca de su entorno. Esto se denomina la _Consola Kudu_ o el _Panel de SCM_ para la aplicación web.

Puede acceder a este panel, vaya al vínculo **https://&lt;el nombre de la aplicación >.scm.azurewebsites.net/**.

Algunas de las ventajas que ofrece Kudu son:

-   configuración del entorno de la aplicación
-   secuencia de registro
-   captura de diagnóstico
-   depurar consola en la que puede ejecutar los cmdlets de Powershell y los comandos básicos de DOS.


Otra característica útil de Kudu es que, en caso de que la aplicación es iniciar excepciones de primera oportunidad, puede usar Kudu y la herramienta SysInternals Procdump crear memoria vuelca. Estos volcados de memoria son instantáneas del proceso y pueden a menudo ayudar a solucionar los problemas más complicados con la aplicación web.

Para obtener más información sobre las características disponibles en Kudu, vea [Herramientas en línea de sitios Web de Azure que debe conocer](/blog/windows-azure-websites-online-tools-you-should-know-about/).

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

 ![Reinicie la aplicación para solucionar errores HTTP 502 puerta de enlace incorrecta y 503 Servicio no disponible](./media/app-service-web-troubleshoot-HTTP-502-503/2-restart.png)

También puede administrar la aplicación web de Powershell de Azure. Para obtener más información, vea [Usar PowerShell de Azure con el Administrador de recursos de Azure](../powershell-azure-resource-manager.md).
