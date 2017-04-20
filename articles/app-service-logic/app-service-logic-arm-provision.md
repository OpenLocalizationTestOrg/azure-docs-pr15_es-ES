<properties 
    pageTitle="Crear una aplicación de la lógica de uso de plantillas de administrador de recursos de Azure en la aplicación de servicio de Azure | Microsoft Azure" 
    description="Usar una plantilla de administrador de recursos de Azure para implementar una aplicación de lógica vacío para definir los flujos de trabajo." 
    services="logic-apps" 
    documentationCenter="" 
    authors="MSFTMan" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="logic-apps" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/25/2016" 
    ms.author="deonhe"/>

# <a name="create-a-logic-app-using-a-template"></a>Crear una aplicación de lógica usando una plantilla

Usar una plantilla de administrador de recursos de Azure para crear una aplicación de lógica vacía que puede utilizarse para definir los flujos de trabajo. Puede definir los recursos que se implementan y cómo definir los parámetros que se especifican cuando se ejecuta la implementación. Puede usar esta plantilla para sus propias implementaciones o personalizarlo para satisfacer sus necesidades.

Para obtener más detalles sobre las propiedades de la aplicación de lógica, vea [API de administración de flujo de trabajo de lógica de aplicación](https://msdn.microsoft.com/library/azure/mt643788.aspx). 

Para obtener ejemplos de la definición de sí misma, vea [las definiciones de aplicación de la lógica de autor](app-service-logic-author-definitions.md). 

Para obtener más información sobre la creación de plantillas, vea [Crear plantillas de administrador de recursos de Azure](../resource-group-authoring-templates.md).

Para la plantilla completa, vea la [plantilla de aplicación de la lógica](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json).

## <a name="what-you-will-deploy"></a>¿Qué se va a implementar

Con esta plantilla, implemente una aplicación de lógica.

Para ejecutar la implementación automáticamente, seleccione el botón siguiente:  

[![Implementar en Azure](media/app-service-logic-arm-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

## <a name="parameters"></a>Parámetros

[AZURE.INCLUDE [app-service-logic-deploy-parameters](../../includes/app-service-logic-deploy-parameters.md)]

### <a name="testuri"></a>testUri

     "testUri": {
        "type": "string",
        "defaultValue": "http://azure.microsoft.com/en-us/status/feed/"
      }
    
## <a name="resources-to-deploy"></a>Recursos para implementar

### <a name="logic-app"></a>Aplicación de lógica

Crea la aplicación de la lógica.

Las plantillas se usa un valor de parámetro para el nombre de la aplicación de lógica. Establece la ubicación de la aplicación de la lógica en la misma ubicación que el grupo de recursos. 

Esta definición concreto ejecuta una vez cada hora y hace ping a la ubicación especificada en el parámetro **testUri** . 

    {
      "type": "Microsoft.Logic/workflows",
      "apiVersion": "2016-06-01",
      "name": "[parameters('logicAppName')]",
      "location": "[resourceGroup().location]",
      "tags": {
        "displayName": "LogicApp"
      },
      "properties": {
        "definition": {
          "$schema": "http://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "testURI": {
              "type": "string",
              "defaultValue": "[parameters('testUri')]"
            }
          },
          "triggers": {
            "recurrence": {
              "type": "recurrence",
              "recurrence": {
                "frequency": "Hour",
                "interval": 1
              }
            }
          },
          "actions": {
            "http": {
              "type": "Http",
              "inputs": {
                "method": "GET",
                "uri": "@parameters('testUri')"
              },
              "runAfter": {}
            }
          },
          "outputs": {}
        },
        "parameters": {}
      }
    }


## <a name="commands-to-run-deployment"></a>Comandos para ejecutar la implementación

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

    New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -ResourceGroupName ExampleDeployGroup

### <a name="azure-cli"></a>CLI de Azure

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -g ExampleDeployGroup


 
