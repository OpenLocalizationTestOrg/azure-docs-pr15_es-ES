<properties
   pageTitle="Plantilla de administrador de recursos para las asignaciones de roles | Microsoft Azure"
   description="Muestra el esquema de administrador de recursos para implementar una asignación de roles a través de una plantilla."
   services="azure-resource-manager"
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
   ms.date="10/03/2016"
   ms.author="tomfitz"/>

# <a name="role-assignments-template-schema"></a>Esquema de la plantilla de las asignaciones de roles

Asigna un usuario, grupo o principal del servicio a una función a un ámbito especificado.

## <a name="resource-format"></a>Formato de recursos

Para crear una asignación de roles, agregue el esquema siguiente a la sección de recursos de la plantilla.
    
    {
        "type": string,
        "apiVersion": "2015-07-01",
        "name": string,
        "dependsOn": [ array values ],
        "properties":
        {
            "roleDefinitionId": string,
            "principalId": string,
            "scope": string
        }
    }

## <a name="values"></a>Valores

Las tablas siguientes describen los valores que se debe establecer en el esquema.

| Nombre | Obligatorio | Descripción |
| ---- | -------- | ----------- |
| tipo | Sí    | Tipo de recurso que se va a crear.<br /><br /> Grupo de recursos:<br />**Microsoft.Authorization/roleAssignments**<br /><br />Recurso:<br />**{proveedor nombres} / {tipo de recurso} / proveedores/roleAssignments** |
| apiVersion |Sí | La versión de la API para crear el recurso.<br /><br /> Use **2015-07-01**. | 
| nombre | Sí | Un identificador único global para la nueva asignación de roles. |
| dependsOn | No | Una matriz de valores separados por comas de un recurso nombres o identificadores únicos de recursos.<br /><br />La colección de recursos que depende de esta asignación de roles. Si asignar un rol que el ámbito de un recurso y que se implementan recursos en la misma plantilla, incluya ese nombre de recurso en este elemento para asegurarse de que el recurso se implementa en primer lugar. |
| propiedades | Sí | El objeto de propiedades que identifica la definición de función, principal y ámbito. |

### <a name="properties-object"></a>objeto de propiedades

| Nombre | Obligatorio | Descripción |
| ---- | -------- | ----------- |
| roleDefinitionId | Sí |  El identificador de una definición de rol existente que se utilizará en la asignación de roles.<br /><br /> Use el siguiente formato:<br /> **/ subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}** |
| principalId | Sí | El identificador único global para una identidad existente. Este valor se asigna al identificador dentro del directorio y puede apuntar a un usuario, el servicio principal o el grupo de seguridad. |
| ámbito | No | El ámbito en el que se aplica esta asignación de roles.<br /><br />Para los grupos de recursos, use:<br />**/resourceGroups/ /Subscriptions/ {Id. de suscripción} {nombre de grupo de recursos}**  <br /><br />Para los recursos, use:<br />**/ subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{provider-namespace}/{resource-type}/{resource-name}** |  |


## <a name="how-to-use-the-role-assignment-resource"></a>Cómo usar el recurso de asignación de roles

Agregar una asignación de roles a la plantilla cuando tenga que agregar un usuario, grupo o principal del servicio a una función durante la implementación. Las asignaciones de roles se heredan de niveles superiores del ámbito, de modo si ya ha agregado una entidad de seguridad a una función en el nivel de la suscripción, no es necesario reasignar para el recurso o grupo de recursos.

Hay muchos valores del identificador que debe proporcionar cuando se trabaja con las asignaciones de roles. Puede recuperar los valores a través de PowerShell o CLI de Azure.

### <a name="powershell"></a>PowerShell

El nombre de la asignación de roles requiere un identificador único global. Puede generar un identificador nuevo **nombre** con:

    $name = [System.Guid]::NewGuid().toString()

Puede recuperar el identificador de definición de la función con:

    $roledef = (Get-AzureRmRoleDefinition -Name Reader).id

Puede recuperar el identificador de la entidad de seguridad con uno de los siguientes comandos.

Para un grupo denominado **auditores**:

    $principal = (Get-AzureRmADGroup -SearchString Auditors).id

Para un usuario denominado **exampleperson**:

    $principal = (Get-AzureRmADUser -SearchString exampleperson).id

Para un servicio principal denominado **exampleapp**:

    $principal = (Get-AzureRmADServicePrincipal -SearchString exampleapp).id 

### <a name="azure-cli"></a>CLI de Azure

Puede recuperar el identificador de definición de la función con:

    azure role show Reader --json | jq .[].Id -r

Puede recuperar el identificador de la entidad de seguridad con uno de los siguientes comandos.

Para un grupo denominado **auditores**:

    azure ad group show --search Auditors --json | jq .[].objectId -r

Para un usuario denominado **exampleperson**:

    azure ad user show --search exampleperson --json | jq .[].objectId -r

Para un servicio principal denominado **exampleapp**:

    azure ad sp show --search exampleapp --json | jq .[].objectId -r

## <a name="examples"></a>Ejemplos

La siguiente plantilla recibe un identificador de una función y un identificador de usuario, grupo o principal del servicio. Asigna el rol en el nivel de grupo de recursos.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "roleDefinitionId": {
                "type": "string"
            },
            "roleAssignmentId": {
                "type": "string"
            },
            "principalId": {
                "type": "string"
            }
        },
        "resources": [
            {
              "type": "Microsoft.Authorization/roleAssignments",
              "apiVersion": "2015-07-01",
              "name": "[parameters('roleAssignmentId')]",
              "properties":
              {
                "roleDefinitionId": "[concat(subscription().id, '/providers/Microsoft.Authorization/roleDefinitions/', parameters('roleDefinitionId'))]",
                "principalId": "[parameters('principalId')]",
                "scope": "[concat(subscription().id, '/resourceGroups/', resourceGroup().name)]"
              }
            }
        ],
        "outputs": {}
    }



La plantilla siguiente crea una cuenta de almacenamiento y asigna la función de lector a la cuenta de almacenamiento. Los identificadores de dos grupos y la función lector se han incluido en la plantilla para simplificar la implementación. Estos valores se podrían recuperar en tiempo de implementación mediante en secuencia de comandos y pasa como parámetros.

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "roleName": {
          "type": "string"
        },
        "groupToAssign": {
          "type": "string",
          "allowedValues": [
            "Auditors",
            "Limited"
          ]
        }
      },
      "variables": {
        "readerRole": "[concat('/subscriptions/',subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
        "storageName": "[concat('storage', uniqueString(resourceGroup().id))]",
        "scope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Storage/storageAccounts/', variables('storageName'))]",
        "Auditors": "1c272299-9729-462a-8d52-7efe5ece0c5c",
        "Limited": "7c7250f0-7952-441c-99ce-40de5e3e30b5",
        "fullRoleName": "[concat(variables('storageName'), '/Microsoft.Authorization/', parameters('roleName'))]"
      },
      "resources": [
        {
          "apiVersion": "2016-01-01",
          "type": "Microsoft.Storage/storageAccounts",
          "name": "[variables('storageName')]",
          "location": "[resourceGroup().location]",
          "sku": {
            "name": "Standard_LRS"
          },
          "kind": "Storage",
          "tags": {
            "displayName": "MyStorageAccount"
          },
          "properties": {}
        },
        {
          "type": "Microsoft.Storage/storageAccounts/providers/roleAssignments",
          "apiVersion": "2015-07-01",
          "name": "[variables('fullRoleName')]",
          "dependsOn": [
            "[variables('storageName')]"
          ],
          "properties": {
            "roleDefinitionId": "[variables('readerRole')]",
            "principalId": "[variables(parameters('groupToAssign'))]"
          }
        }
      ]
    }

## <a name="quickstart-templates"></a>Tutorial rápido plantillas

Las siguientes plantillas muestran cómo usar el recurso de asignación de roles:

- [Asignar rol integrado al grupo de recursos](https://azure.microsoft.com/documentation/templates/101-rbac-builtinrole-resourcegroup)
- [Asignar rol integrado a máquina virtual existente](https://azure.microsoft.com/documentation/templates/101-rbac-builtinrole-virtualmachine)
- [Asignar rol integrado a varias máquinas virtuales existentes](https://azure.microsoft.com/documentation/templates/201-rbac-builtinrole-multipleVMs)

## <a name="next-steps"></a>Pasos siguientes

- Para obtener información acerca de la estructura de la plantilla, vea [crear un administrador de recursos de Azure plantillas](resource-group-authoring-templates.md).
- Para obtener más información acerca de control de acceso basado en roles, vea [Control de acceso basado en roles de Azure Active Directory](./active-directory/role-based-access-control-configure.md).
