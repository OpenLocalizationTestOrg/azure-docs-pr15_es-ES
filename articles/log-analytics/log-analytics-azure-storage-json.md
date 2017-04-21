<properties
    pageTitle="Analizar los registros de diagnóstico Azure mediante el análisis de registro | Microsoft Azure"
    description="Análisis de registro puede leer los registros de los servicios de Azure que escribir registros de diagnóstico Azure almacenamiento en formato JSON de blobs."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="banders"/>


# <a name="analyze-azure-diagnostic-logs-using-log-analytics"></a>Analizar los registros de diagnóstico Azure mediante el análisis de registro

Análisis de registro puede recopilar los registros de los siguientes servicios de Azure que escribir [registros de diagnóstico de Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) en el almacenamiento de blobs en formato JSON:

+ Automatización (vista preliminar)
+ Depósito clave (vista preliminar)
+ Aplicación de puerta de enlace (vista preliminar)
+ Grupo de seguridad de la red (vista preliminar)

Las secciones siguientes le guiará por usar PowerShell para:

+ Configurar el análisis de registro para recopilar los registros de almacenamiento para cada recurso  
+ Habilitar la solución de análisis de registro para el servicio de Azure

Antes de que el análisis de registro puede recopilar datos de estos recursos, debe estar habilitado diagnóstico de Azure. Usar el `Set-AzureRmDiagnosticSetting` cmdlet para habilitar el registro.

Consulte los siguientes artículos para obtener más información sobre cómo habilitar el registro de diagnóstico:

+ [Depósito clave](../key-vault/key-vault-logging.md)
+ [Puerta de enlace de aplicación](../application-gateway/application-gateway-diagnostics.md)
+ [Grupo de seguridad de red](../virtual-network/virtual-network-nsg-manage-log.md)

Esta documentación también incluye detalles sobre:

+ Solución de problemas de recopilación de datos
+ Detener la recopilación de datos

## <a name="configure-log-analytics-to-collect-azure-diagnostic-logs"></a>Configurar el análisis de registro para recopilar registros de diagnóstico de Azure

Recopilar registros para estos servicios y habilitar la solución visualizar los registros se realizan mediante secuencias de comandos de PowerShell.

El ejemplo siguiente habilita el registro en todos los recursos admitidos

```
# update to be the storage account that logs will be written to. Storage account must be in the same region as the resource to monitor
# format is similar to "/subscriptions/ec11ca60-ab12-345e-678d-0ea07bbae25c/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount"
$storageAccountId = ""

$supportedResourceTypes = ("Microsoft.Automation/AutomationAccounts", "Microsoft.KeyVault/Vaults", "Microsoft.Network/NetworkSecurityGroups", "Microsoft.Network/ApplicationGateways")

# update location to match your storage account location
$resources = Get-AzureRmResource | where { $_.ResourceType -in $supportedResourceTypes -and $_.Location -eq "westus" }

foreach ($resource in $resources) {
    Set-AzureRmDiagnosticSetting -ResourceId $resource.ResourceId -StorageAccountId $storageAccountId -Enabled $true -RetentionEnabled $true -RetentionInDays 1
}
```


Para algunos recursos, es posible realizar la configuración anterior desde el portal de Azure siguiendo los pasos descritos en [registros de diagnóstico de información general de Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-diagnostic-logs).

## <a name="configure-log-analytics-to-collect-azure-diagnostic-logs"></a>Configurar el análisis de registro para recopilar registros de diagnóstico de Azure

Le proporcionamos un módulo de script de PowerShell que exporta dos cmdlets para ayudar con la configuración de análisis de registro:

1. `Add-AzureDiagnosticsToLogAnalyticsUI`pide entrada y se puede configurar configuraciones sencillas
2. `Add-AzureDiagnosticsToLogAnalytics`toma los recursos para supervisar como entrada y, a continuación, configura el análisis de registro  

### <a name="pre-requisites"></a>Requisitos previos

1. PowerShell Azure con versión 1.0.8 o una versión posterior de los cmdlets de perspectivas operativas.
  - [Cómo instalar y configurar PowerShell de Azure](../powershell-install-configure.md)
  - Comprobar la versión de cmdlets:`Import-Module AzureRM.OperationalInsights -MinimumVersion 1.0.8 `
2. Registro de diagnóstico está configurado para el recurso de Azure que desea supervisar. Usar `Set-AzureRmDiagnosticSetting` o hacer referencia a [Usar el análisis de registro para recopilar datos de cuentas de almacenamiento de Azure](log-analytics-azure-storage.md) para saber cómo habilitar diagnósticos.
3. Un área de trabajo de [Análisis de registro](https://portal.azure.com/#create/Microsoft.LogAnalyticsOMS)  
4. El módulo de AzureDiagnosticsAndLogAnalytics PowerShell
  - Descargue el módulo [AzureDiagnosticsAndLogAnalytics](https://www.powershellgallery.com/packages/AzureDiagnosticsAndLogAnalytics/) desde la Galería de PowerShell

### <a name="option-1-run-the-interactive-configuration-scripts"></a>Opción 1: Ejecute las secuencias de comandos de configuración interactiva

Abra el PowerShell y ejecutar:

```
# Connect to Azure
Login-AzureRmAccount
# If you have diagnostics logs being written to classic storage you will also need to run
Add-AzureAccount

# Import the module
Install-Module -Name AzureDiagnosticsAndLogAnalytics

# Run the UI configuration script
Add-AzureDiagnosticsToLogAnalyticsUI

```

Se muestra una lista de opciones disponibles y le pide que realice su selección.
Se le pedirá que realice las selecciones para cada uno de estos procedimientos:

+ Tipos de recursos (servicios de Azure) para recopilar registros de
+ Instancias de recursos para recopilar registros de
+ Área de trabajo de análisis de registro para recopilar los datos

Después de ejecutar esta secuencia de comandos, debería ver registros de análisis de registro unos 30 minutos después de escribirán los datos de diagnóstico nuevos al almacenamiento. Si los registros no están disponibles después de este momento, consulte la sección Solución de problemas.

### <a name="option-2-build-a-list-of-resources-and-pass-them-to-the-configuration-cmdlet"></a>Opción 2: Crear una lista de recursos y pasarlos a la configuración cmdlet

Puede crear una lista de recursos que tienen diagnóstico de Azure habilitado y, a continuación, pasar los recursos para el cmdlet de configuración.

Puede ver información adicional sobre el cmdlet ejecutando `Get-Help Add-AzureDiagnosticsToLogAnalytics`.


Para encontrar más detalles acerca OMS [Cmdlets de PowerShell de análisis de registro](https://msdn.microsoft.com/library/mt188224.aspx)

>[AZURE.NOTE] Si área de trabajo y recursos están en varias suscripciones a Azure, cambiar entre ellos según sea necesario mediante`Select-AzureRmSubscription -SubscriptionId <Subscription the resource is in>`

```
# Connect to Azure
Login-AzureRmAccount
# If you have diagnostics logs being written to classic storage you will also need to run
Add-AzureAccount

# Import the module
Install-Module -Name AzureDiagnosticsAndLogAnalytics

# Update the values below with the name of the resource that has Azure diagnostics enabled and the name of your Log Analytics workspace
$resourceName = "***example-resource***"
$workspaceName = "***log-analytics-workspace***"

# Find the resource to monitor:
$resource = Find-AzureRmResource -ResourceType "Microsoft.KeyVault/Vaults" -ResourceNameContains $resourceName

# Find the Log Analytics workspace to configure, for example:
$workspace = Find-AzureRmResource -ResourceType "Microsoft.OperationalInsights/workspaces" -ResourceNameContains $workspaceName

# Perform configuration
Add-AzureDiagnosticsToLogAnalytics $resource $workspace

# Enable the Log Analytics solution
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -intelligencepackname KeyVault -Enabled $true

```
Después de ejecutar esta secuencia de comandos, debería ver registros de análisis de registro unos 30 minutos después de escribirán los datos de diagnóstico nuevos al almacenamiento. Si los registros no están disponibles después de este momento, consulte la sección Solución de problemas.  

>[AZURE.NOTE] La configuración no está visible en el portal de Azure. Puede comprobar la configuración utilizando la `Get-AzureRmOperationalInsightsStorageInsight` cmdlet.  


## <a name="stopping-log-analytics-from-collecting-azure-diagnostic-logs"></a>Detener el análisis de registro de recopilación de registros de diagnóstico Azure

Para eliminar la configuración de análisis de registro para un recurso, use la `Remove-AzureRmOperationalInsightsStorageInsight` cmdlet.

## <a name="troubleshooting-configuration-for-azure-diagnostic-logs"></a>Solución de problemas de configuración de registros de diagnóstico de Azure

*Problema*

Al configurar un recurso de registro al almacenamiento clásico, se muestra el siguiente error:

```
Select-AzureSubscription : The subscription id 7691b0d1-e786-4757-857c-7360e61896c3 doesn't exist.

Parameter name: id
```

*Resolución*

Inicie sesión en la API para el modelo de implementación clásica con`Add-AzureAccount`

## <a name="troubleshooting-data-collection-for-azure-diagnostic-logs"></a>Solución de problemas de recopilación de datos de Azure registros de diagnóstico

Si no está viendo datos para el recurso de Azure en el análisis de registro, puede usar los siguientes pasos de solución de problemas:

+ Comprobar los datos que pasan a la cuenta de almacenamiento
+ Compruebe que la solución de análisis de registro para el servicio está habilitada
+ Compruebe que el análisis de registro está configurado para leer de almacenamiento
+ Actualizar la clave de cuenta de almacenamiento

### <a name="verify-data-is-flowing-to-the-storage-account"></a>Comprobar datos fluye a la cuenta de almacenamiento

Puede comprobar si hay datos fluye a la cuenta de almacenamiento con una herramienta que permite explorar el almacenamiento de Azure (por ejemplo, Visual Studio) o mediante PowerShell.

Para buscar la cuenta de almacenamiento, el recurso está configurado para iniciar sesión para usar el siguiente PowerShell:

`Find-AzureRmResource -ResourceType "Microsoft.KeyVault/Vaults" | select ResourceId | Get-AzureRmDiagnosticSetting `

El contenedor de almacenamiento utilizado por diagnósticos de Azure tiene un nombre con el formato:  

`insights-logs-<Category>`

Consulte [usar cmdlets de almacenamiento para comprobar un contenedor de datos recientes](../storage/storage-powershell-guide-full.md) para obtener más información sobre la visualización del contenido de la cuenta de almacenamiento.

### <a name="verify-the-log-analytics-solution-for-the-service-is-enabled"></a>Compruebe que la solución de análisis de registro para el servicio está habilitada

Si usa `Add-AzureDiagnosticsToLogAnalyticsUI`, la solución de análisis de registro correcta se habilita automáticamente para usted.

Para comprobar si una solución está habilitada, ejecute el siguiente PowerShell:

`Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $logAnalyticsResourceGroup -WorkspaceName $logAnalyticsWorkspaceName`

Si no está habilitada la solución, puede habilitar mediante el siguiente PowerShell:

`Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $logAnalyticsResourceGroup -WorkspaceName $logAnalyticsWorkspaceName -IntelligencePackName $solution -Enabled $true`

Para buscar el nombre de la solución que desea habilitar para cada tipo de recurso, use el siguiente PowerShell (esta variable está disponible una vez que haya importado el módulo):

`$MonitorableResourcesToOMSSolutions`

### <a name="verify-that-log-analytics-is-configured-to-read-from-storage"></a>Compruebe que el análisis de registro está configurado para leer de almacenamiento

Si agrega recursos adicionales de Azure, debe habilitar el registro de diagnósticos para ellos y a continuación, configurar el análisis de registro para ellos.
Para comprobar qué cuentas de almacenamiento y recursos de análisis de registro está configurado para recopilar registros para usar el siguiente PowerShell:

```
# Find the Workspace ResourceGroup and Name
$logAnalyticsWorkspace = Get-AzureRmOperationalInsightsWorkspace

#Get the configuration for all resources:
Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $logAnalyticsWorkspace.ResourceGroupName -WorkspaceName $logAnalyticsWorkspace.Name

#Get the just the resources configured:
Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $logAnalyticsWorkspace.ResourceGroupName -WorkspaceName $logAnalyticsWorkspace.Name | select Containers
```

### <a name="update-the-storage-key"></a>Actualizar la clave de almacenamiento

Si cambia la clave para la cuenta de almacenamiento, también debe actualizarse con la nueva clave de la configuración de análisis de registro.
Puede actualizar la configuración de análisis de registro con una nueva clave con el siguiente PowerShell:

`Set-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $logAnalyticsWorkspace.ResourceGroupName -WorkspaceName $logAnalyticsWorkspace.Name –Name <Storage Insight Name> -StorageAccountKey $newKey `

Para buscar el nombre de una perspectiva de almacenamiento, use la `Get-AzureRmOperationalInsightsStorageInsight` cmdlet, tal como se muestra en los ejemplos anteriores.

## <a name="next-steps"></a>Pasos siguientes

- Ese diagnósticos de escritura a almacenamiento de tablas o registros de IIS escritos en almacenamiento de blobs de servicios de [almacenamiento de blobs de uso para IIS y almacenamiento de la tabla de eventos](log-analytics-azure-storage-iis-table.md) para leer los registros de Azure.
- [Habilitar soluciones](log-analytics-add-solutions.md) para proporcionar una perspectiva de los datos.
- [Usar consultas de búsqueda](log-analytics-log-searches.md) para analizar los datos.
