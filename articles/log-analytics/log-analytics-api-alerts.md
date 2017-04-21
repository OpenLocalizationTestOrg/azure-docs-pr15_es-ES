<properties
   pageTitle="API de REST de alerta de análisis de registro"
   description="La API de REST de alerta de análisis de registro le permite crear y administrar alertas en conjunto de aplicaciones de administración de operaciones (OMS).  Este artículo proporciona detalles de la API y varios ejemplos para realizar operaciones diferentes."
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="bwren" />

# <a name="log-analytics-alert-rest-api"></a>API de REST de alerta de análisis de registro

La API de REST de alerta de análisis de registro le permite crear y administrar alertas en conjunto de aplicaciones de administración de operaciones (OMS).  Este artículo proporciona detalles de la API y varios ejemplos para realizar operaciones diferentes.

La API de REST de búsqueda de análisis de registro es RESTful y se puede acceder a través de la API de REST de administrador de recursos de Azure. En este documento encontrará ejemplos donde se tiene acceso a la API de una línea de comandos de PowerShell con [ARMClient](https://github.com/projectkudu/ARMClient), una herramienta de línea de comandos de Abrir origen que simplifica invocar la API del Administrador de recursos de Azure. El uso de ARMClient y PowerShell es una de muchas opciones para tener acceso a la API de búsqueda de análisis de registro. Con estas herramientas puede utilizar la API de administrador de recursos de Azure REST para realizar llamadas a áreas de trabajo OMS y realizar los comandos de búsqueda dentro de ellas. La API mostrará resultados de búsqueda para usted en formato JSON, para que pueda utilizar los resultados de búsqueda de diferentes maneras mediante programación.

## <a name="prerequisites"></a>Requisitos previos
Actualmente, solo se pueden crear alertas con una búsqueda guardada en el análisis de registro.  Puede hacer referencia a la [API de REST de búsqueda de registro](log-analytics-log-search-api.md) para obtener más información.

## <a name="schedules"></a>Programaciones
Una búsqueda guardada puede tener una o más programaciones. La programación define la frecuencia con la búsqueda es ejecutar y el intervalo de tiempo en los que se identifica los criterios.
Programaciones tienen las propiedades de la tabla siguiente.

| (Propiedad)  | Descripción |
|:--|:--|
| Intervalo | ¿Con qué frecuencia se ejecuta la búsqueda. Medida en minutos. |
| QueryTimeSpan | El intervalo de tiempo en el que se evalúa los criterios. Debe ser igual o mayor que el intervalo. Medida en minutos. |
| Versión | La versión de la API que se usa.  Actualmente, siempre se debe establecer a 1. |

Por ejemplo, considere la posibilidad de una consulta de evento con un intervalo de 15 minutos y un intervalo de tiempo de 30 minutos. En este caso, la consulta se ejecutará cada 15 minutos y se activaría una alerta si los criterios continuación a resolver a true durante un período de 30 minutos.

### <a name="retrieving-schedules"></a>Recuperar las programaciones
Use el método Get para recuperar todos los planes de una búsqueda guardada.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules?api-version=2015-03-20

Use el método Get con un identificador de programación para recuperar una programación de una búsqueda guardada.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20

A continuación se muestra una respuesta de ejemplo para una programación.

    {
        "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace/savedSearches/0f0f4853-17f8-4ed1-9a03-8e888b0d16ec/schedules/a17b53ef-bd70-4ca4-9ead-83b00f2024a8",
        "etag": "W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\"",
        "properties": {
        "Interval": 15,
        "QueryTimeSpan": 15
    }

### <a name="creating-a-schedule"></a>Crear una programación
Use el método de superponer con un identificador único de programación para crear una nueva programación.  Tenga en cuenta que dos programaciones no pueden tener el mismo ID incluso si están asociados a diferentes búsquedas guardadas.  Cuando se crea una programación en la consola OMS, se crea un GUID para el identificador de programación.

    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson

### <a name="editing-a-schedule"></a>Edición de una programación
Use el método de superponer con un identificador de programación existente para la misma búsqueda guardada para modificar la programación.  El cuerpo de la solicitud debe incluir etag de la programación.

    $scheduleJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\""','properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson


### <a name="deleting-schedules"></a>Eliminar programaciones
Use el método de eliminación con un identificador de programación para eliminar una programación.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20


## <a name="actions"></a>Acciones
Una programación puede tener varias acciones. Una acción puede definir uno o varios procesos para realizar como enviar un correo o iniciar un runbook, o bien puede definir un umbral que determina cuándo los resultados de una búsqueda coinciden con algunos criterios.  Algunas acciones definirá ambas para que se realicen los procesos cuando se alcanza el umbral.

Todas las acciones tienen las propiedades de la tabla siguiente.  Diferentes tipos de alertas que tienen diferentes propiedades adicionales que se describen a continuación.

| (Propiedad) | Descripción |
|:--|:--|
| Tipo | Tipo de acción.  Actualmente, los valores posibles son Webhook y alerta. |
| Nombre | Nombre para mostrar de la alerta. |
| Versión | La versión de la API que se usa.  Actualmente, siempre se debe establecer a 1. |

### <a name="retrieving-actions"></a>Acciones de recuperación
Use el método Get para recuperar todas las acciones de una programación.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules/{Schedule ID}/actions?api-version=2015-03-20

Usar el método Get con el ID de acción para recuperar una acción determinada para una programación.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/actions/{Action ID}?api-version=2015-03-20

### <a name="creating-or-editing-actions"></a>Crear o editar acciones
Use el método superponer con un ID exclusivo a la programación para crear una nueva acción.  Cuando se crea una acción en la consola OMS, un GUID es para el Id.

Use el método de superponer con un ID existente para la misma búsqueda guardada para modificar la programación.  El cuerpo de la solicitud debe incluir etag de la programación.

El formato de solicitud para crear una nueva acción varía según el tipo de acción para que estos ejemplos se proporcionan en las secciones siguientes.

### <a name="deleting-actions"></a>Eliminar acciones
Use el método Delete con el ID de acción para eliminar una acción.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/Actions/{Action ID}?api-version=2015-03-20

### <a name="alert-actions"></a>Acciones de alerta
Una programación debería tener una y solo una acción de alerta.  Acciones de alerta tienen uno o más de las secciones en la tabla siguiente.  Se describen con más detalle.

| Sección | Descripción |
|:--|:--|
| Umbral | Criterios cuando se ejecuta la acción. |  
| EmailNotification | Enviar correo a varios destinatarios. |
| Corrección | Inicie un runbook de automatización de Azure para intentar corregir el problema identificado. |

#### <a name="thresholds"></a>Umbrales
Una acción de alerta debería tener solo un umbral.  Si los resultados de una búsqueda guardada coincide con el umbral de una acción asociada con la búsqueda, se ejecutan otros procesos en acción.  Una acción también puede contener solo un umbral de modo que se puede usar con las acciones de otros tipos que no contienen los umbrales.

Umbrales de tienen las propiedades de la tabla siguiente.

| (Propiedad) | Descripción |
|:--|:--|
| Operador | Operador de comparación de umbral. <br> gt = mayor que <br> lt = menor que |
| Valor | Valor de umbral. |

Por ejemplo, considere la posibilidad de una consulta de eventos con un intervalo de 15 minutos, un intervalo de tiempo de 30 minutos y un umbral de más de 10. En este caso, la consulta se ejecutará cada 15 minutos y se activaría una alerta si devuelven 10 eventos que se crearon durante un período de 30 minutos.

A continuación se muestra una respuesta de ejemplo para una acción con solo un umbral.  

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My threshold action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Version": 1
    }

Use el método de superponer con un identificador único de acción para crear una nueva acción de umbral para una programación.  

    $thresholdJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

Use el método de superponer con un ID existente para modificar una acción de umbral para una programación.  El cuerpo de la solicitud debe incluir etag de la acción.

    $thresholdJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

#### <a name="email-notification"></a>Notificación de correo electrónico
Notificaciones de correo electrónico enviar correo a uno o más destinatarios.  Incluyen las propiedades de la tabla siguiente.

| (Propiedad) | Descripción |
|:--|:--|
| Destinatarios | Lista de direcciones de correo. |
| Asunto | El asunto del mensaje de correo. |
| Datos adjuntos | Datos adjuntos no se admiten actualmente, por lo que siempre tendrá un valor de "Ninguno". |

A continuación se muestra una respuesta de ejemplo para una acción de notificación de correo electrónico con un umbral.  

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My email action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "EmailNotification": {
            "Recipients": [
                "recipient1@contoso.com",
                "recipient2@contoso.com"
            ],
            "Subject": "This is the subject",
            "Attachment": "None"
        },
        "Version": 1
    }

Use el método de superponer con un identificador único de acción para crear una nueva acción de correo electrónico para una programación.  En el ejemplo siguiente se crea una notificación de correo electrónico con un umbral para que el correo se envía cuando los resultados de la búsqueda guardada superan el umbral.

    $emailJson = "{'properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myemailaction?api-version=2015-03-20 $ emailJson

Use el método de superponer con un ID existente para modificar una acción de correo electrónico para una programación.  El cuerpo de la solicitud debe incluir etag de la acción.

    $emailJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myemailaction?api-version=2015-03-20 $ emailJson

#### <a name="remediation-actions"></a>Acciones de corrección
Correcciones inicie un runbook de automatización de Azure que intenta solucionar el problema identificado por la alerta.  Debe crear un webhook para runbook utilizado en una acción de corrección y luego especifique el URI de la propiedad WebhookUri.  Cuando se crea esta acción mediante la consola OMS, se crea automáticamente un nuevo webhook para runbook.

Correcciones incluyen las propiedades de la tabla siguiente.

| (Propiedad) | Descripción |
|:--|:--|
| RunbookName | Nombre del runbook. Debe coincidir con un runbook publicado en la cuenta de automatización configurada de la solución de automatización en el área de trabajo OMS. |
| WebhookUri | URI de la webhook.
| Caducidad | La fecha de vencimiento y la hora de la webhook.  Si la webhook no tiene una caducidad, esto puede ser cualquier fecha futura válida. |

A continuación se muestra una respuesta de ejemplo para una acción de corrección con un umbral.

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My remediation action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Remediation": {
            "RunbookName": "My-Runbook",
            "WebhookUri": "https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d",
            "Expiry": "2018-02-25T18:27:20"
            },
        "Version": 1
    }

Use el método de superponer con un identificador único de acción para crear una nueva acción de corrección para una programación.  En el ejemplo siguiente se crea una solución con un umbral para que se inicia runbook cuando los resultados de la búsqueda guardada superan el umbral.

    $remediateJson = "{'properties': { 'Type':'Alert', 'Name': 'My Remediation Action', 'Version':'1', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'Remediation': {'RunbookName': 'My-Runbook', 'WebhookUri':'https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d', 'Expiry':'2018-02-25T18:27:20Z'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myremediationaction?api-version=2015-03-20 $remediateJson

Use el método de superponer con un ID existente para modificar una acción de corrección para una programación.  El cuerpo de la solicitud debe incluir etag de la acción.

    $remediateJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Type':'Alert', 'Name': 'My Remediation Action', 'Version':'1', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'Remediation': {'RunbookName': 'My-Runbook', 'WebhookUri':'https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d', 'Expiry':'2018-02-25T18:27:20Z'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myremediationaction?api-version=2015-03-20 $remediateJson

#### <a name="example"></a>Ejemplo
A continuación se muestra un ejemplo completo para crear una nueva alerta de correo electrónico.  Esto crea una nueva programación junto con una acción que contiene un umbral y correo electrónico.

    $subscriptionId = "3d56705e-5b26-5bcc-9368-dbc8d2fafbfc"
    $workspaceId    = "MyWorkspace"
    $searchId       = "51cf0bd9-5c74-6bcb-927e-d1e9080b934e"

    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' }"
    armclient put /subscriptions/$subscriptionId/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/$workspaceId/savedSearches/$searchId/schedules/myschedule?api-version=2015-03-20 $scheduleJson

    $thresholdJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/$subscriptionId/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/$workspaceId/savedSearches/$searchId/schedules/myschedule/actions/mythreshold?api-version=2015-03-20 $thresholdJson

    $emailJson = "{'properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/$subscriptionId/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/$workspaceId/savedSearches/$searchId/schedules/myschedule/actions/myemailaction?api-version=2015-03-20 $emailJson

### <a name="webhook-actions"></a>Acciones de Webhook
Acciones de Webhook iniciar un proceso llamando a una dirección URL y, opcionalmente, proporcionar una carga que se envíen.  Son similares a las acciones de corrección excepto están diseñados para webhooks que puede invocar procesos que no sean runbooks automatización de Azure.  También proporcionan la opción adicional de proporcionar una carga se ha entregado al proceso remoto.

Acciones de Webhook no tiene un umbral pero deben agregarse a una programación que tiene una acción con un umbral de alerta.  Puede agregar varias acciones de Webhook todo se ejecutará cuando se alcanza el umbral.

Acciones de Webhook incluyen las propiedades de la tabla siguiente.

| (Propiedad) | Descripción |
|:--|:--|
| WebhookUri | El asunto del mensaje de correo. |
| CustomPayload | Carga personalizado que se envíen a la webhook.  El formato dependen de lo que espera la webhook. |

A continuación se muestra una respuesta de ejemplo para webhook acción y una acción de alerta asociada con un umbral.

    {
        "__metadata": {},
        "value": [
            {
                "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/bwren/savedSearches/2d1b30fb-7f48-4de5-9614-79ee244b52de/schedules/b80f5621-7217-4007-b32d-165d14377093/Actions/72884702-acf9-4653-bb67-f42436b342b4",
                "etag": "W/\"datetime'2016-02-26T20%3A25%3A00.6862124Z'\"",
                "properties": {
                    "Type": "Webhook",
                    "Name": "My Webhook Action",
                    "WebhookUri": "https://oaaswebhookdf.cloudapp.net/webhooks?token=VfkYTIlpk%2fc%2bJBP",
                    "CustomPayload": "{\"fielld1\":\"value1\",\"field2\":\"value2\"}",
                    "Version": 1
                }
            },
            {
                "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/bwren/savedSearches/2d1b30fb-7f48-4de5-9614-79ee244b52de/schedules/b80f5621-7217-4007-b32d-165d14377093/Actions/90a27cf8-71b7-4df2-b04f-54ed01f1e4b6",
                "etag": "W/\"datetime'2016-02-26T20%3A25%3A00.565204Z'\"",
                "properties": {
                    "Type": "Alert",
                    "Name": "Threshold for my webhook action",
                    "Threshold": {
                        "Operator": "gt",
                        "Value": 10
                    },
                    "Version": 1
                }
            }
        ]
    }

#### <a name="create-or-edit-a-webhook-action"></a>Crear o modificar una acción webhook
Use el método de superponer con un identificador único de acción para crear una nueva acción webhook para una programación.  En el ejemplo siguiente se crea una acción de Webhook y una acción de alerta con un umbral para que la webhook se activará cuando los resultados de la búsqueda guardada superan el umbral.

    $thresholdAction = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdAction

    $webhookAction = "{'properties': {'Type': 'Webhook', 'Name': 'My Webhook", 'WebhookUri': 'https://oaaswebhookdf.cloudapp.net/webhooks?token=VrkYTKlhk%2fc%2bKBP', 'CustomPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}', 'Version': 1 }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mywebhookaction?api-version=2015-03-20 $webhookAction

Use el método de superponer con un ID existente para modificar una acción de webhook para una programación.  El cuerpo de la solicitud debe incluir etag de la acción.

    $webhookAction = "{'etag': 'W/\"datetime'2016-02-26T20%3A25%3A00.6862124Z'\"','properties': {'Type': 'Webhook', 'Name': 'My Webhook", 'WebhookUri': 'https://oaaswebhookdf.cloudapp.net/webhooks?token=VrkYTKlhk%2fc%2bKBP', 'CustomPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}', 'Version': 1 }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mywebhookaction?api-version=2015-03-20 $webhookAction

## <a name="next-steps"></a>Pasos siguientes

- Use la [API de REST para realizar búsquedas de registro](log-analytics-log-search-api.md) de análisis de registro.
