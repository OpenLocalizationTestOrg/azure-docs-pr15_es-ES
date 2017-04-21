<properties
    pageTitle="Crear una máquina Virtual de Windows con la supervisión y el diagnóstico usando la plantilla de administrador de recursos de Azure | Microsoft Azure"
    description="Use una plantilla de administrador de recursos de Azure para crear una nueva máquina virtual de Windows con la extensión de diagnóstico de Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="sbtron"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/15/2015"
    ms.author="saurabh"/>

# <a name="create-a-windows-virtual-machine-with-monitoring-and-diagnostics-using-azure-resource-manager-template"></a>Crear una máquina Virtual de Windows con la supervisión y el diagnóstico usando la plantilla de administrador de recursos de Azure

La extensión de diagnósticos de Azure proporciona la supervisión y capacidades de diagnósticos de Windows en función de Azure máquina virtual. Para habilitar estas funciones en la máquina virtual, incluida la extensión como parte de la plantilla de administrador de recursos de azure. Consulte [Creación plantillas de administrador de recursos de Azure con VM extensiones](virtual-machines-windows-extensions-authoring-templates.md) para obtener más información sobre cómo incluir cualquier extensión como parte de una plantilla de máquina virtual. Este artículo describe cómo puede agregar la extensión de diagnósticos de Azure a una plantilla de máquina virtual de windows.  
  

## <a name="add-the-azure-diagnostics-extension-to-the-vm-resource-definition"></a>Agregar la extensión de diagnósticos de Azure a la definición de recursos VM 

Para habilitar la extensión de diagnóstico en una máquina Virtual de Windows, debe agregar la extensión como un recurso VM en la plantilla de administrador de recursos.

Para un administrador de recursos simple basado en Máquina Virtual agrega la configuración de la extensión a la matriz de *recursos* para la máquina Virtual: 

    "resources": [
                {
                    "name": "Microsoft.Insights.VMDiagnosticsSettings",
                    "type": "extensions",
                    "location": "[resourceGroup().location]",
                    "apiVersion": "2015-06-15",
                    "dependsOn": [
                        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
                    ],
                    "tags": {
                        "displayName": "AzureDiagnostics"
                    },
                    "properties": {
                        "publisher": "Microsoft.Azure.Diagnostics",
                        "type": "IaaSDiagnostics",
                        "typeHandlerVersion": "1.5",
                        "autoUpgradeMinorVersion": true,
                        "settings": {
                            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), variables('vmName'), variables('wadcfgxend')))]",
                            "storageAccount": "[parameters('existingdiagnosticsStorageAccountName')]"
                        },
                        "protectedSettings": {
                            "storageAccountName": "[parameters('existingdiagnosticsStorageAccountName')]",
                            "storageAccountKey": "[listkeys(variables('accountid'), '2015-05-01-preview').key1]",
                            "storageAccountEndPoint": "https://core.windows.net"
                        }
                    }
                }
            ]


Otra convención común se agregue la configuración de extensión en el nodo de recursos de raíz de la plantilla en lugar de definir en nodo de recursos de la máquina virtual. Con este enfoque debe especificar explícitamente una relación jerárquica entre la extensión y la máquina virtual con los valores de *nombre* y *tipo* . Por ejemplo: 
  
    "name": "[concat(variables('vmName'),'Microsoft.Insights.VMDiagnosticsSettings')]",
    "type": "Microsoft.Compute/virtualMachines/extensions",

La extensión siempre está asociada a la máquina virtual, puede directamente definir directamente en el nodo de recursos de la máquina virtual o definir en el nivel de base y use la convención de nomenclatura jerárquica para asociar con la máquina virtual.

Conjuntos de escala de máquina Virtual de la configuración de extensiones se especifica en la propiedad *extensionProfile* de la *VirtualMachineProfile*.
   
La propiedad de *publisher* con el valor de **Microsoft.Azure.Diagnostics** y la propiedad de *tipo* con el valor de **IaaSDiagnostics** para identificar la extensión de diagnósticos de Azure.

El valor de la propiedad *name* puede utilizarse para hacer referencia a la extensión del grupo de recursos. Establecer específicamente para **Microsoft.Insights.VMDiagnosticsSettings** le permitirá identificar fácilmente por la Azure clásica portal portal garantizar que los gráficos de supervisión aparecen correctamente en el portal de clásico de Azure.

El *typeHandlerVersion* especifica la versión de la extensión que desea usar. Establecer *autoUpgradeMinorVersion* versión secundaria **Verdadero** , se asegura de que obtendrá la última versión secundaria de la extensión que está disponible. Se recomienda establecer siempre *autoUpgradeMinorVersion* siempre será **true** para que siempre llegar a utilizar la última extensión de diagnósticos disponible con todas las nuevas características y correcciones de errores. 

El elemento de *configuración* contiene propiedades de configuraciones para la extensión que se pueden establecer y leer de la extensión (también se conoce como configuración público). La propiedad *xmlcfg* contiene configuración basado en xml para los registros de diagnósticos contadores etcetera que se cobrará por el agente de diagnóstico. Para obtener más información sobre el propio esquema xml, vea [Esquema de configuración de diagnósticos](https://msdn.microsoft.com/library/azure/dn782207.aspx) . Es una práctica común almacenar la configuración de xml real como una variable en la plantilla de administrador de recursos de Azure y, a continuación, concatenar y base 64 codificar para establecer el valor de *xmlcfg*. Consulte la sección sobre [las variables de configuración de diagnósticos](#diagnostics-configuration-variables) para conocer más acerca de cómo almacenar el archivo xml en variables. La propiedad *storageAccount* especifica el nombre de la cuenta de almacenamiento a la que se transferirán los datos de diagnóstico. 
 
Las propiedades de *protectedSettings* (también se conoce como configuración privada) se pueden establecer, pero no se pueden leer después de que se establece. La naturaleza solo escritura de *protectedSettings* resulta útil para almacenar información confidencial como la clave de cuenta de almacenamiento donde se pueden escribir los datos de diagnóstico.    

## <a name="specifying-diagnostics-storage-account-as-parameters"></a>Especificar la cuenta de almacenamiento de diagnósticos como parámetros 

Lo diagnósticos extensión json fragmento de código anterior supone dos parámetros *existingdiagnosticsStorageAccountName* y *existingdiagnosticsStorageResourceGroup* para especificar la cuenta de almacenamiento de información de diagnóstico se almacenarán los datos de diagnóstico. Especifica la cuenta de almacenamiento de información de diagnóstico como un parámetro es fácil cambiar la cuenta de almacenamiento de información de diagnóstico en distintos entornos, por ejemplo, es aconsejable usar una cuenta de almacenamiento de diagnósticos diferente para probar y otro diferente para la implementación de producción.  

        "existingdiagnosticsStorageAccountName": {
            "type": "string",
            "metadata": {
        "description": "The name of an existing storage account to which diagnostics data will be transfered."
            }        
        },
        "existingdiagnosticsStorageResourceGroup": {
            "type": "string",
            "metadata": {
        "description": "The resource group for the storage account specified in existingdiagnosticsStorageAccountName"
            }
        }

Es recomendable especificar una cuenta de almacenamiento de información de diagnóstico en otro grupo de recursos que el grupo de recursos para la máquina virtual. Un grupo de recursos puede considerarse una unidad de implementación con su propia duración, una máquina virtual se puede implementar y volver a instalar como nuevas actualizaciones de las configuraciones se realizan en él pero desea seguir almacenando los datos de diagnóstico en la misma cuenta de almacenamiento a través de las implementaciones de máquina virtual. Con la cuenta de almacenamiento en un recurso diferente, habilita la cuenta de almacenamiento para que acepte datos de varias implementaciones de máquina virtual, lo que posibilita solucionar problemas en las distintas versiones.

>[AZURE.NOTE] Si crea una plantilla de máquina virtual de windows de Visual Studio puede establecerse la cuenta de almacenamiento predeterminada para usar la misma cuenta de almacenamiento donde se carga la máquina virtual de disco duro virtual. Esto es para simplificar la instalación inicial de la máquina virtual. Debe considerar volver a la plantilla para usar una cuenta de almacenamiento diferente que puede pasar como un parámetro. 

## <a name="diagnostics-configuration-variables"></a>Variables de configuración de diagnósticos
 
Lo diagnósticos extensión json fragmento de código anterior define una variable *accountid* para simplificar la introducción de la clave de cuenta de almacenamiento para el almacenamiento de diagnósticos:   
    
    "accountid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/',parameters('existingdiagnosticsStorageResourceGroup'), '/providers/','Microsoft.Storage/storageAccounts/', parameters('existingdiagnosticsStorageAccountName'))]"


La propiedad *xmlcfg* para la extensión de diagnósticos se define usando múltiples variables concatenadas. Los valores de las variables están en xml, por lo que necesitan un carácter de escape correctamente cuando se establece las variables de json.

A continuación describe el xml de configuración de diagnósticos que recopila contadores de rendimiento de nivel de sistema estándar junto con algunos registros de eventos de windows y los registros de diagnósticos de infraestructura. Se ha de escape y el formato correcto para que la configuración se puede pegar directamente en la sección de variables de la plantilla. Ver el [Esquema de configuración de diagnósticos](https://msdn.microsoft.com/library/azure/dn782207.aspx) para obtener un ejemplo más humano legible del xml de configuración.
    
        "wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
        "wadperfcounters1": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Processor Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU utilization\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Privileged Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU privileged time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% User Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU user time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor Information(_Total)\\Processor Frequency\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"CPU frequency\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\System\\Processes\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Processes\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Threads\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Handle Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Handles\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\% Committed Bytes In Use\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Memory usage\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Available Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory available\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Committed Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory committed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Commit Limit\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory commit limit\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active time\" locale=\"en-us\"/></PerformanceCounterConfiguration>",
        "wadperfcounters2": "<PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Read Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active read time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Write Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active write time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Transfers/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Reads/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk read operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Writes/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk write operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Read Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk read speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Write Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk write speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\LogicalDisk(_Total)\\% Free Space\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk free space (percentage)\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
        "wadcfgxstart": "[concat(variables('wadlogs'), variables('wadperfcounters1'), variables('wadperfcounters2'), '<Metrics resourceId=\"')]",
        "wadmetricsresourceid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name , '/providers/', 'Microsoft.Compute/virtualMachines/')]",
        "wadcfgxend": "\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>"

El nodo de xml de definición de métricas en la configuración anterior es un elemento de configuración importantes que define cómo se agregado y almacena los contadores de rendimiento definidos anteriormente en el código xml de nodo *PerformanceCounter* . 

> [AZURE.IMPORTANT] Estas métricas unidad los gráficos y las alertas de supervisión en el portal de Azure.  El nodo **métricas** con *resourceID* y **MetricAggregation** debe incluirse en la configuración de diagnósticos de la máquina virtual si desea ver los datos de supervisión de VM en el portal de Azure. 

A continuación se muestra un ejemplo del código xml para definiciones de estadísticas: 

        <Metrics resourceId="/subscriptions/subscription().subscriptionId/resourceGroups/resourceGroup().name/providers/Microsoft.Compute/virtualMachines/vmName">
            <MetricAggregation scheduledTransferPeriod="PT1H"/>
            <MetricAggregation scheduledTransferPeriod="PT1M"/>
        </Metrics>

El atributo *resourceID* identifica la máquina virtual en la suscripción. Asegúrese de usar las funciones subscription() y resourceGroup() para que la plantilla actualiza automáticamente los valores basados en la suscripción y el grupo de recursos que va a implementar.

Si va a crear varias máquinas virtuales en un bucle, tendrá que rellenar el valor *resourceID* con una función de copyIndex() correctamente diferenciar cada VM individual. El valor de *xmlCfg* puede actualizarse para admitir esto como sigue:  

    "xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), concat(parameters('vmNamePrefix'), copyindex()), variables('wadcfgxend')))]", 

El valor de MetricAggregation de *PT1H* y *PT1M* indicar una agregación más de un minuto y una agregación más de una hora.

## <a name="wadmetrics-tables-in-storage"></a>Tablas de WADMetrics de almacenamiento

La configuración de métricas anterior generará tablas en su cuenta de almacenamiento de diagnósticos con las siguientes convenciones de nomenclatura:

- **WADMetrics** : prefijo estándar para todas las tablas de WADMetrics
- **PT1H** o **PT1M** : significa que la tabla contiene datos agregados más de 1 hora o 1 minuto
- **P10D** : significa que la tabla debe contener datos para 10 días a partir de la tabla iniciarse de recopilación de datos
- **V2S** : constante de cadena
- **aaaammdd** : la fecha en que inició la tabla de recopilación de datos

Ejemplo: *WADMetricsPT1HP10DV2S20151108* contendrá datos métricas agregados más de una hora de 10 días a partir de noviembre de 11 de 2015    

Cada tabla WADMetrics contiene las columnas siguientes:

- **PartitionKey**: el partitionkey se construye basándose en el valor de *resourceID* para identificar el recurso de máquina virtual. para por ejemplo: 002Fsubscriptions:<subscriptionID>: 002FresourceGroups:002F<ResourceGroupName>: 002Fproviders:002FMicrosoft:002ECompute:002FvirtualMachines:002F<vmName>  
- **RowKey** : sigue el formato <Descending time tick>:<Performance Counter Name>. El cálculo de marca de tiempo descendente es marcas de tiempo máximo menos la hora de comienzo del período de agregación. Por ejemplo Si ha iniciado el período de muestra en noviembre de 10 de 2015 y 00:00Hrs UTC el cálculo sería: DateTime.MaxValue.Ticks - (DateTime(2015,11,10,0,0,0,DateTimeKind.Utc) nuevo. Marcas). Obtener la clave de la fila del contador de rendimiento de bytes disponibles de memoria tendrá un aspecto: 2519551871999999999__:005CMemory:005CAvailable:0020 Bytes
- **CounterName** : es el nombre del contador de rendimiento. Esto coincide con el *counterSpecifier* definido en el archivo de configuración de xml.
- **Máximo** : el valor máximo del contador de rendimiento durante el período de agregación.
- **Mínimo** : el valor mínimo del contador de rendimiento durante el período de agregación.
- **Total** : la suma de todos los valores del contador de rendimiento informado durante el período de agregación.
- **Recuento** : informa del número total de valores de contador de rendimiento.
- **Promedio** : el valor promedio (total/recuento) del contador de rendimiento durante el período de agregación.


## <a name="next-steps"></a>Pasos siguientes

- Para una plantilla de ejemplo completo de una máquina virtual de Windows con la extensión de diagnósticos vea [201 vm-supervisión-diagnósticos-extensión](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-monitoring-diagnostics-extension)   
- Implementar la plantilla de administrador de recursos con [PowerShell de Azure](virtual-machines-windows-ps-manage.md) o [línea de comandos de Azure](virtual-machines-linux-cli-deploy-templates.md)
- Obtenga más información sobre la [creación de plantillas de administrador de recursos de Azure](../resource-group-authoring-templates.md)







