<properties
    pageTitle="Usar PowerShell para crear y configurar un área de trabajo de análisis de registro | Microsoft Azure"
    description="Iniciar el análisis utiliza datos desde servidores en sus instalaciones o infraestructura de nube. Puede recopilar datos del equipo de almacenamiento de Azure cuando generados por el diagnóstico de Azure."
    services="log-analytics"
    documentationCenter=""
    authors="richrundmsft"
    manager="jochan"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="powershell"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="richrund"/>

# <a name="manage-log-analytics-using-powershell"></a>Administrar el uso de PowerShell de análisis de registro

Puede utilizar los [cmdlets de PowerShell de análisis de registro] (https://msdn.microsoft.com/library/mt188224(v=azure.300\).aspx) para realizar varias funciones de análisis de registro desde una línea de comandos o como parte de una secuencia de comandos.  Ejemplos de las tareas que puede realizar con PowerShell:

+ Crear un área de trabajo
+ Agregar o quitar una solución
+ Importar y exportar búsquedas guardadas
+ Crear un grupo de equipo
+ Habilitar la colección de registros de IIS de equipos con instalado el agente de Windows
+ Recopilar contadores de rendimiento de equipos Linux y Windows
+ Recopilar eventos de registro del sistema en equipos Linux 
+ Recopilar eventos de registros de eventos de Windows
+ Recopilar registros de eventos personalizados
+ Agregar al agente de análisis de registro a una máquina virtual de Azure
+ Configurar el análisis de registro para indizar los datos recopilados mediante diagnóstico de Azure


Este artículo proporciona dos ejemplos de código que muestran algunas de las funciones que se pueden realizar desde PowerShell.  Puede hacer referencia a [referencia de cmdlet de PowerShell de análisis de registro] (https://msdn.microsoft.com/library/mt188224(v=azure.300\).aspx) para otras funciones.

> [AZURE.NOTE] Análisis de registro anteriormente se denominaba perspectivas operativas, lo que es el nombre utilizado en los cmdlets.

## <a name="prerequisites"></a>Requisitos previos

Para usar PowerShell con el área de trabajo de análisis de registro, debe tener:

+ Una suscripción a Azure y 
+ El área de trabajo de análisis de registro de Azure vinculado a la suscripción de Azure.

Si ha creado un área de trabajo OMS, pero no está todavía vinculada una suscripción de Azure puede crear el vínculo:

+ En el portal de Azure
+ En el portal OMS o 
+ Uso de los cmdlets Get-AzureRmOperationalInsightsLinkTargets y AzureRmOperationalInsightsWorkspace de nuevo.


## <a name="create-and-configure-a-log-analytics-workspace"></a>Crear y configurar un área de trabajo de análisis de registro

El siguiente ejemplo de script muestra cómo:

1.  Crear un área de trabajo
2.  Las soluciones disponibles de la lista
3.  Agregar soluciones al área de trabajo
4.  Búsquedas de importación guardado
5.  Búsquedas de exportación guardado
6.  Crear un grupo de equipo
7.  Habilitar la colección de registros de IIS de equipos con instalado el agente de Windows
8.  Recopilar contadores de rendimiento de disco lógico de equipos Linux (% utiliza inodos; Megabytes libres; % Utiliza espacio; Transferencias de disco/seg; Lecturas de s.; Disco/seg)
9.  Recopilar eventos de registro del sistema de equipos Linux
10. Recopilar eventos de Error y advertencia del registro de eventos de aplicación de equipos con Windows
11. Recopilar contador de rendimiento de la memoria disponible MB de equipos con Windows
12. Recopilar un registro personalizado 


```

$ResourceGroup = "oms-example"
$WorkspaceName = "log-analytics-" + (Get-Random -Maximum 99999) # workspace names need to be unique - Get-Random helps with this for the example code
$Location = "westeurope"

# List of solutions to enable
$Solutions = "Security", "Updates", "SQLAssessment"

# Saved Searches to import
$ExportedSearches = @"
[
    {
        "Category":  "My Saved Searches",
        "DisplayName":  "WAD Events (All)",
        "Query":  "Type=Event SourceSystem:AzureStorage ",
        "Version":  1
    },
    {        
        "Category":  "My Saved Searches",
        "DisplayName":  "Current Disk Queue Length",
        "Query":  "Type=Perf ObjectName=LogicalDisk InstanceName=\"C:\" CounterName=\"Current Disk Queue Length\"",
        "Version":  1
    }
]
"@ | ConvertFrom-Json

# Custom Log to collect
$CustomLog = @"
{
    "customLogName": "sampleCustomLog1", 
    "description": "Example custom log datasource", 
    "inputs": [
        { 
            "location": { 
            "fileSystemLocations": { 
                "windowsFileTypeLogPaths": [ "e:\\iis5\\*.log" ], 
                "linuxFileTypeLogPaths": [ "/var/logs" ] 
                } 
            }, 
        "recordDelimiter": { 
            "regexDelimiter": { 
                "pattern": "\\n", 
                "matchIndex": 0, 
                "matchIndexSpecified": true, 
                "numberedGroup": null 
                } 
            } 
        }
    ], 
    "extractions": [
        { 
            "extractionName": "TimeGenerated", 
            "extractionType": "DateTime", 
            "extractionProperties": { 
                "dateTimeExtraction": { 
                    "regex": null, 
                    "joinStringRegex": null 
                    } 
                } 
            }
        ] 
    }
"@

# Create the resource group if needed
try {
    Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzureRmResourceGroup -Name $ResourceGroup -Location $Location
}

# Create the workspace
New-AzureRmOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup

# List all solutions and their installation status
Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Add solutions
foreach ($solution in $Solutions) {
    Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true
}

#List enabled solutions
(Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Where({($_.enabled -eq $true)})

# Import Saved Searches
foreach ($search in $ExportedSearches) {
    $id = $search.Category + "|" + $search.DisplayName
    New-AzureRmOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId $id -DisplayName $search.DisplayName -Category $search.Category -Query $search.Query -Version $search.Version
}

# Export Saved Searches
(Get-AzureRmOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Value.Properties | ConvertTo-Json 

# Create Computer Group
New-AzureRmOperationalInsightsComputerGroup -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId "My Web Servers" -DisplayName "Web Servers" -Category "My Saved Searches" -Query "Computer=""web*"" | distinct Computer" -Version 1

# Enable IIS Log Collection using agent
Enable-AzureRmOperationalInsightsIISLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Perf
New-AzureRmOperationalInsightsLinuxPerformanceObjectDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Logical Disk" -InstanceName "*"  -CounterNames @("% Used Inodes", "Free Megabytes", "% Used Space", "Disk Transfers/sec", "Disk Reads/sec", "Disk Reads/sec", "Disk Writes/sec") -IntervalSeconds 20  -Name "Example Linux Disk Performance Counters"
Enable-AzureRmOperationalInsightsLinuxCustomLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Syslog
New-AzureRmOperationalInsightsLinuxSyslogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -Facility "kern" -CollectEmergency -CollectAlert -CollectCritical -CollectError -CollectWarning -Name "Example kernal syslog collection"
Enable-AzureRmOperationalInsightsLinuxSyslogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Windows Event
New-AzureRmOperationalInsightsWindowsEventDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -EventLogName "Application" -CollectErrors -CollectWarnings -Name "Example Application Event Log"

# Windows Perf
New-AzureRmOperationalInsightsWindowsPerformanceCounterDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Memory" -InstanceName "*" -CounterName "Available MBytes" -IntervalSeconds 20 -Name "Example Windows Performance Counter"

# Custom Logs
New-AzureRmOperationalInsightsCustomLogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -CustomLogRawJson "$CustomLog" -Name "Example Custom Log Collection"

```

## <a name="configuring-log-analytics-to-index-azure-diagnostics"></a>Configuración de análisis de registro indizar diagnóstico de Azure 

Supervisión sin agente de Azure recursos, los recursos necesitan tener diagnóstico de Azure habilitado y configurado para escribir en una cuenta de almacenamiento. Análisis de registro, a continuación, se puede configurar para recopilar los registros de la cuenta de almacenamiento. Incluye recursos que debe realizar la configuración anterior:

+ Servicios de nube clásica (funciones web y trabajador)
+ Clústeres tela servicio
+ Grupos de seguridad de red
+ Clave depósitos y 
+ Puertas de enlace de aplicación

También puede usar PowerShell para configurar un área de trabajo de análisis de registro en una suscripción de Azure para recopilar registros de suscripciones de Azure diferentes.

El ejemplo siguiente muestra cómo:

1.  Enumerar las cuentas existentes de almacenamiento y ubicaciones de análisis de registro se indizar los datos de
2.  Crear una configuración de lectura desde una cuenta de almacenamiento
3.  Actualizar la configuración recién creada para indizar los datos desde otras ubicaciones
4.  Eliminar la configuración recién creada

```
# validTables = "WADWindowsEventLogsTable", "LinuxsyslogVer2v0", "WADServiceFabric*EventTable", "WADETWEventTable" 
$workspace = (Get-AzureRmOperationalInsightsWorkspace).Where({$_.Name -eq "your workspace name"})

# Update these two lines with the storage account resource ID and the storage account key for the storage account you want to Log Analytics to  
$storageId = "/subscriptions/ec11ca60-1234-491e-5678-0ea07feae25c/resourceGroups/demo/providers/Microsoft.Storage/storageAccounts/wadv2storage"
$key = "abcd=="

# List existing insights
Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name

# Create a new insight
New-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -StorageAccountResourceId $storageId -StorageAccountKey $key -Tables @("WADWindowsEventLogsTable") -Containers @("wad-iis-logfiles")

# Update existing insight
Set-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -Tables @("WADWindowsEventLogsTable", "WADETWEventTable") -Containers @("wad-iis-logfiles", "insights-logs-networksecuritygroupevent/resourceId=/SUBSCRIPTIONS/ec11ca60-1234-491e-5678-0ea07feae25c/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO")

# Remove the insight
Remove-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" 

```

## <a name="next-steps"></a>Pasos siguientes

- [Cmdlets de PowerShell de análisis de registro de revisión](http://msdn.microsoft.com/library/mt188224.aspx) para obtener más información sobre cómo usar PowerShell para la configuración de análisis de registro.

