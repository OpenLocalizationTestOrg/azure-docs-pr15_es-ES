<properties
   pageTitle="Secreto clave depósito con el Administrador de recursos plantilla | Microsoft Azure"
   description="Muestra cómo pasar un secreto de un depósito clave como un parámetro durante la implementación."
   services="azure-resource-manager,key-vault"
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
   ms.date="06/23/2016"
   ms.author="tomfitz"/>

# <a name="pass-secure-values-during-deployment"></a>Pasar valores seguros durante la implementación

Cuando necesite pasar un valor seguro (por ejemplo, una contraseña) como un parámetro durante la implementación, puede almacenar el valor como un secreto en un [Depósito de clave de Azure](./key-vault/key-vault-whatis.md) y hacer referencia al valor en otras plantillas de administrador de recursos. Incluya sólo una referencia al secreto en la plantilla para que nunca se expone el secreto y no es necesario introducir manualmente el valor para el secreto cada vez que se implementa los recursos. Especificar qué usuarios o identidades de servicio pueden tener acceso al secreto.  

## <a name="deploy-a-key-vault-and-secret"></a>Implementar un depósito clave y secreto

Para crear un depósito clave que se puede hacer referencia de otras plantillas de administrador de recursos, debe establecer la propiedad **enabledForTemplateDeployment** en **true**y debe conceder acceso al usuario o que se ejecutará la implementación que hace referencia el secreto principal del servicio.

Para obtener información sobre cómo implementar un depósito clave y secreto, vea [clave depósito esquemas](resource-manager-template-keyvault.md) y [clave depósito secreto](resource-manager-template-keyvault-secret.md).

## <a name="reference-a-secret-with-static-id"></a>Hacer referencia a un secreto con identificador estático

Se hace referencia el secreto desde dentro de un archivo de parámetros que se pasa a la plantilla. Referencia el secreto pasando el identificador de recursos del depósito clave y el nombre del secreto. En este ejemplo, el secreto de clave depósito ya debe existir y está usando un valor estático para el mismo Id. de recurso.

    "parameters": {
      "adminPassword": {
        "reference": {
          "keyVault": {
            "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
          }, 
          "secretName": "sqlAdminPassword" 
        } 
      }
    }

Un archivo completo parámetro podría ser similar:

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "sqlsvrAdminLogin": {
          "value": ""
        },
        "sqlsvrAdminLoginPassword": {
          "reference": {
            "keyVault": {
              "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
            },
            "secretName": "adminPassword"
          }
        }
      }
    }

El parámetro que acepta el secreto debería **securestring**. En el ejemplo siguiente se muestra las secciones correspondientes de una plantilla que implementa un servidor de SQL server que requiere una contraseña de administrador.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "sqlsvrAdminLogin": {
                "type": "string",
                "minLength": 4
            },
            "sqlsvrAdminLoginPassword": {
                "type": "securestring"
            },
            ...
        },
        "resources": [
        {
            "name": "[variables('sqlsvrName')]",
            "type": "Microsoft.Sql/servers",
            "location": "[resourceGroup().location]",
            "apiVersion": "2014-04-01-preview",
            "properties": {
                "administratorLogin": "[parameters('sqlsvrAdminLogin')]",
                "administratorLoginPassword": "[parameters('sqlsvrAdminLoginPassword')]"
            },
            ...
        }],
        "variables": {
            "sqlsvrName": "[concat('sqlsvr', uniqueString(resourceGroup().id))]"
        },
        "outputs": { }
    }

## <a name="reference-a-secret-with-dynamic-id"></a>Hacer referencia a un secreto con id dinámico

La sección anterior mostraba cómo pasar un identificador de recurso estático para el secreto depósito clave. Sin embargo, en algunos casos, debe hacer referencia a un secreto de depósito clave varía en función de la implementación actual. En ese caso, no puede codificar el identificador de recursos en el archivo de parámetros. Desgraciadamente, no puede generar dinámicamente el identificador del recurso en el archivo de parámetros porque no se permiten expresiones de plantilla en el archivo de parámetros.

Para generar dinámicamente el identificador de recursos para un secreto depósito clave, deberá mover el recurso que necesita el secreto en una plantilla anidada. En la plantilla maestra, agregue la plantilla anidada y pasar un parámetro que contiene el identificador de recursos generado dinámicamente.

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "vaultName": {
          "type": "string"
        },
        "secretName": {
          "type": "string"
        }
      },
      "resources": [
        {
          "apiVersion": "2015-01-01",
          "name": "nestedTemplate",
          "type": "Microsoft.Resources/deployments",
          "properties": {
            "mode": "incremental",
            "templateLink": {
              "uri": "https://www.contoso.com/AzureTemplates/newVM.json",
              "contentVersion": "1.0.0.0"
            },
            "parameters": {
              "adminPassword": {
                "reference": {
                  "keyVault": {
                    "id": "[concat(resourceGroup().id, '/providers/Microsoft.KeyVault/vaults/', parameters('vaultName'))]"
                  },
                  "secretName": "[parameters('secretName')]"
                }
              }
            }
          }
        }
      ],
      "outputs": {}
    }


## <a name="next-steps"></a>Pasos siguientes

- Para obtener información general sobre los depósitos claves, vea [Introducción a Azure clave depósito](./key-vault/key-vault-get-started.md).
- Para obtener información sobre cómo usar un depósito clave con una máquina Virtual, vea [Consideraciones de seguridad para el Administrador de recursos de Azure](best-practices-resource-manager-security.md).
- Para obtener ejemplos completos de información confidencial de clave de referencia, vea [ejemplos de clave de cámara](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).

