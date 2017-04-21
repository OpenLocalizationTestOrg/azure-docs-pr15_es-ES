<properties
   pageTitle="Implementar recursos con la API de REST y plantilla | Microsoft Azure"
   description="Usar el Administrador de recursos de Azure y REST API de administrador de recursos para implementar un recursos Azure. Los recursos se definen en una plantilla de administrador de recursos."
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
   ms.date="07/11/2016"
   ms.author="tomfitz"/>

# <a name="deploy-resources-with-resource-manager-templates-and-resource-manager-rest-api"></a>Implementar recursos a las plantillas de administrador de recursos y REST API de administrador de recursos

> [AZURE.SELECTOR]
- [PowerShell](resource-group-template-deploy.md)
- [CLI de Azure](resource-group-template-deploy-cli.md)
- [Portal](resource-group-template-deploy-portal.md)
- [API DE REST](resource-group-template-deploy-rest.md)

En este artículo se explica cómo usar la API de REST de administrador de recursos con las plantillas de administrador de recursos para implementar los recursos de Azure.  

> [AZURE.TIP] Para obtener ayuda con un error de depuración durante la implementación, consulte:
>
> - [Operaciones de implementación de vista con la API de REST](resource-manager-troubleshoot-deployments-rest.md) para obtener información sobre cómo obtener la información que le ayuda a solucionar el error
> - [Solución de problemas comunes al implementar recursos de Azure con el Administrador de recursos de Azure](resource-manager-common-deployment-errors.md) para obtener información sobre cómo resolver los errores comunes de implementación

La plantilla puede ser un archivo local o en un archivo externo que está disponible a través de un URI. Cuando la plantilla se encuentra en una cuenta de almacenamiento, puede restringir el acceso a la plantilla y proporcionar un token de acceso compartido de firma (SA) durante la implementación.

[AZURE.INCLUDE [resource-manager-deployments](../includes/resource-manager-deployments.md)]

## <a name="deploy-with-the-rest-api"></a>Implementar con la API de REST
1. Establezca [encabezados y parámetros comunes](https://msdn.microsoft.com/library/azure/8d088ecc-26eb-42e9-8acc-fe929ed33563#bk_common), incluidos los símbolos de autenticación.
2. Si no tiene un grupo de recursos existente, cree un grupo de recursos. Proporcione el identificador de la suscripción, el nombre del nuevo grupo de recursos y la ubicación que necesita para la solución. Para obtener más información, vea [crear un grupo de recursos](https://msdn.microsoft.com/library/azure/dn790525.aspx).

        PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2015-01-01
          <common headers>
          {
            "location": "West US",
            "tags": {
               "tagname1": "tagvalue1"
            }
          }
   
3. Validar la implementación antes de ejecutar al ejecutar la operación [validar una implementación de la plantilla](https://msdn.microsoft.com/library/azure/dn790547.aspx) . Cuando se prueba la implementación, proporcionar parámetros tal como lo haría al ejecutar la implementación (que se muestra en el siguiente paso).

3. Crear una implementación. Proporciona su identificación de suscripción, el nombre del grupo de recursos para implementar, el nombre de la implementación y un vínculo a la plantilla. Para obtener información sobre el archivo de plantilla, vea el [archivo de parámetros](#parameter-file). Para obtener más información acerca de la API de REST para crear un grupo de recursos, vea [crear una implementación de la plantilla](https://msdn.microsoft.com/library/azure/dn790564.aspx). Observe que el **modo** está establecido en **incremento**. Para ejecutar una implementación completa, establezca **el modo** en **completado**. Tenga cuidado al usar el modo completo como accidentalmente puede eliminar los recursos que no están en la plantilla.
    
        PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
          <common headers>
          {
            "properties": {
              "templateLink": {
                "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
                "contentVersion": "1.0.0.0",
              },
              "mode": "Incremental",
              "parametersLink": {
                "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
                "contentVersion": "1.0.0.0",
              }
            }
          }
   
      Si desea registrar el contenido de la respuesta, el contenido de la solicitud o ambos, incluyen **debugSetting** en la convocatoria.

        "debugSetting": {
          "detailLevel": "requestContent, responseContent"
        }

      Puede configurar su cuenta de almacenamiento para que use un token de acceso compartido firma (SA). Para obtener más información, vea [Delegar acceso con una firma de acceso compartido](https://msdn.microsoft.com/library/ee395415.aspx).

4. Obtener el estado de la implementación de la plantilla. Para obtener más información, consulte [obtener información acerca de una implementación de la plantilla](https://msdn.microsoft.com/library/azure/dn790565.aspx).

          GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
           <common headers>

[AZURE.INCLUDE [resource-manager-parameter-file](../includes/resource-manager-parameter-file.md)]

## <a name="next-steps"></a>Pasos siguientes
- Para obtener un ejemplo de implementación de recursos a través de la biblioteca de cliente .NET, vea [recursos de implementar mediante una plantilla y bibliotecas de .NET](virtual-machines/virtual-machines-windows-csharp-template.md).
- Para definir parámetros de plantilla, consulte [creación de plantillas](resource-group-authoring-templates.md#parameters).
- Para obtener instrucciones sobre cómo implementar la solución en distintos entornos, consulte [entornos de desarrollo y prueba en Microsoft Azure](solution-dev-test-environments.md).
- Para obtener información detallada sobre el uso de una referencia KeyVault pasar valores seguros, vea [pasar valores seguros durante la implementación](resource-manager-keyvault-parameter.md).
