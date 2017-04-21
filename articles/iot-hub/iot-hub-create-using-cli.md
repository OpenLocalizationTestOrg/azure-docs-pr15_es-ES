<properties
    pageTitle="Crear un concentrador IoT mediante CLI de Azure | Microsoft Azure"
    description="Siga este artículo para crear un concentrador IoT mediante la interfaz de línea de comandos de Azure."
    services="iot-hub"
    documentationCenter=".net"
    authors="BeatriceOltean"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="multiple"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/21/2016"
     ms.author="boltean"/>

# <a name="create-an-iot-hub-using-azure-cli"></a>Crear un concentrador IoT mediante CLI de Azure

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introducción

Puede usar la interfaz de línea de comandos de Azure para crear y administrar Azure IoT hubs mediante programación. En este artículo se muestra cómo usar la CLI Azure para crear un concentrador IoT.

Para completar este tutorial, se necesita lo siguiente:

- Una cuenta de Azure active. Si no tiene una cuenta, puede crear una [cuenta gratuita] de[ lnk-free-trial] de dos minutos.
- [Azure CLI 0.10.4] [ lnk-CLI-install] o posterior. Si ya dispone de Azure CLI puede validar la versión actual en el símbolo del sistema con el siguiente comando:
```
    azure --version
```

> [AZURE.NOTE] Azure tiene dos diferentes modelos de implementación para crear y trabajar con recursos: [Administrador de recursos de Azure y clásica](../resource-manager-deployment-model.md). CLI Azure debe estar en modo de administrador de recursos de Azure:
```
    azure config mode arm
```

## <a name="set-your-azure-account-and-subscription"></a>Configurar su cuenta de Azure y suscripción 

1. En el inicio de sesión de símbolo del sistema, escriba el comando siguiente
```
    azure login
```
Utilizar el explorador web sugeridos y el código para autenticar.

2. Si tiene varias suscripciones de Azure, la conexión a Azure concederá acceso a todas las suscripciones de Azure asociada a sus credenciales. Puede ver las suscripciones de Azure, así como cuál es el valor predeterminado, con el comando
```
    azure account list 
```

Para establecer el contexto de la suscripción en la que desea ejecutar el resto de la utilización de comandos

```
    azure account set <subscription name>
```

3. Si no tiene un grupo de recursos puede crear uno con nombre **exampleResourceGroup** 
```
    azure group create -n exampleResourceGroup -l westus
```

> [AZURE.TIP] El artículo [usar CLI Azure para administrar grupos de recursos y recursos de Azure] [ lnk-CLI-arm] proporciona más información sobre cómo usar Azure CLI para administrar los recursos de Azure. 


## <a name="create-an-iot-hub"></a>Crear un concentrador IoT

Parámetros requeridos:

```
 azure iothub create -g <resource-group> -n <name> -l <location> -s <sku-name> -u <units>  
    - <resourceGroup> The resource group name (case insensitive alphanumeric, underscore and hyphen, 1-64 length)
    - <name> (The name of the IoT hub to be created. The format is case insensitive alphanumeric, underscore and hyphen, 3-50 length )
    - <location> (The location (azure region/datacenter) where the IoT hub will be provisioned.
    - <sku-name> (The name of the sku, one of: [F1, S1, S2, S3] etc. For the latest full list refer to the pricing page for IoT Hub.
    - <units> (The number of provisioned units. Range : F1 [1-1] : S1, S2 [1-200] : S3 [1-10]. IoT Hub units are based on your total message count and the number of devices you want to connect.)
```
Para ver todos los parámetros disponibles para la creación puede usar el comando de ayuda en el símbolo del sistema
```
    azure iothub create -h 
```
Ejemplo rápido:

 Para crear un concentrador IoT denominado **exampleIoTHubName** en el grupo de recursos **exampleResourceGroup** simplemente ejecute el siguiente comando
```
    azure iothub create -g exampleResourceGroup -n exampleIoTHubName -l westus -k s1 -u 1
```

> [AZURE.NOTE] Este comando de Azure CLI crea un concentrador de IoT S1 estándar para el que se cargarán. Puede eliminar el concentrador IoT **exampleIoTHubName** con el siguiente comando 
```
    azure iothub delete -g exampleResourceGroup -n exampleIoTHubName
```


## <a name="next-steps"></a>Pasos siguientes
Para obtener más información sobre el desarrollo de concentrador IoT, consulte lo siguiente:
- [Concentrador IoT SDK][lnk-sdks]

Para explorar aún más las capacidades de concentrador IoT, consulte:

- [Con el Portal de Azure para administrar IoT concentrador][lnk-portal]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-CLI-install]: ../xplat-cli-install.md
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-CLI-arm]: ../xplat-cli-azure-resource-manager.md

[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-portal]: iot-hub-create-through-portal.md 
