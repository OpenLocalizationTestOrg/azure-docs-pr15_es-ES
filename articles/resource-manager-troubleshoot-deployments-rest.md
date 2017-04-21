<properties
   pageTitle="Ver las operaciones de implementación con la API de REST | Microsoft Azure"
   description="Describe cómo utilizar la API de REST de administrador de recursos de Azure para detectar problemas de implementación de administrador de recursos."
   services="azure-resource-manager,virtual-machines"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure"
   ms.date="06/13/2016"
   ms.author="tomfitz"/>

# <a name="view-deployment-operations-with-azure-resource-manager-rest-api"></a>Operaciones de implementación de vista con la REST API de administrador de recursos de Azure

> [AZURE.SELECTOR]
- [Portal](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [CLI de Azure](resource-manager-troubleshoot-deployments-cli.md)
- [API DE REST](resource-manager-troubleshoot-deployments-rest.md)

Si ha recibido un error al implementar recursos en Azure, desea ver más detalles acerca de las operaciones de implementación que se han ejecutado. La API de REST proporciona operaciones que le permiten encontrar los errores y determinar las soluciones posibles.

[AZURE.INCLUDE [resource-manager-troubleshoot-introduction](../includes/resource-manager-troubleshoot-introduction.md)]

Puede evitar algunos errores al validar la plantilla y la infraestructura antes de la implementación. También puede registrar solicitud adicional e información de respuesta durante la implementación que puede ser útil más adelante para solucionar este problema. Para obtener información sobre la validación y registrar información de convocatoria y respuesta, vea [implementar un grupo de recursos con el Administrador de recursos de Azure plantilla](resource-group-template-deploy-rest.md).

## <a name="troubleshoot-with-rest-api"></a>Solucionar problemas con la API de REST

1. Implementar los recursos de la operación de [creación de una implementación de la plantilla](https://msdn.microsoft.com/library/azure/dn790564.aspx) . Para conservar la información que puede ser útil para depurar, establezca la propiedad de **debugSetting** en solicitud de JSON para **requestContent** o **responseContent**. 

        PUT https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
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
              },
              "debugSetting": {
                "detailLevel": "requestContent, responseContent"
              }
            }
          }

    De forma predeterminada, el valor de **debugSetting** se establece en **Ninguno**. Al especificar el valor de **debugSetting** , considere detenidamente el tipo de información que se pasa en durante la implementación. Al registrar información sobre la solicitud y la respuesta, se podrían exponer datos confidenciales que se recuperen a través de las operaciones de implementación. 

2. Obtener información acerca de una implementación de la operación de [obtener información acerca de una implementación de la plantilla](https://msdn.microsoft.com/library/azure/dn790565.aspx) .

        GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}

    En la respuesta, tenga en cuenta los elementos **provisioningState** , **correlationId** y **errores** . El **correlationId** se usa para realizar un seguimiento de los eventos relacionados y puede ser útil cuando se trabaja con el soporte técnico para solucionar un problema.
    
        { 
          ...
          "properties": {
            "provisioningState":"Failed",
            "correlationId":"d5062e45-6e9f-4fd3-a0a0-6b2c56b15757",
            ...
            "error":{
              "code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see http://aka.ms/arm-debug for usage details.",
              "details":[{"code":"Conflict","message":"{\r\n  \"error\": {\r\n    \"message\": \"Conflict\",\r\n    \"code\": \"Conflict\"\r\n  }\r\n}"}]
            }  
          }
        }

3. Obtenga información acerca de las operaciones de implementación con la operación de [todas las operaciones de implementación de plantilla de lista](https://msdn.microsoft.com/library/azure/dn790518.aspx) . 

        GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}

    La respuesta incluye información de solicitud o respuesta según lo especificado en la propiedad **debugSetting** durante la implementación.
    
        {
          ...
          "properties": 
          {
            ...
            "request":{
              "content":{
                "location":"West US",
                "properties":{
                  "accountType": "Standard_LRS"
                }
              }
            },
            "response":{
              "content":{
                "error":{
                  "message":"Conflict","code":"Conflict"
                }
              }
            }
          }
        }

4. Obtener eventos de los registros de auditoría para la implementación con la operación de [los eventos de administración de una suscripción de la lista](https://msdn.microsoft.com/library/azure/dn931934.aspx) .

        GET https://management.azure.com/subscriptions/{subscription-id}/providers/microsoft.insights/eventtypes/management/values?api-version={api-version}&$filter={filter-expression}&$select={comma-separated-property-names}


## <a name="next-steps"></a>Pasos siguientes

- Para obtener ayuda con la resolución de errores de implementación particular, consulte [resolver errores comunes al implementar recursos de Azure con el Administrador de recursos de Azure](resource-manager-common-deployment-errors.md).
- Para obtener información sobre cómo usar los registros de auditoría para supervisar otros tipos de acciones, consulte [con el Administrador de recursos de operaciones de auditoría](resource-group-audit.md).
- Para validar la implementación antes de ejecutar en él, vea [implementar un grupo de recursos con el Administrador de recursos de Azure plantilla](resource-group-template-deploy.md).
