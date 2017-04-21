<properties
    pageTitle="Crear un espacio de nombres de Bus de servicio usando una plantilla de administrador de recursos | Microsoft Azure"
    description="Usar el Administrador de recursos de Azure plantilla para crear un espacio de nombres de Bus de servicio"
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
    ms.date="10/04/2016"
    ms.author="sethm;shvija"/>

# <a name="create-a-service-bus-namespace-using-an-azure-resource-manager-template"></a>Crear un espacio de nombres de Bus de servicio mediante una plantilla de administrador de recursos de Azure

En este artículo se describe cómo usar una plantilla de administrador de recursos de Azure que crea un espacio de nombres de Bus de servicio de tipo **mensajería** con un SKU estándar básico. El artículo también define los parámetros que se especifican para la ejecución de la implementación. Puede usar esta plantilla para sus propias implementaciones o personalizarlo para satisfacer sus necesidades.

Para obtener más información sobre la creación de plantillas, vea [plantillas de administrador de recursos de Azure de creación][].

Para la plantilla completa, vea la [plantilla de espacio de nombres de Bus de servicio][] en GitHub.

>[AZURE.NOTE] Las siguientes plantillas de administrador de recursos de Azure están disponibles para su descarga e implementación. 
>
>-    [Crear un espacio de nombres de evento Hubs con un grupo de concentrador de evento y comercial](../event-hubs/event-hubs-resource-manager-namespace-event-hub.md)
>-    [Crear un espacio de nombres de Bus de servicio con cola](service-bus-resource-manager-namespace-queue.md)
>-    [Crear un espacio de nombres de Bus de servicio con tema y suscripción](service-bus-resource-manager-namespace-topic.md)
>-    [Crear un espacio de nombres de Bus de servicio con cola y la autorización de regla](service-bus-resource-manager-namespace-auth-rule.md)
>
>Para comprobar si las últimas plantillas, visite la Galería de [Plantillas de Azure tutorial rápido][] y buscar Bus de servicio.

## <a name="what-will-you-deploy"></a>¿Qué va a distribuir?

Con esta plantilla se implementará un espacio de nombres de Bus de servicio con un [básica, estándar o Premium](https://azure.microsoft.com/pricing/details/service-bus/) SKU.

Para ejecutar la implementación automáticamente, haga clic en el botón siguiente:

[![Implementar en Azure](./media/service-bus-resource-manager-namespace/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-servicebus-create-namespace%2Fazuredeploy.json)

## <a name="parameters"></a>Parámetros

Con el Administrador de recursos de Azure, definir parámetros para los valores que desea especificar cuando se implementa la plantilla. La plantilla incluye una sección denominada `Parameters` que contiene todos los valores de parámetro. Debe definir un parámetro para los valores que varían en función del proyecto que está implementando o basado en el entorno de que implementación. Debe definir parámetros para los valores que se mantienen siempre el mismo. Cada valor de parámetro se utiliza en la plantilla para definir los recursos que se implementan.

Esta plantilla define los siguientes parámetros.

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

### <a name="servicebussku"></a>serviceBusSKU

El nombre de la [SKU](https://azure.microsoft.com/pricing/details/service-bus/) de Bus de servicio para crear.

```
"serviceBusSku": { 
    "type": "string", 
    "allowedValues": [ 
        "Basic", 
        "Standard",
        "Premium" 
    ], 
    "defaultValue": "Standard", 
    "metadata": { 
        "description": "The messaging tier for service Bus namespace" 
    } 

```

La plantilla define los valores que están permitidos para este parámetro (básico, estándar o Premium) y asigna un valor predeterminado (estándar) si no se especifica ningún valor.

Para obtener más información sobre precios de Bus de servicio, consulte [precios de Bus de servicio y facturación][].

### <a name="servicebusapiversion"></a>serviceBusApiVersion

La versión de la API de Bus de servicio de la plantilla.

```
"serviceBusApiVersion": { 
       "type": "string", 
       "defaultValue": "2015-08-01", 
       "metadata": { 
           "description": "Service Bus ApiVersion used by the template" 
       } 
```

## <a name="resources-to-deploy"></a>Recursos para implementar

### <a name="service-bus-namespace"></a>Espacio de nombres de Bus de servicio

Crea un espacio de nombres estándar de Bus de servicio de tipo **mensajería**.

```
"resources": [
    {
        "apiVersion": "[parameters('serviceBusApiVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "kind": "Messaging",
        "sku": {
            "name": "StandardSku",
            "tier": "Standard"
        },
        "properties": {
        }
    }
]
```

## <a name="commands-to-run-deployment"></a>Comandos para ejecutar la implementación

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

```
New-AzureRmResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json
```

### <a name="azure-cli"></a>CLI de Azure

```
azure config mode arm

azure group deployment create <my-resource-group> <my-deployment-name> --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json
```

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha creado e implementado recursos mediante el Administrador de recursos de Azure, obtenga información sobre cómo administrar estos recursos lea estos artículos:

- [Administrar Bus de servicio con PowerShell](service-bus-powershell-how-to-provision.md)
- [Administrar recursos de Bus de servicio con el Explorador de Bus de servicio](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)

  [Creación de plantillas de administrador de recursos de Azure]: ../resource-group-authoring-templates.md
  [Plantilla de espacio de nombres de Bus de servicio]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/
  [Tutorial rápido Azure plantillas]: https://azure.microsoft.com/documentation/templates/?term=service+bus
  [Bus de servicio de facturación y precios]: https://azure.microsoft.com/documentation/articles/service-bus-pricing-billing/
  [Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
  [Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
