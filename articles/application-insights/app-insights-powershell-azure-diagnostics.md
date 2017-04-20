<properties
    pageTitle="Usar PowerShell para configurar perspectivas de aplicación en un Azure | Microsoft Azure"
    description="Automatizar la configuración de diagnósticos de Azure en la canalización de impresiones de aplicación."
    services="application-insights"
    documentationCenter=".net"
    authors="sbtron"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="11/17/2015"
    ms.author="awills"/>

# <a name="using-powershell-to-set-up-application-insights-for-an-azure-web-app"></a>Usar PowerShell para configurar perspectivas de aplicación para una aplicación web de Azure

[Microsoft Azure](https://azure.com) puede ser [configurado para enviar diagnósticos de Azure](app-insights-azure-diagnostics.md) para [Impresiones de aplicación de Visual Studio](app-insights-overview.md). Los diagnósticos se relacionan con los servicios de nube de Azure y máquinas virtuales de Azure. De manera complementaria la telemetría que se envía desde dentro de la aplicación con el SDK de perspectivas de aplicación. Como parte de la automatización del proceso de creación de nuevos recursos en Azure, puede configurar diagnósticos con PowerShell.

## <a name="azure-template"></a>Azure plantilla

Si la aplicación web está en Azure y crear los recursos mediante una plantilla de administrador de recursos de Azure, puede configurar perspectivas de aplicación agregando este nodo de recursos:

    {
      resources: [
        /* Create Application Insights resource */
        {
          "apiVersion": "2015-05-01",
          "type": "microsoft.insights/components",
          "name": "nameOfAIAppResource",
          "location": "centralus",
          "kind": "web",
          "properties": { "ApplicationId": "nameOfAIAppResource" },
          "dependsOn": [
            "[concat('Microsoft.Web/sites/', myWebAppName)]"
          ]
        }
       ]
     } 

* `nameOfAIAppResource`-un nombre para el recurso de información de la aplicación
* `myWebAppName`-el identificador de la aplicación web


## <a name="enable-diagnostics-extension-as-part-of-deploying-a-cloud-service"></a>Habilitar la extensión de diagnósticos como parte de la implementación de un servicio de nube

La `New-AzureDeployment` cmdlet tiene un parámetro `ExtensionConfiguration`, que toma una matriz de configuraciones de diagnósticos. Estos pueden crearse con la `New-AzureServiceDiagnosticsExtensionConfig` cmdlet. Por ejemplo:

```ps

    $service_package = "CloudService.cspkg"
    $service_config = "ServiceConfiguration.Cloud.cscfg"
    $diagnostics_storagename = "myservicediagnostics"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

    $primary_storagekey = (Get-AzureStorageKey `
     -StorageAccountName "$diagnostics_storagename").Primary
    $storage_context = New-AzureStorageContext `
       -StorageAccountName $diagnostics_storagename `
       -StorageAccountKey $primary_storagekey

    $webrole_diagconfig = `
     New-AzureServiceDiagnosticsExtensionConfig `
      -Role "WebRole" -Storage_context $storageContext `
      -DiagnosticsConfigurationPath $webrole_diagconfigpath
    $workerrole_diagconfig = `
     New-AzureServiceDiagnosticsExtensionConfig `
      -Role "WorkerRole" `
      -StorageContext $storage_context `
      -DiagnosticsConfigurationPath $workerrole_diagconfigpath

    New-AzureDeployment `
      -ServiceName $service_name `
      -Slot Production `
      -Package $service_package `
      -Configuration $service_config `
      -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig)

``` 

## <a name="enable-diagnostics-extension-on-an-existing-cloud-service"></a>Habilitar la extensión de diagnósticos en un servicio de nube existente

En un servicio existente, use `Set-AzureServiceDiagnosticsExtension`.

```ps
 
    $service_name = "MyService"
    $diagnostics_storagename = "myservicediagnostics"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"
    $primary_storagekey = (Get-AzureStorageKey `
         -StorageAccountName "$diagnostics_storagename").Primary
    $storage_context = New-AzureStorageContext `
        -StorageAccountName $diagnostics_storagename `
        -StorageAccountKey $primary_storagekey

    Set-AzureServiceDiagnosticsExtension `
        -StorageContext $storage_context `
        -DiagnosticsConfigurationPath $webrole_diagconfigpath `
        -ServiceName $service_name `
        -Slot Production `
        -Role "WebRole" 
    Set-AzureServiceDiagnosticsExtension `
        -StorageContext $storage_context `
        -DiagnosticsConfigurationPath $workerrole_diagconfigpath `
        -ServiceName $service_name `
        -Slot Production `
        -Role "WorkerRole"
```

## <a name="get-current-diagnostics-extension-configuration"></a>Obtener la configuración actual de extensión de diagnóstico

```ps

    Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```


## <a name="remove-diagnostics-extension"></a>Quitar la extensión de diagnóstico

```ps

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

Si habilita la extensión de diagnósticos mediante `Set-AzureServiceDiagnosticsExtension` o `New-AzureServiceDiagnosticsExtensionConfig` sin el parámetro de la función, a continuación, puede quitar la extensión con `Remove-AzureServiceDiagnosticsExtension` sin el parámetro de la función. Si se ha utilizado el parámetro de la función cuando se habilita la extensión, a continuación, se debe también utilizar al quitar la extensión.

Para quitar la extensión de diagnósticos de cada rol individual:

```ps

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```


## <a name="see-also"></a>Vea también

* [Supervisar Azure aplicaciones con información de la aplicación de servicios de nube](app-insights-cloudservices.md)
* [Enviar diagnóstico de Azure a perspectivas de aplicación](app-insights-azure-diagnostics.md)
* [Automatizar la configuración de alertas](app-insights-powershell-alerts.md)

