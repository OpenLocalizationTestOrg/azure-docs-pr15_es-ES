<properties
    pageTitle="Establece la escala de máquina Virtual Linux Autoescala | Microsoft Azure"
    description="Configurar el ajuste automático de una escala de máquina Virtual Linux establecer usando CLI de Azure"
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
    ms.date="09/27/2016"
    ms.author="davidmu"/>

# <a name="automatically-scale-linux-machines-in-a-virtual-machine-scale-set"></a>Ajustar automáticamente equipos Linux en un conjunto de escala de máquina virtual

Conjuntos de escala de máquina virtual facilitan implementar y administrar idénticos máquinas virtuales como un conjunto. Conjuntos de escala proporcionan una capa cálculo altamente scalable y personalizable para las aplicaciones de gran escala y admiten imágenes de la plataforma de Windows, imágenes de la plataforma de Linux, imágenes personalizadas y extensiones. Para obtener más información, vea [Información general de conjuntos de escala de máquina Virtual](virtual-machine-scale-sets-overview.md).

En este tutorial se muestra cómo crear un conjunto de escala de máquinas virtuales de Linux con la última versión de Ubuntu Linux. El tutorial también muestra cómo ajustar automáticamente los equipos en el conjunto. Crear la escala y configura escalar mediante la creación de una plantilla de administrador de recursos de Azure e implementación mediante CLI de Azure. Para obtener más información sobre las plantillas, vea [plantillas de administrador de recursos de Azure de creación](../resource-group-authoring-templates.md). Para obtener más información acerca de la escala automática de conjuntos de escala, consulte [escala automática y conjuntos de escala de máquina Virtual](virtual-machine-scale-sets-autoscale-overview.md).

En este tutorial, implementar las extensiones y los recursos siguientes:

- Microsoft.Storage/storageAccounts
- Microsoft.Network/virtualNetworks
- Microsoft.Network/publicIPAddresses
- Microsoft.Network/loadBalancers
- Microsoft.Network/networkInterfaces
- Microsoft.Compute/virtualMachines
- Microsoft.Compute/virtualMachineScaleSets
- Microsoft.Insights.VMDiagnosticsSettings
- Microsoft.Insights/autoscaleSettings

Para obtener más información acerca de los recursos de administrador de recursos, vea [calcular Azure, red y los proveedores de almacenamiento en el Administrador de recursos de Azure](../virtual-machines/virtual-machines-linux-compare-deployment-models.md).

Antes de comenzar a con los pasos de este tutorial, [instalar CLI Azure](../xplat-cli-install.md).

## <a name="step-1-create-a-resource-group-and-a-storage-account"></a>Paso 1: Crear un grupo de recursos y una cuenta de almacenamiento

1. **Inicie sesión en Microsoft Azure** - en la interfaz de línea de comandos (fiesta, Terminal, comando preguntar), cambie a modo de administrador de recursos y, a continuación, [inicie sesión con su trabajo o escuela id](../xplat-cli-connect.md#use-the-log-in-method). Siga las indicaciones para una experiencia de inicio de sesión interactivo a su cuenta de Azure.

        azure config mode arm

        azure login

    > [AZURE.NOTE] Si tiene un trabajo o escuela ID y no tiene habilitada la autenticación de dos factores, utilice `azure login -u` con el identificador de iniciar sesión sin una sesión interactiva. Si no tiene un trabajo o escuela ID, puede [crear un trabajo o escuela id de cuenta personal de Microsoft](../virtual-machines/virtual-machines-linux-create-aad-work-id.md).

2. **Crear un grupo de recursos** : todos los recursos que se deben implementar en un grupo de recursos. Para este tutorial, el nombre de grupo de recursos **vmsstest1**.

        azure group create vmsstestrg1 centralus

3. **Implementar una cuenta de almacenamiento en el nuevo grupo de recursos** : esta cuenta de almacenamiento es donde se almacena la plantilla. Crear una cuenta de almacenamiento denominada **vmsstestsa**.

        azure storage account create -g vmsstestrg1 -l centralus --kind Storage --sku-name LRS vmsstestsa

## <a name="step-2-create-the-template"></a>Paso 2: Crear la plantilla
Una plantilla de administrador de recursos de Azure permite implementar y administrar recursos Azure juntos mediante una descripción de JSON de los parámetros de implementación asociado y recursos.

1. En el editor favorito, cree el archivo VMSSTemplate.json y agregue la estructura JSON inicial para la plantilla de soporte.

        {
          "$schema":"http://schema.management.azure.com/schemas/2014-04-01-preview/VM.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
          },
          "variables": {
          },
          "resources": [
          ]
        }

2. Parámetros no son siempre obligatorios, pero proporciona una manera de valores de entrada cuando se implementa la plantilla. Agregar estos parámetros en el elemento primario de parámetros que haya agregado a la plantilla.

        "vmName": { "type": "string" },
        "vmSSName": { "type": "string" },
        "instanceCount": { "type": "string" },
        "adminUsername": { "type": "string" },
        "adminPassword": { "type": "securestring" },
        "resourcePrefix": { "type": "string" }

    - Establece un nombre para la máquina virtual independiente que se usa para tener acceso a los equipos de la escala.
    - Un nombre para la cuenta de almacenamiento de información se almacena en la plantilla.
    - Establece el número de instancias de máquinas virtuales para crear inicialmente en la escala.
    - Un nombre y contraseña de la cuenta de administrador en los equipos virtuales.
    - Prefijo de nombre para los recursos que se crean para admitir el conjunto de escala.

3. Variables de pueden usarse en una plantilla para especificar valores que se pueden cambiar con frecuencia o que tenga que crear a partir de una combinación de valores de parámetro. Agregue estas variables debajo del elemento primario de las variables que haya agregado a la plantilla.

        "dnsName1": "[concat(parameters('resourcePrefix'),'dn1')]",
        "dnsName2": "[concat(parameters('resourcePrefix'),'dn2')]",
        "publicIP1": "[concat(parameters('resourcePrefix'),'ip1')]",
        "publicIP2": "[concat(parameters('resourcePrefix'),'ip2')]",
        "loadBalancerName": "[concat(parameters('resourcePrefix'),'lb1')]",
        "virtualNetworkName": "[concat(parameters('resourcePrefix'),'vn1')]",
        "nicName": "[concat(parameters('resourcePrefix'),'nc1')]",
        "lbID": "[resourceId('Microsoft.Network/loadBalancers',variables('loadBalancerName'))]",
        "frontEndIPConfigID": "[concat(variables('lbID'),'/frontendIPConfigurations/loadBalancerFrontEnd')]",
        "storageAccountSuffix": [ "a", "g", "m", "s", "y" ],
        "diagnosticsStorageAccountName": "[concat(parameters('resourcePrefix'), 'a')]",
        "accountid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/','Microsoft.Storage/storageAccounts/', variables('diagnosticsStorageAccountName'))]",
        "wadlogs": "<WadCfg><DiagnosticMonitorConfiguration>",
        "wadperfcounter": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor\\PercentProcessorTime\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU percentage guest OS\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
        "wadcfgxstart": "[concat(variables('wadlogs'),variables('wadperfcounter'),'<Metrics resourceId=\"')]",
        "wadmetricsresourceid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name ,'/providers/','Microsoft.Compute/virtualMachineScaleSets/',parameters('vmssName'))]",
        "wadcfgxend": "[concat('\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>')]"

    - Nombres DNS que se utilizan en las interfaces de red.
    - Los nombres de direcciones IP y prefijos para la red virtual y subredes.
    - Los nombres y los identificadores de la red virtual, cargar equilibrador e interfaces de red.
    - Establecer los nombres de cuenta de almacenamiento para las cuentas asociadas a los equipos de la escala.
    - Configuración de la extensión de diagnósticos que esté instalada en los equipos virtuales. Para obtener más información acerca de la extensión de diagnóstico, consulte [crear una máquina Virtual de Windows con la supervisión y el diagnóstico usando la plantilla de administrador de recursos de Azure](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md).

4. Agregue el recurso de la cuenta de almacenamiento en el elemento primario de recursos que haya agregado a la plantilla. Esta plantilla utiliza un bucle para crear las cuentas de almacenamiento cinco recomendadas donde se almacenan los discos de sistema operativo y los datos de diagnóstico. Este conjunto de cuentas puede admitir hasta 100 máquinas virtuales de un conjunto de escala, que es el máximo actual. Cada cuenta de almacenamiento se denomina con un indicador que se definió en las variables combinadas con el sufijo que proporciona en los parámetros de la plantilla.

        {
          "type": "Microsoft.Storage/storageAccounts",
          "name": "[concat(parameters('resourcePrefix'), variables('storageAccountSuffix')[copyIndex()])]",
          "apiVersion": "2015-06-15",
          "copy": {
            "name": "storageLoop",
            "count": 5
          },
          "location": "[resourceGroup().location]",
          "properties": { "accountType": "Standard_LRS" }
        },

5. Agregue el recurso de red virtual. Para obtener más información, vea [Proveedor de recursos de red](../virtual-network/resource-groups-networking.md).

        {
          "apiVersion": "2015-06-15",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "[variables('virtualNetworkName')]",
          "location": "[resourceGroup().location]",
          "properties": {
            "addressSpace": { "addressPrefixes": [ "10.0.0.0/16" ] },
            "subnets": [
              {
                "name": "subnet1",
                "properties": { "addressPrefix": "10.0.0.0/24" }
              }
            ]
          }
        },

6. Agregue los recursos de dirección IP públicos que utilizan la interfaz de red y el equilibrador de carga.

        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/publicIPAddresses",
          "name": "[variables('publicIP1')]",
          "location": "[resourceGroup().location]",
          "properties": {
            "publicIPAllocationMethod": "Dynamic",
            "dnsSettings": {
              "domainNameLabel": "[variables('dnsName1')]"
            }
          }
        },
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/publicIPAddresses",
          "name": "[variables('publicIP2')]",
          "location": "[resourceGroup().location]",
          "properties": {
            "publicIPAllocationMethod": "Dynamic",
            "dnsSettings": {
              "domainNameLabel": "[variables('dnsName2')]"
            }
          }
        },

7. Agregue el recurso de equilibrador de carga que se utiliza el conjunto de escala. Para obtener más información, vea [Compatibilidad con el Administrador de recursos de Azure para equilibrador de carga](../load-balancer/load-balancer-arm.md).

        {
          "apiVersion": "2015-06-15",
          "name": "[variables('loadBalancerName')]",
          "type": "Microsoft.Network/loadBalancers",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIP1'))]"
          ],
          "properties": {
            "frontendIPConfigurations": [
              {
                "name": "loadBalancerFrontEnd",
                "properties": {
                  "publicIPAddress": {
                    "id": "[resourceId('Microsoft.Network/publicIPAddresses/', variables('publicIP1'))]"
                  }
                }
              }
            ],
            "backendAddressPools": [ { "name": "bepool1" } ],
            "inboundNatPools": [
              {
                "name": "natpool1",
                "properties": {
                  "frontendIPConfiguration": {
                    "id": "[variables('frontEndIPConfigID')]"
                  },
                  "protocol": "tcp",
                  "frontendPortRangeStart": 50000,
                  "frontendPortRangeEnd": 50500,
                  "backendPort": 22
                }
              }
            ]
          }
        },

8. Agregue el recurso de interfaz de red que se utiliza la máquina virtual independiente. Porque los equipos de un conjunto de escala no están disponible a través de una dirección IP pública, se crea una máquina virtual distinta en la misma red virtual para tener acceso remoto a los equipos.

        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "[variables('nicName')]",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIP2'))]",
            "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
          ],
          "properties": {
            "ipConfigurations": [
              {
                "name": "ipconfig1",
                "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "publicIPAddress": {
                    "id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicIP2'))]"
                  },
                  "subnet": {
                    "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/virtualNetworks/',variables('virtualNetworkName'),'/subnets/subnet1')]"
                  }
                }
              }
            ]
          }
        },

9. Agregar la máquina virtual independiente en la misma red que el conjunto de escala.

        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Compute/virtualMachines",
          "name": "[parameters('vmName')]",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "storageLoop",
            "[concat('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
          ],
          "properties": {
            "hardwareProfile": { "vmSize": "Standard_A1" },
            "osProfile": {
              "computername": "[parameters('vmName')]",
              "adminUsername": "[parameters('adminUsername')]",
              "adminPassword": "[parameters('adminPassword')]"
            },
            "storageProfile": {
              "imageReference": {
                "publisher": "Canonical",
                "offer": "UbuntuServer",
                "sku": "14.04.4-LTS",
                "version": "latest"
              },
              "osDisk": {
                "name": "[concat(parameters('resourcePrefix'), 'os1')]",
                "vhd": {
                  "uri":  "[concat('https://',parameters('resourcePrefix'),'a.blob.core.windows.net/vhds/',parameters('resourcePrefix'),'os1.vhd')]"
                },
                "caching": "ReadWrite",
                "createOption": "FromImage"
              }
            },
            "networkProfile": {
              "networkInterfaces": [
                {
                  "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
                }
              ]
            }
          }
        },

10. Agregar que recurso de conjunto de la escala de la máquina virtual y especifique la extensión de diagnósticos que se instala en todos los equipos virtuales en el conjunto de escala. Muchas de las opciones para este recurso son similares con el recurso de máquina virtual. Las diferencias principales son el elemento de capacidad que especifica el número de máquinas virtuales en el conjunto de escala y upgradePolicy que especifica cómo se realizan las actualizaciones a máquinas virtuales. No se ha creado el conjunto de escala hasta que todas las cuentas de almacenamiento se crean como se indica con el elemento dependsOn.

            {
              "type": "Microsoft.Compute/virtualMachineScaleSets",
              "apiVersion": "2016-03-30",
              "name": "[parameters('vmSSName')]",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "storageLoop",
                "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
                "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]"
              ],
              "sku": {
                "name": "Standard_A1",
                "tier": "Standard",
                "capacity": "[parameters('instanceCount')]"
              },
              "properties": {
                "upgradePolicy": {
                  "mode": "Manual"
                },
                "virtualMachineProfile": {
                  "storageProfile": {
                    "osDisk": {
                      "vhdContainers": [
                        "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[0],'.blob.core.windows.net/vmss')]",
                        "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[1],'.blob.core.windows.net/vmss')]",
                        "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[2],'.blob.core.windows.net/vmss')]",
                        "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[3],'.blob.core.windows.net/vmss')]",
                        "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[4],'.blob.core.windows.net/vmss')]"
                      ],
                      "name": "vmssosdisk",
                      "caching": "ReadOnly",
                      "createOption": "FromImage"
                    },
                    "imageReference": {
                      "publisher": "Canonical",
                      "offer": "UbuntuServer",
                      "sku": "14.04.4-LTS",
                      "version": "latest"
                    }
                  },
                  "osProfile": {
                    "computerNamePrefix": "[parameters('vmSSName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "adminPassword": "[parameters('adminPassword')]"
                  },
                  "networkProfile": {
                    "networkInterfaceConfigurations": [
                      {
                        "name": "networkconfig1",
                        "properties": {
                          "primary": "true",
                          "ipConfigurations": [
                            {
                              "name": "ip1",
                              "properties": {
                                "subnet": {
                                  "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/virtualNetworks/',variables('virtualNetworkName'),'/subnets/subnet1')]"
                                },
                                "loadBalancerBackendAddressPools": [
                                  {
                                    "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/loadBalancers/',variables('loadBalancerName'),'/backendAddressPools/bepool1')]"
                                  }
                                ],
                                "loadBalancerInboundNatPools": [
                                  {
                                    "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/loadBalancers/',variables('loadBalancerName'),'/inboundNatPools/natpool1')]"
                                  }
                                ]
                              }
                            }
                          ]
                        }
                      }
                    ]
                  },
                  "extensionProfile": {
                    "extensions": [
                      {
                        "name":"LinuxDiagnostic",
                        "properties": {
                          "publisher":"Microsoft.OSTCExtensions",
                          "type":"LinuxDiagnostic",
                          "typeHandlerVersion":"2.1",
                          "autoUpgradeMinorVersion":false,
                          "settings": {
                            "xmlCfg":"[base64(concat(variables('wadcfgxstart'),variables('wadmetricsresourceid'),variables('wadcfgxend')))]",
                            "storageAccount":"[variables('diagnosticsStorageAccountName')]"
                          },
                          "protectedSettings": {
                            "storageAccountName":"[variables('diagnosticsStorageAccountName')]",
                            "storageAccountKey":"[listkeys(variables('accountid'), '2015-06-15').key1]",
                            "storageAccountEndPoint":"https://core.windows.net"
                          }
                        }
                      }
                    ]
                  }
                }
              }
            },

11. Agregue el recurso autoscaleSettings que define cómo el conjunto de escala se ajusta en función de uso del procesador en los equipos en el conjunto.

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
                          "metricName": "\\Processor\\PercentProcessorTime",
                          "metricNamespace": "",
                          "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]",
                          "timeGrain": "PT1M",
                          "statistic": "Average",
                          "timeWindow": "PT5M",
                          "timeAggregation": "Average",
                          "operator": "GreaterThan",
                          "threshold": 50.0
                        },
                        "scaleAction": {
                          "direction": "Increase",
                          "type": "ChangeCount",
                          "value": "1",
                          "cooldown": "PT5M"
                        }
                      }
                    ]
                  }
                ],
                "targetResourceUri": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]"
              }
            }

    En este tutorial, estos valores son importantes:

    - **metricName** - este valor es el mismo que el que se define en la variable wadperfcounter contador de rendimiento. Usar dicha variable, la extensión de diagnósticos recopila **Processor\PercentProcessorTime** contador.
    - **metricResourceUri** - este valor es el identificador de recursos del conjunto de escala de máquina virtual.
    - **timeGrain** : este valor es el nivel de detalle de las mediciones que se recopilan. En esta plantilla, se establece en un minuto.
    - **estadística** : este valor determina cómo se combinan las métricas para adaptarse a la acción de escala automático. Los valores posibles son: promedio, Min, Max. En esta plantilla, que se recopila el uso de CPU total promedio de las máquinas virtuales.
    - **ventana de tiempo** : este valor es el intervalo de tiempo en el que se recopilan los datos de la instancia. Debe estar entre 5 minutos y 12 horas.
    - **timeAggregation** : este valor determina cómo se deben combinar los datos que se recopilan con el tiempo. El valor predeterminado es el promedio. Los valores posibles son: promedio, mínimo, máximo, apellidos, Total, recuento.
    - **operador** : este valor es el operador que se usa para comparar los datos de métrica y el umbral. Los valores posibles son: es igual a NotEquals, mayor, GreaterThanOrEqual, LessThan, LessThanOrEqual.
    - **umbral** : este valor activa la acción de escala. En esta plantilla, máquinas se agregan a la escala de establecer cuando el promedio de uso de CPU entre máquinas del conjunto es más de 50%.
    - **dirección** : este valor determina la acción que se realiza cuando se obtiene el valor de umbral. Los valores posibles son Subir o Bajar. En esta plantilla, el número de equipos virtuales en el conjunto de escala aumenta si el umbral es más de 50% en la ventana de tiempo definido.
    - **tipo** : este valor es el tipo de acción que debe realizarse y debe estar establecida en ChangeCount.
    - **valor** : este valor es el número de máquinas virtuales que se agregan o se eliminan del conjunto de escala. Este valor debe ser 1 o mayor. El valor predeterminado es 1. En esta plantilla, el número de equipos en la escala de establece aumenta en 1 cuando se alcanza el umbral.
    - **enfriamiento** : este valor es la cantidad de tiempo de espera desde la última acción de escalado antes de que se produce la acción siguiente. Este valor debe estar entre un minuto y una semana.

12. Guarde el archivo de plantilla.    

## <a name="step-3-upload-the-template-to-storage"></a>Paso 3: Cargar la plantilla en almacenamiento

La plantilla se puede cargar siempre y cuando conoce el nombre y la clave principal de la cuenta de almacenamiento que creó en el paso 1.

1. En la interfaz de línea de comandos (fiesta, Terminal, símbolo), ejecute los comandos para establecer las variables de entorno necesarias para tener acceso a la cuenta de almacenamiento:

        export AZURE_STORAGE_ACCOUNT={account_name}
        export AZURE_STORAGE_ACCESS_KEY={key}

    Puede obtener la clave haciendo clic en el icono de llave al ver el recurso de la cuenta de almacenamiento en el portal de Azure. Cuando se utiliza un símbolo del sistema de Windows, escriba **establecer** en lugar de exportación.

2. Crear el contenedor para almacenar la plantilla.

        azure storage container create -p Blob templates

3. Cargue el archivo de plantilla en el nuevo contenedor.

        azure storage blob upload VMSSTemplate.json templates VMSSTemplate.json

## <a name="step-4-deploy-the-template"></a>Paso 4: Implementar la plantilla

Ahora que ha creado la plantilla, puede empezar a implementar los recursos. Use este comando para iniciar el proceso:

    azure group deployment create --template-uri https://vmsstestsa.blob.core.windows.net/templates/VMSSTemplate.json vmsstestrg1 vmsstestdp1

Al presionar introducir, se le pide que proporcione los valores de las variables que asignado. Proporcionar estos valores:

    vmName: vmsstestvm1
    vmSSName: vmsstest1
    instanceCount: 5
    adminUserName: vmadmin1
    adminPassword: VMpass1
    resourcePrefix: vmsstest

Tardará aproximadamente 15 minutos para todos los recursos para implementar correctamente.

>[AZURE.NOTE]También puede usar la capacidad del portal para implementar los recursos. Utilice este vínculo: https://portal.azure.com/#create/Microsoft.Template/uri/<link to VM Scale Set JSON template>

## <a name="step-5-monitor-resources"></a>Paso 5: Recursos de Monitor

Puede obtener información acerca de los conjuntos de escala de máquina virtual con estos métodos:

 - El portal de Azure - actualmente puede obtener una cantidad limitada de información con el portal.
 - El [Explorador de recursos de Azure](https://resources.azure.com/) - esta herramienta es la mejor para explorar el estado actual de su conjunto de escala. Siga esta ruta de acceso y verá la vista de instancia del conjunto de escala que ha creado:

        subscriptions > {your subscription} > resourceGroups > vmsstestrg1 > providers > Microsoft.Compute > virtualMachineScaleSets > vmsstest1 > virtualMachines

 - Azure CLI - Utilice este comando para obtener información:

        azure resource show -n vmsstest1 -r Microsoft.Compute/virtualMachineScaleSets -o 2015-06-15 -g vmsstestrg1

 - Conectarse a la máquina virtual de jumpbox como lo haría con cualquier otro equipo y, a continuación, puede tener acceso remoto a las máquinas virtuales de la escala establecida para supervisar los procesos individuales.

>[AZURE.NOTE]Encontrará una API de REST completa para obtener información acerca de los conjuntos de escala en [Conjuntos de escala de máquina Virtual](https://msdn.microsoft.com/library/mt589023.aspx).

## <a name="step-6-remove-the-resources"></a>Paso 6: Quitar los recursos

Dado que le cobrarán para los recursos utilizados en Azure, siempre es una buena práctica para eliminar los recursos que ya no son necesarios. No es necesario eliminar cada recurso por separado de un grupo de recursos. Puede eliminar el grupo de recursos y todos sus recursos se eliminan automáticamente.

        azure group delete vmsstestrg1

## <a name="next-steps"></a>Pasos siguientes

- Buscar ejemplos de Azure supervisar características de [Azure Monitor entre plataformas CLI rápido iniciar las muestras](../monitoring-and-diagnostics/insights-cli-samples.md)
- Obtenga información sobre las características de notificación de [usar Autoescala acciones para enviar notificaciones de alerta de correo electrónico y webhook en el Monitor de Azure](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md)
- Obtenga información sobre cómo [los registros de auditoría de uso para enviar notificaciones de alerta de correo electrónico y webhook en el Monitor de Azure](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md)
- Consulte la plantilla [Autoescala un conjunto de escala de VM ejecuta una aplicación de Ubuntu, Apache, PHP](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale) que configura una pila de luz ejercer la funcionalidad de escala automático de los conjuntos de escala de máquina Virtual.
