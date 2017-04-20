<properties 
    pageTitle="Supervisar las aplicaciones de la lógica de servicio de aplicaciones de Azure | Microsoft Azure" 
    description="Cómo ver lo han hecho sus aplicaciones de lógica" 
    authors="jeffhollan" 
    manager="erikre" 
    editor="" 
    services="logic-apps" 
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="jehollan"/>

# <a name="monitor-your-logic-apps"></a>Supervisar las aplicaciones de lógica

Después de [crear una aplicación de lógica](app-service-logic-create-a-logic-app.md), puede ver el historial completo de su ejecución en el portal de Azure.  También puede configurar servicios como diagnósticos de Azure y Azure alertas para supervisar los eventos en tiempo real y alertas por eventos como "cuando se ejecuta más de 5 errores en una hora."

## <a name="monitor-in-the-azure-portal"></a>Monitor en el Portal de Azure

Para ver el historial, seleccione **Examinar**y a continuación, seleccione **Lógica de aplicaciones**. Se muestra una lista de todas las aplicaciones de lógica en la suscripción.  Seleccione la aplicación de la lógica que desea supervisar.  Verá una lista de todas las acciones y desencadenadores que se han producido para esta aplicación lógica.

![Información general](./media/app-service-logic-monitor-your-logic-apps/overview.png)

Hay algunas secciones de este módulo que son útiles:

- **Resumen** enumera **todos se ejecuta** y el **Historial de desencadenador**
    - Se ejecuta la aplicación más reciente de la lógica de lista **todo funciona** .  Puede haga clic en cualquier fila para obtener detalles sobre la ejecución, o haga clic en el icono de más ejecuciones de la lista.
    - **Historial de desencadenador** muestra todas las actividades de desencadenador para esta aplicación lógica.  Actividad de desencadenador podría ser una comprobación de "Omitidos" para los nuevos datos (por ejemplo, buscando para ver si se ha agregado un nuevo archivo a FTP), "Correcto" lo que significa que se devolvieron datos aplique una aplicación lógica o "Error" corresponde a un error en la configuración.
- **Diagnósticos** le permite ver detalles de tiempo de ejecución y eventos y suscribirse a [Las alertas de Azure](#adding-azure-alerts)

>[AZURE.NOTE] Todos los detalles de tiempo de ejecución y eventos se cifran al resto dentro del servicio de aplicación de la lógica. Sólo que se hayan descifrado a una petición de vista de un usuario. También se puede controlar el acceso a estos eventos por el Control de acceso de Azure Role-Based (RBAC).

### <a name="view-the-run-details"></a>Ver los detalles de ejecución

Esta lista de ejecuciones muestra el **estado**, la **Hora de inicio**y la **duración** de la particular ejecución. Seleccione cualquier fila para ver detalles en el que se ejecutan.

La vista de supervisión muestra cada paso de la ejecución, las entradas y salidas y mensajes de error que pueden tener occurre.

![Ejecutar y acciones](./media/app-service-logic-monitor-your-logic-apps/monitor-view.png)

Si necesita detalles adicionales como el **Identificador de correlación** ejecución (que se pueden usar para la API de REST), hacer clic en el botón **Detalles** .  Esto incluye todos los pasos, estado y entradas/salidas para la ejecución.

## <a name="azure-diagnostics-and-alerts"></a>Diagnóstico de Azure y alertas

Además de los detalles que proporciona el Portal de Azure y API de REST anteriores, puede configurar la aplicación de la lógica para usar diagnósticos de Azure para más detalles enriquecidos y depuración.

1. Haga clic en la sección **diagnóstico** del módulo de aplicación de lógica
1. Haga clic aquí para configurar las **Opciones de diagnóstico**
1. Configurar una cuenta de almacenamiento o evento concentrador para emitir datos a

    ![Configuración de diagnósticos de Azure](./media/app-service-logic-monitor-your-logic-apps/diagnostics.png)

### <a name="adding-azure-alerts"></a>Adición de alertas de Azure

Una vez configurados los diagnósticos, puede agregar alertas de Azure aplique cuando se traspasan determinados umbrales.  En el módulo de **diagnóstico** , seleccione el mosaico de **alertas** y **Agregar aviso**.  Esto le guiará en configurar una alerta basada en un número de umbrales y mediciones.

![Métrica de alerta de Azure](./media/app-service-logic-monitor-your-logic-apps/alerts.png)

Puede configurar la **condición**, **umbral**y **período** como desee.  Por último, puede configurar una dirección de correo electrónico para enviar una notificación a o configurar un webhook.  Puede usar el [desencadenador de solicitud](../connectors/connectors-native-reqres.md) en una aplicación de la lógica se ejecute en una alerta también (para hacer cosas como [publicar en el margen de demora](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app), [Enviar un texto](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)o [Agregar un mensaje de una cola](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)).

### <a name="azure-diagnostics-settings"></a>Configuración de diagnósticos de Azure

Cada uno de estos eventos contiene detalles sobre la aplicación de lógica y un evento como estado.  Aquí tiene un ejemplo de un evento de *ActionCompleted* :

```javascript
{
            "time": "2016-07-09T17:09:54.4773148Z",
            "workflowId": "/SUBSCRIPTIONS/80D4FE69-ABCD-EFGH-A938-9250F1C8AB03/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/MYLOGICAPP",
            "resourceId": "/SUBSCRIPTIONS/80D4FE69-ABCD-EFGH-A938-9250F1C8AB03/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/MYLOGICAPP/RUNS/08587361146922712057/ACTIONS/HTTP",
            "category": "WorkflowRuntime",
            "level": "Information",
            "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
            "properties": {
                "$schema": "2016-06-01",
                "startTime": "2016-07-09T17:09:53.4336305Z",
                "endTime": "2016-07-09T17:09:53.5430281Z",
                "status": "Succeeded",
                "code": "OK",
                "resource": {
                    "subscriptionId": "80d4fe69-ABCD-EFGH-a938-9250f1c8ab03",
                    "resourceGroupName": "MyResourceGroup",
                    "workflowId": "cff00d5458f944d5a766f2f9ad142553",
                    "workflowName": "MyLogicApp",
                    "runId": "08587361146922712057",
                    "location": "eastus",
                    "actionName": "Http"
                },
                "correlation": {
                    "actionTrackingId": "e1931543-906d-4d1d-baed-dee72ddf1047",
                    "clientTrackingId": "my-custom-tracking-id"
                },
                "trackedProperties": {
                    "myProperty": "<value>"
                }
            }
        }
```

Las dos propiedades que son especialmente útiles para realizar el seguimiento y supervisión son *clientTrackingId* y *trackedProperties*.  

#### <a name="client-tracking-id"></a>Identificador de seguimiento del cliente

El cliente de Id. de seguimiento es un valor que se correlación de eventos en una aplicación de lógica ejecutar, incluidos los flujos de trabajo anidadas denominados como parte de una aplicación de lógica.  Este ID se genera automáticamente si no se proporcionan, pero puede especificar manualmente el cliente de Id. de seguimiento de un desencadenador pasando un `x-ms-client-tracking-id` encabezado con el valor del identificador de la solicitud de desencadenador (desencadenador de solicitud, desencadenador HTTP o webhook desencadenador).

#### <a name="tracked-properties"></a>Propiedades de marcas

Propiedades de marcas se pueden agregar a acciones en la definición del flujo de trabajo para realizar un seguimiento de entradas o salidas de datos de diagnóstico.  Esto puede ser útil si desea realizar un seguimiento de los datos como un "ID" en su telemetría.  Para agregar una propiedad marca, incluya el `trackedProperties` propiedad en una acción.  Entradas de una sola acciones y resultados, pero se puede utilizar el control de marcas propiedades pueden solo la `correlation` propiedades de los eventos de correlación de acciones en una ejecución.

```javascript
{
    "myAction": {
        "type": "http",
        "inputs": {
            "uri": "http://uri",
            "headers": {
                "Content-Type": "application/json"
            },
            "body": "@triggerBody()"
        },
        "trackedProperties":{
            "myActionHTTPStatusCode": "@action()['outputs']['statusCode']",
            "myActionHTTPValue": "@action()['outputs']['body']['foo']",
            "transactionId": "@action()['inputs']['body']['bar']"
        }
    }
}
```

### <a name="extending-your-solutions"></a>Ampliar sus soluciones

Puede aprovechar esta telemetría del concentrador de evento o almacenamiento en otros servicios como [Conjunto de aplicaciones de administración de las operaciones de](https://www.microsoft.com/cloud-platform/operations-management-suite) [Análisis de secuencia de Azure](https://azure.microsoft.com/services/stream-analytics/)y [Power BI](https://powerbi.com) tener la supervisión en tiempo real de los flujos de trabajo de integración.

## <a name="next-steps"></a>Pasos siguientes
- [Ejemplos y escenarios para lógica aplicaciones comunes](app-service-logic-examples-and-scenarios.md)
- [Crear una plantilla de implementación de la aplicación de lógica](app-service-logic-create-deploy-template.md)
- [Características de integración de Enterprise](app-service-logic-enterprise-integration-overview.md)
