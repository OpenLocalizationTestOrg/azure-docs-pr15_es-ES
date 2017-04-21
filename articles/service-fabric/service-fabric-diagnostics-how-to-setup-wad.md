<properties
   pageTitle="Recopilar registros mediante los diagnósticos de Azure | Microsoft Azure"
   description="En este artículo se describe cómo configurar diagnósticos de Azure para recopilar registros de un clúster de tela de servicio que se ejecuta en Azure."
   services="service-fabric"
   documentationCenter=".net"
   authors="ms-toddabel"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/28/2016"
   ms.author="toddabel"/>


# <a name="collect-logs-by-using-azure-diagnostics"></a>Recopilar registros mediante los diagnósticos de Azure

> [AZURE.SELECTOR]
- [Windows](service-fabric-diagnostics-how-to-setup-wad.md)
- [Linux](service-fabric-diagnostics-how-to-setup-lad.md)

Cuando utiliza un clúster de Azure servicio tela, es una buena idea recopilar los registros de todos los nodos en una ubicación central. Con los registros en una ubicación central le ayuda a analizar y solucionar problemas en el clúster, o en las aplicaciones y servicios que se ejecutan en ese clúster.

Una forma de cargar y recopilar registros es usar la extensión de diagnósticos de Azure, que carga los registros para el almacenamiento de Azure. Los registros no están directamente en el almacenamiento de información útiles. Pero puede usar un proceso externo para leer los eventos de almacenamiento y colóquelos en un producto como [Análisis de registro](../log-analytics/log-analytics-service-fabric.md), [Búsqueda elástico](service-fabric-diagnostic-how-to-use-elasticsearch.md)u otra solución de análisis de registro.

## <a name="prerequisites"></a>Requisitos previos
Deberá usar estas herramientas para realizar algunas de las operaciones en este documento:

* [Diagnóstico de Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) (relacionados con los servicios de nube de Azure pero tiene ejemplos e información útil)
* [Administrador de recursos de Azure](../azure-resource-manager/resource-group-overview.md)
* [Azure PowerShell](../powershell-install-configure.md)
* [Cliente de administrador de recursos de Azure](https://github.com/projectkudu/ARMClient)
* [Azure plantilla de administrador de recursos](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md)


## <a name="log-sources-that-you-might-want-to-collect"></a>Orígenes de registro que desea recopilar
- **Registros de tela de servicio**: emitido desde la plataforma a canales estándares de seguimiento de eventos de Windows (ETW) y origen de eventos. Registros pueden ser uno de los distintos tipos:
  - Los eventos operativos: los registros de las operaciones que realiza la plataforma de servicio tela. Ejemplos de creación de aplicaciones y servicios, cambios de estado de nodo y obtener información de actualización.
  - [Actores confiables eventos del modelo de programación](service-fabric-reliable-actors-diagnostics.md)
  - [Servicios confiables eventos del modelo de programación](service-fabric-reliable-services-diagnostics.md)
- **Eventos de aplicación**: eventos emitido desde el código del servicio y escribe mediante el uso de la clase auxiliar de origen de eventos proporcionada en las plantillas de Visual Studio. Para obtener más información sobre cómo escribir registros de la aplicación, vea [Monitor y diagnosticar servicios en una configuración de desarrollo de equipo local](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).


## <a name="deploy-the-diagnostics-extension"></a>Implementar la extensión de diagnóstico
Es el primer paso para recopilar registros implementar la extensión de diagnóstico en cada una de las máquinas virtuales en el clúster de servicio tela. La extensión de diagnósticos recopila registros en cada VM y ellos cargarán en la cuenta de almacenamiento que especifique. Los pasos varían ligeramente en función de si se utiliza el portal de Azure o el Administrador de recursos de Azure. Los pasos también varían en función de si la implementación forma parte de la creación de un clúster o para un clúster que ya existe. Veamos los pasos necesarios para cada escenario.

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-through-the-portal"></a>Implementar la extensión de diagnósticos como parte de la creación de un clúster a través del portal
Para implementar la extensión de diagnósticos en las máquinas virtuales en el clúster como parte de la creación de un clúster, use el panel de configuración de diagnósticos se muestra en la siguiente imagen. Para habilitar la recopilación de eventos de actores fiable o servicios confiables, asegúrese de que diagnósticos está establecida **en** (configuración predeterminada). Después de crear el clúster, no puede cambiar esta configuración a través del portal.

![Configuración de diagnósticos de Azure en el portal de la creación de un clúster](./media/service-fabric-diagnostics-how-to-setup-wad/portal-cluster-creation-diagnostics-setting.png)

La compatibilidad de *requiere* equipo de soporte técnico de Azure registros para ayudar a resolver cualquier solicitud de soporte que cree. Estos registros se recogen en tiempo real y se almacenan en una de las cuentas de almacenamiento que creó en el grupo de recursos. La configuración de diagnósticos de configura eventos de la aplicación. Estos eventos incluyen algunos eventos de tela de servicio de nivel del sistema que se almacenan en el almacenamiento de Azure, [Servicios confiables](service-fabric-reliable-services-diagnostics.md) eventos y eventos de [Actores confiable](service-fabric-reliable-actors-diagnostics.md) .

Productos como [Búsqueda elástico](service-fabric-diagnostic-how-to-use-elasticsearch.md) o su propio proceso pueden obtener los eventos de la cuenta de almacenamiento. Actualmente no hay ninguna manera filtrar o efectuar el mantenimiento de los eventos que se envían a la tabla. Si decide no implementar un proceso para quitar los eventos de la tabla, la tabla seguirá creciendo.

Cuando está creando un clúster a través del portal, es muy recomendable que descargar la plantilla *antes de hacer clic en * *Aceptar*** para crear el clúster. Para obtener información detallada, consulte [configurar un clúster de servicio tela mediante una plantilla de administrador de recursos de Azure](service-fabric-cluster-creation-via-arm.md). Necesitará la plantilla para realizar cambios más adelante, ya que no puede realizar cambios mediante el portal.

Puede exportar plantillas desde el portal con los pasos siguientes. Sin embargo, estas plantillas pueden ser más difíciles de utilizar porque podrían tener valores nulos que les falta información necesaria.

1. Abra el grupo de recursos.
2. Seleccione **configuración** para mostrar el panel de configuración.
3. Seleccione **implementaciones** para mostrar el panel de historial de implementación.
4. Seleccione una implementación de para ver los detalles de la implementación.
5. Seleccione **Exportar plantilla** para mostrar el panel de la plantilla.
6. Seleccione **Guardar en archivo** para exportar un archivo .zip que contiene la plantilla, parámetros y archivos de PowerShell.

Después de exportar los archivos, debe realizar una modificación. Editar el archivo parameters.json y quite el elemento **adminPassword** . Esto hará que solicite la contraseña cuando se ejecuta el script de implementación. Cuando se está ejecutando el script de implementación, debe corregir los valores de parámetro nulo.

Para utilizar la plantilla descargada para actualizar una configuración:

1. Extraer el contenido a una carpeta en el equipo local.
2. Modificar el contenido para reflejar la nueva configuración.
3. Iniciar PowerShell y cambie a la carpeta que haya extraído el contenido.
4. Ejecutar **deploy.ps1** y rellene el identificador de la suscripción, el nombre del grupo de recursos (use el mismo nombre para actualizar la configuración) y un nombre de implementación únicos.


### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-by-using-azure-resource-manager"></a>Implementar la extensión de diagnósticos como parte de la creación de un clúster mediante el Administrador de recursos de Azure
Para crear un clúster mediante el Administrador de recursos, debe agregar la configuración de diagnósticos JSON a la plantilla de administrador de recursos de clúster completa antes de crear el clúster. Le proporcionamos una plantilla de administrador de recursos de clúster de cinco VM de ejemplo con la configuración de diagnósticos de agregado como parte de nuestras ejemplos de plantillas de administrador de recursos. Puede ver en esta ubicación en la Galería de ejemplos de Azure: [clúster de cinco nodos con el Administrador de recursos de diagnósticos de ejemplo de plantilla](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype-wad).

Para ver la configuración de diagnósticos en la plantilla de administrador de recursos, abra el archivo azuredeploy.json y busque **IaaSDiagnostics**. Para crear un clúster con esta plantilla, seleccione el botón de **implementar a Azure** disponible en el vínculo anterior.

Como alternativa, puede descargar el Administrador de recursos de ejemplo, realizar cambios en él y crear un clúster con la plantilla modificada mediante la `New-AzureRmResourceGroupDeployment` comando en una ventana de PowerShell de Azure. Vea el código siguiente para los parámetros que se pasan al comando. Para obtener información detallada sobre cómo implementar un grupo de recursos con PowerShell, vea el artículo [implementar un grupo de recursos con la plantilla de administrador de recursos de Azure](../resource-group-template-deploy.md).

```powershell

New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -Name $deploymentName -TemplateFile $pathToARMConfigJsonFile -TemplateParameterFile $pathToParameterFile –Verbose
```

### <a name="deploy-the-diagnostics-extension-to-an-existing-cluster"></a>Implementar la extensión de diagnósticos a un clúster existente
Si tiene un clúster existente que no tiene diagnósticos implementados, o si desea modificar una configuración existente, puede agregar o actualizar. Modificar la plantilla de administrador de recursos que se utiliza para crear el clúster existente o descargar la plantilla desde el portal como se describió anteriormente. Modificar el archivo template.json al realizar las siguientes tareas.

Agregar un nuevo recurso de almacenamiento a la plantilla agregando a la sección de recursos.

```json
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[parameters('applicationDiagnosticsStorageAccountName')]",
  "location": "[parameters('computeLocation')]",
  "properties": {
    "accountType": "[parameters('applicationDiagnosticsStorageAccountType')]"
  },
  "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
  }
},
```

 A continuación, agregue a la sección de parámetros justo después de las definiciones de cuenta de almacenamiento, entre `supportLogStorageAccountName` y `vmNodeType0Name`. Reemplace el texto de marcador de posición para *Insertar aquí nombre de cuenta de almacenamiento* por el nombre de la cuenta de almacenamiento.

```json
    "applicationDiagnosticsStorageAccountType": {
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS"
      ],
      "defaultValue": "Standard_LRS",
      "metadata": {
        "description": "Replication option for the application diagnostics storage account"
      }
    },
    "applicationDiagnosticsStorageAccountName": {
      "type": "string",
      "defaultValue": "storage account name goes here",
      "metadata": {
        "description": "Name for the storage account that contains application diagnostics data from the cluster"
      }
    },
```
A continuación, actualice la `VirtualMachineProfile` sección del archivo template.json agregando el código siguiente dentro de la matriz de extensiones. Asegúrese de agregar un punto y coma al principio o al final, según dónde se inserta.

```json
{
    "name": "[concat(parameters('vmNodeType0Name'),'_Microsoft.Insights.VMDiagnosticsSettings')]",
    "properties": {
        "type": "IaaSDiagnostics",
        "autoUpgradeMinorVersion": true,
        "protectedSettings": {
        "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
        "storageAccountEndPoint": "https://core.windows.net/"
        },
        "publisher": "Microsoft.Azure.Diagnostics",
        "settings": {
        "WadCfg": {
            "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": "50000",
            "EtwProviders": {
                "EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
                {
                    "provider": "Microsoft-ServiceFabric-Services",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableServiceEventTable"
                    }
                }
                ],
                "EtwManifestProviderConfiguration": [
                {
                    "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                    "scheduledTransferLogLevelFilter": "Information",
                    "scheduledTransferKeywordFilter": "4611686018427387904",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricSystemEventTable"
                    }
                }
                ]
            }
            }
        },
        "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountName')]"
        },
        "typeHandlerVersion": "1.5"
    }
}
```

Después de modificar el archivo template.json tal como se describe, vuelva a publicar la plantilla de administrador de recursos. Si la plantilla se exportó, ejecute el archivo deploy.ps1 vuelve a publicar la plantilla. Después de implementar, asegúrese de que **ProvisioningState** es **correcta**.


## <a name="update-diagnostics-to-collect-and-upload-logs-from-new-eventsource-channels"></a>Actualización de diagnóstico para recopilar y cargar registros de canales de origen de eventos nuevos
Para actualizar los diagnósticos para recopilar registros de canales de origen de eventos nuevos que representan una nueva aplicación que se va a implementar, lleve a cabo los mismos pasos de la [sección anterior](#deploywadarm) para la configuración de diagnósticos de un clúster existente.

Actualizar la `EtwEventSourceProviderConfiguration` sección en el archivo template.json para agregar entradas para los canales de origen de eventos nuevos antes de aplicar la configuración de actualización utilizando la `New-AzureRmResourceGroupDeployment` comandos de PowerShell. El nombre del origen de evento se define como parte del código en el archivo generado por Visual Studio ServiceEventSource.cs.

Por ejemplo, si su origen de eventos se denomina mi origen de eventos, agregue el código siguiente para colocar los eventos de mi origen de eventos en una tabla denominada MyDestinationTableName.

```json
        {
            "provider": "My-Eventsource",
            "scheduledTransferPeriod": "PT5M",
            "DefaultEvents": {
            "eventDestination": "MyDestinationTableName"
            }
        }
```

Para recopilar contadores de rendimiento o registros de eventos, modifique la plantilla de administrador de recursos usando los ejemplos proporcionados en [crear una máquina virtual de Windows con la supervisión y diagnósticos mediante una plantilla de administrador de recursos de Azure](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md). A continuación, vuelva a publicar la plantilla de administrador de recursos.

## <a name="next-steps"></a>Pasos siguientes
Para conocer con más detalle qué eventos debe tener un aspecto solucionar problemas, vea los eventos de diagnósticos de [Actores confiable](service-fabric-reliable-actors-diagnostics.md) y [Servicios confiables](service-fabric-reliable-services-diagnostics.md).


## <a name="related-articles"></a>Artículos relacionados
* [Aprenda a recopilar contadores de rendimiento o registros mediante la extensión de diagnóstico](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md)
* [Solución de tela de servicio en el análisis de registro](../log-analytics/log-analytics-service-fabric.md)
