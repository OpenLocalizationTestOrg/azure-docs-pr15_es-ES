<properties 
   pageTitle="Instancia nivel IP pública (ILPIP) | Microsoft Azure"
   description="Descripción de ILPIP (puntos) y cómo se administran"
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
   ms.date="02/10/2016"
   ms.author="jdial" />

# <a name="instance-level-public-ip-overview"></a>Introducción IP de público nivel de instancia
Una instancia IP pública nivel (ILPIP) es una dirección IP pública que se puede asignar directamente a su instancia VM o función, en lugar del servicio de nube su instancia VM o función residen en. Esto no producirá a la dirección VIP (IP virtual) que se asigna a su servicio de nube. En su lugar, es una dirección IP adicional que puede usar para conectarse directamente a su instancia VM o función.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Obtenga información sobre cómo llevar a [cabo estos pasos con el modelo de administrador de recursos](virtual-network-ip-addresses-overview-arm.md). 

Asegúrese de que comprender cómo funcionan [las direcciones IP](virtual-network-ip-addresses-overview-classic.md) en Azure.

>[AZURE.NOTE] En el pasado, un ILPIP se conoce como una puntos, lo que significa IP pública. 

![Diferencia entre ILPIP y VIP](./media/virtual-networks-instance-level-public-ip/Figure1.png)

Como se muestra en la figura 1, el servicio de nube se accede mediante VIP, mientras las máquinas virtuales individuales se accede normalmente mediante VIP:&lt;número de puerto&gt;. Asignando un ILPIP a una máquina virtual específica, que se puede acceder VM directamente con esa dirección IP.

Cuando se crea un servicio de nube en Azure, registros de DNS A correspondientes se crean automáticamente para permitir el acceso al servicio a través de un nombre de dominio completo (FQDN) en lugar de usar a la dirección VIP real. El mismo proceso ocurre para ILPIP, que permite el acceso a la instancia VM o función por FQDN en lugar de la ILPIP. Por ejemplo, si crea un servicio de nube denominado *contosoadservice*y configurar una función de web denominada *contosoweb* con dos instancias, Azure registrará los siguientes registros para los casos:

- contosoweb\_IN_0.contosoadservice.cloudapp.net
- contosoweb\_IN_1.contosoadservice.cloudapp.net 

>[AZURE.NOTE] Puede asignar solo un ILPIP para cada instancia VM o función. Puede utilizar un máximo de 5 ILPIP por suscripción. En este momento, ILPIP no se admite para máquinas virtuales de múltiples NIC.

## <a name="why-should-i-request-an-ilpip"></a>¿Por qué debo solicitar una ILPIP?
Si desea que pueda conectarse a su instancia VM o función mediante una dirección IP asignada directamente, en lugar de usar la nube de servicio VIP:&lt;número de puerto&gt;, solicitar un ILPIP para su máquina virtual o su instancia del rol.
- **FTP pasivo** - tener un ILPIP en la máquina virtual, puede recibir el tráfico en prácticamente cualquier puerto, no tendrá que abrir un extremo para recibir tráfico. Esto permite escenarios como FTP pasivo donde los puertos son seleccionados dinámicamente.
- **IP saliente** : el tráfico saliente que provienen de la máquina virtual sale con la ILPIP como origen e identifica la máquina virtual a entidades externas.

## <a name="how-to-request-an-ilpip-during-vm-creation"></a>Cómo solicitar una ILPIP durante la creación de VM
La siguiente secuencia de comandos de PowerShell crea un nuevo servicio de nube denominado *FTPService*, a continuación, recupera una imagen de Azure, crea una máquina virtual denominada *FTPInstance* con la imagen recuperada, Establece la máquina virtual para usar un ILPIP y agrega la máquina virtual para el nuevo servicio:

    New-AzureService -ServiceName FTPService -Location "Central US"
    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name FTPInstance -InstanceSize Small -ImageName $image.ImageName `
  	| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
  	| Set-AzurePublicIP -PublicIPName ftpip | New-AzureVM -ServiceName FTPService -Location "Central US"

## <a name="how-to-retrieve-ilpip-information-for-a-vm"></a>Cómo recuperar información de ILPIP para una máquina virtual
Para ver la información de ILPIP de la máquina virtual creada con esta secuencia de comandos, ejecute el siguiente comando PowerShell y observar los valores de *PublicIPAddress* y *PublicIPName*:

    Get-AzureVM -Name FTPInstance -ServiceName FTPService

    DeploymentName              : FTPService
    Name                        : FTPInstance
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : ReadyRole
    IpAddress                   : 100.74.118.91
    InstanceStateDetails        : 
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : FTPInstance
    InstanceUpgradeDomain       : 0
    InstanceSize                : Small
    HostName                    : FTPInstance
    AvailabilitySetName         : 
    DNSName                     : http://ftpservice888.cloudapp.net/
    Status                      : ReadyRole
    GuestAgentStatus            : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
    ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
    PublicIPAddress             : 104.43.142.188
    PublicIPName                : ftpip
    NetworkInterfaces           : {}
    ServiceName                 : FTPService
    OperationDescription        : Get-AzureVM
    OperationId                 : 568d88d2be7c98f4bbb875e4d823718e
    OperationStatus             : OK

## <a name="how-to-remove-an-ilpip-from-a-vm"></a>Cómo quitar una ILPIP de una máquina virtual
Para quitar el ILPIP agregado a la máquina virtual en la secuencia de comandos anterior, ejecute el siguiente comando PowerShell:
    
    Get-AzureVM -ServiceName FTPService -Name FTPInstance `
  	| Remove-AzurePublicIP `
  	| Update-AzureVM

## <a name="how-to-add-an-ilpip-to-an-existing-vm"></a>Cómo agregar una ILPIP a una máquina virtual existente
Para agregar una ILPIP VM creados mediante la secuencia de comandos anterior, ejecute el siguiente comando:

    Get-AzureVM -ServiceName FTPService -Name FTPInstance `
  	| Set-AzurePublicIP -PublicIPName ftpip2 `
  	| Update-AzureVM

## <a name="how-to-associate-an-ilpip-to-a-vm-by-using-a-service-configuration-file"></a>Cómo se asocia un ILPIP a una máquina virtual mediante un archivo de configuración de servicio
También puede asociar una ILPIP a una máquina virtual mediante un archivo de configuración (CSCFG) de servicio. El xml de ejemplo siguiente muestra cómo configurar un servicio de nube para usar un ILPIP denominada *MyPublicIP* para una instancia de rol: 
    
    <?xml version="1.0" encoding="utf-8"?>
    <ServiceConfiguration serviceName="ReservedIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
      <Role name="WebRole1">
        <Instances count="1" />
        <ConfigurationSettings>
          <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
        </ConfigurationSettings>
      </Role>
      <NetworkConfiguration>
        <VirtualNetworkSite name="VNet"/>
        <AddressAssignments>
          <InstanceAddress roleName="VMRolePersisted">
            <Subnets>
              <Subnet name="Subnet1"/>
              <Subnet name="Subnet2"/>
            </Subnets>
            <PublicIPs>
              <PublicIP name="MyPublicIP" domainNameLabel="MyPublicIP" />
            </PublicIPs>
          </InstanceAddress>
        </AddressAssignments>
      </NetworkConfiguration>
    </ServiceConfiguration>

## <a name="next-steps"></a>Pasos siguientes

- Comprender cómo funciona la [asignación de direcciones IP](virtual-network-ip-addresses-overview-classic.md) en el modelo de implementación clásico.

- Obtenga información sobre las [direcciones IP reservada](virtual-networks-reserved-public-ip.md).
 