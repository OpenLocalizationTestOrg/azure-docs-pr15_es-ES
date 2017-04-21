<properties
    pageTitle="Crear un espacio de nombres de Bus de servicio con el tema de la suscripción y regla con una plantilla de administrador de recursos de Azure | Microsoft Azure"
    description="Crear un espacio de nombres de Bus de servicio con tema, la suscripción y la regla mediante la plantilla de administrador de recursos de Azure"
    services="service-bus"
    documentationCenter=".net"
    authors="ShubhaVijayasarathy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="tbd"
    ms.topic="article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="10/25/2016"
    ms.author="ShubhaVijayasarathy"/>

# <a name="create-a-service-bus-namespace-with-topic-subscription-and-rule-using-an-azure-resource-manager-template"></a>Crear un espacio de nombres de Bus de servicio con tema, la suscripción y regla con una plantilla de administrador de recursos de Azure

Este artículo le muestra cómo usar una plantilla de administrador de recursos de Azure que crea un espacio de nombres de Bus de servicio con un tema, la suscripción y la regla (filtro). Obtenga información sobre cómo definir los recursos que se implementan y cómo definir los parámetros que especifica cuando se ejecuta la implementación. Puede usar esta plantilla para sus propias implementaciones o personalizarlo para satisfacer sus necesidades

Para obtener más información sobre la creación de plantillas, vea [plantillas de administrador de recursos de Azure de creación][].

Para obtener más información sobre prácticas y las tramas de recursos de Azure convenciones de nomenclatura, vea [Convenciones de nomenclatura de recursos de Azure][].

Para la plantilla completa, vea la plantilla de [espacio de nombres de Bus de servicio con tema, la suscripción y la regla][] .

>[AZURE.NOTE] Las siguientes plantillas de administrador de recursos de Azure están disponibles para su descarga e implementación.
>
>-    [Crear un espacio de nombres de Bus de servicio con cola y la autorización de regla](service-bus-resource-manager-namespace-auth-rule.md)
>-    [Crear un espacio de nombres de Bus de servicio con cola](service-bus-resource-manager-namespace-queue.md)
>-    [Crear un espacio de nombres de Bus de servicio](service-bus-resource-manager-namespace.md)
>-    [Crear un espacio de nombres de Bus de servicio con tema y suscripción](service-bus-resource-manager-namespace-topic.md)
>
>Para comprobar si las últimas plantillas, visite la Galería de [Plantillas de Azure tutorial rápido][] y buscar Bus de servicio.

## <a name="what-will-you-deploy"></a>¿Qué va a distribuir?

Con esta plantilla, implementar un espacio de nombres de Bus de servicio con tema, la suscripción y la regla (filtro).

[Temas de Bus de servicios y suscripciones](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) proporcionar un formulario de uno a varios de comunicación en un modelo *de publicación o suscripción* . Al usar temas y suscripciones, componentes de una aplicación distribuida no comunican directamente con otros, en su lugar intercambiar mensajes a través del tema que actúa como intermediario. Una suscripción a un tema se asemeja a una cola virtual que recibe copia de los mensajes que se han enviado al tema. Un filtro de suscripción permite especificar qué mensajes enviados a un tema debe aparecer dentro de una suscripción de un tema específico.

## <a name="what-are-rules-filters"></a>¿Cuáles son las reglas (filtros)?

En muchos casos, se deben procesar mensajes que tienen características específicas de diferentes formas. Para habilitar esta opción, puede configurar las suscripciones para buscar mensajes que han deseado propiedades y, a continuación, realizan ciertas modificaciones en las propiedades. Mientras las suscripciones de Bus de servicio ver todos los mensajes enviados al tema, solo puede copiar un subconjunto de los mensajes a la cola de suscripción virtual. Esto se logra con filtros de suscripción. Para obtener más información sobre rules(filters), consulte [suscripciones, temas y colas de Bus de servicio][].

Para ejecutar la implementación automáticamente, haga clic en el botón siguiente:

[![Implementar en Azure](./media/service-bus-resource-manager-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-topic-subscription-rule%2Fazuredeploy.json)

## <a name="parameters"></a>Parámetros

Con el Administrador de recursos de Azure, debe definir parámetros para los valores que desea especificar cuando se implementa la plantilla. La plantilla incluye una sección denominada `Parameters` que contiene todos los valores de parámetro. Debe definir un parámetro para los valores que varían en función del proyecto que está implementando o basado en el entorno de que implementación. Debe definir parámetros para los valores que siempre se mantienen. Cada valor de parámetro se utiliza en la plantilla para definir los recursos que se implementan.

La plantilla define los parámetros siguientes:

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName

El nombre del espacio de nombres de Bus de servicio para crear.

```
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="servicebustopicname"></a>serviceBusTopicName

El nombre del tema que creó en el espacio de nombres de Bus de servicio.

```
"serviceBusTopicName": {
"type": "string"
}
```

### <a name="servicebussubscriptionname"></a>serviceBusSubscriptionName

El nombre de la suscripción que creó en el espacio de nombres de Bus de servicio.

```
"serviceBusSubscriptionName": {
"type": "string"
}
```
### <a name="servicebusrulename"></a>serviceBusRuleName

El nombre de la rule(filter) creado en el espacio de nombres de Bus de servicio.

```
   "serviceBusRuleName": {
   "type": "string",
  }
```
### <a name="servicebusapiversion"></a>serviceBusApiVersion

La versión de la API de Bus de servicio de la plantilla.

```
"serviceBusApiVersion": {
"type": "string"
}
```
## <a name="resources-to-deploy"></a>Recursos para implementar

Crea un espacio de nombres estándar de Bus de servicio de tipo **mensajería**, con el tema y suscripción y reglas.

```
 "resources": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "sku": {
            "name": "Standard",
            "tier": "Standard"
        },
        "resources": [{
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusTopicName')]",
            "type": "Topics",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusTopicName')]"
            },
            "resources": [{
                "apiVersion": "[variables('sbVersion')]",
                "name": "[parameters('serviceBusSubscriptionName')]",
                "type": "Subscriptions",
                "dependsOn": [
                    "[parameters('serviceBusTopicName')]"
                ],
                "properties": {},
                "resources": [{
                    "apiVersion": "[variables('sbVersion')]",
                    "name": "[parameters('serviceBusRuleName')]",
                    "type": "Rules",
                    "dependsOn": [
                        "[parameters('serviceBusSubscriptionName')]"
                    ],
                    "properties": {
                        "filter": {
                            "sqlExpression": "StoreName = 'Store1'"
                        },
                        "action": {
                            "sqlExpression": "set FilterTag = 'true'"
                        }
                    }
                }]
            }]
        }]
    }]
```

## <a name="commands-to-run-deployment"></a>Comandos para ejecutar la implementación

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```
New-AzureResourceGroupDeployment -Name \<deployment-name\> -ResourceGroupName \<resource-group-name\> -TemplateUri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-subscription-rule/azuredeploy.json>
```

## <a name="azure-cli"></a>CLI de Azure

```
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-subscription-rule/azuredeploy.json>
```

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha creado e implementado recursos mediante el Administrador de recursos de Azure, obtenga información sobre cómo administrar estos recursos, consulte estos artículos:

- [Administrar Bus de servicio de Azure mediante la automatización de Azure](service-bus-automation-manage.md)
- [Administrar Bus de servicio con PowerShell](service-bus-powershell-how-to-provision.md)
- [Administrar recursos de Bus de servicio con el Explorador de Bus de servicio](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)


  [Creación de plantillas de administrador de recursos de Azure]: ../resource-group-authoring-templates.md
  [Tutorial rápido Azure plantillas]: https://azure.microsoft.com/documentation/templates/?term=service+bus
  [Learn more about Service Bus topics and subscriptions]: service-bus-queues-topics-subscriptions.md
  [Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
  [Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
  [Convenciones de nomenclatura de recursos de Azure]: https://azure.microsoft.com/en-us/documentation/articles/guidance-naming-conventions/
  [Espacio de nombres de Bus de servicio con tema, la suscripción y la regla]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-topic-subscription-rule/
  [Suscripciones, temas y colas de Bus de servicio]:service-bus-queues-topics-subscriptions.md
  
