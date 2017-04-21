<properties
    pageTitle="Administración de máquinas virtuales con el Administrador de recursos de Azure y C# | Microsoft Azure"
    description="Administrar máquinas virtuales con el Administrador de recursos de Azure y C#."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="davidmu"/>

# <a name="manage-azure-virtual-machines-using-azure-resource-manager-and-c"></a>Administrar máquinas virtuales de Azure con el Administrador de recursos de Azure y C#  

Las tareas de este artículo muestran cómo administrar máquinas virtuales, como iniciar, detener y actualizar. Una máquina virtual debe existir en un grupo de recursos para completar las tareas de este artículo.

Para completar las tareas de este artículo, debe:

- [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- [Un símbolo de autenticación](../resource-group-authenticate-service-principal.md)

## <a name="create-a-visual-studio-project-and-install-packages"></a>Crear un proyecto de Visual Studio e instalar paquetes

Paquetes de NuGet son las formas más fáciles de instalar las bibliotecas que necesita para finalizar las tareas de este artículo. Las bibliotecas que instale de este artículo son Azure Active Directory Authentication Library y la biblioteca de proveedor de recursos de cálculo. Siga estos pasos para obtener las bibliotecas en Visual Studio:

1. Haga clic en **archivo** > **nueva** > **proyecto**.

2. En las **plantillas de** > **Visual C#**, seleccione **Aplicación de consola**, escriba el nombre y la ubicación del proyecto y, a continuación, haga clic en **Aceptar**.

3. Haga clic en el nombre del proyecto en el Explorador de soluciones y, a continuación, haga clic en **Administrar paquetes de NuGet**.

4. Tipo *Active Directory* en el cuadro de búsqueda, haga clic en **instalar** para el paquete de Active Directory Authentication Library y, a continuación, siga las instrucciones para instalar el paquete.

5. En la parte superior de la página, seleccione **Incluir preliminar**. Tipo *Microsoft.Azure.Management.Compute* en el cuadro de búsqueda, haga clic en **instalar** para las bibliotecas de .NET calcular y, a continuación, siga las instrucciones para instalar el paquete.

Ahora ya está listo para empezar a usar las bibliotecas para administrar su máquinas virtuales.

## <a name="set-up-the-project"></a>Configurar el proyecto

Ahora que la aplicación se crea y se instalan las bibliotecas, cree un token con la información de la aplicación. Este token se usa para autenticar solicitudes para el Administrador de recursos de Azure.

1. Abra el archivo Program.cs para el proyecto que ha creado y, a continuación, agregue estas instrucciones using a la parte superior del archivo:

        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure.Management.Compute;
        using Microsoft.Azure.Management.Compute.Models;
        using Microsoft.Rest;
        
2. Agregar variables al método principal de la clase de programa para especificar el nombre del grupo de recursos y el nombre de la máquina virtual y su identificador de suscripción:

        var groupName = "resource group name";
        var vmName = "virtual machine name";  
        var subscriptionId = "subsciption id";

    Puede encontrar el identificador de suscripción ejecutando Get-AzureRmSubscription.
    
3. Para obtener el token que es necesaria para crear las credenciales, agregue este método a la clase Program:

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
    
4. Para crear las credenciales, agregue este código al método Main en Program.cs:

        var token = GetAccessTokenAsync();
        var credential = new TokenCredentials(token.Result.AccessToken);

5. Guarde el archivo Program.cs.

## <a name="display-information-about-a-virtual-machine"></a>Mostrar información sobre una máquina virtual

1. Agregar este método a la clase Program del proyecto que creó anteriormente:

        public static async void GetVirtualMachineAsync(
          TokenCredentials credential, 
          string groupName, 
          string vmName, 
          string subscriptionId)
        {
          Console.WriteLine("Getting information about the virtual machine...");

          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          var vmResult = await computeManagementClient.VirtualMachines.GetAsync(
            groupName, 
            vmName, 
            InstanceViewTypes.InstanceView);

          Console.WriteLine("hardwareProfile");
          Console.WriteLine("   vmSize: " + vmResult.HardwareProfile.VmSize);

          Console.WriteLine("\nstorageProfile");
          Console.WriteLine("  imageReference");
          Console.WriteLine("    publisher: " + vmResult.StorageProfile.ImageReference.Publisher);
          Console.WriteLine("    offer: " + vmResult.StorageProfile.ImageReference.Offer);
          Console.WriteLine("    sku: " + vmResult.StorageProfile.ImageReference.Sku);
          Console.WriteLine("    version: " + vmResult.StorageProfile.ImageReference.Version);
          Console.WriteLine("  osDisk");
          Console.WriteLine("    osType: " + vmResult.StorageProfile.OsDisk.OsType);
          Console.WriteLine("    name: " + vmResult.StorageProfile.OsDisk.Name);
          Console.WriteLine("    createOption: " + vmResult.StorageProfile.OsDisk.CreateOption);
          Console.WriteLine("    uri: " + vmResult.StorageProfile.OsDisk.Vhd.Uri);
          Console.WriteLine("    caching: " + vmResult.StorageProfile.OsDisk.Caching);

          Console.WriteLine("\nosProfile");
          Console.WriteLine("  computerName: " + vmResult.OsProfile.ComputerName);
          Console.WriteLine("  adminUsername: " + vmResult.OsProfile.AdminUsername);
          Console.WriteLine("  provisionVMAgent: " + vmResult.OsProfile.WindowsConfiguration.ProvisionVMAgent.Value);
          Console.WriteLine("  enableAutomaticUpdates: " + vmResult.OsProfile.WindowsConfiguration.EnableAutomaticUpdates.Value);

          Console.WriteLine("\nnetworkProfile");
          foreach (NetworkInterfaceReference nic in vmResult.NetworkProfile.NetworkInterfaces)
          {
            Console.WriteLine("  networkInterface id: " + nic.Id);
          }

          Console.WriteLine("\nvmAgent");
          Console.WriteLine("  vmAgentVersion" + vmResult.InstanceView.VmAgent.VmAgentVersion);
          Console.WriteLine("    statuses");
          foreach (InstanceViewStatus stat in vmResult.InstanceView.VmAgent.Statuses)
          {
            Console.WriteLine("    code: " + stat.Code);
            Console.WriteLine("    level: " + stat.Level);
            Console.WriteLine("    displayStatus: " + stat.DisplayStatus);
            Console.WriteLine("    message: " + stat.Message);
            Console.WriteLine("    time: " + stat.Time);
          }

          Console.WriteLine("\ndisks");
          foreach (DiskInstanceView idisk in vmResult.InstanceView.Disks)
          {
            Console.WriteLine("  name: " + idisk.Name);
            Console.WriteLine("  statuses");
            foreach (InstanceViewStatus istat in idisk.Statuses)
            {
              Console.WriteLine("    code: " + istat.Code);
              Console.WriteLine("    level: " + istat.Level);
              Console.WriteLine("    displayStatus: " + istat.DisplayStatus);
              Console.WriteLine("    time: " + istat.Time);
            }
          }

          Console.WriteLine("\nVM general status");
          Console.WriteLine("  provisioningStatus: " + vmResult.ProvisioningState);
          Console.WriteLine("  id: " + vmResult.Id);
          Console.WriteLine("  name: " + vmResult.Name);
          Console.WriteLine("  type: " + vmResult.Type);
          Console.WriteLine("  location: " + vmResult.Location);
          Console.WriteLine("\nVM instance status");
          foreach (InstanceViewStatus istat in vmResult.InstanceView.Statuses)
          {
            Console.WriteLine("\n  code: " + istat.Code);
            Console.WriteLine("  level: " + istat.Level);
            Console.WriteLine("  displayStatus: " + istat.DisplayStatus);
          }
          
        }

2. Para llamar al método que acaba de agregar, agregue este código al método Main:

        GetVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();
    
3. Guarde el archivo Program.cs.

4. Haga clic en **Iniciar** en Visual Studio y, a continuación, inicie sesión en Azure AD con el mismo nombre de usuario y contraseña que utiliza con su suscripción.

    Cuando ejecuta este método, verá algo parecido a este ejemplo:
    
        Getting information about the virtual machine...
        hardwareProfile
          vmSize: Standard_A0

        storageProfile
          imageReference
            publisher: MicrosoftWindowsServer
            offer: WindowsServer
            sku: 2012-R2-Datacenter
            version: latest
          osDisk
            osType: Windows
            name: myosdisk
            createOption: FromImage
            uri: http://store1.blob.core.windows.net/vhds/myosdisk.vhd
            caching: ReadWrite

          osProfile
            computerName: vm1
            adminUsername: account1
            provisionVMAgent: True
            enableAutomaticUpdates: True

          networkProfile
            networkInterface 
              id: /subscriptions/{subscription-id}
                /resourceGroups/rg1/providers/Microsoft.Network/networkInterfaces/nc1

          vmAgent
            vmAgentVersion2.7.1198.766
            statuses
            code: ProvisioningState/succeeded
            level: Info
            displayStatus: Ready
            message: GuestAgent is running and accepting new configurations.
            time: 4/13/2016 8:35:32 PM

          disks
            name: myosdisk
            statuses
              code: ProvisioningState/succeeded
              level: Info
              displayStatus: Provisioning succeeded
              time: 4/13/2016 8:04:36 PM

          VM general status
            provisioningStatus: Succeeded
            id: /subscriptions/{subscription-id}
              /resourceGroups/rg1/providers/Microsoft.Compute/virtualMachines/vm1
            name: vm1
            type: Microsoft.Compute/virtualMachines
            location: centralus

          VM instance status
            code: ProvisioningState/succeeded
              level: Info
              displayStatus: Provisioning succeeded
            code: PowerState/running
              level: Info
              displayStatus: VM running

## <a name="stop-a-virtual-machine"></a>Detener una máquina virtual

Puede dejar una máquina virtual de dos maneras. Puede dejar una máquina virtual y mantener su configuración, pero seguirán cobrará por él, o puede dejar una máquina virtual y desasignar. Cuando una máquina virtual se ha quitado todos los recursos asociados también son extremos manera y facturación.

1. Comentar cualquier código que ya se ha agregado al método principal, excepto el código para obtener las credenciales.

2. Agregar este método a la clase Program:

        public static async void StopVirtualMachineAsync(
          TokenCredentials credential, 
          string groupName, 
          string vmName, 
          string subscriptionId)
        {
          Console.WriteLine("Stopping the virtual machine...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          await computeManagementClient.VirtualMachines.PowerOffAsync(groupName, vmName);
        }

    Si desea cancelar la máquina virtual, cambie la llamada apagar este código:

        computeManagementClient.VirtualMachines.Deallocate(groupName, vmName);

3. Para llamar al método que acaba de agregar, agregue este código al método Main:

        StopVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();

4. Guarde el archivo Program.cs.

5. Haga clic en **Iniciar** en Visual Studio y, a continuación, inicie sesión en Azure AD con el mismo nombre de usuario y contraseña que utiliza con su suscripción.

    Debería ver el estado del cambio de máquina virtual su interrupción. Si ejecutó el método de que llamada Deallocate, el estado se detiene (asignación).

## <a name="start-a-virtual-machine"></a>Iniciar una máquina virtual

1. Comentar cualquier código que ya se ha agregado al método principal, excepto el código para obtener las credenciales.

2. Agregar este método a la clase Program:

        public static async void StartVirtualMachineAsync(
          TokenCredentials credential, 
          string groupName, 
          string vmName, 
          string subscriptionId)
        {
          Console.WriteLine("Starting the virtual machine...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          await computeManagementClient.VirtualMachines.StartAsync(groupName, vmName);
        }

3. Para llamar al método que acaba de agregar, agregue este código al método Main:

        StartVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();

4. Guarde el archivo Program.cs.

5. Haga clic en **Iniciar** en Visual Studio y, a continuación, inicie sesión en Azure AD con el mismo nombre de usuario y contraseña que utiliza con su suscripción.

    Debería ver el estado de la máquina virtual cambiar a en ejecución.

## <a name="restart-a-running-virtual-machine"></a>Reiniciar una máquina virtual

1. Comentar cualquier código que ya se ha agregado al método principal, excepto el código para obtener las credenciales.

2. Agregar este método a la clase Program:

        public static async void RestartVirtualMachineAsync(
          TokenCredentials credential,
          string groupName,
          string vmName,
          string subscriptionId)
        {
          Console.WriteLine("Restarting the virtual machine...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          await computeManagementClient.VirtualMachines.RestartAsync(groupName, vmName);
        }

3. Para llamar al método que acaba de agregar, agregue este código al método Main:

        RestartVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();

4. Guarde el archivo Program.cs.

5. Haga clic en **Iniciar** en Visual Studio y, a continuación, inicie sesión en Azure AD con el mismo nombre de usuario y contraseña que utiliza con su suscripción.

## <a name="resize-a-virtual-machine"></a>Cambiar el tamaño de una máquina virtual

En este ejemplo se muestra cómo cambiar el tamaño de una máquina virtual.

1. Comentar cualquier código que ya se ha agregado al método principal, excepto el código para obtener las credenciales.

2. Agregar este método a la clase Program:

        public static async void UpdateVirtualMachineAsync(
          TokenCredentials credential, 
          string groupName, 
          string vmName, 
          string subscriptionId)
        {
          Console.WriteLine("Updating the virtual machine...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          var vmResult = await computeManagementClient.VirtualMachines.GetAsync(groupName, vmName);
          vmResult.HardwareProfile.VmSize = "Standard_A1";
          await computeManagementClient.VirtualMachines.CreateOrUpdateAsync(groupName, vmName, vmResult);
        }

3. Para llamar al método que acaba de agregar, agregue este código al método Main:

        UpdateVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();

4. Guarde el archivo Program.cs.

5. Haga clic en **Iniciar** en Visual Studio y, a continuación, inicie sesión en Azure AD con el mismo nombre de usuario y contraseña que utiliza con su suscripción.

    Verá el tamaño del cambio de máquina virtual Standard_A1.

## <a name="add-a-data-disk-to-a-virtual-machine"></a>Agregar un disco de datos a una máquina virtual

Este ejemplo muestra cómo agregar un disco de datos a una máquina virtual.

1. Comentar cualquier código que ya se ha agregado al método principal, excepto el código para obtener las credenciales.

2. Agregar este método a la clase Program:

        public static async void AddDataDiskAsync(
          TokenCredentials credential, 
          string groupName, 
          string vmName, 
          string subscriptionId)
        {
          Console.WriteLine("Adding the disk to the virtual machine...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          var vmResult = await computeManagementClient.VirtualMachines.GetAsync(groupName, vmName);
          vmResult.StorageProfile.DataDisks.Add(
            new DataDisk
              {
                Lun = 0,
                Name = "mydatadisk1",
                Vhd = new VirtualHardDisk
                  {
                    Uri = "https://mystorage1.blob.core.windows.net/vhds/mydatadisk1.vhd"
                  },
                CreateOption = DiskCreateOptionTypes.Empty,
                DiskSizeGB = 2,
                Caching = CachingTypes.ReadWrite
              });
          await computeManagementClient.VirtualMachines.CreateOrUpdateAsync(groupName, vmName, vmResult);
        }

3. Para llamar al método que acaba de agregar, agregue este código al método Main:

        AddDataDiskAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();

4. Guarde el archivo Program.cs.

5. Haga clic en **Iniciar** en Visual Studio y, a continuación, inicie sesión en Azure AD con el mismo nombre de usuario y contraseña que utiliza con su suscripción.

## <a name="delete-a-virtual-machine"></a>Eliminar una máquina virtual

1. Comentar cualquier código que ya se ha agregado al método principal, excepto el código para obtener las credenciales.

2. Agregar este método a la clase Program:

        public static async void DeleteVirtualMachineAsync(
          TokenCredentials credential, 
          string groupName, 
          string vmName, 
          string subscriptionId)
        {
          Console.WriteLine("Deleting the virtual machine...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          await computeManagementClient.VirtualMachines.DeleteAsync(groupName, vmName);
        }

3. Para llamar al método que acaba de agregar, agregue este código al método Main:

        DeleteVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();

4. Guarde el archivo Program.cs.

5. Haga clic en **Iniciar** en Visual Studio y, a continuación, inicie sesión en Azure AD con el mismo nombre de usuario y contraseña que utiliza con su suscripción.

## <a name="next-steps"></a>Pasos siguientes

Si hay problemas con una implementación, puede buscar en [implementaciones de grupo de recursos de solución de problemas con el portal de Azure](../resource-manager-troubleshoot-deployments-portal.md)
