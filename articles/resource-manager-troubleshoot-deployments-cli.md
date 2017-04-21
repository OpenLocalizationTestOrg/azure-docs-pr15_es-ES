<properties
   pageTitle="Ver las operaciones de implementación con Azure CLI | Microsoft Azure"
   description="Describe cómo utilizar la CLI de Azure para detectar problemas de implementación de administrador de recursos."
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
   ms.date="08/15/2016"
   ms.author="tomfitz"/>

# <a name="view-deployment-operations-with-azure-cli"></a>Operaciones de implementación de vista con CLI de Azure

> [AZURE.SELECTOR]
- [Portal](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [CLI de Azure](resource-manager-troubleshoot-deployments-cli.md)
- [API DE REST](resource-manager-troubleshoot-deployments-rest.md)

Si ha recibido un error al implementar recursos en Azure, desea ver más detalles acerca de las operaciones de implementación que se han ejecutado. Azure CLI proporciona comandos que le permiten encontrar los errores y determinar las soluciones posibles.

[AZURE.INCLUDE [resource-manager-troubleshoot-introduction](../includes/resource-manager-troubleshoot-introduction.md)]

Puede evitar algunos errores al validar la plantilla y la infraestructura antes de la implementación. También puede registrar solicitud adicional e información de respuesta durante la implementación que puede ser útil más adelante para solucionar este problema. Para obtener información sobre la validación y registrar información de convocatoria y respuesta, vea [implementar un grupo de recursos con el Administrador de recursos de Azure plantilla](resource-group-template-deploy-cli.md).

## <a name="use-audit-logs-to-troubleshoot"></a>Usar registros de auditoría para solucionar problemas

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

Para ver errores para una implementación, siga estos pasos:

1. Para ver los registros de auditoría, ejecute el comando **Mostrar registro de grupo de azure** . Puede incluir el **--última implementación** opción para recuperar solo el registro para la implementación de más reciente.

        azure group log show ExampleGroup --last-deployment

2. El comando **Mostrar grupo de azure registro** devuelve una gran cantidad de información. Para solucionar este problema, normalmente desea centrarse en las operaciones de error. La siguiente secuencia de comandos usa la opción **--json** y la utilidad JSON [jq](https://stedolan.github.io/jq/) para buscar en el registro de errores de implementación.

        azure group log show ExampleGroup --json | jq '.[] | select(.status.value == "Failed")'
        
        {
        "claims": {
          "aud": "https://management.core.windows.net/",
          "iss": "https://sts.windows.net/<guid>/",
          "iat": "1442510510",
          "nbf": "1442510510",
          "exp": "1442514410",
          "ver": "1.0",
          "http://schemas.microsoft.com/identity/claims/tenantid": "{guid}",
          "http://schemas.microsoft.com/identity/claims/objectidentifier": "{guid}",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress": "someone@example.com",
          "puid": "XXXXXXXXXXXXXXXX",
          "http://schemas.microsoft.com/identity/claims/identityprovider": "example.com",
          "altsecid": "1:example.com:XXXXXXXXXXX",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "<hash string="">",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "Tom",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "FitzMacken",
          "name": "Tom FitzMacken",
          "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
          "groups": "{guid}",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "example.com#someone@example.com",
          "wids": "{guid}",
          "appid": "{guid}",
          "appidacr": "0",
          "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
          "http://schemas.microsoft.com/claims/authnclassreference": "1",
          "ipaddr": "000.000.000.000"
        },
        "properties": {
          "statusCode": "Conflict",
          "statusMessage": "{\"Code\":\"Conflict\",\"Message\":\"Website with given name mysite already exists.\",\"Target\":null,\"Details\":[{\"Message\":\"Website with given name
            mysite already exists.\"},{\"Code\":\"Conflict\"},{\"ErrorEntity\":{\"Code\":\"Conflict\",\"Message\":\"Website with given name mysite already exists.\",\"ExtendedCode\":
            \"54001\",\"MessageTemplate\":\"Website with given name {0} already exists.\",\"Parameters\":[\"mysite\"],\"InnerErrors\":null}}],\"Innererror\":null}"
        },
        ...

    Puede ver **Propiedades** incluye información en json sobre la operación con error.

    Puede usar el **--detallado** y **- vv** opciones para ver más información de los registros.  Usar el **--detallado** opción para mostrar los pasos de las operaciones que se vaya a través de `stdout`. Para un historial de completar la solicitud, use la opción **- vv** . Los mensajes con frecuencia proporcionan indicaciones sobre la causa de los errores.

3. Para centrarse en el mensaje de estado para entradas error, use el siguiente comando:

        azure group log show ExampleGroup --json | jq -r ".[] | select(.status.value == \"Failed\") | .properties.statusMessage"


## <a name="use-deployment-operations-to-troubleshoot"></a>Utilizar las operaciones de implementación para solucionar problemas

1. Obtener el estado general de una implementación con el comando **Mostrar implementación de azure grupo** . En el ejemplo siguiente error en la implementación.

        azure group deployment show --resource-group ExampleGroup --name ExampleDeployment
        
        info:    Executing command group deployment show
        + Getting deployments
        data:    DeploymentName     : ExampleDeployment
        data:    ResourceGroupName  : ExampleGroup
        data:    ProvisioningState  : Failed
        data:    Timestamp          : 2015-08-27T20:03:34.9178576Z
        data:    Mode               : Incremental
        data:    Name             Type    Value
        data:    ---------------  ------  ------------
        data:    siteName         String  ExampleSite
        data:    hostingPlanName  String  ExamplePlan
        data:    siteLocation     String  West US
        data:    sku              String  Free
        data:    workerSize       String  0
        info:    group deployment show command OK

2. Para ver el mensaje de error operaciones para una implementación, use:

        azure group deployment operation list --resource-group ExampleGroup --name ExampleDeployment --json  | jq ".[] | select(.properties.provisioningState == \"Failed\") | .properties.statusMessage.Message"


## <a name="next-steps"></a>Pasos siguientes

- Para obtener ayuda con la resolución de errores de implementación particular, consulte [resolver errores comunes al implementar recursos de Azure con el Administrador de recursos de Azure](resource-manager-common-deployment-errors.md).
- Para obtener información sobre cómo usar los registros de auditoría para supervisar otros tipos de acciones, consulte [con el Administrador de recursos de operaciones de auditoría](resource-group-audit.md).
- Para validar la implementación antes de ejecutarlo, vea [implementar un grupo de recursos con el Administrador de recursos de Azure plantilla](resource-group-template-deploy.md).
