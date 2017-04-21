<properties
    pageTitle="Crear recursos de Bus de servicio mediante plantillas de administrador de recursos de Azure | Microsoft Azure"
    description="Use las plantillas de administrador de recursos de Azure para automatizar la creación de recursos de Bus de servicio"
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
    ms.author="sethm"/>

# <a name="create-service-bus-resources-using-azure-resource-manager-templates"></a>Crear recursos de Bus de servicio mediante plantillas de administrador de recursos de Azure

En este artículo se describe cómo crear e implementar los recursos de Bus de servicio y eventos Hubs mediante plantillas, PowerShell y el proveedor de recursos de Bus de servicio de administrador de recursos de Azure.

Azure plantillas de administrador de recursos ayudan a definir los recursos para implementar una solución y especificar los parámetros y variables que le permiten valores de entrada para distintos entornos. La plantilla se compone de JSON y expresiones que puede usar para generar valores para su implementación. Para obtener información detallada sobre cómo escribir plantillas de administrador de recursos de Azure y una descripción de la plantilla con formato, vea [crear un administrador de recursos de Azure plantillas](../resource-group-authoring-templates.md). 

>[AZURE.NOTE] Los ejemplos de este artículo muestra cómo usar el Administrador de recursos de Azure para crear un espacio de nombres de Bus de servicio y de entidad mensajería (cola). Para obtener otros ejemplos de plantilla, visite la [Galería de plantillas de Azure tutorial rápido][] y busque "Bus de servicio".

## <a name="service-bus-and-event-hubs-resource-manager-templates"></a>Plantillas de Bus de servicio y el Administrador de recursos de evento Hubs

Estas plantillas de Bus de servicio y el Administrador de recursos de evento Hubs Azure están disponibles para su descarga e implementación. Haga clic en los siguientes vínculos para obtener más información acerca de cada uno, con vínculos a las plantillas en GitHub: 

- [Crear un espacio de nombres de Bus de servicio](service-bus-resource-manager-namespace.md)
- [Crear un espacio de nombres de Bus de servicio con cola](service-bus-resource-manager-namespace-queue.md)
- [Crear un espacio de nombres de Bus de servicio con tema y suscripción](service-bus-resource-manager-namespace-topic.md)
- [Crear un espacio de nombres de Bus de servicio con cola y la autorización de regla](service-bus-resource-manager-namespace-auth-rule.md)
- [Crear un espacio de nombres de evento Hubs con un grupo de concentrador de evento y comercial](../event-hubs/event-hubs-resource-manager-namespace-event-hub.md)

## <a name="deploy-with-powershell"></a>Implementar con PowerShell

El procedimiento siguiente describe cómo usar PowerShell para implementar una plantilla de administrador de recursos de Azure que crea un espacio de nombres de Bus de servicios de nivel **estándar** y una cola dentro de ese espacio de nombres. En este ejemplo se basa en la plantilla de [crear un espacio de nombres de Bus de servicio con cola](https://github.com/Azure/azure-quickstart-templates/tree/master/201-servicebus-create-queue) . El flujo de trabajo aproximada es la siguiente:

1. Instale PowerShell.
2. Crear la plantilla y (opcionalmente) un archivo de parámetros.
2. En PowerShell, inicie sesión en su cuenta de Azure.
3. Crear un nuevo grupo de recursos si no existe.
4. Probar la implementación.
5. Si lo desea, defina el modo de implementación.
6. Implementar la plantilla.

Para obtener información sobre cómo implementar plantillas de administrador de recursos de Azure, vea [implementar recursos con las plantillas de administrador de recursos de Azure][].

### <a name="install-powershell"></a>Instale PowerShell

Instalar Azure PowerShell siguiendo las instrucciones de [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md).

### <a name="create-a-template"></a>Crear una plantilla

Duplicar o copie la plantilla [201-servicebus-Crear-cola](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.json) de GitHub:

```
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "serviceBusNamespaceName": {
            "type": "string",
            "metadata": {
                "description": "Name of the Service Bus namespace"
            }
        },
        "serviceBusQueueName": {
            "type": "string",
            "metadata": {
                "description": "Name of the Queue"
            }
        },
        "serviceBusApiVersion": {
            "type": "string",
            "defaultValue": "2015-08-01",
            "metadata": {
                "description": "Service Bus ApiVersion used by the template"
            }
        }
    },
    "variables": {
        "location": "[resourceGroup().location]",
        "sbVersion": "[parameters('serviceBusApiVersion')]",
        "defaultSASKeyName": "RootManageSharedAccessKey",
        "authRuleResourceId": "[resourceId('Microsoft.ServiceBus/namespaces/authorizationRules', parameters('serviceBusNamespaceName'), variables('defaultSASKeyName'))]"
    },
    "resources": [{
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
                "path": "[parameters('serviceBusQueueName')]"
            }
        }]
    }],
    "outputs": {
        "NamespaceConnectionString": {
            "type": "string",
            "value": "[listkeys(variables('authRuleResourceId'), variables('sbVersion')).primaryConnectionString]"
        },
        "SharedAccessPolicyPrimaryKey": {
            "type": "string",
            "value": "[listkeys(variables('authRuleResourceId'), variables('sbVersion')).primaryKey]"
        }
    }
}
```

### <a name="create-a-parameters-file-optional"></a>Crear un archivo de parámetros (opcional)

Para usar un archivo de parámetros opcionales, copie el archivo de [201-servicebus-Crear-cola](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.parameters.json) . Reemplace el valor de `serviceBusNamespaceName` con el nombre del espacio de nombres de Bus de servicio que desea crear en esta implementación y reemplace el valor de `serviceBusQueueName` con el nombre de la cola que desea crear. 

```
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "serviceBusNamespaceName": {
            "value": "<myNamespaceName>"
        },
        "serviceBusQueueName": {
            "value": "<myQueueName>"
        },
        "serviceBusApiVersion": {
            "value": "2015-08-01"
        }
    }
}
```

Para obtener más información, vea el tema del [archivo de parámetros](../resource-group-template-deploy.md#parameter-file) .

### <a name="log-in-to-azure-and-set-the-azure-subscription"></a>Inicie sesión en Azure y configurar la suscripción de Azure

Desde un símbolo del sistema de PowerShell, ejecute el siguiente comando:

```
Login-AzureRmAccount
```

Se le pide que inicie sesión su cuenta de Azure. Después de iniciar sesión, ejecute el comando siguiente para ver sus suscripciones disponibles.

```
Get-AzureRMSubscription
```

Este comando devuelve una lista de suscripciones de Azure disponibles. Elija una suscripción para la sesión actual, ejecute el siguiente comando. Reemplazar `<YourSubscriptionId>` con el GUID de la suscripción de Azure que desea usar.

```
Set-AzureRmContext -SubscriptionID <YourSubscriptionId>
```

### <a name="set-the-resource-group"></a>Configurar el grupo de recursos

Si no tiene un grupo de recursos existente, cree un nuevo grupo de recursos con el comando **AzureRmResourceGroup de nuevo** . Proporcione el nombre del grupo de recursos y la ubicación que desee usar. Por ejemplo:

```
New-AzureRmResourceGroup -Name MyDemoRG -Location "West US"
```

Si es correcto, se muestra un resumen del nuevo grupo de recursos.

```
ResourceGroupName : MyDemoRG
Location          : westus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
```

### <a name="test-the-deployment"></a>Probar la implementación

Validar la implementación, ejecute el `Test-AzureRmResourceGroupDeployment` cmdlet. Cuando se prueba la implementación, proporcionar parámetros tal como lo haría al ejecutar la implementación.

```
Test-AzureRmResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="create-the-deployment"></a>Crear la implementación

Para crear la nueva implementación, ejecute el `New-AzureRmResourceGroupDeployment` comando y proporcione los parámetros es necesarios cuando se le solicite. Los parámetros incluyen un nombre para la implementación, el nombre de su grupo de recursos y la ruta de acceso o la dirección URL para el archivo de plantilla. Si no se especifica el parámetro de **modo** , se usa el valor predeterminado del **incremento** . Para obtener más información, vea [implementaciones completadas e Incremental](../resource-group-template-deploy.md#incremental-and-complete-deployments).

El comando siguiente le pide que especifique los tres parámetros en la ventana de PowerShell:

```
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

Para especificar un archivo de parámetros en su lugar, use el siguiente comando.

```
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

También puede usar parámetros en línea cuando se ejecuta el cmdlet de implementación. El comando es la siguiente:

```
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
```

Para ejecutar una implementación [completa](../resource-group-template-deploy.md#incremental-and-complete-deployments) , establezca el parámetro de **modo** **completado**:

```
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json 
```

### <a name="verify-the-deployment"></a>Comprobar la implementación

Si los recursos se implementan correctamente, se muestra un resumen de la implementación en la ventana de PowerShell:

```
DeploymentName    : MyDemoDeployment
ResourceGroupName : MyDemoRG
ProvisioningState : Succeeded
Timestamp         : 4/19/2016 10:38:30 PM
Mode              : Incremental
TemplateLink      :
Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    serviceBusNamespaceName  String             <namespaceName>
                    serviceBusQueueName  String                 <queueName>
                    serviceBusApiVersion  String                2015-08-01

```

## <a name="next-steps"></a>Pasos siguientes

Ya ha visto el flujo de trabajo básico y comandos para implementar una plantilla de administrador de recursos de Azure. Para obtener más información, visite los siguientes vínculos:

- [Introducción al administrador de recursos Azure][]
- [Implementar recursos con las plantillas de administrador de recursos de Azure][]
- [Creación de plantillas](../resource-group-authoring-templates.md)


[Introducción al administrador de recursos Azure]: ../resource-group-overview.md
[Implementar recursos con las plantillas de administrador de recursos de Azure]: ../resource-group-template-deploy.md
[Galería de plantillas de tutorial rápido de Azure]: https://azure.microsoft.com/documentation/templates/?term=service+bus