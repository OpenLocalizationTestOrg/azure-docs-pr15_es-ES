<properties
    pageTitle="Con el almacenamiento de blobs para IIS y tabla almacenamiento de eventos | Microsoft Azure"
    description="Análisis de registro puede leer los registros para los servicios de Azure que escribir diagnósticos de almacenamiento de tablas o registros de IIS escritos en almacenamiento de blobs."
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
    ms.date="10/25/2016"
    ms.author="banders"/>


# <a name="using-blob-storage-for-iis-and-table-storage-for-events"></a>Uso de almacenamiento de blobs de IIS y almacenamiento de tablas de eventos

Análisis de registro puede leer los registros de los siguientes servicios que escribir diagnósticos de almacenamiento de tablas o escritos en el almacenamiento de blobs de registros de IIS:

+ Servicio tela clústeres (vista preliminar)
+ Máquinas virtuales de Windows
+ Funciones Web/trabajo

Antes de que el análisis de registro puede recopilar datos de estos recursos, debe estar habilitado diagnóstico de Azure.

Una vez diagnósticos están habilitadas, puede usar el portal de Azure o PowerShell configurar análisis de registro para recopilar los registros.

Diagnóstico de Azure es una extensión de Azure que permite recopilar datos de diagnóstico de una función de trabajador, función web o máquina virtual se ejecuta en Azure. Los datos se almacenan en una cuenta de almacenamiento de Azure y, a continuación, pueden recopilar análisis de registro.

Para que análisis de registro recopilar estos registros de diagnósticos de Azure, deben tener los registros de las siguientes ubicaciones:

| Tipo de registro | Tipo de recurso | Ubicación |
| -------- | ------------- | -------- |
| Registros de IIS | Máquinas virtuales de Windows <br> Funciones Web <br> Funciones de trabajo | torunda-iis-archivos de registro (almacenamiento de blobs de Windows) |
| Registro del sistema | Máquinas virtuales de Windows | LinuxsyslogVer2v0 (almacenamiento de la tabla) |
| Servicio tela operativas eventos | Nodos de tela de servicio | WADServiceFabricSystemEventTable |
| Eventos de servicio tela Actor confiable | Nodos de tela de servicio | WADServiceFabricReliableActorEventTable |
| Eventos de servicio confiable tela de servicio | Nodos de tela de servicio | WADServiceFabricReliableServiceEventTable |
| Registros de eventos de Windows | Nodos de tela de servicio <br> Máquinas virtuales de Windows <br> Funciones Web <br> Funciones de trabajo | WADWindowsEventLogsTable (almacenamiento de tablas) |
| Registros de Windows ETW | Nodos de tela de servicio <br> Máquinas virtuales de Windows <br> Funciones Web <br> Funciones de trabajo | WADETWEventTable (almacenamiento de tablas) |

>[AZURE.NOTE] Registros de IIS desde sitios Web de Azure no se admiten actualmente.

En equipos virtuales, tiene la opción de instalar el [agente de análisis de registro](log-analytics-azure-vm-extension.md) en la máquina virtual para habilitar la información adicional. Además de poder analizar registros de IIS y registros de eventos, puede realizar un análisis adicional incluidos seguimiento de cambios de configuración, evaluación SQL y evaluación de actualización.

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection"></a>Habilitar diagnóstico de Azure en una máquina virtual para la colección de registro de registro de eventos y IIS

Use el procedimiento siguiente para habilitar diagnóstico de Azure en una máquina virtual para el registro de eventos y IIS colección de registro con el portal de Microsoft Azure.

### <a name="to-enable-azure-diagnostics-in-a-virtual-machine-with-the-azure-portal"></a>Para habilitar diagnóstico de Azure en una máquina virtual con el portal de Azure

1. Instalar al agente de VM cuando se crea una máquina virtual. Si ya existe la máquina virtual, compruebe que ya está instalado el agente de máquina virtual.
    - En el portal de Azure, vaya a la máquina virtual, seleccione **Configuración opcional**y, a continuación, **Diagnósticos** y **estado** se establece en **On**.

    Al finalizar, la máquina virtual tiene la extensión de diagnósticos de Azure instalado y en ejecución. Esta extensión es responsable de recopilar los datos de diagnóstico.

2. Habilitar la supervisión y configurar el registro de sucesos en una máquina virtual existente. Puede habilitar diagnósticos en el nivel de la máquina virtual. Para habilitar diagnósticos y, a continuación, configurar el registro de eventos, siga estos pasos:
    1. Seleccione la máquina virtual.
    2. Haga clic en **supervisión**.
    3. Haga clic en **Diagnósticos**.
    4. Establecer el **estado** de **activado**.
    5. Seleccione cada registro de diagnósticos que se desea recopilar.
    7. Haga clic en **Aceptar**.

Uso de PowerShell de Azure con mayor precisión puede especificar los eventos que se escriben en el almacenamiento de Azure. Hacer referencia a [recopilar datos de uso de diagnósticos de Azure escritos a almacenamiento de tablas o registros de IIS escrito a blob](log-analytics-azure-storage-json.md).


## <a name="enable-azure-diagnostics-in-a-web-role-for-iis-log-and-event-collection"></a>Habilitar diagnóstico de Azure en una función de Web para la colección de registro y eventos IIS

Consulte [Cómo a habilitar diagnósticos en un servicio de nube](../cloud-services/cloud-services-dotnet-diagnostics.md) de pasos generales sobre cómo habilitar diagnóstico de Azure. Las siguientes instrucciones usar esta información y personalizar para su uso con el análisis de registro.

Con los diagnósticos de Azure habilitan:

- Registros IIS se almacenan de forma predeterminada, con los datos de registro que se transfieren en el intervalo de transferencia de scheduledTransferPeriod.
- Registros de eventos de Windows no se transfieren de forma predeterminada.

### <a name="to-enable-diagnostics"></a>Para habilitar diagnósticos

Para habilitar los registros de eventos de Windows, o para cambiar la scheduledTransferPeriod, configurar diagnósticos de Azure con el archivo de configuración de XML (diagnostics.wadcfg), como se muestra en [paso 4: crear el archivo de configuración de diagnósticos e instale la extensión](../cloud-services/cloud-services-dotnet-diagnostics.md)

El archivo de configuración de ejemplo siguientes recopila registros de IIS y todos los eventos de los registros de aplicación y del sistema:

```
    <?xml version="1.0" encoding="utf-8" ?>
    <DiagnosticMonitorConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"
          configurationChangePollInterval="PT1M"
          overallQuotaInMB="4096">

      <Directories bufferQuotaInMB="0"
         scheduledTransferPeriod="PT10M">  
        <!-- IISLogs are only relevant to Web roles -->
        <IISLogs container="wad-iis" directoryQuotaInMB="0" />
      </Directories>

      <WindowsEventLog bufferQuotaInMB="0"
         scheduledTransferLogLevelFilter="Verbose"
         scheduledTransferPeriod="PT10M">
        <DataSource name="Application!*" />
        <DataSource name="System!*" />
      </WindowsEventLog>

    </DiagnosticMonitorConfiguration>
```

Asegúrese de que su ConfigurationSettings especifica una cuenta de almacenamiento, como en el ejemplo siguiente:

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"/>
    </ConfigurationSettings>
```

Los valores de **nombre de la cuenta** y **AccountKey** se encuentran en el portal de Azure en el panel de la cuenta de almacenamiento, en administrar las teclas de acceso. El protocolo de la cadena de conexión debe ser **https**.

Una vez que se aplica la configuración de diagnóstico actualizada a su servicio de nube y está escribiendo diagnósticos al almacenamiento de Azure, ya está listo para configurar el análisis de registro.


## <a name="use-the-azure-portal-to-collect-logs-from-azure-storage"></a>Usar el portal de Azure para recopilar registros de almacenamiento de Azure

Puede usar el portal de Azure para configurar el análisis de registro para recopilar los registros para los servicios de Azure siguientes:

+ Servicio tela clústeres
+ Máquinas virtuales de Windows
+ Funciones Web/trabajo

En el portal de Azure, desplácese hasta el área de trabajo de análisis de registro y realice las siguientes tareas:

1. Haga clic en *registros de cuentas de almacenamiento*
2. Haga clic en *Agregar* tarea
3. Seleccione la cuenta de almacenamiento que contiene los registros de diagnóstico
  - Esta cuenta puede ser una cuenta de almacenamiento clásico o una cuenta de almacenamiento de administrador de recursos de Azure
4. Seleccione el tipo de datos que desean recopilar registros para
  - Las opciones son los registros de IIS; Eventos; Registro del sistema (Linux); ETW registros; Eventos de tela de servicio
5. El valor de origen se rellena automáticamente en función del tipo de datos y no se puede cambiar
6. Haga clic en Aceptar para guardar la configuración

Repita los pasos del 2 al 6 para los tipos de datos que desea que el análisis de registro para recopilar y cuentas de almacenamiento adicional.

En unos 30 minutos, es posible ver datos de la cuenta de almacenamiento de análisis de registro. Solo verá los datos que se escriben en almacenamiento después de aplica la configuración. Análisis de registro no lee los datos existentes de la cuenta de almacenamiento.

>[AZURE.NOTE] El portal no valida que el origen existe en la cuenta de almacenamiento o si se está escribiendo datos nuevos.

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection-using-powershell"></a>Habilitar diagnóstico de Azure en una máquina virtual para el registro de eventos y IIS registro colección con PowerShell

Realice los pasos de [Configuración de análisis de registro indizar diagnóstico de Azure](log-analytics-powershell-workspace-configuration.md#configuring-log-analytics-to-index-azure-diagnostics) usar PowerShell para leer de diagnóstico de Azure que se escribe en el almacenamiento de tablas.

Uso de PowerShell de Azure con mayor precisión puede especificar los eventos que se escriben en el almacenamiento de Azure.
Para obtener más información, vea [Habilitar diagnósticos en Azure máquinas virtuales de Windows](../virtual-machines-dotnet-diagnostics.md).

Puede habilitar y actualizar mediante el siguiente script de PowerShell de diagnóstico de Azure.
También puede usar esta secuencia de comandos con una configuración de registro personalizado.
Modificar la secuencia de comandos para configurar la cuenta de almacenamiento, el nombre de servicio y el nombre de la máquina virtual.
La secuencia de comandos usa cmdlets para máquinas virtuales de Windows clásico.

Revise el siguiente ejemplo de script, copiarlo, modificarlo según sea necesario, guardar los datos de ejemplo como un archivo de script de PowerShell y, a continuación, ejecute el script.

```
    #Connect to Azure
    Add-AzureAccount

    # settings to change:
    $wad_storage_account_name = "myStorageAccount"
    $service_name = "myService"
    $vm_name = "myVM"

    #Construct Azure Diagnostics public config and convert to config format

    # Collect just system error events:
    $wad_xml_config = "<WadCfg><DiagnosticMonitorConfiguration><WindowsEventLog scheduledTransferPeriod=""PT1M""><DataSource name=""System!* "" /></WindowsEventLog></DiagnosticMonitorConfiguration></WadCfg>"

    $wad_b64_config = [System.Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes($wad_xml_config))
    $wad_public_config = [string]::Format("{{""xmlCfg"":""{0}""}}",$wad_b64_config)

    #Construct Azure diagnostics private config

    $wad_storage_account_key = (Get-AzureStorageKey $wad_storage_account_name).Primary
    $wad_private_config = [string]::Format("{{""storageAccountName"":""{0}"",""storageAccountKey"":""{1}""}}",$wad_storage_account_name,$wad_storage_account_key)

    #Enable Diagnostics Extension for Virtual Machine

    $wad_extension_name = "IaaSDiagnostics"
    $wad_publisher = "Microsoft.Azure.Diagnostics"
    $wad_version = (Get-AzureVMAvailableExtension -Publisher $wad_publisher -ExtensionName $wad_extension_name).Version # Gets latest version of the extension

    (Get-AzureVM -ServiceName $service_name -Name $vm_name) | Set-AzureVMExtension -ExtensionName $wad_extension_name -Publisher $wad_publisher -PublicConfiguration $wad_public_config -PrivateConfiguration $wad_private_config -Version $wad_version | Update-AzureVM
```


## <a name="next-steps"></a>Pasos siguientes

- [Usar JSON archivos de almacenamiento de blobs](log-analytics-azure-storage-json.md) para leer los registros de Azure servicios que diagnósticos de escritura al almacenamiento de blobs en formato JSON.
- [Habilitar soluciones](log-analytics-add-solutions.md) para proporcionar una perspectiva de los datos.
- [Usar consultas de búsqueda](log-analytics-log-searches.md) para analizar los datos.
