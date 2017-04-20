<properties 
    pageTitle="Script de PowerShell para crear un recurso de aplicación perspectivas" 
    description="Automatizar la creación de recursos de información de la aplicación." 
    services="application-insights" 
    documentationCenter="windows"
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="02/19/2016" 
    ms.author="awills"/>

#  <a name="powershell-script-to-create-an-application-insights-resource"></a>Script de PowerShell para crear un recurso de aplicación perspectivas

*Información de la aplicación está en vista previa.*

Cuando desee supervisar una nueva aplicación - o una nueva versión de una aplicación, con [Recomendaciones de aplicación de Visual Studio](https://azure.microsoft.com/services/application-insights/), configurar un nuevo recurso de Microsoft Azure. Este recurso es donde se analizan y se muestran los datos de telemetría de la aplicación. 

Puede automatizar la creación de un nuevo recurso con PowerShell.

Por ejemplo, si va a desarrollar una aplicación de dispositivo móvil, es probable que, en cualquier momento, habrá varias versiones publicadas de la aplicación en uso por los clientes. No quiere obtener los resultados de telemetría de diferentes versiones mixtas hacia arriba. Para obtener el proceso de compilación para crear un nuevo recurso para cada compilación.

## <a name="script-to-create-an-application-insights-resource"></a>Secuencia de comandos para crear un recurso de aplicación perspectivas

Vea las especificaciones de cmdlet relevantes:

* [Nueva AzureRmResource](https://msdn.microsoft.com/library/mt652510.aspx)
* [Nueva AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt678995.aspx)


*Secuencia de comandos de PowerShell*  

```PowerShell


###########################################
# Set Values
###########################################

# If running manually, uncomment before the first 
# execution to login to the Azure Portal:

# Add-AzureRmAccount

# Set the name of the Application Insights Resource

$appInsightsName = "TestApp"

# Set the application name used for the value of the Tag "AppInsightsApp" 
# - http://azure.microsoft.com/documentation/articles/azure-preview-portal-using-tags/
$applicationTagName = "MyApp"

# Set the name of the Resource Group to use.  
# Default is the application name.
$resourceGroupName = "MyAppResourceGroup"

###################################################
# Create the Resource and Output the name and iKey
###################################################

#Select the azure subscription
Select-AzureSubscription -SubscriptionName "MySubscription"

# Create the App Insights Resource

$resource = New-AzureRmResource `
  -ResourceName $appInsightsName `
  -ResourceGroupName $resourceGroupName `
  -Tag @{ Name = "AppInsightsApp"; Value = $applicationTagName} `
  -ResourceType "Microsoft.Insights/Components" `
  -Location "Central US" `
  -PropertyObject @{"Type"="ASP.NET"} `
  -Force

# Give owner access to the team

New-AzureRmRoleAssignment `
  -SignInName "myteam@fabrikam.com" `
  -RoleDefinitionName Owner `
  -Scope $resource.ResourceId 


#Display iKey
Write-Host "App Insights Name = " $resource.Name
Write-Host "IKey = " $resource.Properties.InstrumentationKey

```

## <a name="what-to-do-with-the-ikey"></a>¿Qué hacer con el iKey

Cada recurso se identifica por su clave de instrumentación (iKey). El iKey es un resultado de la secuencia de comandos de creación de recursos. El script de compilación debe proporcionar que el iKey en el SDK de perspectivas aplicación incrustada en la aplicación.

Hay dos formas de hacer que el iKey disponibles en el SDK de:
  
* En [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md): 
 * `<instrumentationkey>`*iKey*`</instrumentationkey>`
* O en el [código de inicialización](app-insights-api-custom-events-metrics.md): 
 * `Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey = "`*iKey*`";`



## <a name="see-also"></a>Vea también

* [Crear perspectivas de aplicaciones y recursos de prueba de web a partir de plantillas](app-insights-powershell.md)
* [Configurar la supervisión de diagnóstico de Azure con PowerShell](app-insights-powershell-azure-diagnostics.md) 
* [Configurar alertas con PowerShell](app-insights-powershell-alerts.md)

 