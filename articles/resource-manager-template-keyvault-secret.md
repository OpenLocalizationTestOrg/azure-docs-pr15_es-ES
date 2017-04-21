<properties
   pageTitle="Plantilla de administrador de recursos para un secreto en un depósito clave | Microsoft Azure"
   description="Muestra el esquema de administrador de recursos para implementar información confidencial depósito clave a través de una plantilla."
   services="azure-resource-manager,key-vault"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/23/2016"
   ms.author="tomfitz"/>

# <a name="key-vault-secret-template-schema"></a>Esquema de plantilla secreto depósito clave

Crea un secreto almacenado en un depósito clave. Este tipo de recurso con frecuencia se implementa como un recurso secundario de [depósito de clave](resource-manager-template-keyvault.md).

## <a name="schema-format"></a>Formato de esquema

Para crear un secreto depósito clave, agregue el siguiente esquema a la plantilla. El secreto puede definirse como un recurso de secundario de un depósito de clave o como recursos de nivel superior. Se puede definir como un recurso secundario cuando se implementa el depósito clave en la misma plantilla. Debe definir el secreto como un recurso de nivel superior cuando el depósito clave no se ha implementado en la misma plantilla, o cuando tiene que crear varias de información confidencial mediante un bucle en el tipo de recurso. 

    {
        "type": enum,
        "apiVersion": "2015-06-01",
        "name": string,
        "properties": {
            "value": string
        },
        "dependsOn": [ array values ]
    }

## <a name="values"></a>Valores

Las tablas siguientes describen los valores que se debe establecer en el esquema.

| Nombre | Valor |
| ---- | ---- | 
| tipo | Enumeración<br />Obligatorio<br />**información confidencial** (cuando se implementa como un recurso secundario de depósito de clave) o<br /> **Microsoft.KeyVault/vaults/secrets** (cuando se implementa como un recurso de nivel superior)<br /><br />Tipo de recurso que se va a crear. |
| apiVersion | Enumeración<br />Obligatorio<br />**2015-06-01** o **vista previa de 2014-12-19**<br /><br />La versión de la API para crear el recurso. | 
| nombre | Cadena<br />Obligatorio<br />Una sola palabra cuando se implementa como un recurso secundario de un depósito de clave o en el formato **{nombre de depósito clave} / {nombre de secreto}** cuando se implementa como un recurso de nivel superior se agregue a un depósito de clave existente.<br /><br />El nombre de la clave para crear. |
| propiedades | Objeto<br />Obligatorio<br />[objeto de propiedades](#properties)<br /><br />Un objeto que especifica el valor de la clave para crear. |
| dependsOn | Matriz<br />Opcional<br />Una lista de valores separados por comas de un recurso nombres o identificadores únicos de recursos.<br /><br />La colección de recursos que depende de este vínculo. Si la cámara clave para el secreto se implementa en la misma plantilla, incluya el nombre del depósito clave en este elemento para asegurarse de que se implementa en primer lugar. |

<a id="properties" />
### <a name="properties-object"></a>objeto de propiedades

| Nombre | Valor |
| ---- | ---- | 
| valor | Cadena<br />Obligatorio<br /><br />Valor secreto para almacenar en la cámara clave. Cuando se pasa un valor para esta propiedad, use un parámetro de tipo **securestring**.  |

    
## <a name="examples"></a>Ejemplos

El primer ejemplo implementa un secreto como un recurso secundario de un depósito de clave.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "keyVaultName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the vault"
                }
            },
            "tenantId": {
                "type": "string",
                "metadata": {
                   "description": "Tenant ID for the subscription and use assigned access to the vault. Available from the Get-AzureRmSubscription PowerShell cmdlet"
                }
            },
            "objectId": {
                "type": "string",
                "metadata": {
                    "description": "Object ID of the AAD user or service principal that will have access to the vault. Available from the Get-AzureRmADUser or the Get-AzureRmADServicePrincipal cmdlets"
                }
            },
            "keysPermissions": {
                "type": "array",
                "defaultValue": [ "all" ],
                "metadata": {
                    "description": "Permissions to grant user to keys in the vault. Valid values are: all, create, import, update, get, list, delete, backup, restore, encrypt, decrypt, wrapkey, unwrapkey, sign, and verify."
                }
            },
            "secretsPermissions": {
                "type": "array",
                "defaultValue": [ "all" ],
                "metadata": {
                    "description": "Permissions to grant user to secrets in the vault. Valid values are: all, get, set, list, and delete."
                }
            },
            "vaultSku": {
                "type": "string",
                "defaultValue": "Standard",
                "allowedValues": [
                    "Standard",
                    "Premium"
                ],
                "metadata": {
                    "description": "SKU for the vault"
                }
            },
            "enabledForDeployment": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "Specifies if the vault is enabled for VM or Service Fabric deployment"
                }
            },
            "enabledForTemplateDeployment": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "Specifies if the vault is enabled for ARM template deployment"
                }
            },
            "enableVaultForVolumeEncryption": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "Specifies if the vault is enabled for volume encryption"
                }
            },
            "secretName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the secret to store in the vault"
                }
            },
            "secretValue": {
                "type": "securestring",
                "metadata": {
                    "description": "Value of the secret to store in the vault"
                }
            }
        },
        "resources": [
        {
            "type": "Microsoft.KeyVault/vaults",
            "name": "[parameters('keyVaultName')]",
            "apiVersion": "2015-06-01",
            "location": "[resourceGroup().location]",
            "tags": {
                "displayName": "KeyVault"
            },
            "properties": {
                "enabledForDeployment": "[parameters('enabledForDeployment')]",
                "enabledForTemplateDeployment": "[parameters('enabledForTemplateDeployment')]",
                "enabledForVolumeEncryption": "[parameters('enableVaultForVolumeEncryption')]",
                "tenantId": "[parameters('tenantId')]",
                "accessPolicies": [
                {
                    "tenantId": "[parameters('tenantId')]",
                    "objectId": "[parameters('objectId')]",
                    "permissions": {
                        "keys": "[parameters('keysPermissions')]",
                        "secrets": "[parameters('secretsPermissions')]"
                    }
                }],
                "sku": {
                    "name": "[parameters('vaultSku')]",
                    "family": "A"
                }
            },
            "resources": [
            {
                "type": "secrets",
                "name": "[parameters('secretName')]",
                "apiVersion": "2015-06-01",
                "properties": {
                    "value": "[parameters('secretValue')]"
                },
                "dependsOn": [
                    "[concat('Microsoft.KeyVault/vaults/', parameters('keyVaultName'))]"
                ]
            }]
        }]
    }

En el segundo ejemplo se implementa el secreto como un recurso de nivel superior que se almacena en una caja fuerte clave existente.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "keyVaultName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the existing vault"
                }
            },
            "secretName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the secret to store in the vault"
                }
            },
            "secretValue": {
                "type": "securestring",
                "metadata": {
                    "description": "Value of the secret to store in the vault"
                }
            }
        },
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.KeyVault/vaults/secrets",
                "apiVersion": "2015-06-01",
                "name": "[concat(parameters('keyVaultName'), '/', parameters('secretName'))]",
                "properties": {
                    "value": "[parameters('secretValue')]"
                }
            }
        ],
        "outputs": {}
    }


## <a name="next-steps"></a>Pasos siguientes

- Para obtener información general sobre los depósitos claves, vea [Introducción a Azure clave depósito](./key-vault/key-vault-get-started.md).
- Para obtener un ejemplo de un secreto depósito clave de referencia al implementar plantillas, vea [pasar valores seguros durante la implementación](resource-manager-keyvault-parameter.md).


