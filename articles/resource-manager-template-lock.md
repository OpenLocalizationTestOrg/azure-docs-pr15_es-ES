<properties
   pageTitle="Plantilla de administrador de recursos para bloqueos de recursos | Microsoft Azure"
   description="Muestra el esquema de administrador de recursos para implementar bloqueos de recursos a través de una plantilla."
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

# <a name="resource-locks-template-schema"></a>Esquema de plantillas de bloqueos de recursos

Crea un bloqueo en un recurso y su elemento secundario recursos.

## <a name="schema-format"></a>Formato de esquema

Para crear un bloqueo, agregue el esquema siguiente a la sección de recursos de la plantilla.
    
    {
        "type": enum,
        "apiVersion": "2015-01-01",
        "name": string,
        "dependsOn": [ array values ],
        "properties":
        {
            "level": enum,
            "notes": string
        }
    }



## <a name="values"></a>Valores

Las tablas siguientes describen los valores que se debe establecer en el esquema.

| Nombre | Obligatorio | Descripción |
| ---- | -------- | ----------- |
| tipo | Sí | Tipo de recurso que se va a crear.<br /><br />Para los recursos:<br />**{nombres} / {type} / proveedores/bloqueos**<br /><br/>Grupos de recursos:<br />**Microsoft.Authorization/locks** |
| apiVersion | Sí | La versión de la API para crear el recurso.<br /><br />Uso:<br />**2015-01-01**<br /><br /> |
| nombre | Sí | Un valor que especifica el recurso bloquear y un nombre para el bloqueo. Puede tener hasta 64 caracteres y no pueden contener <>, % &,?, o cualquier caracteres de control.<br /><br />Para los recursos:<br />**{resource}/Microsoft.Authorization/{lockname}**<br /><br />Grupos de recursos:<br />**{lockname}** |
| dependsOn | No | Una lista de valores separados por comas de un recurso nombres o identificadores únicos de recursos.<br /><br />La colección de recursos que depende de este bloqueo. Si el recurso que bloquean se implementa en la misma plantilla, incluya ese nombre de recurso en este elemento para asegurarse de que el recurso se implementa en primer lugar. | 
| propiedades | Sí | Objeto que identifica el tipo de bloqueo y notas sobre el bloqueo.<br /><br />Consulte [Propiedades de objeto](#properties-object). |  

### <a name="properties-object"></a>objeto de propiedades

| Nombre | Obligatorio | Descripción |
| ---- | -------- | ----------- |
| nivel   | Sí | El tipo de bloqueo para aplicar el ámbito.<br /><br />**CannotDelete** : los usuarios pueden modificar los recursos, pero no eliminarlo.<br />**Sólo lectura** - pueden leer los usuarios de un recurso, pero no puede eliminarlo ni realizar acciones en él. |
| notas   | No | Descripción del bloqueo. Puede tener hasta 512 caracteres. |


## <a name="how-to-use-the-lock-resource"></a>Cómo usar el recurso de bloqueo

Agregar este recurso a la plantilla para evitar que las acciones especificadas en un recurso. El bloqueo se aplica a todos los usuarios y grupos.

Para crear o eliminar bloqueos de administración, debe tener acceso a **Microsoft.Authorization/** * o * *Microsoft.Authorization/locks/* ** acciones. De las funciones integradas, solo **propietario** y **Administrador de acceso de usuario ** se conceden esas acciones. Para obtener información acerca de control de acceso basado en roles, consulte [Control de acceso basado en roles de Azure](./active-directory/role-based-access-control-configure.md).

El bloqueo se aplica al recurso especificado y los recursos secundarios.

Puede quitar un bloqueo con los comandos de PowerShell **AzureRmResourceLock quitar** o con la [operación de eliminación](https://msdn.microsoft.com/library/azure/mt204562.aspx) de la API de REST.

## <a name="examples"></a>Ejemplos

El ejemplo siguiente aplica un bloqueo eliminar no puede a una aplicación web.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "hostingPlanName": {
                "type": "string"
            }
        },
        "variables": {
            "siteName": "[concat('site',uniqueString(resourceGroup().id))]"
        },
        "resources": [
            {
                "apiVersion": "2015-08-01",
                "name": "[variables('siteName')]",
                "type": "Microsoft.Web/sites",
                "location": "[resourceGroup().location]",
                "properties": {
                    "serverFarmId": "[parameters('hostingPlanName')]"
                },
            },
            {
                "type": "Microsoft.Web/sites/providers/locks",
                "apiVersion": "2015-01-01",
                "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
                "dependsOn": [ "[variables('siteName')]" ],
                "properties":
                {
                    "level": "CannotDelete",
                    "notes": "my notes"
                }
             }
        ],
        "outputs": {}
    }

En el ejemplo siguiente se aplica un bloqueo eliminar no al grupo de recursos.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Authorization/locks",
                "apiVersion": "2015-01-01",
                "name": "MyGroupLock",
                "properties":
                {
                    "level": "CannotDelete",
                    "notes": "my notes"
                }
            }
        ],
        "outputs": {}
    }

## <a name="next-steps"></a>Pasos siguientes

- Para obtener información acerca de la estructura de la plantilla, vea [crear un administrador de recursos de Azure plantillas](resource-group-authoring-templates.md).
- Para obtener más información acerca de los bloqueos, consulte [los recursos de bloqueo con el Administrador de recursos de Azure](resource-group-lock-resources.md).
