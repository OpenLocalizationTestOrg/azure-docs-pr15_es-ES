<properties 
    pageTitle="Vinculación de recursos en el Administrador de recursos de Azure | Microsoft Azure" 
    description="Crear un vínculo entre los recursos relacionados en distintos grupos de recursos en el Administrador de recursos de Azure." 
    services="azure-resource-manager" 
    documentationCenter="" 
    authors="tfitzmac" 
    manager="timlt" 
    editor="tysonn"/>

<tags 
    ms.service="azure-resource-manager" 
    ms.workload="multiple" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/01/2016" 
    ms.author="tomfitz"/>

# <a name="linking-resources-in-azure-resource-manager"></a>Vinculación de recursos en el Administrador de recursos de Azure

Durante la implementación, puede marcar un recurso como dependiente de otro recurso, pero dicho ciclo de vida finaliza en implementación. Después de la implementación, no hay ninguna relación identificada entre recursos dependientes. Administrador de recursos proporciona una característica denominada recursos vinculación para establecer relaciones persistentes entre los recursos.

Con la vinculación de recursos, puede documentar relaciones que abarcan los grupos de recursos. Por ejemplo, es común para la base de datos con su propio ciclo de vida residen en un grupo de recursos, y una aplicación con un ciclo de vida diferente en otro grupo de recursos. La aplicación se conecta a la base de datos, por lo que desea marcar un vínculo entre la aplicación y la base de datos. 

Todos los recursos vinculados deben pertenecer a la misma suscripción. Cada recurso puede estar vinculada a 50 otros recursos. La única manera de recursos relacionados de la consulta es a través de la API de REST. Si alguno de los recursos vinculados son eliminado o movido, el propietario del vínculo debe limpiar el vínculo pendiente. Es **no** ninguna advertencia al eliminar un recurso que está vinculado a otros recursos.

## <a name="linking-in-templates"></a>Vinculación de plantillas

Para definir un vínculo en una plantilla, incluya un tipo de recurso que combina el espacio de nombres del proveedor de recursos y el tipo de recurso de origen con **/providers/links**. El nombre debe incluir el nombre del recurso de origen. Proporcione el identificador de recursos del recurso de destino. En el ejemplo siguiente se establece un vínculo entre un sitio web y una cuenta de almacenamiento.

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


Para obtener una descripción completa de la plantilla con formato, consulte [vínculos a recursos - esquema de la plantilla](resource-manager-template-links.md).

## <a name="linking-with-rest-api"></a>Vincular con la API de REST

Para definir un vínculo entre los recursos implementados, ejecute:

    PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/{provider-namespace}/{resource-type}/{resource-name}/providers/Microsoft.Resources/links/{link-name}?api-version={api-version}

Reemplace {Id. de suscripción} con su identificador de suscripción. Reemplazar {grupo de recursos}, {proveedor de espacio de nombres, {tipo de recurso} y {nombre de recurso} con los valores que identifica el primer recurso en el vínculo. Reemplace {nombre de vínculo} por el nombre del vínculo a crear. Uso de 2015-01-01 para la versión de la api.

En la convocatoria, incluir un objeto que define el segundo recurso en el vínculo:

    {
        "name": "{new-link-name}",
        "properties": {
            "targetId": "subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/{provider-namespace}/{resource-type}/{resource-name}/",
            "notes": "{link-description}",
        }
    }

El elemento de propiedades contiene el identificador del segundo recurso.

Puede consultar los vínculos en su suscripción:

    https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Resources/links?api-version={api-version}

Para obtener más ejemplos, cómo recuperar información acerca de los vínculos, vea [Los recursos vinculados](https://msdn.microsoft.com/library/azure/mt238499.aspx).

## <a name="next-steps"></a>Pasos siguientes

- También puede organizar los recursos con etiquetas. Para obtener información sobre el etiquetado de recursos, vea [usar etiquetas para organizar los recursos](resource-group-using-tags.md).
- Para obtener una descripción de cómo crear plantillas y definir los recursos para implementar, consulte [plantillas de edición](resource-group-authoring-templates.md).
