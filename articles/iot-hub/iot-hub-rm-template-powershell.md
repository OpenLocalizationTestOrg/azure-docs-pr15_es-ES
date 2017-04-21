<properties
    pageTitle="Crear un concentrador IoT usando una plantilla de administrador de recursos de Azure y PowerShell | Microsoft Azure"
    description="Siga este tutorial para empezar a usar el Administrador de recursos de Azure plantillas para crear un concentrador IoT con PowerShell."
    services="iot-hub"
    documentationCenter=".net"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="multiple"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/07/2016"
     ms.author="dobett"/>

# <a name="create-an-iot-hub-using-powershell"></a>Crear un concentrador IoT con PowerShell

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introducción

Puede usar el Administrador de recursos de Azure para crear y administrar Azure IoT hubs mediante programación. En este tutorial se muestra cómo usar una plantilla de administrador de recursos de Azure para crear un concentrador IoT con PowerShell.

> [AZURE.NOTE] Azure tiene dos diferentes modelos de implementación para crear y trabajar con recursos: [Administrador de recursos de Azure y clásica](../resource-manager-deployment-model.md).  En este artículo trata sobre utiliza el modelo de implementación de administrador de recursos de Azure.

Para completar este tutorial, se necesita lo siguiente:

- Una cuenta de Azure active. <br/>Si no tiene una cuenta, puede crear una [cuenta gratuita] de[ lnk-free-trial] de dos minutos.
- [Microsoft Azure PowerShell 1.0] [ lnk-powershell-install] o posterior.

> [AZURE.TIP] El artículo [Usando PowerShell de Azure con el Administrador de recursos de Azure] [ lnk-powershell-arm] proporciona más información sobre cómo usar las secuencias de comandos de PowerShell y plantillas de administrador de recursos de Azure para crear recursos de Azure. 

## <a name="connect-to-your-azure-subscription"></a>Conectarse a su suscripción de Azure

En el símbolo del sistema de PowerShell, escriba el comando siguiente para iniciar sesión en su suscripción de Azure:

```
Login-AzureRmAccount
```

Puede usar los comandos siguientes para descubrir dónde puede implementar un concentrador IoT y las versiones de API admitidas actualmente:

```
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Devices).ResourceTypes | Where-Object ResourceTypeName -eq IoTHubs).Locations
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Devices).ResourceTypes | Where-Object ResourceTypeName -eq IoTHubs).ApiVersions
```

Crear un grupo de recursos para que contenga el concentrador IoT mediante el comando siguiente en una de las ubicaciones de concentrador IoT compatibles. Este ejemplo crea un grupo de recursos denominado **MyIoTRG1**:

```
New-AzureRmResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="submit-an-azure-resource-manager-template-to-create-an-iot-hub"></a>Enviar una plantilla de administrador de recursos de Azure para crear un concentrador IoT

Use una plantilla JSON para crear un concentrador IoT en el grupo de recursos. También puede usar una plantilla de administrador de recursos de Azure para realizar cambios en un concentrador IoT existente.

1. Use un editor de texto para crear una plantilla de administrador de recursos de Azure denominada **template.json** con la siguiente definición de recursos para crear un nuevo concentrador IoT estándar. Este ejemplo agrega el concentrador IoT en la región **Oriental de Estados Unidos** , crea dos grupos de consumidores (**cg1** y **cg2**) en el extremo del evento concentrador compatible y utiliza la versión de **2016-02-03** API. Esta plantilla espera que pasar en el nombre del concentrador IoT como un parámetro llamado **hubName**. Para la lista actual de ubicaciones que admiten IoT concentrador ver [Estado de Azure][lnk-status].

    ```
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": {
          "type": "string"
        }
      },
      "resources": [
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs",
        "name": "[parameters('hubName')]",
        "location": "East US",
        "sku": {
          "name": "S1",
          "tier": "Standard",
          "capacity": 1
        },
        "properties": {
          "location": "East US"
        }
      },
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs/eventhubEndpoints/ConsumerGroups",
        "name": "[concat(parameters('hubName'), '/events/cg1')]",
        "dependsOn": [
          "[concat('Microsoft.Devices/Iothubs/', parameters('hubName'))]"
        ]
      },
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs/eventhubEndpoints/ConsumerGroups",
        "name": "[concat(parameters('hubName'), '/events/cg2')]",
        "dependsOn": [
          "[concat('Microsoft.Devices/Iothubs/', parameters('hubName'))]"
        ]
      }
      ],
      "outputs": {
        "hubKeys": {
          "value": "[listKeys(resourceId('Microsoft.Devices/IotHubs', parameters('hubName')), '2016-02-03')]",
          "type": "object"
        }
      }
    }
    ```

2. Guarde el archivo de plantilla de administrador de recursos de Azure en el equipo local. Este ejemplo se supone que guarde en una carpeta denominada **c:\templates**.

3. Ejecute el comando siguiente para implementar el nuevo concentrador IoT, pasando el nombre de su centro IoT como parámetro. En este ejemplo, el nombre del concentrador IoT es **abcmyiothub** (tenga en cuenta que este nombre debe ser único global, por lo que debe incluir su nombre o iniciales):

    ```
    New-AzureRmResourceGroupDeployment -ResourceGroupName MyIoTRG1 -TemplateFile C:\templates\template.json -hubName abcmyiothub
    ```

4. El resultado muestra las claves para el concentrador IoT que creó.

5. Puede comprobar que la aplicación agrega el nuevo concentrador IoT visitando el [portal de Azure] [ lnk-azure-portal] y ver la lista de recursos, o mediante el cmdlet de PowerShell **Get-AzureRmResource** .

> [AZURE.NOTE] Esta aplicación de ejemplo agrega un concentrador de IoT S1 estándar para el que se cargarán. Puede eliminar el concentrador IoT a través del [portal de Azure] [ lnk-azure-portal] o mediante el cmdlet de PowerShell **Quitar AzureRmResource** cuando haya terminado.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha implementado un concentrador IoT mediante una plantilla de administrador de recursos de Azure con PowerShell, desea explorar aún más:

- Obtenga información sobre las capacidades del [proveedor de recursos de concentrador IoT API de REST]de[lnk-rest-api].
- Lea la [información general del Administrador de recursos de Azure] [ lnk-azure-rm-overview] para obtener más información sobre las capacidades del Administrador de recursos de Azure.

Para obtener más información sobre el desarrollo de concentrador IoT, consulte lo siguiente:

- [Introducción a C SDK][lnk-c-sdk]
- [Concentrador IoT SDK][lnk-sdks]

Para explorar aún más las capacidades de concentrador IoT, consulte:

- [Simulación de un dispositivo con el SDK de puerta de enlace IoT][lnk-gateway]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: ../powershell-install-configure.md
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-azure-rm-overview]: ../azure-resource-manager/resource-group-overview.md
[lnk-powershell-arm]: ../powershell-azure-resource-manager.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
