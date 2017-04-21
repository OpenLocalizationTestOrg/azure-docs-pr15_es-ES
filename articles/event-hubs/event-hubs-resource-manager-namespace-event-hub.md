<properties
    pageTitle="Crear un espacio de nombres de evento Hubs con grupo concentrador de evento y consumidor mediante una plantilla de administrador de recursos de Azure | Microsoft Azure"
    description="Crear un espacio de nombres de evento Hubs con concentrador de evento y un grupo de consumidor con plantilla de administrador de recursos de Azure"
    services="event-hubs"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.devlang="tbd"
    ms.topic="article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="08/31/2016"
    ms.author="sethm;shvija"/>

# <a name="create-an-event-hubs-namespace-with-event-hub-and-consumer-group-using-an-azure-resource-manager-template"></a>Crear un espacio de nombres de evento Hubs con grupo concentrador de evento y consumidor mediante una plantilla de administrador de recursos de Azure

Este artículo le muestra cómo usar una plantilla de administrador de recursos de Azure que crea un espacio de nombres de evento Hubs con un concentrador de evento y un grupo de consumidores. Aprenderá cómo definir los recursos que se implementan y cómo definir los parámetros que especifica cuando se ejecuta la implementación. Puede usar esta plantilla para sus propias implementaciones o personalizarlo para satisfacer sus necesidades

Para obtener más información sobre la creación de plantillas, vea [plantillas de administrador de recursos de Azure de creación][].

Para la plantilla completa, vea la [plantilla de grupo de consumidor y concentrador de evento][] en GitHub.

>[AZURE.NOTE]
>Para comprobar si las últimas plantillas, visite la Galería de [Plantillas de Azure tutorial rápido][] y buscar Hubs de evento.

## <a name="what-will-you-deploy"></a>¿Qué va a distribuir?

Con esta plantilla se implementará un espacio de nombres de evento Hubs con un concentrador de evento y un grupo de consumidores.

[Evento Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) es un evento servicio utilizado para proporcionar entrada de evento y telemetría a Azure a escala masiva, con baja latencia y alta confiabilidad de procesamiento.

Para ejecutar la implementación automáticamente, haga clic en el botón siguiente:

[![Implementar en Azure](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

## <a name="parameters"></a>Parámetros

Con el Administrador de recursos de Azure, definir parámetros para los valores que desea especificar cuando se implementa la plantilla. La plantilla incluye una sección denominada `Parameters` que contiene todos los valores de parámetro. Debe definir un parámetro para los valores que varían en función del proyecto que está implementando o basado en el entorno de que implementación. Debe definir parámetros para los valores que se mantienen siempre el mismo. Cada valor de parámetro se utiliza en la plantilla para definir los recursos que se implementan.

La plantilla define los siguientes parámetros.

### <a name="eventhubnamespacename"></a>eventHubNamespaceName

El nombre del espacio de nombres Hubs de evento para crear.

```
"eventHubNamespaceName": {
"type": "string"
}
```

### <a name="eventhubname"></a>eventHubName

El nombre del concentrador de evento en el evento creado Hubs espacio de nombres.

```
"eventHubName": {
"type": "string"
}
```

### <a name="eventhubconsumergroupname"></a>eventHubConsumerGroupName

El nombre del grupo de consumidores creado para el concentrador de eventos.

```
"eventHubConsumerGroupName": {
"type": "string"
}
```

### <a name="apiversion"></a>apiVersion

La versión de la API de la plantilla.

```
"apiVersion": {
"type": "string"
}
```

## <a name="resources-to-deploy"></a>Recursos para implementar

Crea un espacio de nombres del tipo **EventHubs**, con un concentrador de evento y un grupo de consumidores.

```
"resources":[  
      {  
         "apiVersion":"[variables('ehVersion')]",
         "name":"[parameters('namespaceName')]",
         "type":"Microsoft.EventHub/Namespaces",
         "location":"[variables('location')]",
         "sku":{  
            "name":"Standard",
            "tier":"Standard"
         },
         "resources":[  
            {  
               "apiVersion":"[variables('ehVersion')]",
               "name":"[parameters('eventHubName')]",
               "type":"EventHubs",
               "dependsOn":[  
                  "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
               ],
               "properties":{  
                  "path":"[parameters('eventHubName')]"
               },
               "resources":[  
                  {  
                     "apiVersion":"[variables('ehVersion')]",
                     "name":"[parameters('consumerGroupName')]",
                     "type":"ConsumerGroups",
                     "dependsOn":[  
                        "[parameters('eventHubName')]"
                     ],
                     "properties":{  

                     }
                  }
               ]
            }
         ]
      }
   ],
```

## <a name="commands-to-run-deployment"></a>Comandos para ejecutar la implementación

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json
```

## <a name="azure-cli"></a>CLI de Azure

```
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json][]
```

[Creación de plantillas de administrador de recursos de Azure]: ../resource-group-authoring-templates.md
[Tutorial rápido Azure plantillas]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Plantilla de grupo de concentrador y consumidor de evento]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/
