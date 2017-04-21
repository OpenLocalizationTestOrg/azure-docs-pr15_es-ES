<properties 
    pageTitle="Aprovisionar una caché Redis | Microsoft Azure" 
    description="Use el Administrador de recursos de Azure plantilla para implementar una caché Redis de Azure." 
    services="app-service" 
    documentationCenter="" 
    authors="steved0x" 
    manager="douge" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="web" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/27/2016" 
    ms.author="sdanie"/>

# <a name="create-a-redis-cache-using-a-template"></a>Crear una caché Redis usando una plantilla

En este tema, aprenderá a crear una plantilla de administrador de recursos de Azure que implementa una caché Redis de Azure. La caché puede usarse con una cuenta existente de almacenamiento para mantener los datos de diagnósticos. También aprenderá cómo definir los recursos que se implementan y cómo definir los parámetros que especifica cuando se ejecuta la implementación. Puede usar esta plantilla para sus propias implementaciones o personalizarlo para satisfacer sus necesidades.

Actualmente, se comparten opciones de diagnóstico para la caché de todos en la misma región para una suscripción. Actualizar una caché en la región afecta a todos los otro caché en la región.

Para obtener más información sobre la creación de plantillas, vea [Crear plantillas de administrador de recursos de Azure](../resource-group-authoring-templates.md).

Para la plantilla completa, vea [plantilla Redis caché](https://github.com/Azure/azure-quickstart-templates/blob/master/101-redis-cache/azuredeploy.json).

>[AZURE.NOTE] Plantillas de administrador de recursos para el nuevo [nivel de Premium](cache-premium-tier-intro.md) están disponibles. 
>
>-    [Crear una caché de Redis Premium con clústeres](https://azure.microsoft.com/documentation/templates/201-redis-premium-cluster-diagnostics/)
>-    [Crear Premium Redis caché con persistencia de datos](https://azure.microsoft.com/documentation/templates/201-redis-premium-persistence/)
>-    [Crear Premium Redis caché con VNet y clústeres opcional](https://azure.microsoft.com/documentation/templates/201-redis-premium-vnet-cluster-diagnostics/)
>
>Para comprobar si las últimas plantillas, vea [Plantillas de Azure tutorial rápido](https://azure.microsoft.com/documentation/templates/) y busque `Redis Cache`.

## <a name="what-you-will-deploy"></a>¿Qué se va a implementar

En esta plantilla, se implementa una caché Redis Azure que usa una cuenta existente de almacenamiento de datos de diagnóstico.

Para ejecutar la implementación automáticamente, haga clic en el botón siguiente:

[![Implementar en Azure](./media/cache-redis-cache-arm-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## <a name="parameters"></a>Parámetros

Con el Administrador de recursos de Azure, definir parámetros para los valores que desea especificar cuando se implementa la plantilla. La plantilla incluye una sección denominada parámetros que contiene todos los valores de parámetro.
Debe definir un parámetro para los valores que varían en función del proyecto que está implementando o basado en el entorno de que implementación. Debe definir parámetros para los valores que siempre se mantienen. Cada valor de parámetro se utiliza en la plantilla para definir los recursos que se implementan. 


[AZURE.INCLUDE [app-service-web-deploy-redis-parameters](../../includes/cache-deploy-parameters.md)]

### <a name="rediscachelocation"></a>redisCacheLocation

La ubicación de la caché Redis. Para mejorar el rendimiento, use la misma ubicación como la aplicación para utilizar con la memoria caché.

    "redisCacheLocation": {
      "type": "string"
    }

### <a name="existingdiagnosticsstorageaccountname"></a>existingDiagnosticsStorageAccountName

El nombre de la cuenta de almacenamiento existente para diagnósticos. 

    "existingDiagnosticsStorageAccountName": {
      "type": "string"
    }

### <a name="enablenonsslport"></a>enableNonSslPort

Un valor boolean que indica si se debe permitir el acceso a través de puertos no SSL.

    "enableNonSslPort": {
      "type": "bool"
    }

### <a name="diagnosticsstatus"></a>diagnosticsStatus

Un valor que indica si está habilitado diagnósticos. Usar activado o desactivado.

    "diagnosticsStatus": {
      "type": "string",
      "defaultValue": "ON",
      "allowedValues": [
            "ON",
            "OFF"
        ]
    }
    
## <a name="resources-to-deploy"></a>Recursos para implementar

### <a name="redis-cache"></a>Redis caché

Crea la caché Redis Azure.

    {
      "apiVersion": "2015-08-01",
      "name": "[parameters('redisCacheName')]",
      "type": "Microsoft.Cache/Redis",
      "location": "[parameters('redisCacheLocation')]",
      "properties": {
        "enableNonSslPort": "[parameters('enableNonSslPort')]",
        "sku": {
          "capacity": "[parameters('redisCacheCapacity')]",
          "family": "[parameters('redisCacheFamily')]",
          "name": "[parameters('redisCacheSKU')]"
        }
      },
      "resources": [
        {
          "apiVersion": "2015-07-01",
          "type": "Microsoft.Cache/redis/providers/diagnosticsettings",
          "name": "[concat(parameters('redisCacheName'), '/Microsoft.Insights/service')]",
          "location": "[parameters('redisCacheLocation')]",
          "dependsOn": [
            "[concat('Microsoft.Cache/Redis/', parameters('redisCacheName'))]"
          ],
          "properties": {
            "status": "[parameters('diagnosticsStatus')]",
            "storageAccountName": "[parameters('existingDiagnosticsStorageAccountName')]"
          }
        }
      ]
    }



## <a name="commands-to-run-deployment"></a>Comandos para ejecutar la implementación

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)] 

### <a name="powershell"></a>PowerShell

    New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -ResourceGroupName ExampleDeployGroup -redisCacheName ExampleCache -redisCacheLocation "West US"

### <a name="azure-cli"></a>CLI de Azure

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -g ExampleDeployGroup


