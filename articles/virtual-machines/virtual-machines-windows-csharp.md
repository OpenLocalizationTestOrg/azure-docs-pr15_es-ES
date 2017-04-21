<properties
    pageTitle="Implementar recursos Azure mediante C# | Microsoft Azure"
    description="Aprenda a usar C# y el Administrador de recursos de Azure para crear recursos de Microsoft Azure."
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
    ms.date="10/06/2016"
    ms.author="davidmu"/>

# <a name="deploy-azure-resources-using-c"></a>Implementar recursos de Azure mediante C# 

En este artículo se muestra cómo crear recursos Azure mediante C#.

En primer lugar, debe asegurarse de que haya terminado de estas tareas:

- Instalar [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- Comprobar la instalación de [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) o [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855)
- Obtener un [símbolo de autenticación](../resource-group-authenticate-service-principal.md)

Tardará unos 30 minutos para realizar estos pasos.

## <a name="step-1-create-a-visual-studio-project-and-install-the-libraries"></a>Paso 1: Crear un proyecto de Visual Studio e instalar las bibliotecas

Paquetes de NuGet son la manera más sencilla de instalar las bibliotecas que necesita para finalizar este tutorial. Para obtener las bibliotecas que necesita en Visual Studio, siga estos pasos:

1. Haga clic en **archivo** > **nueva** > **proyecto**.

2. En las **plantillas de** > **Visual C#**, seleccione **Aplicación de consola**, escriba el nombre y la ubicación del proyecto y, a continuación, haga clic en **Aceptar**.

3. Haga clic en el nombre del proyecto en el Explorador de soluciones y, a continuación, haga clic en **Administrar paquetes de NuGet**.

4. Tipo *Active Directory* en el cuadro de búsqueda, haga clic en **instalar** para el paquete de Active Directory Authentication Library y, a continuación, siga las instrucciones para instalar el paquete.

5. En la parte superior de la página, seleccione **Incluir preliminar**. Tipo *Microsoft.Azure.Management.Compute* en el cuadro de búsqueda, haga clic en **instalar** para las bibliotecas de .NET calcular y, a continuación, siga las instrucciones para instalar el paquete.

6. Tipo *Microsoft.Azure.Management.Network* en el cuadro de búsqueda, haga clic en **instalar** para las bibliotecas de .NET de red y, a continuación, siga las instrucciones para instalar el paquete.

7. Tipo *Microsoft.Azure.Management.Storage* en el cuadro de búsqueda, haga clic en **instalar** para las bibliotecas de .NET de almacenamiento y, a continuación, siga las instrucciones para instalar el paquete.

8. Escriba *Microsoft.Azure.Management.ResourceManager* en el cuadro de búsqueda, haga clic en **instalar** para las bibliotecas de administración de recursos.

Ahora ya está listo para empezar a usar las bibliotecas para crear la aplicación.

## <a name="step-2-create-the-credentials-that-are-used-to-authenticate-requests"></a>Paso 2: Crear las credenciales que se usan para autenticar solicitudes

Ahora aplicar formato a la información de la aplicación que ha creado previamente en las credenciales que se utilizan para autenticar solicitudes para el Administrador de recursos de Azure.

1. Abra el archivo Program.cs para el proyecto que ha creado y, a continuación, agregue estas instrucciones using a la parte superior del archivo:

        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure.Management.ResourceManager;
        using Microsoft.Azure.Management.ResourceManager.Models;
        using Microsoft.Azure.Management.Storage;
        using Microsoft.Azure.Management.Storage.Models;
        using Microsoft.Azure.Management.Network;
        using Microsoft.Azure.Management.Network.Models;
        using Microsoft.Azure.Management.Compute;
        using Microsoft.Azure.Management.Compute.Models;
        using Microsoft.Rest;

2. Para crear el token que es necesario, agregue este método a la clase Program:

        private static async Task<AuthenticationResult> GetAccessTokenAsync()
        {
          var cc = new ClientCredential("{client-id}", "{client-secret}");
          var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
          var token = await context.AcquireTokenAsync("https://management.azure.com/", cc);
          if (token == null)
          {
            throw new InvalidOperationException("Could not get the token");
          }
          return token;
        }

    Reemplazar {identificador de cliente} con el identificador de Azure Active Directory aplicación, {cliente secreto} con la tecla de acceso de la aplicación de AD y {Id. de inquilinos} con el identificador de inquilino para su suscripción. Puede encontrar el identificador del inquilino ejecutando Get-AzureRmSubscription. Puede encontrar la clave de acceso a través del portal de Azure.

3. Para llamar al método que haya agregado anteriormente, agregue este código al método Main en el archivo Program.cs:

        var token = GetAccessTokenAsync();
        var credential = new TokenCredentials(token.Result.AccessToken);

4. Guarde el archivo Program.cs.

## <a name="step-3-register-the-resource-providers-and-create-the-resources"></a>Paso 3: Registrar los proveedores de recursos y crear los recursos

### <a name="register-the-providers-and-create-a-resource-group"></a>Registrar los proveedores y crear un grupo de recursos

Todos los recursos deben incluirse en un grupo de recursos. Antes de agregar recursos a un grupo, la suscripción debe estar registrada con los proveedores de recursos.

1. Agregar variables al método principal de la clase de programa para especificar los nombres que desea usar para los recursos:

        var groupName = "resource group name";
        var subscriptionId = "subsciption id";
        var location = "location name";
        var storageName = "storage account name";
        var ipName = "public ip name";
        var subnetName = "subnet name";
        var vnetName = "virtual network name";
        var nicName = "network interface name";
        var avSetName = "availability set name";
        var vmName = "virtual machine name";  
        var adminName = "administrator account name";
        var adminPassword = "administrator account password";
        
    Reemplace todos los valores de variables con los nombres y el identificador que desea usar. Puede encontrar el identificador de suscripción ejecutando Get-AzureRmSubscription.

2. Para crear el grupo de recursos y registrar los proveedores, agregue este método a la clase Program:

        public static async Task<ResourceGroup> CreateResourceGroupAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location)
        {
          var resourceManagementClient = new ResourceManagementClient(credential)
            { SubscriptionId = subscriptionId };
            
          Console.WriteLine("Registering the providers...");
          var rpResult = resourceManagementClient.Providers.Register("Microsoft.Storage");
          Console.WriteLine(rpResult.RegistrationState);
          rpResult = resourceManagementClient.Providers.Register("Microsoft.Network");
          Console.WriteLine(rpResult.RegistrationState);
          rpResult = resourceManagementClient.Providers.Register("Microsoft.Compute");
          Console.WriteLine(rpResult.RegistrationState);
          
          Console.WriteLine("Creating the resource group...");
          var resourceGroup = new ResourceGroup { Location = location };
          return await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(groupName, resourceGroup);
        }

3. Para llamar al método que haya agregado anteriormente, agregue este código al método Main:

        var rgResult = CreateResourceGroupAsync(
          credential,
          groupName,
          subscriptionId,
          location);
        Console.WriteLine(rgResult.Result.Properties.ProvisioningState);
        Console.ReadLine();

### <a name="create-a-storage-account"></a>Crear una cuenta de almacenamiento

Se necesita una [cuenta de almacenamiento](../storage/storage-create-storage-account.md) para almacenar el archivo de disco duro virtual que se crea la máquina virtual.

1. Para crear la cuenta de almacenamiento, agregue este método a la clase Program:

        public static async Task<StorageAccount> CreateStorageAccountAsync(
          TokenCredentials credential,       
          string groupName,
          string subscriptionId,
          string location,
          string storageName)
        {
          Console.WriteLine("Creating the storage account...");
          var storageManagementClient = new StorageManagementClient(credential)
            { SubscriptionId = subscriptionId };
          return await storageManagementClient.StorageAccounts.CreateAsync(
            groupName,
            storageName,
            new StorageAccountCreateParameters()
            {
              Sku = new Microsoft.Azure.Management.Storage.Models.Sku() 
                { Name = SkuName.StandardLRS},
              Kind = Kind.Storage,
              Location = location
            }
          );
        }

2. Para llamar al método que haya agregado anteriormente, agregue este código al método principal de la clase Program:

        var stResult = CreateStorageAccountAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          storageName);
        Console.WriteLine(stResult.Result.ProvisioningState);  
        Console.ReadLine();

### <a name="create-the-public-ip-address"></a>Crear la dirección IP pública

Se necesita una dirección IP pública para comunicarse con el equipo virtual.

1. Para crear la dirección IP pública de la máquina virtual, agregue este método a la clase Program:

        public static async Task<PublicIPAddress> CreatePublicIPAddressAsync(
          TokenCredentials credential,  
          string groupName,
          string subscriptionId,
          string location,
          string ipName)
        {
          Console.WriteLine("Creating the public ip...");
          var networkManagementClient = new NetworkManagementClient(credential)
            { SubscriptionId = subscriptionId };
          return await networkManagementClient.PublicIPAddresses.CreateOrUpdateAsync(
            groupName,
            ipName,
            new PublicIPAddress
            {
              Location = location,
              PublicIPAllocationMethod = "Dynamic"
            }
          );
        }

2. Para llamar al método que haya agregado anteriormente, agregue este código al método principal de la clase Program:

        var ipResult = CreatePublicIPAddressAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          ipName);
        Console.WriteLine(ipResult.Result.ProvisioningState);  
        Console.ReadLine();

### <a name="create-the-virtual-network"></a>Crear una red virtual

Debe ser una máquina virtual que se crea con el modelo de implementación de administrador de recursos en una red virtual.

1. Para crear una subred y una red virtual, agregue este método a la clase Program:

        public static async Task<VirtualNetwork> CreateVirtualNetworkAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location,
          string vnetName,
          string subnetName)
        {
          Console.WriteLine("Creating the virtual network...");
          var networkManagementClient = new NetworkManagementClient(credential)
            { SubscriptionId = subscriptionId };
          
          var subnet = new Subnet
          {
            Name = subnetName,
            AddressPrefix = "10.0.0.0/24"
          };
          
          var address = new AddressSpace {
            AddressPrefixes = new List<string> { "10.0.0.0/16" }
          };
          
          return await networkManagementClient.VirtualNetworks.CreateOrUpdateAsync(
            groupName,
            vnetName,
            new VirtualNetwork
            {
              Location = location,
              AddressSpace = address,
              Subnets = new List<Subnet> { subnet }
            }
          );
        }
        
2. Para llamar al método que haya agregado anteriormente, agregue este código al método principal de la clase Program:

        var vnResult = CreateVirtualNetworkAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          vnetName,
          subnetName);
        Console.WriteLine(vnResult.Result.ProvisioningState);  
        Console.ReadLine();
        
### <a name="create-the-network-interface"></a>Crear la interfaz de red

Una máquina virtual necesita una interfaz de red para comunicarse en la red virtual.

1. Para crear una interfaz de red, agregue este método a la clase Program:

        public static async Task<NetworkInterface> CreateNetworkInterfaceAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location,
          string subnetName,
          string vnetName,
          string ipName,
          string nicName)
        {
          Console.WriteLine("Creating the network interface...");
          var networkManagementClient = new NetworkManagementClient(credential)
            { SubscriptionId = subscriptionId };
          var subnetResponse = await networkManagementClient.Subnets.GetAsync(
            groupName,
            vnetName,
            subnetName
          );
          var pubipResponse = await networkManagementClient.PublicIPAddresses.GetAsync(groupName, ipName);

          return await networkManagementClient.NetworkInterfaces.CreateOrUpdateAsync(
            groupName,
            nicName,
            new NetworkInterface
            {
              Location = location,
              IpConfigurations = new List<NetworkInterfaceIPConfiguration>
              {
                new NetworkInterfaceIPConfiguration
                {
                  Name = nicName,
                  PublicIPAddress = pubipResponse,
                  Subnet = subnetResponse
                }
              }
            }
          );
        }

2. Para llamar al método que haya agregado anteriormente, agregue este código al método principal de la clase Program:

        var ncResult = CreateNetworkInterfaceAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          subnetName,
          vnetName,
          ipName,
          nicName);
        Console.WriteLine(ncResult.Result.ProvisioningState);  
        Console.ReadLine();

### <a name="create-an-availability-set"></a>Crear un conjunto de disponibilidad

Conjuntos de disponibilidad que sea más fácil de administrar el mantenimiento de los equipos virtuales utilizados por la aplicación.

1. Para crear el conjunto de disponibilidad, agregue este método a la clase Program:

        public static async Task<AvailabilitySet> CreateAvailabilitySetAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location,
          string avsetName)
        {
          Console.WriteLine("Creating the availability set...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          return await computeManagementClient.AvailabilitySets.CreateOrUpdateAsync(
            groupName,
            avsetName,
            new AvailabilitySet()
            {
              Location = location
            }
          );
        }

2. Para llamar al método que haya agregado anteriormente, agregue este código al método principal de la clase Program:

        var avResult = CreateAvailabilitySetAsync(
          credential,  
          groupName,
          subscriptionId,
          location,
          avSetName);
        Console.ReadLine();

### <a name="create-a-virtual-machine"></a>Crear una máquina virtual

Ahora que ha creado todos los recursos de soporte, puede crear una máquina virtual.

1. Para crear la máquina virtual, agregue este método a la clase Program:

        public static async Task<VirtualMachine> CreateVirtualMachineAsync(
          TokenCredentials credential, 
          string groupName,
          string subscriptionId,
          string location,
          string nicName,
          string avsetName,
          string storageName,
          string adminName,
          string adminPassword,
          string vmName)
        {
          var networkManagementClient = new NetworkManagementClient(credential)
            { SubscriptionId = subscriptionId };
          var nic = networkManagementClient.NetworkInterfaces.Get(groupName, nicName);

          var computeManagementClient = new ComputeManagementClient(credential);
          computeManagementClient.SubscriptionId = subscriptionId;
          var avSet = computeManagementClient.AvailabilitySets.Get(groupName, avsetName);

          Console.WriteLine("Creating the virtual machine...");
          return await computeManagementClient.VirtualMachines.CreateOrUpdateAsync(
            groupName,
            vmName,
            new VirtualMachine
            {
              Location = location,
              AvailabilitySet = new Microsoft.Azure.Management.Compute.Models.SubResource
              {
                Id = avSet.Id
              },
              HardwareProfile = new HardwareProfile
              {
                VmSize = "Standard_A0"
              },
              OsProfile = new OSProfile
              {
                AdminUsername = adminName,
                AdminPassword = adminPassword,
                ComputerName = vmName,
                WindowsConfiguration = new WindowsConfiguration
                {
                  ProvisionVMAgent = true
                }
              },
              NetworkProfile = new NetworkProfile
              {
                NetworkInterfaces = new List<NetworkInterfaceReference>
                {
                  new NetworkInterfaceReference { Id = nic.Id }
                }
              },
              StorageProfile = new StorageProfile
              {
                ImageReference = new ImageReference
                {
                  Publisher = "MicrosoftWindowsServer",
                  Offer = "WindowsServer",
                  Sku = "2012-R2-Datacenter",
                  Version = "latest"
                },
                OsDisk = new OSDisk
                {
                  Name = "mytestod1",
                  CreateOption = DiskCreateOptionTypes.FromImage,
                  Vhd = new VirtualHardDisk
                  {
                    Uri = "http://" + storageName + ".blob.core.windows.net/vhds/mytestod1.vhd"
                  }
                }
              }
            }
          );
        }

    >[AZURE.NOTE] En este tutorial, se crea una máquina virtual ejecuta una versión del sistema operativo Windows Server. Para obtener más información acerca de cómo seleccionar otras imágenes, vea [navegar y seleccionar máquina virtual Azure imágenes con Windows PowerShell y CLI Azure](virtual-machines-linux-cli-ps-findimage.md).

2. Para llamar al método que haya agregado anteriormente, agregue este código al método Main:

        var vmResult = CreateVirtualMachineAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          nicName,
          avSetName,
          storageName,
          adminName,
          adminPassword,
          vmName);
        Console.WriteLine(vmResult.Result.ProvisioningState);
        Console.ReadLine();

##<a name="step-4-delete-the-resources"></a>Paso 4: Eliminar los recursos

Dado que le cobrarán para los recursos utilizados en Azure, siempre es una buena práctica para eliminar los recursos que ya no son necesarios. Si desea eliminar las máquinas virtuales y los recursos de soporte, todo lo que debe hacer es eliminar el grupo de recursos.

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

2.  Para llamar al método que haya agregado anteriormente, agregue este código al método Main:

        DeleteResourceGroupAsync(
          credential,
          groupName,
          subscriptionId);
        Console.ReadLine();

## <a name="step-5-run-the-console-application"></a>Paso 5: Ejecute la aplicación de consola

1. Para ejecutar la aplicación de consola, haga clic en **Iniciar** en Visual Studio y, a continuación, inicie sesión en Azure AD con el mismo nombre de usuario y contraseña que utiliza con su suscripción.

2. Presione **ENTRAR** después de cada código de estado se devuelve a crear cada recurso. Después de crear la máquina virtual, realice el siguiente paso antes de presionar ENTRAR para eliminar todos los recursos.

    Tardará unos cinco minutos para esta aplicación de consola ejecutar completamente de principio a fin. Antes de presionar ENTRAR para iniciar la eliminación de recursos, podría tardar unos minutos para comprobar la creación de los recursos en el portal de Azure antes de eliminarlos.

3. Para ver el estado de los recursos, vaya a los registros de auditoría en el portal de Azure:

    ![Examinar los registros de auditoría en el portal de Azure](./media/virtual-machines-windows-csharp/crpportal.png)
    
## <a name="next-steps"></a>Pasos siguientes

- Aproveche las ventajas de usar una plantilla para crear una máquina virtual usando la información de [implementar una máquina Virtual de Azure mediante C# y una plantilla de administrador de recursos](virtual-machines-windows-csharp-template.md).
- Obtenga información sobre cómo administrar la máquina virtual que ha creado revisando [máquinas virtuales de administrar con el Administrador de recursos de Azure y PowerShell](virtual-machines-windows-csharp-manage.md).
