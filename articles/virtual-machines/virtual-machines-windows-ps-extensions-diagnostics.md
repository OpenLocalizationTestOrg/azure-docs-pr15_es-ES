<properties
    pageTitle="Usar PowerShell para habilitar diagnóstico de Azure en una máquina virtual ejecuta Windows | Microsoft Azure"
    services="virtual-machines-windows"
    documentationCenter=""
    description="Obtenga información sobre cómo usar PowerShell para habilitar diagnóstico de Azure en una máquina virtual con Windows"
    authors="sbtron"
    manager="timlt"
    editor=""/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/15/2015"
    ms.author="saurabh"/>


# <a name="use-powershell-to-enable-azure-diagnostics-in-a-virtual-machine-running-windows"></a>Usar PowerShell para habilitar diagnóstico de Azure en una máquina virtual con Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Diagnóstico de Azure es la capacidad de Azure que permite la recopilación de datos de diagnósticos en una aplicación implementada. Puede usar la extensión de diagnóstico para recopilar datos de diagnósticos como registros de aplicación o contadores de rendimiento de una máquina virtual Azure (VM) que se está ejecutando Windows. En este artículo se describe cómo usar Windows PowerShell para habilitar la extensión de diagnósticos de una máquina virtual. Vea [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) para los requisitos previos necesarios para este artículo.

## <a name="enable-the-diagnostics-extension-if-you-use-the-resource-manager-deployment-model"></a>Habilitar la extensión de diagnósticos si utiliza el modelo de implementación de administrador de recursos

Puede habilitar la extensión de diagnósticos mientras se crea una máquina virtual de Windows a través del modelo de implementación de administrador de recursos de Azure agregando la configuración de la extensión a la plantilla de administrador de recursos. Consulte [crear una máquina virtual de Windows con la supervisión y diagnósticos con la plantilla de administrador de recursos de Azure](virtual-machines-windows-extensions-diagnostics-template.md).

Para habilitar la extensión de diagnóstico en una máquina virtual existente que se creó a través del modelo de implementación de administrador de recursos, puede usar el cmdlet [Set-AzureRMVMDiagnosticsExtension](https://msdn.microsoft.com/library/mt603499.aspx) PowerShell como se muestra a continuación.


    $vm_resourcegroup = "myvmresourcegroup"
    $vm_name = "myvm"
    $diagnosticsconfig_path = "DiagnosticsPubConfig.xml"

    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path


*$diagnosticsconfig_path* es la ruta de acceso al archivo que contiene la configuración de diagnósticos en XML, como se describe en el [ejemplo](#sample-diagnostics-configuration) siguiente.  

Si el archivo de configuración de diagnósticos especifica un elemento de **StorageAccount** con un nombre de cuenta de almacenamiento, la secuencia de comandos de *Conjunto AzureRMVMDiagnosticsExtension* establece automáticamente la extensión de diagnóstico para enviar datos de diagnóstico a esa cuenta de almacenamiento. Para que funcione, debe estar en la misma suscripción como la máquina virtual de la cuenta de almacenamiento.

Si se ha especificado ningún **StorageAccount** en la configuración de diagnósticos, debe pasar en el parámetro *StorageAccountName* al cmdlet. Si se especifica el parámetro *StorageAccountName* , el cmdlet siempre usar la cuenta de almacenamiento especificada en el parámetro y no el especificado en el archivo de configuración de diagnósticos.

Si la cuenta de almacenamiento de información de diagnóstico está en una suscripción diferente de la máquina virtual, debe pasar explícitamente en los parámetros *StorageAccountName* y *StorageAccountKey* al cmdlet. El parámetro *StorageAccountKey* no es necesario cuando la cuenta de almacenamiento de diagnósticos se encuentra en la misma suscripción, como el cmdlet puede consultar automáticamente y se establece el valor de clave cuando se habilita la extensión de diagnósticos. Sin embargo, si la cuenta de almacenamiento de información de diagnóstico está en una suscripción diferente, el cmdlet no podrá obtener la clave automáticamente y deberá especificar explícitamente la clave a través del parámetro *StorageAccountKey* .  

    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key

Una vez que la extensión de diagnóstico está habilitada en una máquina virtual, puede obtener la configuración actual mediante el cmdlet [Get-AzureRMVmDiagnosticsExtension](https://msdn.microsoft.com/library/mt603678.aspx) .

    Get-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name

El cmdlet devuelve *PublicSettings*, que contiene la configuración de XML en un formato codificado base 64. Para leer el archivo XML, debe descodificar él.

    $publicsettings = (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

El cmdlet [Quitar AzureRMVmDiagnosticsExtension](https://msdn.microsoft.com/library/mt603782.aspx) puede utilizarse para quitar la extensión de diagnósticos de la máquina virtual.  

## <a name="enable-the-diagnostics-extension-if-you-use-the-classic-deployment-model"></a>Habilitar la extensión de diagnósticos si utiliza el modelo de implementación clásica

Puede usar el cmdlet [Set-AzureVMDiagnosticsExtension](https://msdn.microsoft.com/library/mt589189.aspx) para habilitar una extensión de diagnóstico en una máquina virtual que cree a través del modelo de implementación clásico. En el ejemplo siguiente se muestra cómo crear una nueva máquina virtual a través del modelo de implementación clásica con la extensión de diagnósticos habilitada.

    $VM = New-AzureVMConfig -Name $VM -InstanceSize Small -ImageName $VMImage
    $VM = Add-AzureProvisioningConfig -VM $VM -AdminUsername $Username -Password $Password -Windows
    $VM = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
    New-AzureVM -Location $Location -ServiceName $Service_Name -VM $VM

Para habilitar la extensión de diagnóstico en una máquina virtual existente que se creó a través del modelo de implementación clásica, primero use el cmdlet [Get-AzureVM](https://msdn.microsoft.com/library/mt589152.aspx) para obtener la configuración de la máquina virtual. A continuación, actualice la configuración de la máquina virtual para incluir la extensión de diagnóstico mediante el cmdlet [Set-AzureVMDiagnosticsExtension](https://msdn.microsoft.com/library/mt589189.aspx) . Por último, aplicar la configuración actualizada VM mediante [AzureVM de actualización](https://msdn.microsoft.com/library/mt589121.aspx).

    $VM = Get-AzureVM -ServiceName $Service_Name -Name $VM_Name
    $VM_Update = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
    Update-AzureVM -ServiceName $Service_Name -Name $VM_Name -VM $VM_Update.VM

## <a name="sample-diagnostics-configuration"></a>Configuración de diagnósticos de ejemplo

El siguiente código XML puede usarse para la configuración de diagnósticos público con las secuencias de comandos anteriores. Este ejemplo de configuración transferirá diversos contadores de rendimiento a la cuenta de almacenamiento diagnósticos, junto con los errores de aplicación, seguridad y canales del sistema en los registros de eventos de Windows y los errores de los registros de la infraestructura de diagnóstico.

La configuración debe actualizarse para que se incluyen las siguientes:

- El atributo *resourceID* del elemento **métricas** debe actualizarse con el identificador de recursos de la máquina virtual.
    - El identificador de recursos se puede crear utilizando el modelo siguiente: "/resourceGroups//suscripciones / {*Id. de suscripción para la suscripción con VM*} {*el nombre resourcegroup para VM*} / providers/Microsoft.Compute/virtualMachines/ {*el nombre de VM*}".
    - Por ejemplo, si el identificador de suscripción para la suscripción que se está ejecutando la máquina virtual es **11111111-1111-1111-1111-111111111111**, el nombre del grupo de recursos para el grupo de recursos es **MyResourceGroup**y el nombre de la máquina virtual es **MyWindowsVM**, a continuación, el valor de *resourceID* sería:

        ```
        <Metrics resourceId="/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/MyWindowsVM" >
        ```

    - Para obtener más información acerca de cómo métricas se generan según la configuración de métricas y contadores de rendimiento, consulte la [tabla métricas de diagnósticos de Azure en el almacenamiento](virtual-machines-windows-extensions-diagnostics-template.md#wadmetrics-tables-in-storage).

- El elemento **StorageAccount** debe actualizarse con el nombre de la cuenta de almacenamiento de información de diagnóstico.

    ```
    <?xml version="1.0" encoding="utf-8"?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <WadCfg>
          <DiagnosticMonitorConfiguration overallQuotaInMB="4096">
            <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error"/>
            <PerformanceCounters scheduledTransferPeriod="PT1M">
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU utilization" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Privileged Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU privileged time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% User Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU user time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor Information(_Total)\Processor Frequency" sampleRate="PT15S" unit="Count">
            <annotation displayName="CPU frequency" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\System\Processes" sampleRate="PT15S" unit="Count">
            <annotation displayName="Processes" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Process(_Total)\Thread Count" sampleRate="PT15S" unit="Count">
            <annotation displayName="Threads" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Process(_Total)\Handle Count" sampleRate="PT15S" unit="Count">
            <annotation displayName="Handles" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\% Committed Bytes In Use" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Memory usage" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory available" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory committed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Commit Limit" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory commit limit" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Pool Paged Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory paged pool" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Pool Nonpaged Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory non-paged pool" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Read Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active read time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Write Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active write time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Transfers/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Reads/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk read operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Writes/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk write operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Read Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk read speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Write Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk write speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Read Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average read queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Write Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average write queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(_Total)\% Free Space" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk free space (percentage)" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(_Total)\Free Megabytes" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk free space (MB)" locale="en-us"/>
          </PerformanceCounterConfiguration>
        </PerformanceCounters>
        <Metrics resourceId="(Update with resource ID for the VM)" >
            <MetricAggregation scheduledTransferPeriod="PT1H"/>
            <MetricAggregation scheduledTransferPeriod="PT1M"/>
        </Metrics>
        <WindowsEventLog scheduledTransferPeriod="PT1M">
          <DataSource name="Application!*[System[(Level = 1 or Level = 2)]]"/>
          <DataSource name="Security!*[System[(Level = 1 or Level = 2)]"/>
          <DataSource name="System!*[System[(Level = 1 or Level = 2)]]"/>
        </WindowsEventLog>
          </DiagnosticMonitorConfiguration>
        </WadCfg>
        <StorageAccount>(Update with diagnostics storage account name)</StorageAccount>
    </PublicConfig>
    ```

## <a name="next-steps"></a>Pasos siguientes
- Para obtener información adicional sobre el uso de la capacidad de diagnósticos de Azure y otras técnicas para solucionar problemas, vea [Habilitar diagnósticos en servicios de nube de Azure y máquinas virtuales de Windows](../cloud-services/cloud-services-dotnet-diagnostics.md).
- [Esquema de las configuraciones de diagnósticos](https://msdn.microsoft.com/library/azure/mt634524.aspx) explica las diversas opciones de las configuraciones de XML para la extensión de diagnósticos.
