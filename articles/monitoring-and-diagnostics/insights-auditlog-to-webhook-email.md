<properties
    pageTitle="Configurar un webhook en alertas de registro de actividad de Azure | Microsoft Azure"
    description="Vea cómo usar las alertas de registro de actividad para llamar a webhooks. "
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
    ms.date="10/20/2016"
    ms.author="ashwink"/>

# <a name="configure-a-webhook-on-an-azure-activity-log-alerts"></a>Configurar un webhook en alertas de un registro de actividad de Azure

Webhooks permiten enrutar una notificación de alerta de Azure a otros sistemas de procesamiento posterior o personalizados acciones. Puede usar un webhook en una alerta para redirigir a los servicios que enviar SMS, registrar errores, notificación un equipo a través de servicios de chat y mensajería o hacer cualquier número de otras acciones. Este artículo describe cómo configurar un webhook en una alerta de registro de actividad de Azure y el aspecto de la carga para la publicación de HTTP a un webhook. Para obtener información sobre la configuración y el esquema de una alerta de métrica Azure, [Consulte esta página en su lugar](insights-webhooks-alerts.md). También puede configurar una alerta de registro de actividad para enviar correo electrónico cuando activa.

>[AZURE.NOTE] Esta característica está actualmente en la vista previa, así que esperan variable calidad y el rendimiento.

Puede configurar una alerta de registro de actividad mediante los [Cmdlets de PowerShell de Azure](insights-powershell-samples.md#create-alert-rules), [Entre plataformas CLI](insights-cli-samples.md#work-with-alerts)o [API de REST de Azure Monitor](https://msdn.microsoft.com/library/azure/dn933805.aspx).

## <a name="authenticating-the-webhook"></a>Autenticar la webhook
TThe webhook puede autenticar mediante uno de estos métodos:

1. **Autorización de token** - la webhook URI se guarda con un identificador de token, por ejemplo. `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`
2.  **Autorización básica** - la webhook URI se guarda con un nombre de usuario y contraseña, por ejemplo. `https://userid:password@mysamplealert/webcallback?someparamater=somevalue&foo=bar`

## <a name="payload-schema"></a>Esquema de carga
La operación de publicación contiene la siguiente carga JSON y el esquema para todas las alertas de registro de actividad. Este esquema es similar al utilizado por alertas basadas en métrica.

```
{
        "status": "Activated",
        "context": {
                "resourceProviderName": "Microsoft.Web",
                "event": {
                        "$type": "Microsoft.WindowsAzure.Management.Monitoring.Automation.Notifications.GenericNotifications.Datacontracts.InstanceEventContext, Microsoft.WindowsAzure.Management.Mon.Automation",
                        "authorization": {
                                "action": "Microsoft.Web/sites/start/action",
                                "scope": "/subscriptions/s1/resourcegroups/rg1/providers/Microsoft.Web/sites/leoalerttest"
                        },
                        "eventDataId": "327caaca-08d7-41b1-86d8-27d0a7adb92d",
                        "category": "Administrative",
                        "caller": "myname@mycompany.com",
                        "httpRequest": {
                                "clientRequestId": "f58cead8-c9ed-43af-8710-55e64def208d",
                                "clientIpAddress": "104.43.166.155",
                                "method": "POST"
                        },
                        "status": "Succeeded",
                        "subStatus": "OK",
                        "level": "Informational",
                        "correlationId": "4a40beaa-6a63-4d92-85c4-923a25abb590",
                        "eventDescription": "",
                        "operationName": "Microsoft.Web/sites/start/action",
                        "operationId": "4a40beaa-6a63-4d92-85c4-923a25abb590",
                        "properties": {
                                "$type": "Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage",
                                "statusCode": "OK",
                                "serviceRequestId": "f7716681-496a-4f5c-8d14-d564bcf54714"
                        }
                },
                "timestamp": "Friday, March 11, 2016 9:13:23 PM",
                "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/alertonevent2",
                "name": "alertonevent2",
                "description": "test alert on event start",
                "conditionType": "Event",
                "subscriptionId": "s1",
                "resourceId": "/subscriptions/s1/resourcegroups/rg1/providers/Microsoft.Web/sites/leoalerttest",
                "resourceGroupName": "rg1"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
}
```

|Nombre del elemento       |Descripción|
|---                |---|
|estado             |Se utiliza para las alertas de métrica. Siempre se establece en "activado" para alertas de registro de actividad.|
|contexto            |Contexto del evento.|
|resourceProviderName|El proveedor de recursos de los recursos afectados.|
|conditionType      |Siempre "evento".|
|nombre               |Nombre de la regla de alerta.|
|Id.                 |Id. de recurso de la alerta.|
|Descripción        |Descripción de la alerta como conjunto durante la creación de la alerta.|
|subscriptionId     |Id. de suscripción de Azure|
|marca de tiempo          |Hora en que se generó el evento por el servicio de Azure que procesa la solicitud.|
|resourceId         |Identificador de recursos de los recursos afectados.|
|resourceGroupName  |Nombre del grupo de recursos para los recursos afectados|
|propiedades         |Conjunto de `<Key, Value>` pares (es decir, `Dictionary<String, String>`) que incluye información sobre el evento.|
|evento              |Elemento de metadatos acerca del evento.|
|autorización      |Propiedades RBAC del evento. Generalmente se incluyen la acción de"", "rol" y "ámbito".|
|categoría           |Categoría del evento. Valores compatibles son: administrativo, alertas, seguridad, ServiceHealth, recomendación.|
|autor de la llamada             |Dirección de correo electrónico del usuario que realiza la operación, notificación de UPN o reclamo SPN de disponibilidad. Puede ser null para determinadas llamadas del sistema.|
|correlationId      |Normalmente un GUID en formato de cadena. Eventos con correlationId pertenecen a la misma acción más grande y normalmente comparten un correlationId.|
|eventDescription   |Descripción de texto estático del evento.|
|eventDataId        |Identificador único para el evento.|
|origen de eventos        |Nombre del servicio de Azure o infraestructura que generó el evento.|
|httpRequest        |Normalmente incluye la "clientRequestId", "clientIpAddress" y "método" (método HTTP, por ejemplo, poner).|
|nivel              |Uno de los valores siguientes: "Crítica", "Error", "Advertencia", "Informativo" y "Detallado".|
|operationId        |Normalmente GUID compartido los eventos que corresponde a la única operación.|
|operationName      |Nombre de la operación.|
|propiedades         |Propiedades del evento.|
|estado             |Cadena. Estado de la operación. Incluyen valores comunes: "A", "En curso", "Correcto", "Error", "Activos", "Resuelto".|
|Subestado          |Normalmente incluye el código de estado HTTP de la llamada resto correspondiente. También puede incluir otras cadenas que describe un subestado. Incluyen valores subestado comunes: Aceptar (código de estado HTTP: 200), creado (código de estado HTTP: 201), aceptada (código de estado HTTP: 202), No el contenido (código de estado HTTP: 204), solicitud incorrecta (código de estado HTTP: 400), no se encontró (código de estado HTTP: 404), conflicto (código de estado HTTP: 409), Error interno del servidor (código de estado HTTP: 500), servicio no disponible (código de estado HTTP: 503), tiempo de espera de puerta de enlace (código de estado HTTP: 504)|

## <a name="next-steps"></a>Pasos siguientes
- [Obtenga más información sobre el registro de actividad](monitoring-overview-activity-logs.md)
- [Ejecutar secuencias de comandos de automatización de Azure (Runbooks) en las alertas de Azure](http://go.microsoft.com/fwlink/?LinkId=627081)
- [Use lógica de aplicación para enviar un SMS a través de Twilio desde una alerta de Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app). En este ejemplo es para las alertas métricas, pero puede modificarse para trabajar con una alerta de registro de actividad.
- [Use lógica de aplicación para enviar un mensaje de margen de demora de una alerta de Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app). En este ejemplo es para las alertas métricas, pero puede modificarse para trabajar con una alerta de registro de actividad.
- [Use lógica de aplicación para enviar un mensaje de una cola de Azure desde una alerta de Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app). En este ejemplo es para las alertas métricas, pero puede modificarse para trabajar con una alerta de registro de actividad.
