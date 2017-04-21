<properties
   pageTitle="Plantilla de administrador de recursos para vincular recursos | Microsoft Azure"
   description="Muestra el esquema de administrador de recursos para implementar vínculos entre recursos relacionados a través de una plantilla."
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
   ms.date="04/05/2016"
   ms.author="tomfitz"/>

# <a name="resource-links-template-schema"></a>Esquema de plantillas de vínculos de recursos

Crea un vínculo entre dos recursos. Se aplica el vínculo a un recurso que se conoce como el recurso de origen. El segundo recurso en el vínculo se conoce como el recurso de destino.

## <a name="schema-format"></a>Formato de esquema

Para crear un vínculo, agregue el esquema siguiente a la sección de recursos de la plantilla.
    
    {
        "type": enum,
        "apiVersion": "2015-01-01",
        "name": string,
        "dependsOn": [ array values ],
        "properties":
        {
            "targetId": string,
            "notes": string
        }
    }



## <a name="values"></a>Valores

Las tablas siguientes describen los valores que se debe establecer en el esquema.

| Nombre | Valor |
| ---- | ---- |
| tipo | Enumeración<br />Obligatorio<br />**{nombres} / {type} / proveedores/vínculos**<br /><br />Tipo de recurso que se va a crear. El espacio de nombres de {} y valores de {tipo de} consulte el tipo de espacio de nombres y recursos de proveedor de recursos de origen. |
| apiVersion | Enumeración<br />Obligatorio<br />**2015-01-01**<br /><br />La versión de la API para crear el recurso. |  
| nombre | Cadena<br />Obligatorio<br />**{resouce}/Microsoft.Resources/{linkname}**<br /> hasta 64 caracteres y no pueden contener <>, % &,?, o cualquier caracteres de control.<br /><br />Un valor que especifica el nombre de recurso de origen y un nombre para el vínculo. |
| dependsOn | Matriz<br />Opcional<br />Una lista de valores separados por comas de un recurso nombres o identificadores únicos de recursos.<br /><br />La colección de recursos que depende de este vínculo. Si se implementan los recursos que se vincula en la misma plantilla, incluya los nombres de los recursos en este elemento para asegurarse de que se implementen en primer lugar. | 
| propiedades | Objeto<br />Obligatorio<br />[objeto de propiedades](#properties)<br /><br />Un objeto que identifica el recurso al que vincular y notas sobre el vínculo. |  

<a id="properties" />
### <a name="properties-object"></a>objeto de propiedades

| Nombre | Valor |
| ------- | ---- |
| targetId | Cadena<br />Obligatorio<br />**{Id. de recurso}**<br /><br />El identificador del recurso de destino al que vincular. |
| notas | Cadena<br />Opcional<br />hasta 512 caracteres<br /><br />Descripción del bloqueo. |


## <a name="how-to-use-the-link-resource"></a>Cómo usar el recurso de vínculo

Aplicar un vínculo entre dos recursos cuando los recursos tienen una dependencia que persiste después de la implementación. Por ejemplo, una aplicación puede conectarse a una base de datos en otro grupo de recursos. Puede definir dicha dependencia mediante la creación de un vínculo desde la aplicación a la base de datos. Los vínculos permiten la relación entre dos recursos de documentos. Más adelante, usted u otra persona de su organización puede consultar un recurso para vínculos para descubrir cómo funciona el recurso con otros recursos.

Todos los recursos vinculados deben pertenecer a la misma suscripción. Cada recurso puede estar vinculada a 50 otros recursos. Si alguno de los recursos vinculados son eliminado o movido, el propietario del vínculo debe limpiar el vínculo pendiente.

Para trabajar con vínculos a través del resto, vea [Los recursos vinculados](https://msdn.microsoft.com/library/azure/mt238499.aspx).

Use el siguiente comando PowerShell Azure para ver todos los vínculos en la suscripción. Puede proporcionar otros parámetros para limitar los resultados.

    Get-AzureRmResource -ResourceType Microsoft.Resources/links -isCollection -ResourceGroupName <YourResourceGroupName>

## <a name="examples"></a>Ejemplos

El ejemplo siguiente aplica un bloqueo de solo lectura a una aplicación web.

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
                "type": "Microsoft.Web/serverfarms",
                "name": "[parameters('hostingPlanName')]",
                "location": "[resourceGroup().location]",
                "sku": {
                    "tier": "Free",
                    "name": "f1",
                    "capacity": 0
                },
                "properties": {
                    "numberOfWorkers": 1
                }
            },
            {
                "apiVersion": "2015-08-01",
                "name": "[variables('siteName')]",
                "type": "Microsoft.Web/sites",
                "location": "[resourceGroup().location]",
                "dependsOn": [ "[parameters('hostingPlanName')]" ],
                "properties": {
                    "serverFarmId": "[parameters('hostingPlanName')]"
                }
            },
            {
                "type": "Microsoft.Web/sites/providers/links",
                "apiVersion": "2015-01-01",
                "name": "[concat(variables('siteName'),'/Microsoft.Resources/SiteToStorage')]",
                "dependsOn": [ "[variables('siteName')]" ],
                "properties": {
                    "targetId": "[resourceId('Microsoft.Storage/storageAccounts','storagecontoso')]",
                    "notes": "This web site uses the storage account to store user information."
                }
            }
        ],
        "outputs": {}
    }

## <a name="quickstart-templates"></a>Tutorial rápido plantillas

Las siguientes plantillas de tutorial rápido implementación recursos con un vínculo.

- [Alertas en cola con la aplicación de lógica](https://azure.microsoft.com/documentation/templates/201-alert-to-queue-with-logic-app)
- [Alerta de margen de demora con la aplicación de lógica](https://azure.microsoft.com/documentation/templates/201-alert-to-slack-with-logic-app)
- [Aprovisionar una aplicación de API con una puerta de enlace existente](https://azure.microsoft.com/documentation/templates/201-api-app-gateway-existing)
- [Aprovisionar una aplicación de API con una nueva puerta de enlace](https://azure.microsoft.com/documentation/templates/201-api-app-gateway-new)
- [Crear una aplicación de la aplicación de lógica más API usando una plantilla](https://azure.microsoft.com/documentation/templates/201-logic-app-api-app-create)
- [Aplicación de la lógica que envía un mensaje de texto cuando se activa una alerta](https://azure.microsoft.com/documentation/templates/201-alert-to-text-message-with-logic-app)


## <a name="next-steps"></a>Pasos siguientes

- Para obtener información acerca de la estructura de la plantilla, vea [crear un administrador de recursos de Azure plantillas](resource-group-authoring-templates.md).
