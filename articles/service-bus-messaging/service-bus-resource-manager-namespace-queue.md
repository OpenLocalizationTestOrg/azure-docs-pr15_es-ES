<properties
    pageTitle="Crear un espacio de nombres de Bus de servicio con cola con una plantilla de administrador de recursos de Azure | Microsoft Azure"
    description="Crear un espacio de nombres de Bus de servicio y de una cola con el Administrador de recursos de Azure plantilla"
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="tbd"
    ms.topic="article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="10/14/2016"
    ms.author="sethm;shvija"/>

# <a name="create-a-service-bus-namespace-and-a-queue-using-an-azure-resource-manager-template"></a>Crear un espacio de nombres de Bus de servicio y de una cola con una plantilla de administrador de recursos de Azure

Este artículo le muestra cómo usar una plantilla de administrador de recursos de Azure que crea un espacio de nombres de Bus de servicio y de una cola. Aprenderá cómo definir los recursos que se implementan y cómo definir los parámetros que especifica cuando se ejecuta la implementación. Puede usar esta plantilla para sus propias implementaciones o personalizarlo para satisfacer sus necesidades.

Para obtener más información sobre la creación de plantillas, vea [plantillas de administrador de recursos de Azure de creación][].

Para la plantilla completa, vea la [plantilla de espacio de nombres y cola de Bus de servicio][] en GitHub.

>[AZURE.NOTE] Las siguientes plantillas de administrador de recursos de Azure están disponibles para su descarga e implementación.
>
>-    [Crear un espacio de nombres de Bus de servicio con cola y la autorización de regla](service-bus-resource-manager-namespace-auth-rule.md)
>-    [Crear un espacio de nombres de Bus de servicio con tema y suscripción](service-bus-resource-manager-namespace-topic.md)
>-    [Crear un espacio de nombres de Bus de servicio](service-bus-resource-manager-namespace.md)
>-    [Crear un espacio de nombres de evento Hubs con un grupo de concentrador de evento y comercial](../event-hubs/event-hubs-resource-manager-namespace-event-hub.md)
>
>Para comprobar si las últimas plantillas, visite la Galería de [Plantillas de Azure tutorial rápido][] y busque "Bus de servicio".

## <a name="what-will-you-deploy"></a>¿Qué va a distribuir?

Con esta plantilla se implementará un espacio de nombres de Bus de servicio con una cola.

[Colas de Bus de servicio](service-bus-queues-topics-subscriptions.md#queues) ofrecen primera en, entrega de mensajes de la primera salida (FIFO) a uno o varios de los consumidores competencia.

Para ejecutar la implementación automáticamente, haga clic en el botón siguiente:

[![Implementar en Azure](./media/service-bus-resource-manager-namespace-queue/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-queue%2Fazuredeploy.json)

## <a name="parameters"></a>Parámetros

Con el Administrador de recursos de Azure, definir parámetros para los valores que desea especificar cuando se implementa la plantilla. La plantilla incluye una sección denominada `Parameters` que contiene todos los valores de parámetro. Debe definir un parámetro para los valores que varían en función del proyecto que está implementando o basado en el entorno de que implementación. Debe definir parámetros para los valores que se mantienen siempre el mismo. Cada valor de parámetro se utiliza en la plantilla para definir los recursos que se implementan.

La plantilla define los siguientes parámetros.

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName

El nombre del espacio de nombres de Bus de servicio para crear.

```
"serviceBusNamespaceName": {
"type": "string",
"metadata": { 
    "description": "Name of the Service Bus namespace" 
    }
}
```

### <a name="servicebusqueuename"></a>serviceBusQueueName

El nombre de la cola creada en el espacio de nombres de Bus de servicio.

```
"serviceBusQueueName": {
"type": "string"
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

Crea un espacio de nombres estándar de Bus de servicio de tipo **mensajería**, con una cola.

```
"resources ": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "kind": "Messaging",
        "sku": {
            "name": "StandardSku",
            "tier": "Standard"
        },
        "resources": [{
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusQueueName')]",
            "type": "Queues",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusQueueName')]",
            }
        }]
    }]
```

## <a name="commands-to-run-deployment"></a>Comandos para ejecutar la implementación

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-queue/azuredeploy.json>
```

## <a name="azure-cli"></a>CLI de Azure

```
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-queue/azuredeploy.json>
```

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha creado e implementado recursos mediante el Administrador de recursos de Azure, obtenga información sobre cómo administrar estos recursos, consulte estos artículos:

- [Administrar Bus de servicio con PowerShell](service-bus-powershell-how-to-provision.md)
- [Administrar recursos de Bus de servicio con el Explorador de Bus de servicio](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)


  [Creación de plantillas de administrador de recursos de Azure]: ../resource-group-authoring-templates.md
  [Plantilla de espacio de nombres y cola de Bus de servicio]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/
  [Tutorial rápido Azure plantillas]: https://azure.microsoft.com/documentation/templates/?term=service+bus
  [Learn more about Service Bus queues]: service-bus-queues-topics-subscriptions.md
  [Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
  [Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
