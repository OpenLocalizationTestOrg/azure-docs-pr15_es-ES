<properties
   pageTitle="Implementar recursos con PowerShell y plantilla | Microsoft Azure"
   description="Usar el Administrador de recursos de Azure y Azure PowerShell para implementar un recursos Azure. Los recursos se definen en una plantilla de administrador de recursos."
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
   ms.date="08/15/2016"
   ms.author="tomfitz"/>

# <a name="deploy-resources-with-resource-manager-templates-and-azure-powershell"></a>Implementar recursos a las plantillas de administrador de recursos y PowerShell de Azure

> [AZURE.SELECTOR]
- [PowerShell](resource-group-template-deploy.md)
- [CLI de Azure](resource-group-template-deploy-cli.md)
- [Portal](resource-group-template-deploy-portal.md)
- [API DE REST](resource-group-template-deploy-rest.md)

En este tema se explica cómo usar PowerShell de Azure con las plantillas de administrador de recursos para implementar los recursos de Azure.  

> [AZURE.TIP] Para obtener ayuda con un error de depuración durante la implementación, consulte:
>
> - [Operaciones de implementación de vista con Azure PowerShell](resource-manager-troubleshoot-deployments-powershell.md) para obtener información sobre cómo obtener la información que le ayuda a solucionar el error
> - [Solución de problemas comunes al implementar recursos de Azure con el Administrador de recursos de Azure](resource-manager-common-deployment-errors.md) para obtener información sobre cómo resolver los errores comunes de implementación

La plantilla puede ser un archivo local o en un archivo externo que está disponible a través de un URI. Cuando la plantilla se encuentra en una cuenta de almacenamiento, puede restringir el acceso a la plantilla y proporcionar un token de acceso compartido de firma (SA) durante la implementación.

## <a name="quick-steps-to-deployment"></a>Pasos rápidos para su implementación

En este artículo se describe todas las distintas opciones disponibles durante la implementación. Sin embargo, a menudo solo necesita dos comandos simples. Para comenzar rápidamente con la implementación, use los comandos siguientes:

    New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West US"
    New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -TemplateParameterFile <PathToParameterFile>

Para obtener más información acerca de las opciones de implementación que podría ser mejor a su situación, continúe leyendo este artículo.

[AZURE.INCLUDE [resource-manager-deployments](../includes/resource-manager-deployments.md)]

## <a name="deploy-with-powershell"></a>Implementar con PowerShell

1. Inicie sesión en su cuenta de Azure.

        Add-AzureRmAccount

     Se devuelve un resumen de su cuenta.

        Environment : AzureCloud
        Account     : someone@example.com
        ...

2. Si tiene varias suscripciones, proporcione el identificador de la suscripción que desea usar para su implementación con el comando **Conjunto AzureRmContext** . 

        Set-AzureRmContext -SubscriptionID <YourSubscriptionId>

3. Normalmente, al implementar una nueva plantilla, que desea crear un grupo de recursos que contengan los recursos. Si tiene un grupo de recursos existente que desee para implementar, puede omitir este paso y usar ese grupo de recursos. 

     Para crear un grupo de recursos, proporcione un nombre y una ubicación para el grupo de recursos. Debe proporcionar una ubicación para el grupo de recursos, porque el grupo de recursos almacena metadatos acerca de los recursos. Por motivos de cumplimiento, desea especificar dónde se almacenan metadatos. En general, recomendamos que especifique una ubicación donde se guardarán la mayoría de los recursos. Uso de la misma ubicación, puede simplificar la plantilla.

        New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West US"
   
     Se devuelve un resumen del nuevo grupo de recursos.
   
        ResourceGroupName : ExampleResourceGroup
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
             Actions  NotActions
             =======  ==========
             *
        ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup

4. Antes de ejecutar la implementación, puede validar la configuración de implementación. El cmdlet **AzureRmResourceGroupDeployment de prueba** le permite buscar problemas antes de crear recursos reales. En el ejemplo siguiente se muestra cómo validar una implementación.

        Test-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate>

5. Para implementar recursos en el grupo de recursos, ejecute el comando **AzureRmResourceGroupDeployment de nuevo** y proporcione los parámetros necesarios. Los parámetros incluyen un nombre para la implementación, el nombre de su grupo de recursos, la ruta de acceso o la dirección URL a la plantilla que creó y otros parámetros necesarias para su situación. Si no se especifica el parámetro de **modo** , se usa el valor predeterminado del **incremento** . Para ejecutar una implementación completa, establezca **el modo** en **completado**. Tenga cuidado al usar el modo completo como accidentalmente puede eliminar los recursos que no están en la plantilla.

     Para implementar una plantilla local, use el parámetro **TemplateFile** :

        New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate>

     Para implementar una plantilla de externa, utilice el parámetro de **TemplateUri** :

        New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri <LinkToTemplate>
   
     Tiene las opciones siguientes para proporcionar los valores de parámetro: 
   
     1. Usar parámetros en línea.

            New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -myParameterName "parameterValue"

     2. Utilizar un objeto de parámetro.

            $parameters = @{"<ParameterName>"="<Parameter Value>"}
            New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -TemplateParameterObject $parameters

     3. Usar un archivo de parámetro local. Para obtener información sobre el archivo de plantilla, vea el [archivo de parámetros](#parameter-file).

            New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -TemplateParameterFile <PathToParameterFile>

     4. Usar un archivo de parámetro externas. Para obtener información sobre el archivo de plantilla, vea el [archivo de parámetros](#parameter-file). 

            New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri <LinkToTemplate> -TemplateParameterUri <LinkToParameterFile>

        Cuando se utiliza un archivo de parámetro externas, no se puede pasar a otros valores ya sea en línea o desde un archivo local. Para obtener más información, vea [prioridad de parámetro](#parameter-precendence).

     Después de que se han implementado los recursos, se muestra un resumen de la implementación.

        DeploymentName    : ExampleDeployment
        ResourceGroupName : ExampleResourceGroup
        ProvisioningState : Succeeded
        Timestamp         : 4/14/2015 7:00:27 PM
        Mode              : Incremental
        ...

     Si la plantilla incluye un parámetro con el mismo nombre que uno de los parámetros en el comando de PowerShell, le pide que proporcione un valor de dicho parámetro. El parámetro de la plantilla incluirá el sufijo **FromTemplate**. Por ejemplo, un parámetro denominado **ResourceGroupName** en su plantilla entra en conflicto con el parámetro **ResourceGroupName** en el cmdlet [AzureRmResourceGroupDeployment de nuevo](https://msdn.microsoft.com/library/azure/mt679003.aspx) . Le pide que proporcione un valor para **ResourceGroupNameFromTemplate**. En general, debe evitar esta confusión por no nombrar parámetros con el mismo nombre que los parámetros que se utilizan para operaciones de implementación.

6. Si desea registrar información adicional acerca de la implementación que puede ayudarle a solucionar los errores de implementación, use el parámetro **DeploymentDebugLogLevel** . Puede especificar que el contenido de la solicitud, el contenido de respuesta o ambos haber iniciado con la operación de implementación.

        New-AzureRmResourceGroupDeployment -Name ExampleDeployment -DeploymentDebugLogLevel All -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate>
        
     Para obtener más información sobre cómo usar este contenido depuración para implementaciones de solución de problemas, vea [implementaciones de grupo de recursos de solución de problemas con PowerShell de Azure](resource-manager-troubleshoot-deployments-powershell.md).

## <a name="deploy-template-from-storage-with-sas-token"></a>Implementar la plantilla de almacenamiento con token SA

Puede agregar sus plantillas a una cuenta de almacenamiento y un vínculo a ellos durante la implementación con un símbolo de SA.

> [AZURE.IMPORTANT] Siguiendo los pasos siguientes, el blob que contiene la plantilla es accesible para el propietario de la cuenta. Sin embargo, cuando se crea un token de SA para el blob, el blob es accesible para cualquiera con ese URI. Si otro usuario intercepta el URI, ese usuario es tener acceso a la plantilla. Utilizando un token SA es una buena manera de limitar el acceso a sus plantillas, pero no debe incluir datos confidenciales como contraseñas directamente en la plantilla.

### <a name="add-private-template-to-storage-account"></a>Agregar plantilla privado a la cuenta de almacenamiento

Establecer una cuenta de almacenamiento para las plantillas de los siguientes pasos:

1. Crear un grupo de recursos.

        New-AzureRmResourceGroup -Name ManageGroup -Location "West US"

2. Crear una cuenta de almacenamiento. El nombre de la cuenta de almacenamiento debe ser único en Azure, así que especifique su propio nombre de la cuenta.

        New-AzureRmStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates -Type Standard_LRS -Location "West US"

3. Configurar la cuenta de almacenamiento actual.

        Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates

4. Crear un contenedor. El permiso está establecido en **desactivar** lo que significa que el contenedor sólo es accesible para el propietario.

        New-AzureStorageContainer -Name templates -Permission Off
        
5. Agregar la plantilla al contenedor.

        Set-AzureStorageBlobContent -Container templates -File c:\Azure\Templates\azuredeploy.json
        
### <a name="provide-sas-token-during-deployment"></a>Proporcionar token SA durante la implementación

Para implementar una plantilla privada en una cuenta de almacenamiento, recuperar un token SA e incluirlo en el URI de la plantilla.

1. Si ha cambiado la cuenta de almacenamiento actual, establezca la cuenta de almacenamiento actual a la que contiene las plantillas.

        Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates

2. Crear un símbolo de SA con permisos de lectura y una fecha de caducidad para limitar el acceso. Recuperar el URI completo de la plantilla, incluido el token de SA.

        $templateuri = New-AzureStorageBlobSASToken -Container templates -Blob azuredeploy.json -Permission r -ExpiryTime (Get-Date).AddHours(2.0) -FullUri

3. Implementar la plantilla proporcionando el URI que incluye el token de SA.

        New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri $templateuri

Para obtener un ejemplo de un símbolo de SA con las plantillas vinculadas, consulte [uso de plantillas vinculadas con el Administrador de recursos de Azure](resource-group-linked-templates.md).

[AZURE.INCLUDE [resource-manager-parameter-file](../includes/resource-manager-parameter-file.md)]

## <a name="parameter-precedence"></a>Prioridad de parámetro

Puede usar un archivo de parámetro local en la misma operación de implementación y parámetros de en línea. Por ejemplo, puede especificar algunos valores en el archivo de parámetro local y agregar otros valores en línea durante la implementación. Si proporciona valores para un parámetro en el archivo de parámetro local y en línea, el valor insertado tiene prioridad.

Sin embargo, no puede usar parámetros en línea con un archivo de parámetro externas. Al especificar un archivo de parámetros en el parámetro **TemplateParameterUri** , se pasan por alto todos los parámetros en línea. Debe proporcionar todos los valores de parámetro en el archivo externo. Si la plantilla incluye un valor confidencial que no se incluye en el archivo de parámetros, agregar ese valor a un depósito clave y hacer referencia a la cámara clave en el archivo de parámetro externas o proporcionar dinámicamente todos los valores de parámetro en línea.

Para obtener información detallada sobre el uso de una referencia KeyVault pasar valores seguros, vea [pasar valores seguros durante la implementación](resource-manager-keyvault-parameter.md).

## <a name="next-steps"></a>Pasos siguientes
- Para obtener un ejemplo de implementación de recursos a través de la biblioteca de cliente .NET, vea [recursos de implementar mediante una plantilla y bibliotecas de .NET](virtual-machines/virtual-machines-windows-csharp-template.md).
- Para definir parámetros de plantilla, consulte [creación de plantillas](resource-group-authoring-templates.md#parameters).
- Para obtener instrucciones sobre cómo implementar la solución en distintos entornos, consulte [entornos de desarrollo y prueba en Microsoft Azure](solution-dev-test-environments.md).

