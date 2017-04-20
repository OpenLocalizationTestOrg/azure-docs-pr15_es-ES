<properties
  pageTitle="Conectar un servicio de nube a un controlador de dominio personalizado | Microsoft Azure"
  description="Obtenga información sobre cómo conectar los roles de web o trabajo a un dominio de AD personalizado mediante PowerShell y la extensión de dominio de Active Directory"
  services="cloud-services"
  documentationCenter=""
  authors="Thraka"
  manager="timlt"
  editor=""/>

  <tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/21/2016"
    ms.author="adegeo"/>

# <a name="connecting-azure-cloud-services-roles-to-a-custom-ad-domain-controller-hosted-in-azure"></a>Funciones de servicios de nube de Azure a conectarse a un controlador de dominio de Active Directory hospedado en Azure personalizado

En primer lugar se configurar una red Virtual (VNet) en Azure. A continuación, agregaremos un controlador de dominio de Active Directory (alojado en un equipo Virtual de Azure) a la VNet. A continuación, agregar las funciones de servicio de nube existentes a la VNet creado previamente y posteriormente los conectar con el controlador de dominio.

Antes de empezar, par de cosas que debe tener en cuenta:

1.  Este tutorial usa PowerShell, así pues, asegúrese de que tener instalado de PowerShell de Azure y listo. Para obtener ayuda con la configuración de Azure PowerShell, vea [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md).

2.  Las instancias de controlador de dominio de Active Directory y función de Web y trabajador deben estar en la VNet.

Siga a esta guía paso a paso y si surge algún problema, nos deja un comentario siguiente. Alguien se volver a usted (Sí, hemos leer comentarios).

3. La red que se hace referencia a la nube de servicio <mark>debe ser</mark> una **red virtual clásica**.

## <a name="create-a-virtual-network"></a>Crear una red Virtual

Puede crear una red Virtual en Azure con el portal clásica Azure o PowerShell. Para este tutorial, usaremos PowerShell. Para crear una red Virtual con el portal clásico Azure, vea [Crear una red Virtual](../virtual-network/virtual-networks-create-vnet-arm-pportal.md).

```powershell
#Create Virtual Network

$vnetStr =
@"<?xml version="1.0" encoding="utf-8"?>
<NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
    <VirtualNetworkConfiguration>
    <VirtualNetworkSites>
        <VirtualNetworkSite name="[your-vnet-name]" Location="West US">
        <AddressSpace>
            <AddressPrefix>[your-address-prefix]</AddressPrefix>
        </AddressSpace>
        <Subnets>
            <Subnet name="[your-subnet-name]">
            <AddressPrefix>[your-subnet-range]</AddressPrefix>
            </Subnet>
        </Subnets>
        </VirtualNetworkSite>
    </VirtualNetworkSites>
    </VirtualNetworkConfiguration>
</NetworkConfiguration>
"@;

$vnetConfigPath = "<path-to-vnet-config>"
Set-AzureVNetConfig -ConfigurationPath $vnetConfigPath
```

## <a name="create-a-virtual-machine"></a>Crear una máquina Virtual

Una vez haya completado la configuración de la red Virtual, debe crear un controlador de dominio de Active Directory. En este tutorial, se configurará un controlador de dominio de Active Directory en una máquina Virtual de Azure.

Para ello, cree una máquina virtual a través de PowerShell con los siguientes comandos:

```powershell
# Initialize variables
# VNet and subnet must be classic virtual network resources, not Azure Resource Manager resources.

$vnetname = '<your-vnet-name>'
$subnetname = '<your-subnet-name>'
$vmsvc1 = '<your-hosted-service>'
$vm1 = '<your-vm-name>'
$username = '<your-username>'
$password = '<your-password>'
$affgrp = '<your- affgrp>'

# Create a VM and add it to the Virtual Network

New-AzureQuickVM -Windows -ServiceName $vmsvc1 -Name $vm1 -ImageName $imgname -AdminUsername $username -Password $password -AffinityGroup $affgrp -SubnetNames $subnetname -VNetName $vnetname
```

## <a name="promote-your-virtual-machine-to-a-domain-controller"></a>Promocionar su máquina Virtual a un controlador de dominio
Para configurar la máquina Virtual como un controlador de dominio de Active Directory, debe iniciar sesión en la máquina virtual y configurarlo.

Para iniciar sesión la máquina virtual, puede recibir el archivo RDP a través de PowerShell, ejecute los comandos siguientes.

```powershell
# Get RDP file
Get-AzureRemoteDesktopFile -ServiceName $vmsvc1 -Name $vm1 -LocalPath <rdp-file-path>
```

Una vez que ha iniciado sesión en la máquina virtual, configure la máquina Virtual como un controlador de dominio de Active Directory siguiendo la Guía paso a paso acerca de [cómo configurar el controlador de dominio de Active Directory a clientes](http://social.technet.microsoft.com/wiki/contents/articles/12370.windows-server-2012-set-up-your-first-domain-controller-step-by-step.aspx).

## <a name="add-your-cloud-service-to-the-virtual-network"></a>Agregue su servicio de nube a la red Virtual

A continuación, debe agregar la implementación del servicio de nube a la VNet que acaba de crear. Para ello, modifique la cscfg de servicio de nube agregando las secciones correspondientes a su cscfg con Visual Studio o el editor de su elección.

```xml
<ServiceConfiguration serviceName="[hosted-service-name]" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="[os-family]" osVersion="*">
    <Role name="[role-name]">
    <Instances count="[number-of-instances]" />
    </Role>
    <NetworkConfiguration>

    <!--optional-->
    <Dns>
        <DnsServers><DnsServer name="[dns-server-name]" IPAddress="[ip-address]" /></DnsServers>
    </Dns>
    <!--optional-->

    <!--VNet settings
        VNet and subnet must be classic virtual network resources, not Azure Resource Manager resources.-->
    <VirtualNetworkSite name="[virtual-network-name]" />
    <AddressAssignments>
        <InstanceAddress roleName="[role-name]">
        <Subnets>
            <Subnet name="[subnet-name]" />
        </Subnets>
        </InstanceAddress>
    </AddressAssignments>
    <!--VNet settings-->

    </NetworkConfiguration>
</ServiceConfiguration>
```

A continuación, generar el proyecto de servicios de nube e implementarlo en Azure. Para obtener ayuda con la implementación de su paquete de servicios de nube a Azure, consulte [cómo crear e implementar un servicio de nube](cloud-services-how-to-create-deploy.md#deploy)

## <a name="connect-your-webworker-roles-to-the-domain"></a>Conectar los roles de web o trabajo en el dominio

Una vez que se implementa el proyecto de servicio de nube en Azure, conectar las instancias de la función con el dominio de AD personalizado con la extensión de dominio de Active Directory. Para agregar la extensión de dominio de Active Directory a la implementación de servicios de nube existente y unirse al dominio personalizado, ejecute los siguientes comandos de PowerShell:

```powershell
# Initialize domain variables

$domain = '<your-domain-name>'
$dmuser = '$domain\<your-username>'
$dmpswd = '<your-domain-password>'
$dmspwd = ConvertTo-SecureString $dmpswd -AsPlainText -Force
$dmcred = New-Object System.Management.Automation.PSCredential ($dmuser, $dmspwd)

# Add AD Domain Extension to the cloud service roles

Set-AzureServiceADDomainExtension -Service <your-cloud-service-hosted-service-name> -Role <your-role-name> -Slot <staging-or-production> -DomainName $domain -Credential $dmcred -JoinOption 35
```

Y eso es todo.

Nube servicios ahora deben combinarse en el controlador de dominio personalizado. Si desea obtener más información sobre las distintas opciones disponibles para configurar la extensión de dominio de Active Directory, utilice la Ayuda de PowerShell como se muestra a continuación.

```powershell
help Set-AzureServiceADDomainExtension
help New-AzureServiceADDomainExtensionConfig
```
