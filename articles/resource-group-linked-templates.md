<properties
   pageTitle="Vinculado con el Administrador de recursos de plantillas | Microsoft Azure"
   description="Describe cómo utilizar plantillas vinculadas en una plantilla de administrador de recursos de Azure para crear una solución modular plantilla. Muestra cómo pasar valores de parámetros, especifique un archivo de parámetros y direcciones URL creadas de forma dinámica."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/02/2016"
   ms.author="tomfitz"/>

# <a name="using-linked-templates-with-azure-resource-manager"></a>Uso de plantillas vinculadas con el Administrador de recursos de Azure

Desde dentro de una plantilla de administrador de recursos de Azure, puede vincular a otra plantilla, que le permite descomponer la implementación en un conjunto de destino, plantillas específicas de finalidad. Al igual que con desglosar una aplicación en varias clases de código, descomposición ofrece ventajas en cuanto a probar, reutilización y legibilidad.  

Puede pasar parámetros a una plantilla vinculada desde una plantilla principal y los parámetros pueden asignar directamente a parámetros o variables expuestas por la plantilla de la llamada. La plantilla vinculada también puede pasar una variable de salida a la plantilla de origen, lo que permite un intercambio de datos bidireccional entre las plantillas.

## <a name="linking-to-a-template"></a>Vincular a una plantilla

Crear un vínculo entre dos plantillas agregando un recurso de implementación dentro de la plantilla principal que apunta a la plantilla vinculada. Establezca la propiedad **templateLink** en el URI de la plantilla vinculada. Puede proporcionar los valores de parámetro de la plantilla vinculada, puede especificar los valores directamente en la plantilla o vincular a un archivo de parámetros. En el ejemplo siguiente se utiliza la propiedad de **parámetros** para especificar un valor de parámetro directamente.

    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
         "name": "linkedTemplate", 
         "type": "Microsoft.Resources/deployments", 
         "properties": { 
           "mode": "incremental", 
           "templateLink": {
              "uri": "https://www.contoso.com/AzureTemplates/newStorageAccount.json",
              "contentVersion": "1.0.0.0"
           }, 
           "parameters": { 
              "StorageAccountName":{"value": "[parameters('StorageAccountName')]"} 
           } 
         } 
      } 
    ] 

El servicio de administrador de recursos debe tener acceso a la plantilla vinculada. No puede especificar un archivo local o un archivo que solo está disponible en su red local para la plantilla vinculada. Solo puede proporcionar un valor URI que incluye **http** o **https**. Una opción es la plantilla vinculada en una cuenta de almacenamiento y usar el URI para ese elemento, tal como se muestra en el ejemplo siguiente.

    "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0",
    }

Aunque la plantilla vinculada debe estar disponible externamente, no es necesario que esté disponible públicamente al público. Puede agregar la plantilla a una cuenta de almacenamiento privado que es accesible para solo el propietario de la cuenta de almacenamiento. A continuación, cree un token de firma (SA) de acceso compartido para habilitar el acceso durante la implementación. Agregar ese token SA al URI de la plantilla vinculada. Para conocer los pasos sobre cómo configurar una plantilla en una cuenta de almacenamiento y generar un token SA, vea [implementar recursos a las plantillas de administrador de recursos y Azure PowerShell](resource-group-template-deploy.md) o [implementar con plantillas de administrador de recursos y CLI de Azure](resource-group-template-deploy-cli.md). 

En el ejemplo siguiente se muestra una plantilla padre vincula con otra plantilla. Se tiene acceso a la plantilla vinculada con un símbolo de SA que se pasa como un parámetro.

    "parameters": {
        "sasToken": { "type": "securestring" }
    },
    "resources": [
        {
            "apiVersion": "2015-01-01",
            "name": "linkedTemplate",
            "type": "Microsoft.Resources/deployments",
            "properties": {
              "mode": "incremental",
              "templateLink": {
                "uri": "[concat('https://storagecontosotemplates.blob.core.windows.net/templates/helloworld.json', parameters('sasToken'))]",
                "contentVersion": "1.0.0.0"
              }
            }
        }
    ],

Aunque el token se pasa como una cadena segura, el URI de la plantilla vinculada, incluido el token SA, se registra en las operaciones de implementación para dicho grupo de recursos. Para limitar la exposición, establezca una caducidad para el token.

## <a name="linking-to-a-parameter-file"></a>Vincular a un archivo de parámetros

En el ejemplo siguiente se utiliza la propiedad **parametersLink** para vincular a un archivo de parámetros.

    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
         "name": "linkedTemplate", 
         "type": "Microsoft.Resources/deployments", 
         "properties": { 
           "mode": "incremental", 
           "templateLink": {
              "uri":"https://www.contoso.com/AzureTemplates/newStorageAccount.json",
              "contentVersion":"1.0.0.0"
           }, 
           "parametersLink": { 
              "uri":"https://www.contoso.com/AzureTemplates/parameters.json",
              "contentVersion":"1.0.0.0"
           } 
         } 
      } 
    ] 

El valor URI para el archivo vinculado de parámetro no puede ser un archivo local y debe incluir **http** o **https**. El archivo de parámetros también puede limitar acceso a través de un símbolo de SA.

## <a name="using-variables-to-link-templates"></a>Utilizar variables para vincular plantillas

Los ejemplos anteriores mostraron los valores de dirección URL codificada de los vínculos de la plantilla. Este enfoque puede funcionar en una plantilla simple pero no funciona bien cuando se trabaja con un amplio conjunto de plantillas modulares. En su lugar, puede crear una variable estática que almacena una dirección URL base para la plantilla principal y crear dinámicamente direcciones URL para las plantillas de vinculado desde esa dirección URL base. La ventaja de este enfoque es fácilmente puede mover o la plantilla se bifurque porque solo tiene que cambiar la variable estática de la plantilla principal. La plantilla principal pasa a los URI correcto a lo largo de la plantilla descompuesto.

En el ejemplo siguiente se muestra cómo usar una dirección URL base para crear dos direcciones URL para plantillas vinculadas (**sharedTemplateUrl** y **vmTemplate**). 

    "variables": {
        "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
        "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
        "tshirtSizeSmall": {
            "vmSize": "Standard_A1",
            "diskSize": 1023,
            "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]",
            "vmCount": 2,
            "slaveCount": 1,
            "storage": {
                "name": "[parameters('storageAccountNamePrefix')]",
                "count": 1,
                "pool": "db",
                "map": [0,0],
                "jumpbox": 0
            }
        }
    }

También puede usar [deployment()](resource-group-template-functions.md#deployment) para obtener la dirección URL base para la plantilla actual y utilice para obtener la dirección URL para otras plantillas en la misma ubicación. Este enfoque es útil si cambia la ubicación de la plantilla (quizá debido a un control de versiones), o desea evitar codificar las direcciones URL en el archivo de plantilla. 

    "variables": {
        "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
    }

## <a name="conditionally-linking-to-templates"></a>Vinculación condicionalmente a plantillas

Puede vincular a plantillas diferentes pasando un valor de parámetro que se utiliza para construir el URI de la plantilla vinculada. Este método funciona bien cuando necesita especificar durante la implementación que vinculados de la plantilla que desea utilizar. Por ejemplo, puede especificar una plantilla para una cuenta existente de almacenamiento y otra plantilla para una nueva cuenta de almacenamiento.

En el ejemplo siguiente se muestra un parámetro para un nombre de cuenta de almacenamiento y un parámetro para especificar si la cuenta de almacenamiento es nueva o existente.

    "parameters": {
        "storageAccountName": {
            "type": "String"
        },
        "newOrExisting": {
            "type": "String",
            "allowedValues": [
                "new",
                "existing"
            ]
        }
    },

Crear una variable para la plantilla URI que incluye el valor del parámetro nuevo o existente.

    "variables": {
        "templatelink": "[concat('https://raw.githubusercontent.com/exampleuser/templates/master/',parameters('newOrExisting'),'StorageAccount.json')]"
    },

Para proporcionar ese valor de la variable de los recursos de implementación.

    "resources": [
        {
            "apiVersion": "2015-01-01",
            "name": "linkedTemplate",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "incremental",
                "templateLink": {
                    "uri": "[variables('templatelink')]",
                    "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "StorageAccountName": {
                        "value": "[parameters('storageAccountName')]"
                    }
                }
            }
        }
    ],

La URI se convierte en una plantilla con el nombre **existingStorageAccount.json** o **newStorageAccount.json**. Crear plantillas para los URI.

En el ejemplo siguiente se muestra la plantilla **existingStorageAccount.json** .

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "storageAccountName": {
          "type": "String"
        }
      },
      "variables": {},
      "resources": [],
      "outputs": {
        "storageAccountInfo": {
          "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')),providers('Microsoft.Storage', 'storageAccounts').apiVersions[0])]",
          "type" : "object"
        }
      }
    }

En el ejemplo siguiente se muestra la plantilla de **newStorageAccount.json** . Observe que como almacenamiento existente se devuelve el objeto de la cuenta de almacenamiento de plantilla de cuenta en los resultados. La plantilla maestra funciona con cualquier plantilla vinculada.

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "storageAccountName": {
          "type": "string"
        }
      },
      "resources": [
        {
          "type": "Microsoft.Storage/storageAccounts",
          "name": "[parameters('StorageAccountName')]",
          "apiVersion": "2016-01-01",
          "location": "[resourceGroup().location]",
          "sku": {
            "name": "Standard_LRS"
          },
          "kind": "Storage",
          "properties": {
          }
        }
      ],
      "outputs": {
        "storageAccountInfo": {
          "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('StorageAccountName')),providers('Microsoft.Storage', 'storageAccounts').apiVersions[0])]",
          "type" : "object"
        }
      }
    }

## <a name="complete-example"></a>Ejemplo completo

Las siguientes plantillas de ejemplo muestran una organización simplificada de plantillas vinculadas para ilustrar algunos de los conceptos de este artículo. Se supone que se han agregado las plantillas al mismo contenedor en una cuenta de almacenamiento con acceso público desactivado. La plantilla vinculada pasa un valor a la plantilla principal en la sección de **resultados** .

El archivo **parent.json** está compuesto por:

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "containerSasToken": { "type": "string" }
      },
      "resources": [
        {
          "apiVersion": "2015-01-01",
          "name": "linkedTemplate",
          "type": "Microsoft.Resources/deployments",
          "properties": {
            "mode": "incremental",
            "templateLink": {
              "uri": "[concat(uri(deployment().properties.templateLink.uri, 'helloworld.json'), parameters('containerSasToken'))]",
              "contentVersion": "1.0.0.0"
            }
          }
        }
      ],
      "outputs": {
        "result": {
          "type": "object",
          "value": "[reference('linkedTemplate').outputs.result]"
        }
      }
    }

El archivo **helloworld.json** está compuesto por:

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "variables": {},
      "resources": [],
      "outputs": {
        "result": {
            "value": "Hello World",
            "type" : "string"
        }
      }
    }
    
En PowerShell, obtener un símbolo para el contenedor e implementar las plantillas con:

    Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
    $token = New-AzureStorageContainerSASToken -Name templates -Permission r -ExpiryTime (Get-Date).AddMinutes(30.0)
    New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri ("https://storagecontosotemplates.blob.core.windows.net/templates/parent.json" + $token) -containerSasToken $token

En Azure CLI, obtener un símbolo para el contenedor e implementar las plantillas con el código siguiente. Actualmente, debe proporcionar un nombre para la implementación cuando se usa una plantilla de URI que incluye un símbolo de SA.  

    expiretime=$(date -I'minutes' --date "+30 minutes")  
    azure storage container sas create --container templates --permissions r --expiry $expiretime --json | jq ".sas" -r
    azure group deployment create -g ExampleGroup --template-uri "https://storagecontosotemplates.blob.core.windows.net/templates/parent.json?{token}" -n tokendeploy  

Le pide que proporcione el token de SA como parámetro. Debe comenzar el token con **?**.

## <a name="next-steps"></a>Pasos siguientes
- Para obtener información sobre cómo definir el orden de implementación de los recursos, vea [definir dependencias en las plantillas de administrador de recursos de Azure](resource-group-define-dependencies.md)
- Para obtener información sobre cómo definir un recurso pero crear varias instancias de la misma, vea [crear varias instancias de recursos en el Administrador de recursos de Azure](resource-group-create-multiple.md)
