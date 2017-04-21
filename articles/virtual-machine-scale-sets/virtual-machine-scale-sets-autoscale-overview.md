<properties
    pageTitle="Ajuste de escala automático y máquina virtual escalar conjuntos | Microsoft Azure"
    description="Obtenga información sobre el uso de diagnósticos y recursos de Autoescala escalar automáticamente máquinas virtuales de un conjunto de escala."
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="davidmu"/>

# <a name="automatic-scaling-and-virtual-machine-scale-sets"></a>Ajuste de escala automático y máquina virtual escalar conjuntos

Ajuste de escala automático de máquinas virtuales de un conjunto de escala es la creación o eliminación de equipos en el conjunto según sea necesario para que coincida con los requisitos de rendimiento. A medida que crece el volumen de trabajo, una aplicación puede requerir recursos adicionales para que pueda realizar tareas de forma eficaz.

Escala automática es un proceso automatizado que ayuda a reducir la carga de administración. Al reducir la carga, no es necesario supervisar el rendimiento del sistema continuamente o decidir cómo administrar recursos. Escalar es un proceso elástico. Más recursos pueden agregarse como aumenta la carga, pero como demanda pueden quitarse disminuye recursos para minimizar los costos y mantener los niveles de rendimiento.

Configurar el ajuste de escala automático en una escala de establecer mediante una plantilla de administrador de recursos de Azure, Azure PowerShell, Azure CLI o el portal de Azure.

## <a name="set-up-scaling-by-using-resource-manager-templates"></a>Configurar la escala con las plantillas de administrador de recursos

En lugar de implementar y administrar todos los recursos de la aplicación por separado, utilice una plantilla que implementa todos los recursos en una única operación coordinado. En la plantilla se definen los recursos de la aplicación y se especifican los parámetros de implementación para distintos entornos. La plantilla se compone de JSON y expresiones que puede usar para generar valores para su implementación. Para obtener más información, busque [plantillas de administrador de recursos de Azure de creación](../resource-group-authoring-templates.md).

En la plantilla, especifique el elemento de capacidad:

    "sku": {
      "name": "Standard_A0",
      "tier": "Standard",
      "capacity": 3
    },

Capacidad identifica el número de equipos virtuales en el conjunto. Puede cambiar manualmente la capacidad al implementar una plantilla con un valor diferente. Si va a implementar una plantilla para cambiar únicamente la capacidad, puede incluir únicamente el elemento SKU con la capacidad actualizada.

Cambiar automáticamente la capacidad de la escala establecida mediante la combinación de recursos de autoscaleSettings y la extensión de diagnósticos.

### <a name="configure-the-azure-diagnostics-extension"></a>Configurar la extensión de diagnósticos de Azure

Escala automático sólo se puede hacer si se realiza correctamente en cada máquina virtual del conjunto de escala de la colección de métricas. La extensión de diagnósticos de Azure proporciona las capacidades de supervisión y diagnóstico que satisfaga las necesidades de la colección de métricas del recurso Autoescala. Puede instalar la extensión como parte de la plantilla de administrador de recursos.

En este ejemplo se muestra las variables que se usan en la plantilla para configurar la extensión de diagnósticos:

    "diagnosticsStorageAccountName": "[concat(parameters('resourcePrefix'), 'saa')]",
    "accountid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/', 'Microsoft.Storage/storageAccounts/', variables('diagnosticsStorageAccountName'))]",
    "wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
    "wadperfcounter": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Thread Count\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
    "wadcfgxstart": "[concat(variables('wadlogs'),variables('wadperfcounter'),'<Metrics resourceId=\"')]",
    "wadmetricsresourceid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name ,'/providers/','Microsoft.Compute/virtualMachineScaleSets/',parameters('vmssName'))]",
    "wadcfgxend": "[concat('\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>')]"

Cuando se implementa la plantilla, se proporcionan parámetros. En este ejemplo, se proporcionan el nombre de la cuenta de almacenamiento donde se almacenan los datos y el nombre del conjunto de escala desde el que se recopilan los datos. También en este ejemplo de Windows Server, se recopila solo el subproceso contador de rendimiento. Todos los contadores de rendimiento disponibles en Windows o Linux pueden usarse para recopilar información de diagnóstico y se pueden incluir en la configuración de extensión.

Este ejemplo muestra la definición de la extensión en la plantilla:

    "extensionProfile": {
      "extensions": [
        {
          "name": "Microsoft.Insights.VMDiagnosticsSettings",
          "properties": {
            "publisher": "Microsoft.Azure.Diagnostics",
            "type": "IaaSDiagnostics",
            "typeHandlerVersion": "1.5",
            "autoUpgradeMinorVersion": true,
            "settings": {
              "xmlCfg": "[base64(concat(variables('wadcfgxstart'),variables('wadmetricsresourceid'),variables('wadcfgxend')))]",
              "storageAccount": "[variables('diagnosticsStorageAccountName')]"
            },
            "protectedSettings": {
              "storageAccountName": "[variables('diagnosticsStorageAccountName')]",
              "storageAccountKey": "[listkeys(variables('accountid'), variables('apiVersion')).key1]",
              "storageAccountEndPoint": "https://core.windows.net"
            }
          }
        }
      ]
    }

Cuando se ejecuta la extensión de diagnóstico, se recopilan los datos de una tabla que se encuentra en la cuenta de almacenamiento que especifique. En la tabla WADPerformanceCounters, puede encontrar los datos recopilados:

![](./media/virtual-machine-scale-sets-autoscale-overview/ThreadCountBefore2.png)

### <a name="configure-the-autoscalesettings-resource"></a>Configurar el recurso autoScaleSettings

El recurso autoscaleSettings utiliza la información de la extensión de diagnóstico para decidir si desea aumentar o reducir el número de equipos virtuales en el conjunto de escala.

Este ejemplo muestra la configuración del recurso en la plantilla:

    {
      "type": "Microsoft.Insights/autoscaleSettings",
      "apiVersion": "2015-04-01",
      "name": "[concat(parameters('resourcePrefix'),'as1')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]"
      ],
      "properties": {
        "enabled": true,
        "name": "[concat(parameters('resourcePrefix'),'as1')]",
        "profiles": [
          {
            "name": "Profile1",
            "capacity": {
              "minimum": "1",
              "maximum": "10",
              "default": "1"
            },
            "rules": [
              {
                "metricTrigger": {
                  "metricName": "\\Process(_Total)\\Thread Count",
                  "metricNamespace": "",
                  "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
                  "timeGrain": "PT1M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "GreaterThan",
                  "threshold": 650
                },
                "scaleAction": {
                  "direction": "Increase",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              },
              {
                "metricTrigger": {
                  "metricName": "\\Process(_Total)\\Thread Count",
                  "metricNamespace": "",
                  "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
                  "timeGrain": "PT1M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "LessThan",
                  "threshold": 550
                },
                "scaleAction": {
                  "direction": "Decrease",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              }
            ]
          }
        ],
        "targetResourceUri": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]"
      }
    }

En el ejemplo anterior, se crean dos reglas para definir las acciones de escala automático. La primera regla define la acción de escalado y la segunda regla define la acción de escala. Estos valores se proporcionan en las reglas:

- **metricName** - este valor es el mismo que el contador de rendimiento que haya definido en la variable wadperfcounter para la extensión de diagnósticos. En el ejemplo anterior, se utiliza el contador de subproceso.  
- **metricResourceUri** - este valor es el identificador de recursos del conjunto de escala de máquina virtual. Este identificador contiene el nombre del grupo de recursos, el nombre del proveedor del recurso y el nombre de la escala establecida escalar.
- **timeGrain** : este valor es el nivel de detalle de las mediciones que se recopilan. En el ejemplo anterior, los datos se recopilan en un intervalo de un minuto. Este valor se utiliza con la ventana de tiempo.
- **estadística** : este valor determina cómo se combinan las métricas para adaptarse a la acción de escala automático. Los valores posibles son: promedio, Min, Max.
- **ventana de tiempo** : este valor es el intervalo de tiempo en el que se recopilan los datos de la instancia. Debe estar entre 5 minutos y 12 horas.
- **timeAggregation** : este valor determina cómo se deben combinar los datos que se recopilan con el tiempo. El valor predeterminado es el promedio. Los valores posibles son: promedio, mínimo, máximo, apellidos, Total, recuento.
- **operador** : este valor es el operador que se usa para comparar los datos de métrica y el umbral. Los valores posibles son: es igual a NotEquals, mayor, GreaterThanOrEqual, LessThan, LessThanOrEqual.
- **umbral** : este valor es el valor que activa la acción de escala. Asegúrese de proporcionar suficientes diferencias entre el umbral de la acción de escalado y el umbral de la acción de escala. Si establece los valores que sean de la misma, el sistema prevé cambio constante, lo que evita que implementar una acción de escala. Por ejemplo, si se establecen a 600 subprocesos en el ejemplo anterior no funciona.
- **dirección** : este valor determina la acción que se realiza cuando se obtiene el valor de umbral. Los valores posibles son Subir o Bajar.
- **tipo** : este valor es el tipo de acción que debe realizarse y debe estar establecida en ChangeCount.
- **valor** : este valor es el número de máquinas virtuales que se agregan o se eliminan del conjunto de escala. Este valor debe ser 1 o mayor.
- **enfriamiento** : este valor es la cantidad de tiempo de espera desde la última acción de escalado antes de que se produce la acción siguiente. Este valor debe estar entre un minuto y una semana.

Según el contador de rendimiento que está utilizando, algunos de los elementos de la configuración de la plantilla se usan diferente. En el ejemplo anterior, el contador de rendimiento es el número de subprocesos, el valor de umbral es 650 para una acción de escalado y el valor de umbral es 550 para la acción de escala. Si utiliza un contador como % de tiempo de procesador, el valor de umbral se establece en el porcentaje de uso de CPU que determina una acción de escala.

Cuando se crea una carga en las máquinas virtuales que desencadene una acción de escalado, la capacidad del conjunto aumenta según el valor de la plantilla. Por ejemplo, en una escala de donde se establece la capacidad de 3 y el valor de escala de acción establecido en 1:

![](./media/virtual-machine-scale-sets-autoscale-overview/ResourceExplorerBefore.png)

Cuando se crea la carga que hace que el recuento de subproceso promedio ir por encima del umbral de 650:

![](./media/virtual-machine-scale-sets-autoscale-overview/ThreadCountAfter.png)

Se activa una acción de escalado que hace que la capacidad del conjunto para incrementar en uno:

    "sku": {
      "name": "Standard_A0",
      "tier": "Standard",
      "capacity": 4
    },

Y se agrega una máquina virtual para el conjunto de escala:

![](./media/virtual-machine-scale-sets-autoscale-overview/ResourceExplorerAfter.png)

Tras un periodo de enfriamiento de cinco minutos, el promedio de subprocesos en los equipos permanece 600, otro equipo se agrega al conjunto. Si el número de subprocesos promedio permanece por debajo de 550, se reduce la capacidad del conjunto de escala en uno y un equipo se quita del conjunto.

## <a name="set-up-scaling-using-azure-powershell"></a>Configurar el ajuste de escala con PowerShell de Azure

Para ver ejemplos de uso de PowerShell para configurar el ajuste automático, mire [Azure Monitor PowerShell rápido iniciar las muestras](../monitoring-and-diagnostics/insights-powershell-samples.md).

## <a name="set-up-scaling-using-azure-cli"></a>Configurar escala mediante CLI de Azure

Para ver ejemplos de uso de Azure CLI para configurar el ajuste automático, mire [Monitor de Azure entre plataformas CLI rápido iniciar las muestras](../monitoring-and-diagnostics/insights-cli-samples.md).

## <a name="set-up-scaling-using-the-azure-portal"></a>Configurar el ajuste de escala con el portal de Azure

Para ver un ejemplo de uso del portal de Azure para configurar el ajuste automático, mire [crear un conjunto de escala de máquina Virtual con el portal de Azure](virtual-machine-scale-sets-portal-create.md).

## <a name="investigate-scaling-actions"></a>Investigar escalado de acciones

- [Portal de azure]() - actualmente puede obtener una cantidad limitada de información con el portal.
- [Explorador de recursos de azure]() : esta herramienta es la mejor para explorar el estado actual de su conjunto de escala. Siga esta ruta de acceso y verá la vista de instancia del conjunto de escala que ha creado: suscripciones > {su suscripción} > resourceGroups > {su grupo de recursos} > Proveedores > Microsoft.Compute > virtualMachineScaleSets > {el conjunto de escala} > virtualMachines
- Azure PowerShell: Utilice este comando para obtener información:

        Get-AzureRmResource -name vmsstest1 -ResourceGroupName vmsstestrg1 -ResourceType Microsoft.Compute/virtualMachineScaleSets -ApiVersion 2015-06-15
        Get-Autoscalesetting -ResourceGroup rainvmss -DetailedOutput

- Conectarse a la máquina virtual de jumpbox como lo haría con cualquier otro equipo y, a continuación, puede tener acceso remoto a las máquinas virtuales de la escala establecida para supervisar los procesos individuales.

## <a name="next-steps"></a>Pasos siguientes

- Mire [cambiar automáticamente la escala de equipos de un conjunto de escala de máquina Virtual](virtual-machine-scale-sets-windows-autoscale.md) para ver un ejemplo de cómo crear una escala de conjunto con ajuste de escala automático configurado.
- Buscar ejemplos de Azure supervisar características en [ejemplos de inicio rápido de Azure Monitor PowerShell](../monitoring-and-diagnostics/insights-powershell-samples.md)
- Obtenga información sobre las características de notificación de [usar Autoescala acciones para enviar notificaciones de alerta de correo electrónico y webhook en el Monitor de Azure](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md).
- Obtenga más información sobre cómo [los registros de auditoría de uso para enviar notificaciones de alerta de correo electrónico y webhook en el Monitor de Azure](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md)
- Obtenga más información sobre [escenarios Autoescala avanzadas](./virtual-machine-scale-sets-advanced-autoscale.md).
