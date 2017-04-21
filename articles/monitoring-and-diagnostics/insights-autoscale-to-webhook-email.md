<properties
    pageTitle="Usar acciones de Autoescala para enviar correo electrónico y webhook notificaciones de alerta. | Microsoft Azure"
    description="Aprenda a usar acciones de Autoescala para llamar a las direcciones URL de web o enviar notificaciones de correo electrónico en el Monitor de Azure. "
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
    ms.date="07/19/2016"
    ms.author="ashwink"/>

# <a name="use-autoscale-actions-to-send-email-and-webhook-alert-notifications-in-azure-monitor"></a>Usar acciones de Autoescala para enviar alertas de correo electrónico y webhook en el Monitor de Azure

Este artículo le muestra cómo configurar desencadenadores para que pueda llamar a las direcciones URL de web específica o enviar mensajes de correo electrónico basados en acciones Autoescala en Azure.  

## <a name="webhooks"></a>Webhooks
Webhooks permiten enrutar las notificaciones de alerta Azure a otros sistemas de para las notificaciones de procesamiento posterior o personalizadas. Por ejemplo, enrutamiento la alerta para los servicios que pueden controlar una solicitud de web entrante para enviar SMS, registro de errores, notificar a los servicios de un equipo con el chat o mensajería, etcetera. La URI de webhook debe ser un extremo HTTP o HTTPS válido.

## <a name="email"></a>Correo electrónico
Correo electrónico puede enviarse a cualquier dirección de correo electrónico válido. También se notificará los administradores y coadministradores de la suscripción que se ejecuta la regla.


## <a name="cloud-services-and-web-apps"></a>Servicios de nube y aplicaciones Web
Puede participar en desde el portal de Azure para servicios en la nube y granjas de servidores (aplicaciones Web).

- Elija la métrica de **escala por** .

![Ajustar a](./media/insights-autoscale-to-webhook-email/insights-autoscale-scale-by.png)

## <a name="virtual-machine-scale-sets"></a>Conjuntos de escala de máquina virtual
Para las máquinas virtuales nuevas creadas con el Administrador de recursos (conjuntos de escala de máquina Virtual), puede configurarlo con la API de REST, plantillas de administrador de recursos, PowerShell y CLI. Una interfaz de portal aún no está disponible.
Al utilizar la plantilla de la API de REST o administrador de recursos, incluya el elemento de notificaciones con las siguientes opciones.

```
"notifications": [
      {
        "operation": "Scale",
        "email": {
          "sendToSubscriptionAdministrator": false,
          "sendToSubscriptionCoAdministrators": false,
          "customEmails": [
              "user1@mycompany.com",
              "user2@mycompany.com"
              ]
        },
        "webhooks": [
          {
            "serviceUri": "https://foo.webhook.example.com?token=abcd1234",
            "properties": {
              "optional_key1": "optional_value1",
              "optional_key2": "optional_value2"
            }
          }
        ]
      }
    ]
```
|Campo                              |¿Obligatorio? |Descripción|
|---                                |---        |---|
|operación                          |Sí        |el valor debe ser "Escala"|
|sendToSubscriptionAdministrator    |Sí        |valor debe ser "true" o "false"|
|sendToSubscriptionCoAdministrators |Sí        |valor debe ser "true" o "false"|
|customEmails                       |Sí        |valor puede ser null [] o matriz de cadenas de mensajes de correo electrónico|
|webhooks                           |Sí        |valor puede ser null o válido Uri|
|serviceUri                         |Sí        |un Uri de https válida|
|propiedades                         |Sí        |valor debe ser {} vacía o contiene pares de clave y valor|


## <a name="authentication-in-webhooks"></a>Autenticación en webhooks
Hay dos formas URI de autenticación:

1. Autenticación de base de token, donde guardar la webhook URI con un identificador de token como parámetro de consulta. Por ejemplo, https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue
2. Autenticación básica que utilizar un identificador de usuario y contraseña. Por ejemplo,https://userid:password@mysamplealert/webcallback?someparamater=somevalue&parameter=value

## <a name="autoscale-notification-webhook-payload-schema"></a>Esquema de carga de Autoescala notificación webhook
Cuando se genera la notificación Autoescala, los metadatos siguientes se incluyen en la carga de webhook:

```
{
        "version": "1.0",
        "status": "Activated",
        "operation": "Scale In",
        "context": {
                "timestamp": "2016-03-11T07:31:04.5834118Z",
                "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/autoscalesettings/myautoscaleSetting",
                "name": "myautoscaleSetting",
                "details": "Autoscale successfully started scale operation for resource 'MyCSRole' from capacity '3' to capacity '2'",
                "subscriptionId": "s1",
                "resourceGroupName": "rg1",
                "resourceName": "MyCSRole",
                "resourceType": "microsoft.classiccompute/domainnames/slots/roles",
                "resourceId": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService/slots/Production/roles/MyCSRole",
                "portalLink": "https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService",
                "oldCapacity": "3",
                "newCapacity": "2"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
}
```


|Campo  |¿Obligatorio?|    Descripción|
|---|---|---|
|estado |Sí    |El estado que indica que se ha generado una acción Autoescala|
|operación| Sí |Un aumento de instancias, será "Escala fuera" y para una disminución de instancias, será "Escala en"|
|contexto|   Sí |El contexto de la acción Autoescala|
|marca de tiempo| Sí |Marca de tiempo cuando se ha desencadenado la acción Autoescala|
|Id. |Sí|   Id. del Administrador de recursos de la opción Ajustar automáticamente|
|nombre   |Sí|   El nombre de la opción Ajustar automáticamente|
|detalles|   Sí |Explicación de la acción que realizó el servicio Autoescala y el cambio en el recuento de instancia|
|subscriptionId|    Sí |Id. del recurso de destino que se ajusta la escala de suscripción|
|resourceGroupName| Sí|    Nombre del grupo de recursos del recurso de destino que se ajusta la escala|
|resourceName   |Sí|   Nombre del recurso de destino que se ajusta la escala|
|tipo de recurso   |Sí|   Los tres valores admiten: "microsoft.classiccompute/domainnames/slots/roles" - roles, "microsoft.compute/virtualmachinescalesets" - conjuntos de escala de máquina Virtual y "Microsoft.Web/serverfarms" - la aplicación Web de servicio de nube|
|resourceId |Sí|Id. del Administrador de recursos del recurso de destino que se ajusta la escala|
|portalLink |Sí    |Vínculo de portal Azure a la página de resumen del recurso de destino|
|oldCapacity|   Sí |El recuento de instancia (antiguo) actual cuando Autoescala ha realizado una acción de escala|
|newCapacity|   Sí |El nuevo recuento de instancia Autoescala escala el recurso|
|Propiedades|    No| Opcional. Conjunto de < clave, valor > pares (por ejemplo, diccionario < String, String >). Las propiedades de campo es opcional. En una interfaz de usuario personalizada o un flujo de trabajo de aplicación en función de lógica, puede escribir las claves y los valores que se pueden pasar utilizando la carga. Una forma alternativa para pasar propiedades personalizadas a la llamada saliente de webhook es usar la webhook URI (como parámetros de consulta)|
