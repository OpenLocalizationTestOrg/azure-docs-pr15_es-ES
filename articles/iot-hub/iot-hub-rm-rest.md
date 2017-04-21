<properties
    pageTitle="Crear un concentrador IoT con la API de REST | Microsoft Azure"
    description="Siga este tutorial para empezar a usar la API de REST para crear un concentrador IoT."
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

# <a name="tutorial-create-an-iot-hub-using-a-c-program-and-the-rest-api"></a>Tutorial: Crear un concentrador IoT mediante un programa de C# y la API de REST

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introducción

Puede usar el [proveedor de recursos de concentrador IoT API de REST] [ lnk-rest-api] para crear y administrar Azure IoT hubs mediante programación. En este tutorial se muestra cómo usar el proveedor de recursos de concentrador IoT API de REST para crear un concentrador IoT desde un programa de C#.

> [AZURE.NOTE] Azure tiene dos diferentes modelos de implementación para crear y trabajar con recursos: [Administrador de recursos de Azure y clásica](../resource-manager-deployment-model.md).  En este artículo trata sobre utiliza el modelo de implementación de administrador de recursos de Azure.

Para completar este tutorial, necesita lo siguiente:

- Microsoft Visual Studio 2015.
- Una cuenta de Azure active. <br/>Si no tiene una cuenta, puede crear una [cuenta gratuita] de[ lnk-free-trial] de dos minutos.
- [Microsoft Azure PowerShell 1.0] [ lnk-powershell-install] o posterior.

[AZURE.INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Preparar el proyecto de Visual Studio

1. En Visual Studio, cree un proyecto de Visual C# Windows con la plantilla de proyecto de **Aplicación de consola** . Nombre del proyecto **CreateIoTHubREST**.

2. En el Explorador de soluciones, haga clic en el proyecto y, a continuación, haga clic en **Administrar paquetes de NuGet**.

3. En el Administrador de paquetes de Nuget, active **incluir la versión preliminar**y busque **Microsoft.Azure.Management.ResourceManager**. Haga clic en **instalar**, en **Cambios de la revisión** , haga clic en **Aceptar**y haga clic en **** para aceptar las licencias.

4. En el Administrador de paquetes de Nuget, busque **Microsoft.IdentityModel.Clients.ActiveDirectory**.  Haga clic en **instalar**, en **Cambios de la revisión** , haga clic en **Aceptar**y haga clic en **** para aceptar la licencia.

6. En Program.cs, reemplace las instrucciones **using** existentes con los siguientes elementos:

    ```
    using System;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Newtonsoft.Json;
    using Microsoft.Rest;
    using System.Linq;
    using System.Threading;
    ```
    
7. En Program.cs, agregue las siguientes variables estáticas reemplazando los valores de marcador de posición. Cometió una nota de **ApplicationId**, **SubscriptionId**, **TenantId**y la **contraseña** más adelante en este tutorial. **Nombre del grupo de recursos** es el nombre del grupo de recursos que usa al crear el concentrador IoT, puede ser un grupo de recursos existente o uno nuevo. **Nombre de concentrador IoT** es el nombre del concentrador IoT crea, como **MyIoTHub** (este nombre debe ser único, por lo que debe incluir su nombre o iniciales). **Nombre de la implementación** es un nombre para la implementación, como **Deployment_01**.

    ```
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
    
    static string rgName = "{Resource group name}";
    static string iotHubName = "{IoT Hub name including your initials}";
    ```

[AZURE.INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## <a name="use-the-rest-api-to-create-an-iot-hub"></a>Usar la API de REST para crear un concentrador IoT

Usar la [API de REST de concentrador IoT] [ lnk-rest-api] para crear un concentrador IoT en el grupo de recursos. También puede usar la API de REST para realizar cambios en un concentrador IoT existente.

1. Agregar el siguiente método a Program.cs:
    
    ```
    static void CreateIoTHub(string token)
    {
        
    }
    ```

2. Agregue el código siguiente al método **CreateIoTHub** para crear un objeto **HttpClient** con el símbolo de autenticación en los encabezados:

    ```
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
    ```

3. Agregue el código siguiente al método **CreateIoTHub** para describir el concentrador IoT para crear y generar una representación JSON (de la lista actual de ubicaciones que admiten IoT concentrador ver [Estado de Azure][lnk-status]):

    ```
    var description = new
    {
      name = iotHubName,
      location = "East US",
      sku = new
      {
        name = "S1",
        tier = "Standard",
        capacity = 1
      }
    };
    
    var json = JsonConvert.SerializeObject(description, Formatting.Indented);
    ```

4. Agregue el código siguiente al método **CreateIoTHub** para enviar la solicitud resto de Azure, compruebe la respuesta y recuperar la dirección URL que puede usar para supervisar el estado de la tarea de implementación:

    ```
    var content = new StringContent(JsonConvert.SerializeObject(description), Encoding.UTF8, "application/json");
    var requestUri = string.Format("https://management.azure.com/subscriptions/{0}/resourcegroups/{1}/providers/Microsoft.devices/IotHubs/{2}?api-version=2016-02-03", subscriptionId, rgName, iotHubName);
    var result = client.PutAsync(requestUri, content).Result;
      
    if (!result.IsSuccessStatusCode)
    {
      Console.WriteLine("Failed {0}", result.Content.ReadAsStringAsync().Result);
      return;
    }
    
    var asyncStatusUri = result.Headers.GetValues("Azure-AsyncOperation").First();
    ```

5. Agregue el código siguiente al final del método **CreateIoTHub** para usar la dirección de **asyncStatusUri** recuperada en el paso anterior que para esperar para que la implementación completar:

    ```
    string body;
    do
    {
      Thread.Sleep(10000);
      HttpResponseMessage deploymentstatus = client.GetAsync(asyncStatusUri).Result;
      body = deploymentstatus.Content.ReadAsStringAsync().Result;
    } while (body == "{\"status\":\"Running\"}");
    ```

6. Agregue el código siguiente al final del método **CreateIoTHub** para recuperar las claves del concentrador IoT creado e imprimirlos en la consola:

    ```
    var listKeysUri = string.Format("https://management.azure.com/subscriptions/{0}/resourceGroups/{1}/providers/Microsoft.Devices/IotHubs/{2}/IoTHubKeys/listkeys?api-version=2015-08-15-preview", subscriptionId, rgName, iotHubName);
    var keysresults = client.PostAsync(listKeysUri, null).Result;
    
    Console.WriteLine("Keys: {0}", keysresults.Content.ReadAsStringAsync().Result);
    ```
    
## <a name="complete-and-run-the-application"></a>Completar y ejecute la aplicación

Ahora puede completar la aplicación llamando al método de **CreateIoTHub** antes de generar y ejecutarla.

1. Agregue el código siguiente al final del método **Main** :

    ```
    CreateIoTHub(token.AccessToken);
    Console.ReadLine();
    ```
    
2. Haga clic en **Generar** y **Generar solución**. Corregir los errores.

3. Haga clic en **Depurar** y, a continuación, en **Iniciar depuración** para ejecutar la aplicación. Puede tardar varios minutos para la implementación ejecutar.

4. Puede comprobar que la aplicación agrega el nuevo concentrador IoT visitando el [portal de Azure] [ lnk-azure-portal] y ver la lista de recursos, o mediante el cmdlet de PowerShell **Get-AzureRmResource** .

> [AZURE.NOTE] Esta aplicación de ejemplo agrega un concentrador de IoT S1 estándar para el que se cargarán. Cuando haya terminado, puede eliminar el concentrador IoT a través del [portal de Azure] [ lnk-azure-portal] o mediante el cmdlet de PowerShell **Quitar AzureRmResource** cuando haya terminado.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha implementado un concentrador IoT con la API de REST, desea explorar aún más:

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

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
