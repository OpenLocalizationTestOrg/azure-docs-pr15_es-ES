<properties
   pageTitle="Plantilla de administrador de recursos para depósito clave | Microsoft Azure"
   description="Muestra el esquema de administrador de recursos para implementar depósitos claves a través de una plantilla."
   services="azure-resource-manager,key-vault"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/23/2016"
   ms.author="tomfitz"/>

# <a name="key-vault-template-schema"></a>Esquema de plantilla de cámara clave

Crea un depósito clave.

## <a name="schema-format"></a>Formato de esquema

Para crear un depósito clave, agregue el esquema siguiente a la sección de recursos de la plantilla.

    {
        "type": "Microsoft.KeyVault/vaults",
        "apiVersion": "2015-06-01",
        "name": string,
        "location": string,
        "properties": {
            "enabledForDeployment": bool,
            "enabledForTemplateDeployment": bool,
            "enabledForVolumeEncryption": bool,
            "tenantId": string,
            "accessPolicies": [
                {
                    "tenantId": string,
                    "objectId": string,
                    "permissions": {
                        "keys": [ keys permissions ],
                        "secrets": [ secrets permissions ]
                    }
                }
            ],
            "sku": {
                "name": enum,
                "family": "A"
            }
        },
        "resources": [
             child resources
        ]
    }

## <a name="values"></a>Valores

Las tablas siguientes describen los valores que se debe establecer en el esquema.

| Nombre | Valor |
| ---- | ---- | 
| tipo | Enumeración<br />Obligatorio<br />**Microsoft.KeyVault/vaults**<br /><br />Tipo de recurso que se va a crear. |
| apiVersion | Enumeración<br />Obligatorio<br />**2015-06-01** o **vista previa de 2014-12-19**<br /><br />La versión de la API para crear el recurso. | 
| nombre | Cadena<br />Obligatorio<br />Un nombre único a través de Azure.<br /><br />Nombre del depósito clave para crear. Considere la posibilidad de usar la función [uniqueString](resource-group-template-functions.md#uniquestring) con la convención de nomenclatura para crear un nombre único, tal como se muestra en el ejemplo siguiente. |
| ubicación | Cadena<br />Obligatorio<br />Una región válida para depósitos claves. Para determinar las regiones válidas, vea [admitidos regiones](resource-manager-supported-services.md#supported-regions).<br /><br />Región para hospedar el depósito clave. |
| propiedades | Objeto<br />Obligatorio<br />[objeto de propiedades](#properties)<br /><br />Objeto que especifica el tipo de cámara clave para crear. |
| recursos | Matriz<br />Opcional<br />Valores permitidos: [recursos de tecla depósito secretos](resource-manager-template-keyvault-secret.md)<br /><br />Recursos secundarios para el depósito clave. |

<a id="properties" />
### <a name="properties-object"></a>objeto de propiedades

| Nombre | Valor |
| ---- | ---- | 
| enabledForDeployment | Valor booleano<br />Opcional<br />**true** o **false**<br /><br />Especifica si la cámara está habilitado para su implementación de máquina Virtual o servicio tela. |
| enabledForTemplateDeployment | Valor booleano<br />Opcional<br />**true** o **false**<br /><br />Especifica si la cámara está habilitado para su uso en implementaciones de la plantilla de administrador de recursos. Para obtener más información, vea [pasar valores seguros durante la implementación](resource-manager-keyvault-parameter.md) |
| enabledForVolumeEncryption | Valor booleano<br />Opcional<br />**true** o **false**<br /><br />Especifica si la cámara está habilitado para el cifrado de volumen. |
| tenantId | Cadena<br />Obligatorio<br />**Identificador único global**<br /><br />El identificador de inquilinos para la suscripción. Se puede recuperar con el cmdlet de PowerShell [Get-AzureRmSubscription](https://msdn.microsoft.com/library/azure/mt619284.aspx) o la **cuenta de azure mostrar** comando CLI de Azure. |
| accessPolicies | Matriz<br />Obligatorio<br />[objeto accessPolicies](#accesspolicies)<br /><br />Matriz de objetos hasta 16 que especifican los permisos para el usuario o el servicio principal. |
| SKU | Objeto<br />Obligatorio<br />[objeto de SKU](#sku)<br /><br />SKU para el depósito clave. |

<a id="accesspolicies" />
### <a name="propertiesaccesspolicies-object"></a>objeto properties.accessPolicies

| Nombre | Valor |
| ---- | ---- | 
| tenantId | Cadena<br />Obligatorio<br />**Identificador único global**<br /><br />El identificador del inquilino del inquilino de Azure Active Directory que contiene el **Id. de objeto** de esta directiva de acceso |
| Id. de objeto | Cadena<br />Obligatorio<br />**Identificador único global**<br /><br />El identificador de objeto del usuario de Azure Active Directory o principal del servicio que tenga acceso a la cámara. Puede recuperar el valor de la [Get-AzureRmADUser](https://msdn.microsoft.com/library/azure/mt679001.aspx) o los cmdlets de PowerShell de [Get-AzureRmADServicePrincipal](https://msdn.microsoft.com/library/azure/mt678992.aspx) o los comandos de CLI de Azure **ad azure sp** o **usuario de azure ad** . |
| permisos | Objeto<br />Obligatorio<br />[objeto de permisos](#permissions)<br /><br />Los permisos en este depósito al objeto de Active Directory. |

<a id="permissions" />
### <a name="propertiesaccesspoliciespermissions-object"></a>objeto properties.accessPolicies.permissions

| Nombre | Valor |
| ---- | ---- | 
| teclas | Matriz<br />Obligatorio<br />**todos**, **una copia de seguridad**, **crear**, **descifrar**, **Eliminar**, **cifrar**, **obtener**, **Importar**, **lista**, **Restaurar**, **Inicio de sesión**, **unwrapkey**, **Actualizar**, **Compruebe**, **wrapkey**<br /><br />Los permisos de claves de este depósito a este objeto de Active Directory. Este valor debe especificarse como una matriz de uno o más valores permitidos. |
| información confidencial | Matriz<br />Obligatorio<br />**todos**, **Eliminar**, **obtener**, **lista**, **establecer**<br /><br />Los permisos de información confidencial en este depósito a este objeto de Active Directory. Este valor debe especificarse como una matriz de uno o más valores permitidos. |

<a id="sku" />
### <a name="propertiessku-object"></a>objeto Properties.SKU

| Nombre | Valor |
| ---- | ---- | 
| nombre | Enumeración<br />Obligatorio<br />**estándar**o **premium** <br /><br />El nivel de servicio de KeyVault usar.  Estándar es compatible con teclas de software protegido e información confidencial.  Premium agrega compatibilidad para las claves protegidas con HSM. |
| familia | Enumeración<br />Obligatorio<br />**RESPUESTAS** <br /><br />La familia sku usar. |
 
    
## <a name="examples"></a>Ejemplos

En el ejemplo siguiente se implementa un depósito clave y secreto.

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

## <a name="quickstart-templates"></a>Tutorial rápido plantillas

La siguiente plantilla tutorial implementa un depósito clave.

- [Crear depósito clave](https://azure.microsoft.com/documentation/templates/101-key-vault-create/)


## <a name="next-steps"></a>Pasos siguientes

- Para obtener información general sobre los depósitos claves, vea [Introducción a Azure clave depósito](./key-vault/key-vault-get-started.md).
- Para obtener un ejemplo de un secreto depósito clave de referencia al implementar plantillas, vea [pasar valores seguros durante la implementación](resource-manager-keyvault-parameter.md).

