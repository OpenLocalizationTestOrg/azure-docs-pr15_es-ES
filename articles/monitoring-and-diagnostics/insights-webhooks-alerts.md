<properties
    pageTitle="Configurar webhooks en Azure alertas métricas | Microsoft Azure"
    description="Volver a enrutar Azure alertas a otros sistemas de Azure no."
    authors="kamathashwin"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/15/2016"
    ms.author="ashwink"/>

# <a name="configure-a-webhook-on-an-azure-metric-alert"></a>Configurar un webhook en una alerta de métrica de Azure

Webhooks permiten enrutar una notificación de alerta de Azure a otros sistemas de procesamiento posterior o personalizados acciones. Puede usar un webhook en una alerta para redirigir a los servicios que enviar SMS, registrar errores, notificación un equipo a través de servicios de chat y mensajería o hacer cualquier número de otras acciones. Este artículo describe cómo configurar un webhook en una alerta de métrica Azure y el aspecto de la carga para la publicación de HTTP a un webhook. Para obtener información sobre la configuración y el esquema de un registro de actividad de Azure alertas (alerta sobre los eventos), [Consulte esta página en su lugar](./insights-auditlog-to-webhook-email.md).

Alertas de Azure HTTP POST el contenido de alerta en JSON formato, esquema descrito a continuación, en un webhook URI que proporcionan al crear la alerta. Este URI debe ser un extremo HTTP o HTTPS válido. Azure publicaciones de una entrada por solicitud cuando se activa una alerta.

## <a name="configuring-webhooks-via-the-portal"></a>Configurar webhooks a través del portal

Puede agregar o actualizar el webhook URI en la pantalla de crear o actualizar alertas en el [portal](https://portal.azure.com/).

![Agregar una regla de alerta](./media/insights-webhooks-alerts/Alertwebhook.png)

También puede configurar una alerta para publicar en un webhook URI con los [Cmdlets de PowerShell de Azure](./insights-powershell-samples.md#create-alert-rules), [Entre plataformas CLI](./insights-cli-samples.md#work-with-alerts)o [API de REST de Azure Monitor](https://msdn.microsoft.com/library/azure/dn933805.aspx).

## <a name="authenticating-the-webhook"></a>Autenticar la webhook

La webhook puede autenticar mediante uno de estos métodos:

1. **Autorización de token** - la webhook URI se guarda con un identificador de token, por ejemplo. `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`
2.  **Autorización básica** - la webhook URI se guarda con un nombre de usuario y contraseña, por ejemplo. `https://userid:password@mysamplealert/webcallback?someparamater=somevalue&foo=bar`

## <a name="payload-schema"></a>Esquema de carga

La operación de publicación contiene el esquema para todas las alertas de métrica y la carga JSON siguiente.

```JSON
{
"status": "Activated",
"context": {
            "timestamp": "2015-08-14T22:26:41.9975398Z",
            "id": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.insights/alertrules/ruleName1",
            "name": "ruleName1",
            "description": "some description",
            "conditionType": "Metric",
            "condition": {
                        "metricName": "Requests",
                        "metricUnit": "Count",
                        "metricValue": "10",
                        "threshold": "10",
                        "windowSize": "15",
                        "timeAggregation": "Average",
                        "operator": "GreaterThanOrEqual"
                },
            "subscriptionId": "s1",
            "resourceGroupName": "useast",                                
            "resourceName": "mysite1",
            "resourceType": "microsoft.foo/sites",
            "resourceId": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1",
            "resourceRegion": "centralus",
            "portalLink": "https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1"
},
"properties": {
              "key1": "value1",
              "key2": "value2"
              }
}
```


| Campo | Obligatorio | Conjunto de valores fijo | Notas |
| :-------------| :-------------   | :-------------   | :-------------   |
|estado|Y|"Activado", "Resuelto"|Estado de la alerta basado en las condiciones haya establecido.|
|contexto| Y | | El contexto de la alerta.|
|marca de tiempo| Y | | La hora en que se ha activado la alerta.|
|Id. | Y | | Cada regla de alerta tiene un identificador único.|
|nombre               |Y                  |                   | El nombre de la alerta.|
|Descripción        |Y                  |                           |Descripción de la alerta.|
|conditionType      |Y                  |"Métrica", "Evento"          |Dos tipos de alertas que son compatibles. Una basada en una condición métrica y otra basan en un evento en el registro de actividad. Usar este valor para comprobar si la alerta se basa en sistema métrico o evento.|
|condición          |Y                  |                           | Los campos específicos para comprobar que se basan en el conditionType.|
|metricName         |para las alertas de métricas  |                           |El nombre de la métrica que define la regla que supervisa.|
|metricUnit         |para las alertas de métricas  |"Bytes", "BytesPerSecond", "Contar", "CountPerSecond", "Porcentaje", "Segundos"|     La unidad que se permiten en la métrica. [Se enumeran los valores permitidos](https://msdn.microsoft.com/library/microsoft.azure.insights.models.unit.aspx).|
|metricValue        |para las alertas de métricas  |                           |El valor real de la métrica que provocó el aviso.|
|umbral          |para las alertas de métricas  |                           |El valor de umbral en el que se activa la alerta.|
|windowSize         |para las alertas de métricas  |                           |El período de tiempo que se usa para supervisar la actividad de alerta basada en el umbral. Debe estar entre 5 minutos y 1 día. Formato de la duración de ISO 8601.|
|timeAggregation    |para las alertas de métricas  |"Promedio", "Apellido", "Máximo", "Mínimo", "Ninguna", "Total" | ¿Cómo deben combinarse los datos que se recopilan con el tiempo. El valor predeterminado es el promedio. [Se enumeran los valores permitidos](https://msdn.microsoft.com/library/microsoft.azure.insights.models.aggregationtype.aspx).|
|operador           |para las alertas de métricas  |                           |El operador utilizado para comparar los datos de métrica actuales al conjunto de umbral.|
|subscriptionId     |Y                  |                           |Id. de suscripción de Azure|
|resourceGroupName  |Y                  |                           |Nombre del grupo de recursos para los recursos afectados.|
|resourceName       |Y                  |                           |Nombre de recurso de los recursos afectados.|
|tipo de recurso       |Y                  |                           |Tipo de recurso de los recursos afectados.|
|resourceId         |Y                  |                           |Identificador de recursos de los recursos afectados.|
|resourceRegion     |Y                  |                           |Región o la ubicación de los recursos afectados.|
|portalLink         |Y                  |                           |Vínculo directo a la página de resumen del portal de recursos.|
|propiedades         |N                  |Opcional                   |Conjunto de `<Key, Value>` pares (es decir, `Dictionary<String, String>`) que incluye información sobre el evento. Las propiedades de campo es opcional. En una interfaz de usuario o la lógica basado en la aplicación flujo de trabajo personalizada, los usuarios puedan introducir o valores de la clave que se pueden pasar a través de la carga. La forma alternativa para pasar propiedades personalizadas a la webhook es mediante la uri de webhook (como parámetros de consulta)|


>[AZURE.NOTE] El campo de propiedades sólo se puede establecer con la [API de REST de Azure Monitor](https://msdn.microsoft.com/library/azure/dn933805.aspx).

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre las alertas de Azure y webhooks en el vídeo [Integrar alertas de Azure con PagerDuty](http://go.microsoft.com/fwlink/?LinkId=627080)
- [Ejecutar secuencias de comandos de automatización de Azure (Runbooks) en las alertas de Azure](http://go.microsoft.com/fwlink/?LinkId=627081)
- [Use lógica de aplicación para enviar un SMS a través de Twilio desde una alerta de Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
- [Usar la lógica de aplicación para enviar un mensaje de margen de demora de una alerta de Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
- [Usar la lógica de aplicación para enviar un mensaje a una cola de Azure desde una alerta de Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)
