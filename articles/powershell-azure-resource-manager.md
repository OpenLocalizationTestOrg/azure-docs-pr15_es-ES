<properties 
    pageTitle="Con el Administrador de recursos de PowerShell Azure | Microsoft Azure" 
    description="Introducción al uso de Azure PowerShell para implementar varios recursos como un grupo de recursos a Azure." 
    services="azure-resource-manager" 
    documentationCenter="" 
    authors="tfitzmac" 
    manager="timlt" 
    editor="tysonn"/>

<tags 
    ms.service="azure-resource-manager" 
    ms.workload="multiple" 
    ms.tgt_pltfrm="powershell" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/18/2016" 
    ms.author="tomfitz"/>

# <a name="using-azure-powershell-with-azure-resource-manager"></a>Usar PowerShell Azure con el Administrador de recursos Azure

> [AZURE.SELECTOR]
- [Portal](azure-portal/resource-group-portal.md) 
- [CLI de Azure](xplat-cli-azure-resource-manager.md)
- [Azure PowerShell](powershell-azure-resource-manager.md)
- [API DE REST](resource-manager-rest-api.md)


Administrador de recursos de Azure implementa un enfoque moderno para control de ciclo de vida de Azure recursos. En lugar de crear y administrar recursos individuales, empiece por imaginándose una solución completa, por ejemplo, un blog, una galería de fotografías, un portal de SharePoint o un wiki. Use una plantilla--una representación descriptiva de la solución: para definir un grupo de recursos que contiene todos los recursos que necesita compatibilidad con la solución. A continuación, implementar y administrar ese grupo de recursos como una unidad lógica. 

En este tutorial, aprenderá a usar PowerShell de Azure con el Administrador de recursos de Azure. Le guiará por el proceso de implementar una solución y trabajar con esa solución. Usará Azure PowerShell y una plantilla de administrador de recursos para implementar:

- SQL server - para hospedar la base de datos
- Base de datos SQL - para almacenar los datos
- Reglas de Firewall: para permitir que la aplicación web para conectarse a la base de datos
- Plan de servicios de aplicación - para definir las capacidades y el costo de la aplicación web
- Sitio Web - para ejecutar la aplicación web
- Configuración de Web - para almacenar la cadena de conexión a la base de datos 
- Reglas de alerta - para la supervisión de rendimiento y errores
- Perspectivas de aplicación - configuración de escala automático

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, necesita:

- Una cuenta de Azure
  + Puede [Abrir una cuenta de Azure gratis](/pricing/free-trial/?WT.mc_id=A261C142F): obtenga créditos puede usar para probar los servicios de Azure pagados e incluso después de que se utilizan hasta puede mantener la cuenta y usar libre Azure servicios, como sitios Web. Nunca se cargará su tarjeta de crédito a menos que explícitamente cambia la configuración y pídale que se cargará.
  
  + Puede [activar las ventajas de suscriptor MSDN](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): MSDN su suscripción le proporciona créditos cada mes que puede usar para los servicios de Azure pagados.
- Azure PowerShell 1.0. Para obtener información acerca de esta versión y cómo instalarlo, consulte [cómo instalar y configurar Azure PowerShell](powershell-install-configure.md).

En este tutorial se ha diseñado para principiantes de PowerShell, pero se supone que entender los conceptos básicos, como los módulos, cmdlets y sesiones.

## <a name="get-help-for-cmdlets"></a>Obtener ayuda sobre los cmdlets

Para obtener ayuda detallada para cualquier cmdlet que se observan en este tutorial, use el cmdlet Get-Help. 

    Get-Help <cmdlet-name> -Detailed

Por ejemplo, para obtener ayuda para el cmdlet Get-AzureRmResource, escriba:

    Get-Help Get-AzureRmResource -Detailed

Para obtener una lista de cmdlets en el módulo de recursos con un resumen de la Ayuda, escriba: 

    Get-Command -Module AzureRM.Resources | Get-Help | Format-Table Name, Synopsis

El resultado tendrá un aspecto similar al siguiente fragmento:

    Name                                   Synopsis
    ----                                   --------
    Find-AzureRmResource                   Searches for resources using the specified parameters.
    Find-AzureRmResourceGroup              Searches for resource group using the specified parameters.
    Get-AzureRmADGroup                     Filters active directory groups.
    Get-AzureRmADGroupMember               Get a group members.
    ...

Para obtener ayuda completa de un cmdlet, escriba un comando con el formato:

    Get-Help <cmdlet-name> -Full
  
## <a name="login-to-your-azure-account"></a>Inicie sesión en su cuenta de Azure

Antes de trabajar en la solución, debe iniciar sesión en su cuenta.

Para iniciar sesión en su cuenta de Azure, use el cmdlet **Add-AzureRmAccount** .

    Add-AzureRmAccount

El cmdlet le solicitará las credenciales de inicio de sesión de su cuenta de Azure. Después de iniciar sesión, descarga la configuración de cuenta para que estén disponibles para Azure PowerShell. 

La configuración de la cuenta caduca, por lo que necesitará actualizarlos ocasionalmente. Para actualizar a la configuración de la cuenta, vuelva a ejecutar el **Complemento AzureRmAccount** . 

>[AZURE.NOTE] Los módulos del Administrador de recursos requiere agregar AzureRmAccount. Un archivo de configuración de publicación no es suficiente.     

Si tiene más de una suscripción, proporcione el identificador de la suscripción que desea usar para su implementación con el cmdlet **Set-AzureRmContext** .

    Set-AzureRmContext -SubscriptionID <YourSubscriptionId>

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Antes de implementar los recursos a su suscripción, debe crear un grupo de recursos que contenga los recursos. 

Para crear un grupo de recursos, use el cmdlet **AzureRmResourceGroup de nuevo** .

El comando utiliza el **nombre** de parámetro para especificar un nombre para el grupo de recursos y el parámetro de **ubicación** para especificar su ubicación. En función de lo que hemos descubierto en la sección anterior, usaremos "US Oeste" para la ubicación.

    New-AzureRmResourceGroup -Name TestRG1 -Location "West US"
    
El resultado será similar al:

    ResourceGroupName : TestRG1
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1

Se ha creado correctamente el grupo de recursos.

## <a name="deploy-your-solution"></a>Implementar la solución

En este tema no muestra cómo crear una plantilla o analizar la estructura de la plantilla. Para esa información, vea [Tutorial de plantilla de administrador de recursos](resource-manager-template-walkthrough.md)y [plantillas de administrador de recursos de Azure de creación](resource-group-authoring-templates.md) . Se implementará la plantilla predefinida [aprovisionar una aplicación Web con una base de datos de SQL](https://azure.microsoft.com/documentation/templates/201-web-app-sql-database/) [Azure tutorial de plantillas](https://azure.microsoft.com/documentation/templates/).

Tiene su grupo de recursos y tiene la plantilla, de modo que ya está listo para implementar la infraestructura definida en la plantilla al grupo de recursos. Implementar recursos con el cmdlet **AzureRmResourceGroupDeployment de nuevo** . La plantilla especifica muchos valores predeterminados, que se utilizará para que no es necesario proporcionar los valores para los parámetros. La sintaxis básica tiene el siguiente aspecto:

    New-AzureRmResourceGroupDeployment -ResourceGroupName TestRG1 -administratorLogin exampleadmin -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-sql-database/azuredeploy.json 

Especifique el grupo de recursos y la ubicación de la plantilla. Si la plantilla es un archivo local, usa el parámetro **- TemplateFile** y especifique la ruta de acceso a la plantilla. Puede establecer la **-modo** parámetro **Incremental** o **completado**. De forma predeterminada, el Administrador de recursos realiza una actualización incremental durante la implementación; por lo tanto, no es esencial para establecer **-modo** cuando quiera **Incremental**. Para conocer las diferencias entre estos modos de implementación, vea [implementar una aplicación con el Administrador de recursos de Azure plantilla](resource-group-template-deploy.md). 

###<a name="dynamic-template-parameters"></a>Parámetros de plantilla dinámica

Si está familiarizado con PowerShell, sabrá que puede recorrer los parámetros disponibles para un cmdlet, escriba un signo menos (-) y, a continuación, presionar la tecla TAB. Esta misma funcionalidad también funciona con los parámetros que se definen en la plantilla. Tan pronto como escriba el nombre de plantilla, el cmdlet recupera la plantilla, analiza y agrega los parámetros de plantilla con el comando dinámicamente. Esto facilita mucho especificar los valores de parámetro de plantilla.

Cuando se escribe el comando, se le pide para el parámetro obligatorio que faltan, **administratorLoginPassword**. Y cuando escriba la contraseña, el valor de cadena segura está oculto. Esta estrategia elimina el riesgo de proporcionar una contraseña en texto sin formato.

    cmdlet New-AzureRmResourceGroupDeployment at command pipeline position 1
    Supply values for the following parameters:
    (Type !? for Help.)
    administratorLoginPassword: ********

Si la plantilla incluye un parámetro con un nombre que coincida con uno de los parámetros en el comando para implementar la plantilla (por ejemplo, incluidos un parámetro denominado **ResourceGroupName** en la plantilla que es el mismo que el parámetro **ResourceGroupName** en el cmdlet [New-AzureRmResourceGroupDeployment](https://msdn.microsoft.com/library/azure/mt679003.aspx) ), se le pedirá que proporcione un valor para un parámetro con el sufijo **FromTemplate** (por ejemplo, **ResourceGroupNameFromTemplate**). En general, debe evitar esta confusión por no nombrar parámetros con el mismo nombre que los parámetros que se utilizan para operaciones de implementación.

El comando se ejecuta y devuelve los mensajes que se crean los recursos. Por último, verá el resultado de la implementación.

    DeploymentName    : azuredeploy
    ResourceGroupName : TestRG1
    ProvisioningState : Succeeded
    Timestamp         : 4/11/2016 7:26:11 PM
    Mode              : Incremental
    TemplateLink      :
                Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-sql-database/azuredeploy.json
                ContentVersion : 1.0.0.0
    Parameters        :
                Name             Type                       Value
                ===============  =========================  ==========
                skuName          String                     F1
                skuCapacity      Int                        1
                administratorLogin  String                  exampleadmin
                administratorLoginPassword  SecureString
                databaseName     String                     sampledb
                collation        String                     SQL_Latin1_General_CP1_CI_AS
                edition          String                     Basic
                maxSizeBytes     String                     1073741824
                requestedServiceObjectiveName  String       Basic

    Outputs           :
                Name             Type                       Value
                ===============  =========================  ==========
                siteUri          String                     websites5wdai7p2k2g4.azurewebsites.net
                sqlSvrFqdn       String                     sqlservers5wdai7p2k2g4.database.windows.net
                    
    DeploymentDebugLogLevel :

En pocos pasos, creamos e implementado los recursos necesarios para un sitio Web complejo. 

### <a name="log-debug-information"></a>Información de depuración de registro

Al implementar una plantilla, puede registrar información adicional sobre la solicitud y la respuesta especificando el parámetro **- DeploymentDebugLogLevel** cuando se ejecuta **AzureRmResourceGroupDeployment de nuevo**. Esta información puede ayudarle a solucionar problemas de implementación. El valor predeterminado es **Ninguno** lo que no significa que no hay ninguna solicitud o se registra el contenido de la respuesta. Puede especificar el contenido de la sesión de solicitud, respuesta o ambos.  Para obtener más información sobre implementaciones de solución de problemas y el registro de información de depuración, vea [implementaciones de grupo de recursos de solución de problemas con PowerShell de Azure](resource-manager-troubleshoot-deployments-powershell.md). En el ejemplo siguiente se registra el contenido de la solicitud y el contenido de la implementación.

    New-AzureRmResourceGroupDeployment -ResourceGroupName TestRG1 -DeploymentDebugLogLevel All -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-sql-database/azuredeploy.json 

> [AZURE.NOTE] Al establecer el parámetro DeploymentDebugLogLevel, considere detenidamente el tipo de información que se pasa en durante la implementación. Al registrar información sobre la solicitud y la respuesta, se podrían exponer datos confidenciales que se recuperen a través de las operaciones de implementación. 


## <a name="get-information-about-your-resource-groups"></a>Obtener información acerca de los grupos de recursos

Después de crear un grupo de recursos, puede usar los cmdlets en el módulo de administrador de recursos para administrar los grupos de recursos.

- Para obtener un grupo de recursos en la suscripción, use el cmdlet **Get-AzureRmResourceGroup** :

        Get-AzureRmResourceGroup -ResourceGroupName TestRG1
    
    Que devuelve la siguiente información:

        ResourceGroupName : TestRG1
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG
        
        ...

    Si no especifica un nombre de grupo de recursos, el cmdlet devuelve todos los grupos de recursos en la suscripción.

- Para obtener los recursos del grupo de recursos, use el cmdlet **Buscar AzureRmResource** y su parámetro **ResourceGroupNameContains** . Sin parámetros, buscar AzureRmResource Obtiene todos los recursos de su suscripción de Azure.

        Find-AzureRmResource -ResourceGroupNameContains TestRG1
    
     Que devuelve una lista de recursos con formato como:
        
        Name              : sqlservers5wdai7p2k2g4
        ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1/providers/Microsoft.Sql/servers/sqlservers5wdai7p2k2g4
        ResourceName      : sqlservers5wdai7p2k2g4
        ResourceType      : Microsoft.Sql/servers
        Kind              : v2.0
        ResourceGroupName : TestRG1
        Location          : westus
        SubscriptionId    : {guid}
        Tags              : {System.Collections.Hashtable}
        ...
            
- Puede usar etiquetas para organizar los recursos en su suscripción de forma lógica y recuperar recursos con los cmdlets **AzureRmResource buscar** y **Encontrar AzureRmResourceGroup** .

        Find-AzureRmResource -TagName displayName -TagValue Website

        Name              : webSites5wdai7p2k2g4
        ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1/providers/Microsoft.Web/sites/webSites5wdai7p2k2g4
        ResourceName      : webSites5wdai7p2k2g4
        ResourceType      : Microsoft.Web/sites
        ResourceGroupName : TestRG1
        Location          : westus
        SubscriptionId    : {guid}
                
      There is much more you can do with tags. For more information, see [Using tags to organize your Azure resources](resource-group-using-tags.md).

## <a name="add-to-a-resource-group"></a>Agregar a un grupo de recursos

Para agregar un recurso al grupo de recursos, puede usar el cmdlet **AzureRmResource de nuevo** . Sin embargo, agregando un recurso de esta manera puede provocar confusión futura porque el nuevo recurso no existe en la plantilla. Si volver a implementar la plantilla anterior, sería implementar una solución incompleta. Si va a implementar con frecuencia, le resultará más fácil y más fiable para agregar el nuevo recurso para la plantilla y volver a implementarlo.

## <a name="move-a-resource"></a>Mover un recurso

Puede mover los recursos existentes a un nuevo grupo de recursos. Para obtener ejemplos, vea [Mover recursos a nuevo grupo de recursos o suscripción](resource-group-move-resources.md).

## <a name="export-template"></a>Plantilla de exportación

Para un grupo de recursos existente (implementado a través de PowerShell o uno de los otros métodos como el portal), puede ver la plantilla de administrador de recursos del grupo de recursos. Exportar la plantilla ofrece dos ventajas:

1. Puede automatizar fácilmente las futuras implementaciones de la solución porque toda la infraestructura de está definido en la plantilla.

2. Puede familiarizarse con la sintaxis de la plantilla mirando en JavaScript Object Notation (JSON) que representa la solución.

A través de PowerShell, puede generar una plantilla que representa el estado actual de su grupo de recursos o recuperar la plantilla que se usó para una implementación concreta.

Exportar la plantilla para un grupo de recursos es útil cuando se han realizado cambios en un grupo de recursos y necesita recuperar la representación JSON de su estado actual. Sin embargo, la plantilla generada contiene solo un número mínimo de parámetros y no variables. La mayoría de los valores de la plantilla son modificables. Antes de implementar la plantilla generada, puede convertir varios valores en parámetros para que pueda personalizar la implementación para distintos entornos.

Exportar la plantilla para una implementación concreta es útil cuando necesite ver la plantilla real que se usó para implementar recursos. La plantilla incluye todos los parámetros y variables definidas para la implementación original. Sin embargo, si alguien de su organización ha realizado cambios en el grupo de recursos fuera de lo que se define en la plantilla, esta plantilla no represente el estado actual del grupo de recursos.

> [AZURE.NOTE] La característica de exportación de la plantilla está en vista previa y no todos los tipos de recursos actualmente admiten la exportación de una plantilla. Al intentar exportar una plantilla, es posible que vea un error que indica que no se exportan algunos recursos. Si es necesario, puede definir manualmente estos recursos en la plantilla después de descargarlo.

###<a name="export-template-from-resource-group"></a>Exportar la plantilla de grupo de recursos

Para ver la plantilla para un grupo de recursos, ejecute el cmdlet **AzureRmResourceGroup de exportación** .

    Export-AzureRmResourceGroup -ResourceGroupName TestRG1 -Path c:\Azure\Templates\Downloads\TestRG1.json
    
###<a name="download-template-from-deployment"></a>Descargar plantilla de implementación

Para descargar la plantilla utilizada para una implementación concreta, ejecute el cmdlet **AzureRmResourceGroupDeploymentTemplate guardar** .

    Save-AzureRmResourceGroupDeploymentTemplate -DeploymentName azuredeploy -ResourceGroupName TestRG1 -Path c:\Azure\Templates\Downloads\azuredeploy.json

## <a name="delete-resources-or-resource-group"></a>Eliminar recursos o grupo de recursos

- Para eliminar un recurso del grupo de recursos, use el cmdlet **Quitar AzureRmResource** . Este cmdlet elimina el recurso, pero no elimina el grupo de recursos.

    Este comando quita el sitio Web de Sitio_de_prueba del grupo de recursos de TestRG1.

        Remove-AzureRmResource -Name TestSite -ResourceGroupName TestRG1 -ResourceType "Microsoft.Web/sites" -ApiVersion 2015-08-01

- Para eliminar un grupo de recursos, use el cmdlet **Quitar AzureRmResourceGroup** . Este cmdlet elimina el grupo de recursos y sus recursos.

        Remove-AzureRmResourceGroup -Name TestRG1
        
    Se le pedirá que confirme la eliminación.
        
        Confirm
        Are you sure you want to remove resource group 'TestRG1'
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y

## <a name="deployment-script"></a>Script de implementación

Los ejemplos anteriores de implementación en este tema se mostraba solo los cmdlets individuales necesarias para implementar recursos Azure. En el ejemplo siguiente se muestra un script de implementación que crea el grupo de recursos y a continuación, implementa los recursos.

    <#
      .SYNOPSIS
      Deploys a template to Azure
      
      .DESCRIPTION
      Deploys an Azure Resource Manager template

      .PARAMETER subscriptionId
      The subscription id where the template will be deployed.

      .PARAMETER resourceGroupName
      The resource group where the template will be deployed. Can be the name of an existing or a new resource group.

      .PARAMETER resourceGroupLocation
      Optional, a resource group location. If specified, will try to create a new resource group in this location. If not specified, assumes resource group is existing.

      .PARAMETER deploymentName
      The deployment name.

      .PARAMETER templateFilePath
      Optional, path to the template file. Defaults to template.json.

      .PARAMETER parametersFilePath
      Optional, path to the parameters file. Defaults to parameters.json. If file is not found, will prompt for parameter values based on template.
    #>

    param(
      [Parameter(Mandatory=$True)]
      [string]
      $subscriptionId,

      [Parameter(Mandatory=$True)]
      [string]
      $resourceGroupName,

      [string]
      $resourceGroupLocation,

      [Parameter(Mandatory=$True)]
      [string]
      $deploymentName,

      [string]
      $templateFilePath = "template.json",

      [string]
      $parametersFilePath = "parameters.json"
    )

    #******************************************************************************
    # Script body
    # Execution begins here 
    #******************************************************************************
    $ErrorActionPreference = "Stop"

    # sign in
    Write-Host "Logging in...";
    Add-AzureRmAccount;

    # select subscription
    Write-Host "Selecting subscription '$subscriptionId'";
    Set-AzureRmContext -SubscriptionID $subscriptionId;

    #Create or check for existing resource group
    $resourceGroup = Get-AzureRmResourceGroup -Name $resourceGroupName -ErrorAction SilentlyContinue
    if(!$resourceGroup)
    {
      Write-Host "Resource group '$resourceGroupName' does not exist. To create a new resource group, please enter a location.";
      if(!$resourceGroupLocation) {
        $resourceGroupLocation = Read-Host "resourceGroupLocation";
      }
      Write-Host "Creating resource group '$resourceGroupName' in location '$resourceGroupLocation'";
      New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
    }
    else{
      Write-Host "Using existing resource group '$resourceGroupName'";
    }

    # Start the deployment
    Write-Host "Starting deployment...";
    if(Test-Path $parametersFilePath) {
      New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath -TemplateParameterFile $parametersFilePath;
    } else {
      New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath;
    }

## <a name="next-steps"></a>Pasos siguientes

- Para obtener información sobre cómo crear plantillas de administrador de recursos, vea [Crear plantillas de administrador de recursos de Azure](./resource-group-authoring-templates.md).
- Para obtener información acerca de la implementación de plantillas, vea [implementar una aplicación con la plantilla de administrador de recursos de Azure](./resource-group-template-deploy.md).
- Para obtener un ejemplo detallado de la implementación de un proyecto, vea [implementar microservices predecible en Azure](app-service-web/app-service-deploy-complex-application-predictably.md).
- Para obtener información sobre solución de problemas de una implementación que no se pudo, vea [implementaciones de grupo de recursos de solución de problemas en Azure](./resource-manager-troubleshoot-deployments-powershell.md).

