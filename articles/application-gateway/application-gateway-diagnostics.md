<properties 
   pageTitle="Supervisar los registros de acceso y el rendimiento y métricas de puerta de enlace de aplicación | Microsoft Azure"
   description="Obtenga información sobre cómo habilitar y administrar registros de acceso y el rendimiento de puerta de enlace de aplicación"
   services="application-gateway"
   documentationCenter="na"
   authors="amitsriva"
   manager="rossort"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/26/2016"
   ms.author="amitsriva" />

# <a name="diagnostics-logging-and-metrics-for-application-gateway"></a>Registro de diagnóstico y métrica de puerta de enlace de aplicación

Azure proporciona la capacidad para supervisar recursos con el registro y mediciones

[**Registro**](#enable-logging-with-powershell) - registro permite rendimiento, access y otros registros que se guarden o consumido desde un recurso para seguimiento.

[**Métrica**](#metrics) - la puerta de enlace de aplicaciones actualmente tiene una métrica. Esta métrica mide el rendimiento de la puerta de enlace de aplicación en Bytes por segundo.

Puede usar diferentes tipos de registros en Azure para administrar y solucionar problemas de puertas de enlace de aplicación. Algunos de estos registros se pueden acceder a través del portal y todos los registros pueden extraídos de un almacenamiento de blobs de Windows Azure y se ven en distintas herramientas, como [El análisis de registro](../log-analytics/log-analytics-azure-networking-analytics.md), Excel y PowerBI. Puede obtener más información sobre los distintos tipos de registros de la siguiente lista:

- **Registros de auditoría:** Puede usar [Los registros de auditoría de Azure](../monitoring-and-diagnostics/insights-debugging-with-events.md) (anteriormente conocida como registros operativos) para ver todas las operaciones que se envía a su suscripción de Azure y su estado. Registros de auditoría habilitados de forma predeterminada y se pueden visualizar en el portal de vista previa de Azure.
- **Acceder a registros:** Puede utilizar este registro para ver el patrón de acceso de puerta de enlace de aplicación y analizar importante de información de IP del llamador, URL solicitada, latencia de respuesta, devolver código de bytes de entrada y salida. Registro de acceso se recopila cada 300 segundos. Este registro contiene un registro por cada instancia de puerta de enlace de aplicación. La instancia de puerta de enlace de aplicación puede identificarse por propiedad 'instanceId'.
- **Registros de rendimiento:** Puede utilizar este registro para ver el rendimiento de las instancias de puerta de enlace de aplicaciones. Este registro captura información de rendimiento en función de cada instancia incluidos solicitud total servida, rendimiento en bytes, número total de peticiones servidas, recuento de errores de solicitud de instancia de back-end positivos y negativos. Registro de rendimiento se recopila cada 60 segundos.
- **Registros del firewall:** Puede utilizar este registro para ver las solicitudes que se registran mediante el modo de detección o prevención de una puerta de enlace de aplicación que está configurado con el servidor de aplicaciones web.

>[AZURE.WARNING] Registros solo están disponibles para los recursos implementados en el modelo de implementación de administrador de recursos. No puede usar los registros de recursos en el modelo de implementación clásico. Para comprender mejor de los dos modelos, haga referencia al artículo de [Descripción de administrador de recursos e implementación clásico](../resource-manager-deployment-model.md) .

## <a name="enable-logging-with-powershell"></a>Habilitar el registro con PowerShell

Registro de auditoría se habilita automáticamente para todos los recursos de administrador de recursos. Debe habilitar acceso y registro para iniciar la recopilación de datos disponibles a través de los registros del rendimiento. Para habilitar el registro, consulte los siguientes pasos: 

1. Nota El identificador del recurso de su cuenta de almacenamiento, donde se almacenan los datos del registro. Esto sería del formulario: /subscriptions/\<subscriptionId\>/resourceGroups/\<el nombre del grupo de recursos\>/providers/Microsoft.Storage/storageAccounts/\<nombre de la cuenta de almacenamiento\>. Puede usar cualquier cuenta de almacenamiento en su suscripción. Puede usar el portal de vista previa para buscar esta información.

    ![Obtener una vista previa de portal - diagnósticos de puerta de enlace de aplicaciones](./media/application-gateway-diagnostics/diagnostics1.png)

2. Nota es Id. de recurso de la aplicación la puerta de enlace para que el registro esté habilitada. Esto sería del formulario: /subscriptions/\<subscriptionId\>/resourceGroups/\<el nombre del grupo de recursos\>/providers/Microsoft.Network/applicationGateways/\<nombre de puerta de enlace de aplicación\>. Puede usar el portal de vista previa para buscar esta información.

    ![Obtener una vista previa de portal - diagnósticos de puerta de enlace de aplicaciones](./media/application-gateway-diagnostics/diagnostics2.png)

3. Habilitar el registro de diagnóstico mediante el siguiente cmdlet de powershell:

        Set-AzureRmDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true  

>[AZURE.INFORMATION] registros de auditoría, no se requiere una cuenta de almacenamiento independiente. El uso de almacenamiento en el registro de rendimiento y acceso incurre cargos de servicio.

## <a name="enable-logging-with-azure-portal"></a>Habilitar el registro con el portal de Azure

### <a name="step-1"></a>Paso 1

Desplácese hasta el recurso en el portal de Azure. Haga clic en **registros de diagnóstico**. Si esta es la primera vez que la configuración de diagnósticos de la hoja es similar a la siguiente imagen:

De puerta de enlace de aplicación, 3 registros están disponibles.

- Registro de acceso
- Registro de rendimiento
- Registro de Firewall

Haga clic en **Activar diagnósticos** para iniciar la recopilación de datos.

![módulo de configuración de diagnósticos][1]

### <a name="step-2"></a>Paso 2

En el módulo de la **configuración de diagnósticos de** la configuración de cómo se establecen los registros de diagnóstico. En este ejemplo, el análisis de registro se usa para almacenar los registros. En **El análisis de registro** para configurar el área de trabajo, haga clic en **Configurar** . Hubs de evento y una cuenta de almacenamiento pueden usarse para guardar los registros de diagnósticos también.

![módulo de diagnóstico][2]

### <a name="step-3"></a>Paso 3

Elija un área de OMS existente o cree uno nuevo. En este ejemplo se usa una existente.

![áreas de trabajo de OMS][3]

### <a name="step-4"></a>Paso 4

Cuando haya terminado, confirme la configuración y haga clic en **Guardar** para guardar la configuración.

![confirmar la selección][4]

## <a name="audit-log"></a>Registro de auditoría

Azure genera este registro (anteriormente conocida como "registro operativa") de forma predeterminada.  Los registros se conservan de 90 días en el almacén de registros de eventos de Azure. Más información acerca de estos registros, lea el artículo [Ver eventos y registros de auditoría](../monitoring-and-diagnostics/insights-debugging-with-events.md) .

## <a name="access-log"></a>Registro de acceso

Este registro solo se genera si se ha habilitado en una función de puerta de enlace de aplicación como se describe en los pasos anteriores. Los datos se almacenan en la cuenta de almacenamiento que especificó cuando habilita el registro. Cada acceso de puerta de enlace de aplicación se registra en formato JSON, tal como se muestra en el ejemplo siguiente:

    {
        "resourceId": "/SUBSCRIPTIONS/<subscription id>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/<application gateway name>",
        "operationName": "ApplicationGatewayAccess",
        "time": "2016-04-11T04:24:37Z",
        "category": "ApplicationGatewayAccessLog",
        "properties": {
            "instanceId":"ApplicationGatewayRole_IN_0",
            "clientIP":"37.186.113.170",
            "clientPort":"12345",
            "httpMethod":"HEAD",
            "requestUri":"/xyz/portal",
            "requestQuery":"",
            "userAgent":"-",
            "httpStatus":"200",
            "httpVersion":"HTTP/1.0",
            "receivedBytes":"27",
            "sentBytes":"202",
            "timeTaken":"359",
            "sslEnabled":"off"
        }
    }

## <a name="performance-log"></a>Registro de rendimiento

Este registro solo se genera si se ha habilitado en una función de puerta de enlace de aplicación como se describe en los pasos anteriores. Los datos se almacenan en la cuenta de almacenamiento que especificó cuando habilita el registro. Se registran los datos siguientes:

    {
        "resourceId": "/SUBSCRIPTIONS/<subscription id>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/<application gateway name>",
        "operationName": "ApplicationGatewayPerformance",
        "time": "2016-04-09T00:00:00Z",
        "category": "ApplicationGatewayPerformanceLog",
        "properties": 
        {
            "instanceId":"ApplicationGatewayRole_IN_1",
            "healthyHostCount":"4",
            "unHealthyHostCount":"0",
            "requestCount":"185",
            "latency":"0",
            "failedRequestCount":"0",
            "throughput":"119427"
        }
    }


## <a name="firewall-log"></a>Registro de Firewall

Este registro solo se genera si se ha habilitado en una puerta de enlace cada aplicación como se describe en los pasos anteriores. Este registro también requiere configurarse dicho servidor de seguridad de la aplicación web de una puerta de enlace de aplicación. Los datos se almacenan en la cuenta de almacenamiento que especificó cuando habilita el registro. Se registran los datos siguientes:

    {
        "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/<applicationGatewayName>",
        "operationName": "ApplicationGatewayFirewall",
        "time": "2016-09-20T00:40:04.9138513Z",
        "category": "ApplicationGatewayFirewallLog",
        "properties":     {
            "instanceId":"ApplicationGatewayRole_IN_0",
            "clientIp":"108.41.16.164",
            "clientPort":1815,
            "requestUri":"/wavsep/active/RXSS-Detection-Evaluation-POST/",
            "ruleId":"OWASP_973336",
            "message":"XSS Filter - Category 1: Script Tag Vector",
            "action":"Logged",
            "site":"Global",
            "message":"XSS Filter - Category 1: Script Tag Vector",
            "details":{"message":" Warning. Pattern match "(?i)(<script","file":"/owasp_crs/base_rules/modsecurity_crs_41_xss_attacks.conf","line":"14"}}
    }

## <a name="view-and-analyze-the-audit-log"></a>Ver y analizar el registro de auditoría

Puede ver y analizar los datos de registro de auditoría con cualquiera de los métodos siguientes:

- **Herramientas azure:** Recuperar información de los registros de auditoría a través de PowerShell de Azure, la línea de comandos de Azure (CLI), la API de REST de Azure o el portal de vista previa de Azure.  En el artículo de [operaciones de auditoría con el Administrador de recursos](../resource-group-audit.md) se detallan instrucciones paso a paso para cada método.
- **Power BI:** Si todavía no tiene una cuenta de [Power BI](https://powerbi.microsoft.com/pricing) , puede intentar de forma gratuita. Con el [paquete de Power BI de contenido de los registros de auditoría de Azure](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/) puede analizar sus datos con paneles preconfigurados que puede usar como-está o personalizar.

## <a name="view-and-analyze-the-access-performance-and-firewall-log"></a>Ver y analizar el registro de acceso, el rendimiento y el firewall

[Registro de análisis](../log-analytics/log-analytics-azure-networking-analytics.md) de Azure puede recopilar el contador y archivos desde su cuenta de almacenamiento de blobs de registro de eventos e incluye visualizaciones y capacidades de búsqueda eficaces para analizar los registros.

También puede conectarse a su cuenta de almacenamiento y recuperar las entradas de registro JSON para los registros de acceso y el rendimiento. Una vez que descargar los archivos JSON, puede convertirlos en CSV y view en Excel, PowerBI o cualquier otra herramienta de visualización de datos.

>[AZURE.TIP] Si está familiarizado con Visual Studio y conceptos básicos de cambiar los valores de constantes y variables de C#, puede usar las [Herramientas de convertidor de registro](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponibles desde Github.

## <a name="metrics"></a>Métricas

Métricas son una característica para determinados recursos Azure donde puede ver contadores de rendimiento en el portal. De puerta de enlace de aplicación, una métrica está disponible en el momento de escribir este artículo. Esta métrica es el rendimiento y puede verse en el portal. Navegue hasta una puerta de enlace de la aplicación y haga clic en **métricas**.  Seleccione el rendimiento en la sección **métricas disponibles** para ver los valores. En la siguiente imagen, puede ver un ejemplo con los filtros que se pueden usar para mostrar los datos en intervalos de tiempo diferente.

Para ver una lista de actual métricas de soporte técnico, visite [métricas compatibles con el Monitor de Azure](../monitoring-and-diagnostics/monitoring-supported-metrics.md)

![vista métrica][5]

## <a name="alert-rules"></a>Reglas de alertas

Las reglas de alertas se pueden iniciar en función de métrica en un recurso. Esto significa para la puerta de enlace de aplicaciones, una alerta puede llamar a un webhook o un administrador de correo electrónico si el rendimiento de la puerta de enlace de aplicación es por encima, debajo o a un umbral durante un período de tiempo especificado.

En el ejemplo siguiente se le guiará a través de la creación de una regla de alerta que envía un correo electrónico a un administrador después de que se ha infringido un umbral de rendimiento.

### <a name="step-1"></a>Paso 1

Haga clic en **Agregar aviso métrica** para iniciar. Este módulo también se puede acceder desde el módulo de métricas.

![módulo de reglas de alertas][6]

### <a name="step-2"></a>Paso 2

En el módulo de **Agregar regla** , rellene el nombre, la condición y notificar a las secciones y haga clic en **Aceptar** cuando haya terminado.

El selector de **condición** permite 4 valores, **mayor que**, **mayor o igual**, **menor que**o **menor o igual a**.

El selector de **período** , permite la selección de un período de 5 minutos a 6 horas.

Seleccionando **los lectores, colaboradores y los propietarios de correo electrónico** el correo electrónico puede ser dinámico en función de los usuarios que tienen acceso a ese recurso. En caso contrario, puede ofrecer una lista separada por comas de los usuarios en el cuadro de texto **email(s) Administrador adicionales** .

![Agregar módulo de regla][7]

Si se infringe el umbral, un correo electrónico llega similar a la que se muestra en la siguiente imagen:

![correo electrónico de umbral infringido][8]

Una vez que una alerta de métrica se ha creado y ofrece una descripción general de todas las reglas de alerta, se muestra una lista de las alertas.

![vista de la regla de alerta][9]

Para obtener más información acerca de las notificaciones de alerta, visite [notificaciones de alerta de recepción](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

Para conocer más acerca de webhooks y cómo puede usarlos con alertas, visite [configurar un webhook en una alerta de métrica de Azure](../monitoring-and-diagnostics/insights-webhooks-alerts.md)

## <a name="next-steps"></a>Pasos siguientes

- Visualizar contador y registros de eventos con [El análisis de registro](../log-analytics/log-analytics-azure-networking-analytics.md) 
- Entrada de blog de [visualizar los registros de auditoría de Azure con Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) .
- [Ver y analizar los registros de auditoría de Azure en Power BI y mucho más](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) entrada de blog.

[1]: ./media/application-gateway-diagnostics/figure1.png
[2]: ./media/application-gateway-diagnostics/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png