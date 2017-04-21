<properties
   pageTitle="Ver las operaciones de implementación con PowerShell | Microsoft Azure"
   description="Describe cómo usar Azure PowerShell para detectar problemas de implementación de administrador de recursos."
   services="azure-resource-manager,virtual-machines"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure"
   ms.date="06/14/2016"
   ms.author="tomfitz"/>

# <a name="view-deployment-operations-with-azure-powershell"></a>Operaciones de implementación de vista con PowerShell de Azure

> [AZURE.SELECTOR]
- [Portal](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [CLI de Azure](resource-manager-troubleshoot-deployments-cli.md)
- [API DE REST](resource-manager-troubleshoot-deployments-rest.md)

Puede ver las operaciones de una implementación a través de PowerShell de Azure. Es posible que más le interese viendo las operaciones cuando haya recibido un error durante la implementación para que este artículo se centra en operaciones erróneas de visualización. PowerShell ofrece cmdlets que le permiten encontrar los errores fácilmente y determinar las soluciones posibles.

[AZURE.INCLUDE [resource-manager-troubleshoot-introduction](../includes/resource-manager-troubleshoot-introduction.md)]

Puede evitar algunos errores al validar la plantilla y la infraestructura antes de la implementación. También puede registrar solicitud adicional e información de respuesta durante la implementación que puede ser útil más adelante para solucionar este problema. Para obtener información sobre la validación y registrar información de convocatoria y respuesta, vea [implementar un grupo de recursos con el Administrador de recursos de Azure plantilla](resource-group-template-deploy.md).

## <a name="use-deployment-operations-to-troubleshoot"></a>Utilizar las operaciones de implementación para solucionar problemas

1. Para obtener el estado general de una implementación, use el comando **Get-AzureRmResourceGroupDeployment** . Puede filtrar los resultados para solo implementaciones de error.

        Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup | Where-Object ProvisioningState -eq Failed
        
    Que devuelve el error implementaciones en el siguiente formato:
        
        DeploymentName          : Microsoft.Template
        ResourceGroupName       : ExampleGroup
        ProvisioningState       : Failed
        Timestamp               : 6/14/2016 9:55:21 PM
        Mode                    : Incremental
        TemplateLink            :
        Parameters              :
                    Name                Type                 Value
                    ===============     ===================  ==========
                    storageAccountName  String               tfstorage9855
                    adminUsername       String               tfadmin
                    adminPassword       SecureString
                    dnsNameforLBIP      String               dns
                    vmNamePrefix        String               myVM
                    imagePublisher      String               MicrosoftWindowsServer
                    imageOffer          String               WindowsServer
                    imageSKU            String               2012-R2-Datacenter
                    lbName              String               myLB
                    nicNamePrefix       String               nic
                    publicIPAddressName String               myPublicIP
                    vnetName            String               myVNET
                    vmSize              String               Standard_D1

        Outputs                 :
        DeploymentDebugLogLevel :

2. Cada implementación normalmente se compone de varias operaciones, con cada operación que representa un paso del proceso de implementación. Para descubrir qué ha fallado con una implementación, normalmente necesita ver detalles acerca de las operaciones de implementación. Puede ver el estado de las operaciones con **Get-AzureRmResourceGroupDeploymentOperation**.

        Get-AzureRmResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName Microsoft.Template
        
    Que devuelve varias operaciones con cada uno de ellos en el siguiente formato:
        
        Id             : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/Microsoft.Template/operations/A3EB2DA598E0A780
        OperationId    : A3EB2DA598E0A780
        Properties     : @{provisioningOperation=Create; provisioningState=Succeeded; timestamp=2016-06-14T21:55:15.0156208Z;
                         duration=PT23.0227078S; trackingId=11d376e8-5d6d-4da8-847e-6f23c6443fbf;
                         serviceRequestId=0196828d-8559-4bf6-b6b8-8b9057cb0e23; statusCode=OK; targetResource=}
        PropertiesText : {duration:PT23.0227078S, provisioningOperation:Create, provisioningState:Succeeded,
                         serviceRequestId:0196828d-8559-4bf6-b6b8-8b9057cb0e23...}

3. Para obtener más detalles acerca de las operaciones error, recuperar las propiedades de las operaciones de estado de **error** .

        (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed
        
    Que devuelve todas las operaciones error con cada uno de ellos en el siguiente formato:
        
        provisioningOperation : Create
        provisioningState     : Failed
        timestamp             : 2016-06-14T21:54:55.1468068Z
        duration              : PT3.1449887S
        trackingId            : f4ed72f8-4203-43dc-958a-15d041e8c233
        serviceRequestId      : a426f689-5d5a-448d-a2f0-9784d14c900a
        statusCode            : BadRequest
        statusMessage         : @{error=}
        targetResource        : @{id=/subscriptions/{guid}/resourceGroups/ExampleGroup/providers/
                                Microsoft.Network/publicIPAddresses/myPublicIP;
                                resourceType=Microsoft.Network/publicIPAddresses; resourceName=myPublicIP}

    Nota El identificador de seguimiento para la operación. Que usa para centrarse en una operación determinada en el paso siguiente.

4. Para obtener el mensaje de estado de una operación particular, utilice el siguiente comando:

        ((Get-AzureRmResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object trackingId -eq f4ed72f8-4203-43dc-958a-15d041e8c233).StatusMessage.error
        
    Que devuelve:
        
        code           message                                                                        details
        ----           -------                                                                        -------
        DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}

## <a name="use-audit-logs-to-troubleshoot"></a>Usar registros de auditoría para solucionar problemas

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

Para ver errores para una implementación, siga estos pasos:

1. Para recuperar las entradas del registro, ejecute el comando **Get-AzureRmLog** . Puede usar los parámetros **ResourceGroup** y **estado** para devolver solo los eventos que no se pudo para un único grupo de recursos. Si no especifica una hora de inicio y finalización, se devuelven las entradas de la última hora.
Por ejemplo, para recuperar las operaciones de error de la última hora ejecutar:

        Get-AzureRmLog -ResourceGroup ExampleGroup -Status Failed

    Puede especificar un intervalo de tiempo determinado. En el ejemplo siguiente, se tratan acciones erróneas para el último día. 

        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-1) -Status Failed
      
    O bien, puede establecer un inicio exacto y la hora de finalización para las acciones de error:

        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00 -EndTime 2015-09-10T06:00 -Status Failed

2. Si el comando devuelve demasiadas entradas y propiedades, puede centrar sus esfuerzos de auditoría por recuperar la propiedad de **Propiedades** . También deberá incluimos el parámetro **DetailedOutput** para ver los mensajes de error.

        (Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-1) -DetailedOutput).Properties
        
    Que devuelve las propiedades de las entradas del registro en el siguiente formato:
        
        Content
        -------
        {} 
        {[statusCode, BadRequest], [statusMessage, {"error":{"code":"DnsRecordInUse","message":"DNS record dns.westus.clouda...
        {[statusCode, BadRequest], [serviceRequestId, a426f689-5d5a-448d-a2f0-9784d14c900a], [statusMessage, {"error":{"code...

3. En función de estos resultados, nos centraremos en el segundo elemento. Puede refinar los resultados consultando el mensaje de estado de esa entrada.

        ((Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput -StartTime (Get-Date).AddDays(-1)).Properties[1].Content["statusMessage"] | ConvertFrom-Json).error
        
    Que devuelve:
        
        code           message                                                                        details
        ----           -------                                                                        -------
        DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}



## <a name="next-steps"></a>Pasos siguientes

- Para obtener ayuda con la resolución de errores de implementación particular, consulte [resolver errores comunes al implementar recursos de Azure con el Administrador de recursos de Azure](resource-manager-common-deployment-errors.md).
- Para obtener información sobre cómo usar los registros de auditoría para supervisar otros tipos de acciones, consulte [con el Administrador de recursos de operaciones de auditoría](resource-group-audit.md).
- Para validar la implementación antes de ejecutar en él, vea [implementar un grupo de recursos con el Administrador de recursos de Azure plantilla](resource-group-template-deploy.md).

