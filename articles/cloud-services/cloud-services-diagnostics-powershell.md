<properties
    pageTitle="Habilitar diagnósticos en servicios de nube de Azure con PowerShell | Microsoft Azure"
    description="Aprenda a habilitar diagnósticos de servicios de nube con PowerShell"
    services="cloud-services"
    documentationCenter=".net"
    authors="Thraka"
    manager="timlt"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="adegeo"/>


# <a name="enable-diagnostics-in-azure-cloud-services-using-powershell"></a>Habilitar diagnósticos en servicios de nube de Azure con PowerShell

Puede recopilar datos de diagnóstico como registros de aplicaciones, etc. desde un servicio de nube mediante la extensión de Azure diagnósticos de contador de rendimiento. En este artículo se describe cómo habilitar la extensión de Azure diagnósticos de un servicio de nube con PowerShell.  Vea [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) para los requisitos previos necesarios para este artículo.

## <a name="enable-diagnostics-extension-as-part-of-deploying-a-cloud-service"></a>Habilitar la extensión de diagnósticos como parte de la implementación de un servicio de nube

Este enfoque de buena para tipo de integración continua de escenarios donde se puede habilitar la extensión de diagnósticos como parte de la implementación del servicio de nube. Al crear una nueva implementación de servicio de nube puede habilitar la extensión de diagnósticos pasando el parámetro *ExtensionConfiguration* al cmdlet [AzureDeployment de nuevo](https://msdn.microsoft.com/library/azure/mt589089.aspx) . El parámetro *ExtensionConfiguration* toma una matriz de configuraciones de diagnósticos que pueden crearse con el cmdlet [AzureServiceDiagnosticsExtensionConfig de nuevo](https://msdn.microsoft.com/library/azure/mt589168.aspx) .

En el ejemplo siguiente se muestra cómo habilitar diagnósticos de un servicio de nube con un WebRole y WorkerRole cada una con una configuración de diagnósticos diferente.

    $service_name = "MyService"
    $service_package = "CloudService.cspkg"
    $service_config = "ServiceConfiguration.Cloud.cscfg"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml"
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

    $webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
    $workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath

    New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig)

Si el archivo de configuración de diagnósticos especifica un elemento de StorageAccount con un nombre de cuenta de almacenamiento, el cmdlet New-AzureServiceDiagnosticsExtensionConfig usar automáticamente esa cuenta de almacenamiento. Para que funcione, debe estar en la misma suscripción como el servicio de nube que se implementa la cuenta de almacenamiento.

De Azure SDK 2.6 en adelante publicación los archivos de configuración de extensión generados por MSBuild destino incluirá el nombre de la cuenta de almacenamiento basada en la cadena de configuración de diagnósticos especificada en el archivo de configuración de servicio (.cscfg). La siguiente secuencia de comandos muestra cómo analizar los archivos de configuración de extensión desde la salida de destino de publicación y configurar la extensión de diagnósticos de cada rol al implementar el servicio de nube.

    $service_name = "MyService"
    $service_package = "C:\build\output\CloudService.cspkg"
    $service_config = "C:\build\output\ServiceConfiguration.Cloud.cscfg"

    #Find the Extensions path based on service configuration file
    $extensionsSearchPath = Join-Path -Path (Split-Path -Parent $service_config) -ChildPath "Extensions"

    $diagnosticsExtensions = Get-ChildItem -Path $extensionsSearchPath -Filter "PaaSDiagnostics.*.PubConfig.xml"
    $diagnosticsConfigurations = @()
    foreach ($extPath in $diagnosticsExtensions)
    {
    #Find the RoleName based on file naming convention PaaSDiagnostics.<RoleName>.PubConfig.xml
    $roleName = ""
    $roles = $extPath -split ".",0,"simplematch"
    if ($roles -is [system.array] -and $roles.Length -gt 1)
        {
        $roleName = $roles[1]
        $x = 2
        while ($x -le $roles.Length)
            {
               if ($roles[$x] -ne "PubConfig")
                {
                    $roleName = $roleName + "." + $roles[$x]
                }
                else
                {
                    break
                }
                $x++
            }
        $fullExtPath = Join-Path -path $extensionsSearchPath -ChildPath $extPath
        $diagnosticsconfig = New-AzureServiceDiagnosticsExtensionConfig -Role $roleName -DiagnosticsConfigurationPath $fullExtPath
        $diagnosticsConfigurations += $diagnosticsconfig
        }
    }
    New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration $diagnosticsConfigurations

Visual Studio Online usa un enfoque similar para deploymnts automatizado de servicios en la nube con la extensión de diagnósticos. Consulte [Publicar AzureCloudDeployment.ps1](https://github.com/Microsoft/vso-agent-tasks/blob/master/Tasks/AzureCloudPowerShellDeployment/Publish-AzureCloudDeployment.ps1) para obtener un ejemplo completo.

Si se ha especificado ningún StorageAccount en la configuración de diagnósticos, debe pasar en el parámetro StorageAccountName al cmdlet. Si se especifica el parámetro StorageAccountName, el cmdlet usar siempre la cuenta de almacenamiento especificado en el parámetro y no el que se especifica en el archivo de configuración de diagnósticos.

Si la cuenta de almacenamiento de información de diagnóstico está en una suscripción diferente del servicio de nube, debe pasar explícitamente en los parámetros StorageAccountName y StorageAccountKey al cmdlet. El parámetro StorageAccountKey no es necesario cuando la cuenta de almacenamiento de diagnósticos se encuentra en la misma suscripción, como el cmdlet puede consultar automáticamente y se establece el valor de clave cuando se habilita la extensión de diagnósticos. Sin embargo, si la cuenta de almacenamiento de información de diagnóstico está en una suscripción diferente, el cmdlet no podrá obtener la clave automáticamente y deberá especificar explícitamente la clave a través del parámetro StorageAccountKey.

    $webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
    $workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key


## <a name="enable-diagnostics-extension-on-an-existing-cloud-service"></a>Habilitar la extensión de diagnósticos en un servicio de nube existente

Puede usar el cmdlet [Set-AzureServiceDiagnosticsExtension](https://msdn.microsoft.com/library/azure/mt589140.aspx) para habilitar o actualizar la configuración de diagnósticos de un servicio de nube que se está ejecutando.


    $service_name = "MyService"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml"
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

    $webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
    $workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath

    Set-AzureServiceDiagnosticsExtension -DiagnosticsConfiguration @($webrole_diagconfig,$workerrole_diagconfig) -ServiceName $service_name


## <a name="get-current-diagnostics-extension-configuration"></a>Obtener la configuración actual de extensión de diagnóstico
Use el cmdlet [Get-AzureServiceDiagnosticsExtension](https://msdn.microsoft.com/library/azure/mt589204.aspx) para obtener la configuración de diagnósticos actual de un servicio de nube.

    Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"

## <a name="remove-diagnostics-extension"></a>Quitar la extensión de diagnóstico
Para desactivar los diagnósticos en un servicio de nube puede usar el cmdlet [Quitar AzureServiceDiagnosticsExtension](https://msdn.microsoft.com/library/azure/mt589183.aspx) .

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"

Si habilita la extensión de diagnósticos con *Conjunto AzureServiceDiagnosticsExtension* o el *Nuevo AzureServiceDiagnosticsExtensionConfig* sin el parámetro de la *función* puede quitar la extensión con *Quitar AzureServiceDiagnosticsExtension* sin el parámetro de la *función* . Si se ha utilizado el parámetro de la *función* cuando se habilita la extensión, a continuación, se debe también utilizar al quitar la extensión.

Para quitar la extensión de diagnósticos de cada rol individual:

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"


## <a name="next-steps"></a>Pasos siguientes

- Para obtener información adicional sobre el uso de diagnósticos de Azure y otras técnicas para solucionar problemas, vea [Habilitar diagnósticos en servicios de nube de Azure y máquinas virtuales de Windows](cloud-services-dotnet-diagnostics.md).
- El [Esquema de configuración de diagnósticos](https://msdn.microsoft.com/library/azure/dn782207.aspx) explica las diversas opciones de las configuraciones de xml para la extensión de diagnósticos.
- Para obtener información sobre cómo habilitar la extensión de diagnóstico para máquinas virtuales de Windows, vea [crear una máquina Virtual de Windows con la supervisión y el diagnóstico usando la plantilla de administrador de recursos de Azure](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md)  
