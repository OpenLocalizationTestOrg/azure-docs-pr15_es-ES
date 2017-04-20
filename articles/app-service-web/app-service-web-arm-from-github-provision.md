<properties 
    pageTitle="Implementar una aplicación web que está vinculada a un repositorio de GitHub" 
    description="Usar una plantilla de administrador de recursos de Azure para implementar una aplicación web que contiene un proyecto desde un repositorio de GitHub." 
    services="app-service" 
    documentationCenter="" 
    authors="cephalin" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/27/2016" 
    ms.author="cephalin"/>

# <a name="deploy-a-web-app-linked-to-a-github-repository"></a>Implementar una aplicación web vinculada a un repositorio de GitHub

En este tema, obtendrá información sobre cómo crear una plantilla de administrador de recursos de Azure que implementa una aplicación web que está vinculada a un proyecto en un repositorio de GitHub. Aprenderá cómo definir los recursos que se implementan y cómo definir los parámetros que especifica cuando se ejecuta la implementación. Puede usar esta plantilla para sus propias implementaciones o personalizarlo para satisfacer sus necesidades.

Para obtener más información sobre la creación de plantillas, vea [Crear plantillas de administrador de recursos de Azure](../resource-group-authoring-templates.md).

Para la plantilla completa, consulte [Web App vinculada a la plantilla de GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-github-deploy/azuredeploy.json).

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="what-you-will-deploy"></a>¿Qué se va a implementar

Con esta plantilla se implemente una aplicación web que contiene el código de un proyecto en GitHub.

Para ejecutar la implementación automáticamente, haga clic en el botón siguiente:

[![Implementar en Azure](./media/app-service-web-arm-from-github-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-github-deploy%2Fazuredeploy.json)

## <a name="parameters"></a>Parámetros

[AZURE.INCLUDE [app-service-web-deploy-web-parameters](../../includes/app-service-web-deploy-web-parameters.md)]

### <a name="repourl"></a>repoURL

La dirección URL de repositorio de GitHub que contiene el proyecto para implementar. Este parámetro contiene un valor predeterminado, pero este valor solo está destinada a mostrar cómo proporcionar la dirección URL de repositorio. Puede usar este valor para probar la plantilla pero desea proporcionar la dirección URL de su propio repositorio cuando se trabaja con la plantilla.

    "repoURL": {
        "type": "string",
        "defaultValue": "https://github.com/davidebbo-test/Mvc52Application.git"
    }

### <a name="branch"></a>rama

Rama del repositorio para usar cuando se implementa la aplicación. El valor predeterminado es principal, pero puede proporcionar el nombre de cualquier rama del repositorio que desea instalar.

    "branch": {
        "type": "string",
        "defaultValue": "master"
    }
    
## <a name="resources-to-deploy"></a>Recursos para implementar

[AZURE.INCLUDE [app-service-web-deploy-web-host](../../includes/app-service-web-deploy-web-host.md)]

### <a name="web-app"></a>Aplicación Web

Crea la aplicación web que está vinculada al proyecto en GitHub. 

Especifique el nombre de la aplicación web mediante el parámetro de **nombre del sitio** y la ubicación de la aplicación web mediante el parámetro **siteLocation** . En el elemento **dependsOn** , la plantilla define la aplicación web como depende del servicio de hospedaje plan. Dado que es depende del plan de hospedaje, no se crea la aplicación web hasta que haya finalizado el plan de hospedaje se ha creado. El elemento **dependsOn** solo se usa para especificar el orden de implementación. Si no se marca la aplicación web como depende del plan de hospedaje, Administrador de recursos de Azure intentará crear ambos recursos al mismo tiempo y puede recibir un error si la aplicación web se crea antes el plan de hospedaje.

La aplicación web también tiene un recurso secundario que se define en la sección **recursos** a continuación. Este recurso secundario define el control de código fuente para el proyecto implementado con la aplicación web. En esta plantilla, el control de código fuente está vinculado a un repositorio de GitHub determinado. Se define el repositorio de GitHub con el código **"RepoUrl": "https://github.com/davidebbo-test/Mvc52Application.git"** podría codificar la dirección URL de repositorio cuando desee crear una plantilla que implementa varias veces un solo proyecto mientras que requieren el número mínimo de parámetros.
En lugar de codificar la dirección URL de repositorio, puede agregar un parámetro de la dirección URL de repositorio y usar ese valor de la propiedad **RepoUrl** .

    {
      "apiVersion": "2015-08-01",
      "name": "[parameters('siteName')]",
      "type": "Microsoft.Web/sites",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
      ],
      "properties": {
        "serverFarmId": "[parameters('hostingPlanName')]"
      },
      "resources": [
        {
          "apiVersion": "2015-08-01",
          "name": "web",
          "type": "sourcecontrols",
          "dependsOn": [
            "[resourceId('Microsoft.Web/Sites', parameters('siteName'))]"
          ],
          "properties": {
            "RepoUrl": "[parameters('repoURL')]",
            "branch": "[parameters('branch')]",
            "IsManualIntegration": true
          }
        }
      ]
    }

## <a name="commands-to-run-deployment"></a>Comandos para ejecutar la implementación

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

    New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-github-deploy/azuredeploy.json -siteName ExampleSite -hostingPlanName ExamplePlan -siteLocation "West US" -ResourceGroupName ExampleDeployGroup

### <a name="azure-cli"></a>CLI de Azure

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-github-deploy/azuredeploy.json


 
