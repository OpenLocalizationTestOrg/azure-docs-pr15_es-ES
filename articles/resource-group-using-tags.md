<properties
    pageTitle="Usar etiquetas para organizar los recursos de Azure | Microsoft Azure"
    description="Muestra cómo aplicar etiquetas para organizar los recursos para facturación y administrar."
    services="azure-resource-manager"
    documentationCenter=""
    authors="tfitzmac"
    manager="timlt"
    editor="tysonn"/>

<tags
    ms.service="azure-resource-manager"
    ms.workload="multiple"
    ms.tgt_pltfrm="AzurePortal"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/08/2016"
    ms.author="tomfitz"/>


# <a name="using-tags-to-organize-your-azure-resources"></a>Usar etiquetas para organizar los recursos de Azure

Administrador de recursos le permite organizar lógicamente los recursos mediante la aplicación de etiquetas. Las etiquetas consisten en pares de clave/valor que identifican los recursos con las propiedades que defina. Para marcar los recursos como pertenecientes a la misma categoría, aplicar la misma etiqueta a los recursos.

Cuando se ve recursos con una etiqueta determinada, vea los recursos de todos los grupos de los recursos. No se limitan a solo los recursos en el mismo grupo de recursos, que le permite organizar los recursos de forma independiente de las relaciones de implementación. Las etiquetas pueden ser útiles cuando necesite organizar los recursos de facturación o de administración.

Cada etiqueta que se agrega a un grupo de recursos o se agrega automáticamente a la taxonomía toda la suscripción. También puede rellenar la taxonomía para la suscripción con nombres de etiquetas y valores que desea usar como recursos están etiquetados en el futuro.

Cada grupo de recursos o puede tener un máximo de 15 etiquetas. El nombre de etiqueta se limita a 512 caracteres y el valor de la etiqueta está limitado a 256 caracteres.

> [AZURE.NOTE] Solo puede aplicar etiquetas a los recursos que admiten operaciones de administrador de recursos. Si ha creado una máquina Virtual, una red Virtual o almacenamiento a través del modelo de implementación clásica (como a través del portal clásico), no se puede aplicar una etiqueta a ese recurso. Para admitir etiquetado, volver a implementar estos recursos a través del Administrador de recursos. Todos los otros recursos de soporte técnico de etiquetado.

## <a name="templates"></a>Plantillas

Para etiquetar un recurso durante la implementación, simplemente agregar el elemento de **etiquetas** para el recurso que se va a implementar y proporcionar el nombre de etiqueta y el valor. El nombre de etiqueta y el valor no es necesario que ya existen en la suscripción. Puede proporcionar un máximo de 15 etiquetas para cada recurso.

En el ejemplo siguiente se muestra una cuenta de almacenamiento con una etiqueta.

    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2015-06-15",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "tags": {
                "dept": "Finance"
            },
            "properties": 
            {
                "accountType": "Standard_LRS"
            }
        }
    ]

En la actualidad, el Administrador de recursos no admite el procesamiento de un objeto para los nombres de las etiquetas y los valores. En su lugar, pasa un objeto para los valores de etiqueta, pero debe especificar los nombres de etiqueta, como se muestra en el ejemplo siguiente.

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "tagvalues": {
          "type": "object",
          "defaultValue": {
            "dept": "Finance",
            "project": "Test"
          }
        }
      },
      "resources": [
      {
        "apiVersion": "2015-06-15",
        "type": "Microsoft.Storage/storageAccounts",
        "name": "examplestorage",
        "tags": {
          "dept": "[parameters('tagvalues').dept]",
          "project": "[parameters('tagvalues').project]"
        },
        "location": "[resourceGroup().location]",
        "properties": {
          "accountType": "Standard_LRS"
        }
      }]
    }


## <a name="portal"></a>Portal

[AZURE.INCLUDE [resource-manager-tag-resource](../includes/resource-manager-tag-resources.md)]

## <a name="powershell"></a>PowerShell

[AZURE.INCLUDE [resource-manager-tag-resources](../includes/resource-manager-tag-resources-powershell.md)]

## <a name="azure-cli"></a>CLI de Azure

[AZURE.INCLUDE [resource-manager-tag-resources-cli](../includes/resource-manager-tag-resources-cli.md)]

## <a name="rest-api"></a>API DE REST

El portal y PowerShell usan la [REST API de administrador de recursos del](https://msdn.microsoft.com/library/azure/dn848368.aspx) segundo plano. Si necesita integrar etiquetado en otro entorno, puede obtener etiquetas con GET en el identificador del recurso y actualizar el conjunto de etiquetas con una llamada de revisión.


## <a name="tags-and-billing"></a>Etiquetas y facturación

Servicios compatibles, puede usar etiquetas para agrupar los datos de facturación. Por ejemplo, [máquinas virtuales integrado con el Administrador de recursos de Azure](./virtual-machines/virtual-machines-windows-compare-deployment-models.md) le permiten definir y aplicar etiquetas para organizar el uso de facturación para máquinas virtuales de Windows. Si se están ejecutando varias máquinas virtuales para organizaciones diferentes, puede usar las etiquetas para el uso de un grupo por el centro de costo.  
También puede usar etiquetas para clasificar los costos por el entorno de tiempo de ejecución; Por ejemplo, el uso de facturación para máquinas virtuales que se ejecutan en el entorno de producción.

Puede recuperar información acerca de las etiquetas a través de la [API de RateCard y uso de recursos de Azure](billing-usage-rate-card-overview.md) o el archivo de valores separados por comas (CSV) de uso. Descargar el archivo de uso desde el [portal de Azure cuentas](https://account.windowsazure.com/) o el [portal de Asia Oriental](https://ea.azure.com). Para obtener más información sobre cómo puede obtener acceso a información de facturación, consulte [obtener recomendaciones sobre el consumo de recursos de Microsoft Azure](billing-usage-rate-card-overview.md). Para operaciones de la API de REST, vea [Referencia de la API de REST de Azure facturación](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c).

Al descargar el uso CSV para los servicios que admiten etiquetas con la facturación, las etiquetas aparecerán en la columna de **etiquetas** . Para obtener más detalles, vea [comprender su factura de Microsoft Azure](billing/billing-understand-your-bill.md).

![Vea las etiquetas de facturación](./media/resource-group-using-tags/billing_csv.png)

## <a name="next-steps"></a>Pasos siguientes

- Puede aplicar restricciones y convenciones a través de su suscripción con directivas personalizadas. La directiva que defina puede requerir que todos los recursos tienen un valor de una etiqueta determinada. Para obtener más información, vea [Utilizar una directiva para administrar los recursos y controlar el acceso](resource-manager-policy.md).
- Para obtener una introducción sobre cómo usar PowerShell Azure al implementar los recursos, vea [Usar PowerShell de Azure con el Administrador de recursos de Azure](./powershell-azure-resource-manager.md).
- Para obtener una introducción al uso de Azure CLI al implementar los recursos, vea [utilizar CLI Azure para Mac, Linux y Windows con la administración de recursos de Azure](./xplat-cli-azure-resource-manager.md).
- Para obtener una introducción sobre cómo usar el portal, consulte [con el portal de Azure para administrar los recursos de Azure](./azure-portal/resource-group-portal.md)  
