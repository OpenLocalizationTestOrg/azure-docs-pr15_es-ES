<properties
    pageTitle="Crear un espacio de nombres de evento Hubs con concentrador de evento y habilitar el archivo mediante el uso de una plantilla de administrador de recursos de Azure | Microsoft Azure"
    description="Crear un espacio de nombres de evento Hubs con concentrador de evento y habilitar el archivo con el Administrador de recursos de Azure plantilla"
    services="event-hubs"
    documentationCenter=".net"
    authors="ShubhaVijayasarathy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.devlang="tbd"
    ms.topic="article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="09/14/2016"
    ms.author="ShubhaVijayasarathy"/>

# <a name="create-an-event-hubs-namespace-with-event-hub-and-enable-archive-using-an-azure-resource-manager-template"></a>Crear un espacio de nombres de evento Hubs con concentrador de evento y habilitar el archivo mediante el uso de una plantilla de administrador de recursos de Azure

Este artículo le muestra cómo usar una plantilla de administrador de recursos de Azure que crea un espacio de nombres de evento Hubs con un concentrador de evento y permite archivar en su centro de evento. Obtenga información sobre cómo definir los recursos que se implementan y cómo definir los parámetros que especifica cuando se ejecuta la implementación. Puede usar esta plantilla para sus propias implementaciones o personalizarlo para satisfacer sus necesidades

Para obtener más información sobre la creación de plantillas, vea [plantillas de administrador de recursos de Azure de creación][].

Para obtener más información sobre prácticas y las tramas de recursos de Azure convenciones de nomenclatura, vea [Convenciones de nomenclatura de recursos de Azure][].

Para la plantilla completa, consulte el [concentrador de eventos y habilitar la plantilla de archivo][] en GitHub.

>[AZURE.NOTE]
>Para comprobar si las últimas plantillas, visite la Galería de [Plantillas de Azure tutorial rápido][] y buscar Hubs de evento.

## <a name="what-you-deploy"></a>¿Implementar?

Con esta plantilla, implementar un espacio de nombres de evento Hubs con un concentrador de evento y habilitar el archivo.

[Evento Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) es un evento servicio utilizado para proporcionar entrada de evento y telemetría a Azure a escala masiva, con baja latencia y alta confiabilidad de procesamiento. Evento Hubs archivar le permitirá ofrecer automáticamente los datos de secuencias en su Hubs evento al almacenamiento de blobs de Windows Azure de su elección dentro de un tiempo determinado o un intervalo de tamaño de su elección.

Para ejecutar la implementación automáticamente, haga clic en el botón siguiente:

[![Implementar en Azure](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-eventhubs-create-namespace-and-enable-archive%2Fazuredeploy.json)

## <a name="parameters"></a>Parámetros

Con el Administrador de recursos de Azure definir parámetros para los valores que desea especificar cuando se implementa la plantilla. La plantilla incluye una sección denominada `Parameters` que contiene todos los valores de parámetro. Debe definir un parámetro para los valores que varían en función del proyecto que está implementando o basado en el entorno de que implementación. Debe definir parámetros para los valores que siempre se mantienen. Cada valor de parámetro se utiliza en la plantilla para definir los recursos que se implementan.

La plantilla define los siguientes parámetros.

### <a name="eventhubnamespacename"></a>eventHubNamespaceName

El nombre del espacio de nombres Hubs de evento para crear.

```
"eventHubNamespaceName":{  
     "type":"string",
     "metadata":{  
         "description":"Name of the EventHub namespace"
      }
}
```

### <a name="eventhubname"></a>eventHubName

El nombre del evento concentrador en el evento creado Hubs espacio de nombres.

```
"eventHubName":{  
    "type":"string",
    "metadata":{  
        "description":"Name of the Event Hub"
    }
}
```

### <a name="messageretentionindays"></a>messageRetentionInDays

El número de días que desea que los mensajes que se conservan en el Hub de evento. 

```
"messageRetentionInDays":{
    "type":"int",
    "defaultValue": 1,
    "minValue":"1",
    "maxValue":"7",
    "metadata":{
       "description":"How long to retain the data in Event Hub"
     }
 }
```

### <a name="partitioncount"></a>partitionCount

El número de particiones que desee en el Hub de evento.

```
"partitionCount":{
    "type":"int",
    "defaultValue":2,
    "minValue":2,
    "maxValue":32,
    "metadata":{
        "description":"Number of partitions chosen"
    }
 }
```

### <a name="archiveenabled"></a>archiveEnabled

Habilitar el archivo en su centro de evento.

```
"archiveEnabled":{
    "type":"string",
    "defaultValue":"true",
    "allowedValues": [
    "false",
    "true"],
    "metadata":{
        "description":"Enable or disable the Archive for your Event Hub"
    }
 }
```
### <a name="archiveencodingformat"></a>archiveEncodingFormat

El formato de codificación que especifique para serializar los datos del evento.

```
"archiveEncodingFormat":{
    "type":"string",
    "defaultValue":"Avro",
    "allowedValues":[
    "Avro"],
    "metadata":{
        "description":"The encoding format Archive serializes the EventData"
    }
}
```

### <a name="archivetime"></a>archiveTime

El intervalo de tiempo en que inicia al archivo archivar los datos en el almacenamiento de blobs de Windows Azure.

```
"archiveTime":{
    "type":"int",
    "defaultValue":300,
    "minValue":60,
    "maxValue":900,
    "metadata":{
         "description":"the time window in seconds for the archival"
    }
}
```

### <a name="archivesize"></a>archiveSize

El intervalo de tamaño en que inicia al archivo archivar los datos en el almacenamiento de blobs de Windows Azure.

```
"archiveSize":{
    "type":"int",
    "defaultValue":314572800,
    "minValue":10485760,
    "maxValue":524288000,
    "metadata":{
        "description":"the size window in bytes for archival"
    }
}
```

### <a name="destinationstorageaccountresourceid"></a>destinationStorageAccountResourceId

El archivo requiere un id de cuenta de almacenamiento, para habilitar el archivo para el almacenamiento de Azure deseada.

```
 "destinationStorageAccountResourceId":{
    "type":"string",
    "metadata":{
        "description":"Your existing storage Account resource id where you want the blobs be archived"
    }
 }
```

### <a name="blobcontainername"></a>blobContainerName

El contenedor de blobs de Windows donde desea que los datos de evento archivarse.

```
 "blobContainerName":{
    "type":"string",
    "metadata":{
        "description":"Your existing storage Container that you want the blobs archived in"
    }
}
```


### <a name="apiversion"></a>apiVersion

La versión de la API de la plantilla.

```
 "apiVersion":{  
    "type":"string",
    "defaultValue":"2015-08-01",
    "metadata":{  
        "description":"ApiVersion used by the template"
    }
 }
```

## <a name="resources-to-deploy"></a>Recursos para implementar

Crea un espacio de nombres del tipo **EventHubs**, con un concentrador de evento y permite archivar.

```
"resources":[  
      {  
         "apiVersion":"[variables('ehVersion')]",
         "name":"[parameters('eventHubNamespaceName')]",
         "type":"Microsoft.EventHub/Namespaces",
         "location":"[variables('location')]",
         "sku":{  
            "name":"Standard",
            "tier":"Standard"
         },
         "resources":[  
            {  
               "apiVersion":"[variables('ehVersion')]",
               "name":"[parameters('eventHubName')]",
               "type":"EventHubs",
               "dependsOn":[  
                  "[concat('Microsoft.EventHub/namespaces/', parameters('eventHubNamespaceName'))]"
               ],
               "properties":{  
                  "path":"[parameters('eventHubName')]",
                  "MessageRetentionInDays":"[parameters('messageRetentionInDays')]",
                  "PartitionCount":"[parameters('partitionCount')]",
                  "ArchiveDescription":{
                        "enabled":"[parameters('archiveEnabled')]",
                        "encoding":"[parameters('archiveEncodingFormat')]",
                        "intervalInSeconds":"[parameters('archiveTime')]",
                        "sizeLimitInBytes":"[parameters('archiveSize')]",
                        "destination":{
                            "name":"EventHubArchive.AzureBlockBlob",
                            "properties":{
                                "StorageAccountResourceId":"[parameters('destinationStorageAccountResourceId')]",
                                "BlobContainer":"[parameters('blobContainerName')]"
                            }
                        } 
                  }
                  
               }
               
            }
         ]
      }
   ]
```

## <a name="commands-to-run-deployment"></a>Comandos para ejecutar la implementación

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-archive/azuredeploy.json
```

## <a name="azure-cli"></a>CLI de Azure

```
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-archive/azuredeploy.json][]
```

[Creación de plantillas de administrador de recursos de Azure]: ../resource-group-authoring-templates.md
[Tutorial rápido Azure plantillas]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Event Hub and consumer group template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-eventhubs-create-namespace-and-enable-archive/
[Convenciones de nomenclatura de recursos de Azure]: https://azure.microsoft.com/en-us/documentation/articles/guidance-naming-conventions/
[Evento concentrador y habilitar la plantilla de archivo]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-archive
