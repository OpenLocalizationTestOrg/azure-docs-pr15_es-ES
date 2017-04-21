<properties 
   pageTitle="Cómo configurar una dirección IP estática de privado interna"
   description="Descripción de direcciones IP interno estático (DIP) y cómo se administran"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/22/2016"
   ms.author="jdial" />

# <a name="how-to-set-a-static-internal-private-ip-address-using-powershell-classic"></a>Cómo establecer una privada dirección IP estática interna con PowerShell (clásico)
En la mayoría de los casos, no necesitará especificar una dirección IP estática interna para el equipo virtual. Máquinas virtuales en una red virtual recibirá automáticamente una dirección IP interna de un rango que especifique. Pero en algunos casos, especificar una dirección IP estática para una máquina virtual determinada tenga sentido. Por ejemplo, si su máquina virtual va a ejecutar DNS o un controlador de dominio. Una dirección IP estática interna se mantiene con la máquina virtual incluso a través de un estado de detención/deprovision. 

> [AZURE.IMPORTANT] Azure tiene dos diferentes modelos de implementación para crear y trabajar con recursos: [Administrador de recursos y clásica](../resource-manager-deployment-model.md). En este artículo trata sobre utiliza el modelo de implementación clásico. Microsoft recomienda que más nuevas implementaciones de usar el [modelo de implementación de administrador de recursos](virtual-networks-static-private-ip-arm-ps.md).

## <a name="how-to-verify-if-a-specific-ip-address-is-available"></a>Cómo comprobar si hay una dirección IP específica
Para comprobar si la dirección IP *10.0.0.7* está disponible en un vnet denominado *TestVnet*, ejecute el siguiente comando PowerShell y compruebe el valor de *IsAvailable*:

    Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 10.0.0.7 

    IsAvailable          : True
    AvailableAddresses   : {}
    OperationDescription : Test-AzureStaticVNetIP
    OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
    OperationStatus      : Succeeded

>[AZURE.NOTE] Si desea probar el comando encima en un entorno de prueba de errores, siga las instrucciones en [crear una red Virtual](virtual-networks-create-vnet-classic-portal.md) para crear un vnet denominado *TestVnet* y asegurarse de que utiliza el espacio de direcciones *10.0.0.0/8* .

## <a name="how-to-specify-a-static-internal-ip-when-creating-a-vm"></a>Cómo especificar una dirección IP interna estática al crear una máquina virtual
La siguiente secuencia de comandos de PowerShell crea un nuevo servicio de nube denominado *TestService*, a continuación, recupera una imagen de Azure, a continuación, crea una máquina virtual denominada *TestVM* en el nuevo servicio de nube con la imagen recuperada, Establece la máquina virtual de estar en una subred denominada *subred-1*y establece *10.0.0.7* como una dirección IP interna estática de la máquina virtual:

    New-AzureService -ServiceName TestService -Location "Central US"
    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
  	| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
  	| Set-AzureSubnet –SubnetNames Subnet-1 `
  	| Set-AzureStaticVNetIP -IPAddress 10.0.0.7 `
  	| New-AzureVM -ServiceName "TestService" –VNetName TestVnet

## <a name="how-to-retrieve-static-internal-ip-information-for-a-vm"></a>Cómo recuperar información de IP estática interna de una máquina virtual
Para ver la información de IP interna estática de la máquina virtual creada con esta secuencia de comandos, ejecute el siguiente comando PowerShell y observar los valores de la *dirección IP*:

    Get-AzureVM -Name TestVM -ServiceName TestService

    DeploymentName              : TestService
    Name                        : TestVM
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : Provisioning
    IpAddress                   : 10.0.0.7
    InstanceStateDetails        : Windows is preparing your computer for first use...
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : TestVM
    InstanceUpgradeDomain       : 0
    InstanceSize                : Small
    HostName                    : rsR2-797
    AvailabilitySetName         : 
    DNSName                     : http://testservice000.cloudapp.net/
    Status                      : Provisioning
    GuestAgentStatus            : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
    ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
    PublicIPAddress             : 
    PublicIPName                : 
    NetworkInterfaces           : {}
    ServiceName                 : TestService
    OperationDescription        : Get-AzureVM
    OperationId                 : 34c1560a62f0901ab75cde4fed8e8bd1
    OperationStatus             : OK

## <a name="how-to-remove-a-static-internal-ip-from-a-vm"></a>Cómo quitar una dirección IP interna estática de una máquina virtual
Para quitar IP interno estáticas agregado a la máquina virtual en la secuencia de comandos anterior, ejecute el siguiente comando PowerShell:
    
    Get-AzureVM -ServiceName TestService -Name TestVM `
  	| Remove-AzureStaticVNetIP `
  	| Update-AzureVM

## <a name="how-to-add-a-static-internal-ip-to-an-existing-vm"></a>Cómo agregar una dirección IP interna estática a una máquina virtual existente
Para agregar un estático interno IP VM creado con la secuencia de comandos anterior, la ejecución el siguiente comando:

    Get-AzureVM -ServiceName TestService000 -Name TestVM `
  	| Set-AzureStaticVNetIP -IPAddress 10.10.0.7 `
  	| Update-AzureVM

## <a name="next-steps"></a>Pasos siguientes

[IP reservada](virtual-networks-reserved-public-ip.md)

[Nivel de instancia público IP (ILPIP)](virtual-networks-instance-level-public-ip.md)

[API REST de IP reservada](https://msdn.microsoft.com/library/azure/dn722420.aspx)
 
