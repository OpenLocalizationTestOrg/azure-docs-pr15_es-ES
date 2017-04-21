<properties
    pageTitle="Implementar una máquina virtual con una plantilla de administrador de recursos y C# | Microsoft Azure"
    description="Obtenga información sobre cómo usar C# y una plantilla de administrador de recursos para implementar una máquina virtual de Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="davidmu"/>

# <a name="deploy-an-azure-virtual-machine-using-c-and-a-resource-manager-template"></a>Implementar una máquina Virtual de Azure mediante C# y una plantilla de administrador de recursos

Mediante el uso de plantillas y grupos de recursos, es capaz de administrar todos los recursos juntos que son compatibles con la aplicación. Este artículo le muestra cómo usar Visual Studio y C# para configurar la autenticación, cree una plantilla y, a continuación, implementar recursos Azure con la plantilla que ha creado.

En primer lugar, debe asegurarse de que ha hecho estos pasos de configuración:

- Instalar [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- Comprobar la instalación de [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) o [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855)
- Obtener un [símbolo de autenticación](../resource-group-authenticate-service-principal.md)
- Crear un grupo de recursos con [PowerShell de Azure](../resource-group-template-deploy.md), [Azure CLI](../resource-group-template-deploy-cli.md)o [portal de Azure](../resource-group-template-deploy-portal.md).

Tardará unos 30 minutos para realizar estos pasos.
    
## <a name="step-1-create-the-visual-studio-project-the-template-file-and-the-parameters-file"></a>Paso 1: Crear el proyecto de Visual Studio, el archivo de plantilla y el archivo de parámetros

### <a name="create-the-template-file"></a>Crear el archivo de plantilla

Una plantilla de administrador de recursos de Azure permite implementar y administrar recursos Azure juntas. La plantilla es una descripción de JSON de los parámetros de implementación asociado y recursos.

En Visual Studio, siga estos pasos:

1. Haga clic en **archivo** > **nueva** > **proyecto**.

2. En las **plantillas de** > **Visual C#**, seleccione **Aplicación de consola**, escriba el nombre y la ubicación del proyecto y, a continuación, haga clic en **Aceptar**.

3. Haga clic en el nombre del proyecto en el Explorador de soluciones, haga clic en **Agregar** > **Nuevo elemento**.

4. Haga clic en Web, seleccione archivo de JSON, escriba *VirtualMachineTemplate.json* para el nombre y, a continuación, haga clic en **Agregar**.

5. En la apertura y el paréntesis de cierre del archivo VirtualMachineTemplate.json, agregue el elemento de esquema requerido y el elemento contentVersion necesarios:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
        }

6. [Parámetros](../resource-group-authoring-templates.md#parameters) no son siempre obligatorios, pero proporciona una manera de valores de entrada cuando se implementa la plantilla. Agregar el elemento de parámetros y sus elementos secundarios después del elemento contentVersion:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUserName": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
        }

7. [Variables de](../resource-group-authoring-templates.md#variables) puede usarse en una plantilla para especificar valores que se pueden cambiar con frecuencia o que tenga que crear a partir de una combinación de valores de parámetro. Agregue el elemento de variables después de la sección de parámetros:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUsername": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
          "variables": {
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks','myvn1')]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/mysn1')]"  
          },
        }

8. [Recursos](../resource-group-authoring-templates.md#resources) como la máquina virtual, la red virtual y la cuenta de almacenamiento se definen siguiente en la plantilla. Agregue la sección de recursos después de la sección de variables:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUsername": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
          "variables": {
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks','myvn1')]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/mysn1')]"
          },
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "name": "mystorage1",
              "apiVersion": "2015-06-15",
              "location": "[resourceGroup().location]",
              "properties": { "accountType": "Standard_LRS" }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/publicIPAddresses",
              "name": "myip1",
              "location": "[resourceGroup().location]",
              "properties": {
                "publicIPAllocationMethod": "Dynamic",
                "dnsSettings": { "domainNameLabel": "mydns1" }
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/virtualNetworks",
              "name": "myvnet1",
              "location": "[resourceGroup().location]",
              "properties": {
                "addressSpace": { "addressPrefixes": [ "10.0.0.0/16" ] },
                "subnets": [ {
                  "name": "mysn1",
                  "properties": { "addressPrefix": "10.0.0.0/24" }
                } ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/networkInterfaces",
              "name": "mync1",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "Microsoft.Network/publicIPAddresses/myip1",
                "Microsoft.Network/virtualNetworks/myvn1"
              ],
              "properties": {
                "ipConfigurations": [ {
                  "name": "ipconfig1",
                  "properties": {
                    "privateIPAllocationMethod": "Dynamic",
                    "publicIPAddress": {
                      "id": "[resourceId('Microsoft.Network/publicIPAddresses', 'myip1')]"
                    },
                    "subnet": { "id": "[variables('subnetRef')]" }
                  }
                } ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Compute/virtualMachines",
              "name": "myvm1",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "Microsoft.Network/networkInterfaces/mync1",
                "Microsoft.Storage/storageAccounts/mystorage1"
              ],
              "properties": {
                "hardwareProfile": { "vmSize": "Standard_A1" },
                "osProfile": {
                  "computerName": "myvm1",
                  "adminUsername": "[parameters('adminUsername')]",
                  "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                  "imageReference": {
                    "publisher": "MicrosoftWindowsServer",
                    "offer": "WindowsServer",
                    "sku": "2012-R2-Datacenter",
                    "version" : "latest"
                  },
                  "osDisk": {
                    "name": "myosdisk1",
                    "vhd": {
                      "uri": "https://mystorage1.blob.core.windows.net/vhds/myosdisk1.vhd"
                    },
                    "caching": "ReadWrite",
                    "createOption": "FromImage"
                  }
                },
                "networkProfile": {
                  "networkInterfaces" : [ {
                    "id": "[resourceId('Microsoft.Network/networkInterfaces','mync1')]"
                  } ]
                }
              }
            } ]
          }
      
9. Guarde el archivo de plantilla que ha creado.

### <a name="create-the-parameters-file"></a>Crear el archivo de parámetros

Para especificar valores para los parámetros de recursos que se han definido en la plantilla, cree un archivo de parámetros que contiene los valores que se usan cuando se implementa la plantilla. En Visual Studio, siga estos pasos:

1. Haga clic en el nombre del proyecto en el Explorador de soluciones, haga clic en **Agregar** > **Nuevo elemento**.

2. Haga clic en Web, seleccione archivo de JSON, escriba *Parameters.json* para el nombre y, a continuación, haga clic en **Agregar**.

3. Abra el archivo parameters.json y, a continuación, agregue este contenido JSON:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUserName": { "value": "mytestacct1" },
            "adminPassword": { "value": "mytestpass1" }
          }
        }

    >[AZURE.NOTE] En este artículo, se crea una máquina virtual ejecuta una versión del sistema operativo Windows Server. Para obtener más información acerca de cómo seleccionar otras imágenes, vea [navegar y seleccionar máquina virtual Azure imágenes con Windows PowerShell y CLI Azure](virtual-machines-linux-cli-ps-findimage.md).

4. Guarde el archivo de parámetros que ha creado.

## <a name="step-2-install-the-libraries"></a>Paso 2: Instalar las bibliotecas

Paquetes de NuGet son la manera más sencilla de instalar las bibliotecas que necesita para finalizar este tutorial. Necesita la biblioteca de administración de recursos de Azure y Azure Active Directory Authentication Library para crear los recursos. Para obtener estas bibliotecas en Visual Studio, siga estos pasos:

1. Haga clic en el nombre del proyecto en el Explorador de soluciones, haga clic en **Administrar paquetes de NuGet**y, a continuación, haga clic en Examinar.

2. Tipo *Active Directory* en el cuadro de búsqueda, haga clic en **instalar** para el paquete de Active Directory Authentication Library y, a continuación, siga las instrucciones para instalar el paquete.

4. En la parte superior de la página, seleccione **Incluir preliminar**. Tipo *Microsoft.Azure.Management.ResourceManager* en el cuadro de búsqueda, haga clic en **instalar** para las bibliotecas de administración de recursos de Microsoft Azure y, a continuación, siga las instrucciones para instalar el paquete.

Ahora ya está listo para empezar a usar las bibliotecas para crear la aplicación.

## <a name="step-3-create-the-credentials-that-are-used-to-authenticate-requests"></a>Paso 3: Crear las credenciales que se usan para autenticar solicitudes

Se crea la aplicación de Azure Active Directory y se instala la biblioteca de autenticación. Ahora puede formato a la información de la aplicación en las credenciales que se utilizan para autenticar solicitudes al administrador de recursos de Azure.

1. Abra el archivo Program.cs para el proyecto que ha creado y, a continuación, agregue estas instrucciones using a la parte superior del archivo:

        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure.Management.ResourceManager;
        using Microsoft.Azure.Management.ResourceManager.Models;
        using Microsoft.Rest;
        using System.IO;

2.  Agregar este método a la clase de programa para obtener el token que se necesita para crear las credenciales:

        private static async Task<AuthenticationResult> GetAccessTokenAsync()
        {
          var cc = new ClientCredential("{client-id}", "{client-secret}");
          var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
          var token = await context.AcquireTokenAsync("https://management.azure.com/", cc);
          if (token == null)
          {
            throw new InvalidOperationException("Could not get the token.");
          }
          return token;
        }

    Reemplazar {identificador de cliente} con el identificador de Azure Active Directory aplicación, {cliente secreto} con la tecla de acceso de la aplicación de AD y {Id. de inquilinos} con el identificador de inquilino para su suscripción. Puede encontrar el identificador del inquilino ejecutando Get-AzureRmSubscription. Puede encontrar la clave de acceso a través del portal de Azure.

3. Para crear las credenciales, agregue este código al método Main en el archivo Program.cs:

        var token = GetAccessTokenAsync();
        var credential = new TokenCredentials(token.Result.AccessToken);

4. Guarde el archivo Program.cs.

## <a name="step-4-deploy-the-template"></a>Paso 4: Implementar la plantilla

En este paso, utilice el grupo de recursos que creó anteriormente, pero también puede crear un grupo de recursos con el [ResourceGroup](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.models.resourcegroup.aspx) y las clases [ResourceManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx) .

1. Agregar variables al método principal de la clase de programa para especificar los nombres de los recursos que creó anteriormente, el nombre de implementación y el identificador de la suscripción:

        var groupName = "resource group name";
        var subscriptionId = "subsciption id";
        var deploymentName = "deployment name";

    Reemplace el valor de nombre de grupo con el nombre de su grupo de recursos. Reemplace el valor de deploymentName con el nombre que desea usar para la implementación. Puede encontrar el identificador de suscripción ejecutando Get-AzureRmSubscription.

2. Agregar este método a la clase de programa para implementar los recursos en el grupo de recursos con la plantilla que haya definido:

        public static async Task<DeploymentExtended> CreateTemplateDeploymentAsync(
          TokenCredentials credential,
          string groupName,
          string deploymentName,
          string subscriptionId)
        {
          Console.WriteLine("Creating the template deployment...");
          var deployment = new Deployment();
          deployment.Properties = new DeploymentProperties
          {
            Mode = DeploymentMode.Incremental,
            Template = File.ReadAllText("..\\..\\VirtualMachineTemplate.json"),
            Parameters = File.ReadAllText("..\\..\\Parameters.json")
          };
          var resourceManagementClient = new ResourceManagementClient(credential) 
            { SubscriptionId = subscriptionId };
          return await resourceManagementClient.Deployments.CreateOrUpdateAsync(
            groupName,
            deploymentName,
            deployment);
        }

    Si desea implementar la plantilla a partir de una cuenta de almacenamiento, puede reemplazar la propiedad de plantilla con la propiedad TemplateLink.

3. Para llamar al método que acaba de agregar, agregue este código al método Main:

        var dpResult = CreateTemplateDeploymentAsync(
          credential,
          groupName,
          deploymentName,
          subscriptionId);
        Console.WriteLine(dpResult.Result.Properties.ProvisioningState);
        Console.ReadLine();

## <a name="step-5-delete-the-resources"></a>Paso 5: Eliminar los recursos

Dado que le cobrarán para los recursos utilizados en Azure, siempre es una buena práctica para eliminar los recursos que ya no son necesarios. No es necesario eliminar cada recurso por separado de un grupo de recursos. Eliminar el grupo de recursos y todos sus recursos se eliminan automáticamente.

1.  Para eliminar el grupo de recursos, agregue este método a la clase Program:

        public static async void DeleteResourceGroupAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId)
        {
          Console.WriteLine("Deleting resource group...");
          var resourceManagementClient = new ResourceManagementClient(credential)
            { SubscriptionId = subscriptionId };
          await resourceManagementClient.ResourceGroups.DeleteAsync(groupName);
        }

2.  Para llamar al método que acaba de agregar, agregue este código al método Main:

        DeleteResourceGroupAsync(
          credential,
          groupName,
          subscriptionId);
        Console.ReadLine();

##<a name="step-6-run-the-console-application"></a>Paso 6: Ejecute la aplicación de consola

1.  Para ejecutar la aplicación de consola, haga clic en **Iniciar** en Visual Studio y, a continuación, inicie sesión en Azure AD con las mismas credenciales que usa con su suscripción.

2.  Una vez que aparezca el estado aceptado, presione **ENTRAR** .

    Tardará unos cinco minutos para esta aplicación de consola ejecutar completamente de principio a fin. Antes de presionar ENTRAR para iniciar la eliminación de recursos, podría tardar unos minutos para comprobar la creación de los recursos en el portal de Azure antes de eliminarlos.

3. Para ver el estado de los recursos, vaya a los registros de auditoría en el portal de Azure:

    ![Examinar los registros de auditoría en el portal de Azure](./media/virtual-machines-windows-csharp-template/crpportal.png)

## <a name="next-steps"></a>Pasos siguientes

- Si hay problemas con la implementación, sería un paso siguiente buscar en [implementaciones de grupo de recursos de solución de problemas con el portal de Azure](../resource-manager-troubleshoot-deployments-portal.md).
- Obtenga información sobre cómo administrar la máquina virtual que ha creado revisando [máquinas virtuales de administrar con el Administrador de recursos de Azure y PowerShell](virtual-machines-windows-csharp-manage.md).
