<properties
    pageTitle="Crear un concentrador IoT mediante una plantilla ARM y C# | Microsoft Azure"
    description="Siga este tutorial para empezar a usar el Administrador de recursos de Azure plantillas para crear un concentrador IoT con un programa de C#."
    services="iot-hub"
    documentationCenter=".net"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/16/2016"
     ms.author="dobett"/>

# <a name="create-an-iot-hub-using-a-c-program-with-an-azure-resource-manager-template"></a>Crear un concentrador IoT usando un programa de C# con una plantilla de administrador de recursos de Azure

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introducción

Puede usar el Administrador de recursos de Azure para crear y administrar Azure IoT hubs mediante programación. En este tutorial se muestra cómo usar una plantilla de administrador de recursos de Azure para crear un concentrador IoT desde un programa de C#.

> [AZURE.NOTE] Azure tiene dos diferentes modelos de implementación para crear y trabajar con recursos: [Administrador de recursos de Azure y clásica](../resource-manager-deployment-model.md).  En este artículo trata sobre utiliza el modelo de implementación de administrador de recursos de Azure.

Para completar este tutorial, necesita lo siguiente:

- Microsoft Visual Studio 2015.
- Una cuenta de Azure active. <br/>Si no tiene una cuenta, puede crear una [cuenta gratuita] de[ lnk-free-trial] de dos minutos.
- Una [cuenta de almacenamiento de Azure] [ lnk-storage-account] donde puede almacenar sus archivos de plantilla de administrador de recursos de Azure.
- [Microsoft Azure PowerShell 1.0] [ lnk-powershell-install] o posterior.

[AZURE.INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Preparar el proyecto de Visual Studio

1. En Visual Studio, cree un proyecto de Visual C# Windows con la plantilla de proyecto de **Aplicación de consola** . Nombre del proyecto **CreateIoTHub**.

2. En el Explorador de soluciones, haga clic en el proyecto y, a continuación, haga clic en **Administrar paquetes de NuGet**.

3. En el Administrador de paquetes de Nuget, active **incluir la versión preliminar**y busque **Microsoft.Azure.Management.ResourceManager**. Haga clic en **instalar**, en **Cambios de la revisión** , haga clic en **Aceptar**y haga clic en **** para aceptar las licencias.

4. En el Administrador de paquetes de Nuget, busque **Microsoft.IdentityModel.Clients.ActiveDirectory**.  Haga clic en **instalar**, en **Cambios de la revisión** , haga clic en **Aceptar**y haga clic en **** para aceptar la licencia.

5. En Program.cs, reemplace las instrucciones **using** existentes con los siguientes elementos:

    ```
    using System;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```
    
6. En Program.cs, agregue las siguientes variables estáticas reemplazando los valores de marcador de posición. Cometió una nota de **ApplicationId**, **SubscriptionId**, **TenantId**y la **contraseña** más adelante en este tutorial. **Nombre de la cuenta de su almacenamiento de Azure** es el nombre de la cuenta de almacenamiento de Azure donde se almacenan los archivos de plantilla de administrador de recursos de Azure. **Nombre del grupo de recursos** es el nombre del grupo de recursos que usa al crear el concentrador IoT, puede ser un grupo de recursos existente o uno nuevo. **Nombre de la implementación** es un nombre para la implementación, como **Deployment_01**.

    ```
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
    static string storageAddress = "https://{Your storage account name}.blob.core.windows.net";
    static string rgName = "{Resource group name}";
    static string deploymentName = "{Deployment name}";
    ```

[AZURE.INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## <a name="submit-an-azure-resource-manager-template-to-create-an-iot-hub"></a>Enviar una plantilla de administrador de recursos de Azure para crear un concentrador IoT

Usar un archivo de plantilla y un parámetro JSON para crear un concentrador IoT en el grupo de recursos. También puede usar una plantilla de administrador de recursos de Azure para realizar cambios en un concentrador IoT existente.

1. En el Explorador de soluciones, haga clic en el proyecto, haga clic en **Agregar**y, a continuación, haga clic en **Nuevo elemento**. Agregar un archivo JSON denominado **template.json** al proyecto.

2. Reemplazar el contenido de **template.json** con la siguiente definición de recursos para agregar un concentrador IoT estándar para la región **Oriental de Estados Unidos** (de la lista actual de regiones que admiten IoT concentrador ver [Estado de Azure][lnk-status]):

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

3. En el Explorador de soluciones, haga clic en el proyecto, haga clic en **Agregar**y, a continuación, haga clic en **Nuevo elemento**. Agregar un archivo JSON denominado **parameters.json** al proyecto.

4. Reemplazar el contenido de **parameters.json** con la siguiente información de parámetros que se establece un nombre para el nuevo concentrador IoT como **{sus iniciales} mynewiothub**. Observe que el nombre del concentrador IoT debe ser único global por lo que debe incluir su nombre o iniciales):

    ```
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": { "value": "mynewiothub" }
      }
    }
    ```

5. En el **Explorador de servidores**, conectarse a su suscripción de Azure y, en su cuenta de almacenamiento de Azure crear un contenedor de **plantillas**. En el panel **Propiedades** , establezca los permisos de **acceso de lectura público** para el contenedor de **plantillas** en **Blob**.

6. En el **Explorador de servidores**, haga clic en el contenedor de **plantillas** y, a continuación, haga clic en **Contenedor de blobs de Windows Vista**. Haga clic en el botón **Cargar blobs** , seleccione los dos archivos, **parameters.json** y **templates.json**y, a continuación, haga clic en **Abrir** para cargar los archivos JSON en el contenedor de **plantillas** . Las direcciones URL de los BLOB que contiene los datos JSON son:

    ```
    https://{Your storage account name}.blob.core.windows.net/templates/parameters.json
    https://{Your storage account name}.blob.core.windows.net/templates/template.json
    ```

7. Agregar el siguiente método a Program.cs:
    
    ```
    static void CreateIoTHub(ResourceManagementClient client)
    {
        
    }
    ```

5. Agregue el código siguiente al método **CreateIoTHub** para enviar los archivos de plantilla y un parámetro para el Administrador de recursos de Azure:

    ```
    var createResponse = client.Deployments.CreateOrUpdate(
        rgName,
        deploymentName,
        new Deployment()
        {
          Properties = new DeploymentProperties
          {
            Mode = DeploymentMode.Incremental,
            TemplateLink = new TemplateLink
            {
              Uri = storageAddress + "/templates/template.json"
            },
            ParametersLink = new ParametersLink
            {
              Uri = storageAddress + "/templates/parameters.json"
            }
          }
        });
    ```

6. Agregue el código siguiente al método **CreateIoTHub** que muestra el estado y las claves para el nuevo concentrador IoT:

    ```
    string state = createResponse.Properties.ProvisioningState;
    Console.WriteLine("Deployment state: {0}", state);

    if (state != "Succeeded")
    {
      Console.WriteLine("Failed to create iothub");
    }
    Console.WriteLine(createResponse.Properties.Outputs);
    ```

## <a name="complete-and-run-the-application"></a>Completar y ejecute la aplicación

Ahora puede completar la aplicación llamando al método de **CreateIoTHub** antes de generar y ejecutarla.

1. Agregue el código siguiente al final del método **Main** :

    ```
    CreateIoTHub(client);
    Console.ReadLine();
    ```
    
2. Haga clic en **Generar** y **Generar solución**. Corregir los errores.

3. Haga clic en **Depurar** y, a continuación, en **Iniciar depuración** para ejecutar la aplicación. Puede tardar varios minutos para la implementación ejecutar.

4. Puede comprobar que la aplicación agrega el nuevo concentrador IoT visitando el [portal de Azure] [ lnk-azure-portal] y ver la lista de recursos, o mediante el cmdlet de PowerShell **Get-AzureRmResource** .

> [AZURE.NOTE] Esta aplicación de ejemplo agrega un concentrador de IoT S1 estándar para el que se cargarán. Puede eliminar el concentrador IoT a través del [portal de Azure] [ lnk-azure-portal] o mediante el cmdlet de PowerShell **Quitar AzureRmResource** cuando haya terminado.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha implementado un concentrador IoT mediante una plantilla de administrador de recursos de Azure con un programa de C#, desea explorar aún más:

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
[lnk-storage-account]: ../storage/storage-create-storage-account.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
