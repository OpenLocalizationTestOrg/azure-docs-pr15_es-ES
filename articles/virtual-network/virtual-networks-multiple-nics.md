<properties
   pageTitle="Crear una máquina virtual con varias NIC"
   description="Obtenga información sobre cómo crear y configurar máquinas virtuales con varias NIC"
   services="virtual-network, virtual-machines"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-service-management,azure-resource-manager"
/>
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="jdial" />

# <a name="create-a-vm-with-multiple-nics"></a>Crear una máquina virtual con varias NIC

Puede crear máquinas virtuales (VM) en Azure y adjuntar varias interfaces de red (NIC) para cada uno de sus máquinas virtuales. Múltiples NIC es necesario para muchos dispositivos virtual red, como la entrega de aplicaciones y soluciones de optimización de WAN. Múltiples NIC también proporciona más funcionalidades de administración de tráfico de red, incluidos aislamiento del tráfico entre frontal finalizar NIC y hacer copia final NIC o separación plano de tráfico de datos de tráfico de plano de administración.

![Múltiples NIC para VM](./media/virtual-networks-multiple-nics/IC757773.png)

La figura anterior se muestra una máquina virtual con tres NIC, que cada una conectada a una subred diferente.

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]

- VIP a través de Internet (implementaciones clásicas) solo se admite en NIC "predeterminado". Hay solo VIP a la dirección IP del NIC predeterminada.
- En este momento, las direcciones de la instancia nivel público IP (LPIP) (implementaciones clásicas) no son compatibles para múltiples máquinas virtuales de NIC.
- El orden de las NIC desde dentro de la máquina virtual será aleatorio y también podría cambiar en todas las actualizaciones de infraestructura de Azure. Sin embargo, las direcciones IP y MAC ethernet correspondiente direcciones le siguen siendo las mismas. Por ejemplo, suponga que **Eth1** tiene la dirección IP 10.1.0.100 y la dirección de MAC 00-0D-3A-B0-39-0D; Después de una actualización de la infraestructura de Azure y reiniciar el equipo, se podría cambiar a **ethl2**, pero la dirección IP y MAC emparejamiento permanecerán igual. Cuando es iniciado por el cliente, el orden NIC permanecerán igual.
- La dirección de cada NIC en cada máquina virtual debe estar en una subred, varias NIC en una única VM cada se pueden asignar direcciones que están en la misma subred.
- El tamaño de la memoria virtual determina el número de NIC que se pueden crear para una máquina virtual. Referencia del [Servidor de Windows](../virtual-machines/virtual-machines-windows-sizes.md) y [Linux](../virtual-machines/virtual-machines-linux-sizes.md) artículos de tamaños VM para determinar cuántas NIC es compatible con el tamaño de cada máquina virtual. 

## <a name="network-security-groups-nsgs"></a>Grupos de seguridad de red (NSGs)
En una implementación de administrador de recursos, cualquier NIC en una máquina virtual puede estar asociada con una red de seguridad grupo (GSN), incluyendo cualquier NIC en una máquina virtual que tiene varias NIC habilitadas. Si una NIC se asigna una dirección dentro de una subred donde está asociada con un GSN la subred, a continuación, las reglas GSN de la subred aplicarán a esa NIC. Además de asociar subredes NSGs, también puede asociar una NIC con un GSN.

Si una subred está asociada con un GSN y una NIC en esa subred está asociado individualmente a un GSN, se aplican las reglas de GSN asociadas en el **orden del flujo de** acuerdo con la dirección del tráfico que se pasa dentro o fuera de la NIC:

- **El tráfico entrante** cuyo destino es la NIC en cuestión primero fluye a través de la subred desencadenar reglas de GSN de subred, antes de pasar a la NIC, a continuación, desencadenar reglas de GSN de NIC.
- **Tráfico saliente** cuyo origen es la NIC en cuestión fluye en primer lugar fuera de la tarjeta, desencadenar reglas de GSN de NIC, antes de pasar a través de la subred, a continuación, desencadenar reglas de GSN de subred.

Obtener más información acerca de los [Grupos de seguridad de la red](virtual-networks-nsg.md) y cómo se aplican en función de las asociaciones de subredes, máquinas virtuales y NIC..

## <a name="how-to-configure-a-multi-nic-vm-in-a-classic-deployment"></a>Cómo configurar un varios NIC VM en una implementación clásica

Las siguientes instrucciones le ayudará a crear un varios VM NIC que contiene 3 NIC: NIC predeterminada y dos NIC adicionales. Los pasos de configuración creará una máquina virtual que se configurará según el fragmento de archivo de configuración de servicio siguiente:

    <VirtualNetworkSite name="MultiNIC-VNet" Location="North Europe">
    <AddressSpace>
      <AddressPrefix>10.1.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Frontend">
            <AddressPrefix>10.1.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Midtier">
            <AddressPrefix>10.1.1.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Backend">
            <AddressPrefix>10.1.2.0/23</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.1.200.0/28</AddressPrefix>
          </Subnet>
        </Subnets>
    … Skip over the remainder section …
    </VirtualNetworkSite>


Tiene los siguientes requisitos previos antes de intentar ejecutar los comandos de PowerShell en el ejemplo.

- Una suscripción de Azure.
- Una red virtual configurada. Para obtener más información sobre VNets, vea [Introducción a la red Virtual](virtual-networks-overview.md) .
- La última versión de Azure PowerShell descargó e instaló. Vea [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md).

Para crear una máquina virtual con varias NIC, siga los pasos siguientes:

1. Seleccione una imagen de máquina virtual de galería de imágenes de máquina virtual de Azure. Tenga en cuenta que cambian a menudo imágenes y de región que estén disponibles. La imagen especificada en el ejemplo siguiente puede cambiar o no podría estar en su región, así que asegúrese de especificar la imagen que necesita.

        $image = Get-AzureVMImage `
            -ImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201410.01-en.us-127GB.vhd"

1. Crear una configuración de máquina virtual.

        $vm = New-AzureVMConfig -Name "MultiNicVM" -InstanceSize "ExtraLarge" `
            -Image $image.ImageName –AvailabilitySetName "MyAVSet"

1. Crear el inicio de sesión de administrador de forma predeterminada.

        Add-AzureProvisioningConfig –VM $vm -Windows -AdminUserName "<YourAdminUID>" `
            -Password "<YourAdminPassword>"

1. Agregar NIC adicionales a la configuración de la máquina virtual.

        Add-AzureNetworkInterfaceConfig -Name "Ethernet1" `
            -SubnetName "Midtier" -StaticVNetIPAddress "10.1.1.111" -VM $vm
        Add-AzureNetworkInterfaceConfig -Name "Ethernet2" `
            -SubnetName "Backend" -StaticVNetIPAddress "10.1.2.222" -VM $vm

1. Especifique la subred y la dirección IP para la NIC predeterminada.

        Set-AzureSubnet -SubnetNames "Frontend" -VM $vm
        Set-AzureStaticVNetIP -IPAddress "10.1.0.100" -VM $vm

1. Crear la máquina virtual de su red virtual.

        New-AzureVM -ServiceName "MultiNIC-CS" –VNetName "MultiNIC-VNet" –VMs $vm

>[AZURE.NOTE] La VNet que especifique aquí ya debe existir (como se mencionó en los requisitos previos). El ejemplo siguiente especifica una red virtual denominada **MultiNIC VNet**.

## <a name="limitations"></a>Limitaciones

Las limitaciones siguientes son aplicables cuando se usa la característica NIC entre varios:

- Máquinas virtuales de múltiples NIC debe crearse en redes virtuales Azure (VNets). Máquinas virtuales de no VNet no pueden configurarse con múltiples NIC.
- Todas las máquinas virtuales en un conjunto de disponibilidad necesitan usar en NIC único o múltiples NIC No puede haber una mezcla de múltiples NIC VM y máquinas virtuales de NIC único dentro de un conjunto de disponibilidad. Aplicarán reglas de la misma para máquinas virtuales en un servicio de nube.
- No se puede configurar una máquina virtual con NIC única con múltiples NIC (y viceversa) una vez que se implementa sin eliminar y volver a crearlo.


## <a name="secondary-nics-access-to-other-subnets"></a>Acceso de NIC secundario a otras subredes

De forma predeterminada no se configurará con una puerta de enlace, debido a que el flujo del tráfico en la NIC secundarios está limitado para estar dentro de la misma subred NIC secundarias. Si desean que los usuarios para habilitar NIC secundarias para comunicarse fuera de su propia subred, tendrá que agregar una entrada de la tabla de enrutamiento para configurar la puerta de enlace, como se describe a continuación.

>[AZURE.NOTE] Máquinas virtuales creadas antes de julio de 2015 puede tener una puerta de enlace predeterminada configurada para todas las NIC. La puerta de enlace para NIC secundarias no se eliminarán hasta que se reinician estas máquinas virtuales. En sistemas operativos que utilizan el modelo de enrutamiento de host no seguro, como Linux, puede interrumpir la conexión a Internet si el tráfico de entrada y salida usar diferentes NIC.

### <a name="configure-windows-vms"></a>Configurar máquinas virtuales de Windows

Suponga que tiene una máquina virtual de Windows con dos NIC como sigue:

- Dirección IP de NIC principal: 192.168.1.4
- Dirección IP de NIC secundaria: 192.168.2.5

La tabla de rutas de IPv4 para este VM tendría un aspecto similar a este:

    IPv4 Route Table
    ===========================================================================
    Active Routes:
    Network Destination        Netmask          Gateway       Interface  Metric
              0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4      5
            127.0.0.0        255.0.0.0         On-link         127.0.0.1    306
            127.0.0.1  255.255.255.255         On-link         127.0.0.1    306
      127.255.255.255  255.255.255.255         On-link         127.0.0.1    306
        168.63.129.16  255.255.255.255      192.168.1.1      192.168.1.4      6
          192.168.1.0    255.255.255.0         On-link       192.168.1.4    261
          192.168.1.4  255.255.255.255         On-link       192.168.1.4    261
        192.168.1.255  255.255.255.255         On-link       192.168.1.4    261
          192.168.2.0    255.255.255.0         On-link       192.168.2.5    261
          192.168.2.5  255.255.255.255         On-link       192.168.2.5    261
        192.168.2.255  255.255.255.255         On-link       192.168.2.5    261
            224.0.0.0        240.0.0.0         On-link         127.0.0.1    306
            224.0.0.0        240.0.0.0         On-link       192.168.1.4    261
            224.0.0.0        240.0.0.0         On-link       192.168.2.5    261
      255.255.255.255  255.255.255.255         On-link         127.0.0.1    306
      255.255.255.255  255.255.255.255         On-link       192.168.1.4    261
      255.255.255.255  255.255.255.255         On-link       192.168.2.5    261
    ===========================================================================

Observe que la ruta predeterminada (0.0.0.0) solo está disponible para la NIC principal. No podrá tener acceso a los recursos fuera de la subred para la NIC secundaria, tal como se muestra a continuación:

    C:\Users\Administrator>ping 192.168.1.7 -S 192.165.2.5

    Pinging 192.168.1.7 from 192.165.2.5 with 32 bytes of data:
    PING: transmit failed. General failure.
    PING: transmit failed. General failure.
    PING: transmit failed. General failure.
    PING: transmit failed. General failure.

Para agregar una ruta predeterminada en la NIC secundaria, siga los pasos siguientes:

1. Desde el símbolo del sistema, ejecute el comando siguiente para identificar el número de índice para la NIC secundaria:

        C:\Users\Administrator>route print
        ===========================================================================
        Interface List
         29...00 15 17 d9 b1 6d ......Microsoft Virtual Machine Bus Network Adapter #16
         27...00 15 17 d9 b1 41 ......Microsoft Virtual Machine Bus Network Adapter #14
          1...........................Software Loopback Interface 1
         14...00 00 00 00 00 00 00 e0 Teredo Tunneling Pseudo-Interface
         20...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #2
        ===========================================================================

2. Observe la segunda entrada de la tabla con un índice de 27 (en este ejemplo).
3. Desde el símbolo del sistema, ejecute el comando de **Agregar la ruta** como se muestra a continuación. En este ejemplo, se especifica 192.168.2.1 como la puerta de enlace predeterminada para la NIC secundaria:

        route ADD -p 0.0.0.0 MASK 0.0.0.0 192.168.2.1 METRIC 5000 IF 27

4. Para probar la conectividad, vuelva a la línea de comandos e intente hacer ping una subred diferente de la NIC secundaria como se muestra int eh ejemplo siguiente:

        C:\Users\Administrator>ping 192.168.1.7 -S 192.165.2.5

        Reply from 192.168.1.7: bytes=32 time<1ms TTL=128
        Reply from 192.168.1.7: bytes=32 time<1ms TTL=128
        Reply from 192.168.1.7: bytes=32 time=2ms TTL=128
        Reply from 192.168.1.7: bytes=32 time<1ms TTL=128

5. También puede comprobar la tabla de ruta para comprobar la ruta recién agregada, como se muestra a continuación:

        C:\Users\Administrator>route print

        ...

        IPv4 Route Table
        ===========================================================================
        Active Routes:
        Network Destination        Netmask          Gateway       Interface  Metric
                  0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4      5
                  0.0.0.0          0.0.0.0      192.168.2.1      192.168.2.5   5005
                127.0.0.0        255.0.0.0         On-link         127.0.0.1    306

### <a name="configure-linux-vms"></a>Configurar máquinas virtuales de Linux

Para máquinas virtuales de Linux, dado que el comportamiento predeterminado utiliza host débil enrutamiento, recomendamos que las NIC secundarias están limitadas a flujos de tráfico solo dentro de la misma subred. Sin embargo si algunos escenarios requieren conectividad fuera de la subred, los usuarios deben habilitar Directiva según enrutamiento para asegurarse de que el tráfico de entrada y salida utiliza la misma NIC.

## <a name="next-steps"></a>Pasos siguientes

- Implementar [Máquinas virtuales de MultiNIC en un escenario de aplicación de nivel 2 en una implementación de administrador de recursos](virtual-network-deploy-multinic-arm-template.md).
- Implementar [Máquinas virtuales de MultiNIC en un escenario de aplicación de nivel 2 en una implementación clásico](virtual-network-deploy-multinic-classic-ps.md).
