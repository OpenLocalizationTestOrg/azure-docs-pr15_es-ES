<properties 
   pageTitle="Configurar el túnel forzado para conexiones de sitio a otro mediante el modelo de implementación clásica | Microsoft Azure"
   description="Cómo redirigir o forzar todo el tráfico de Internet enlazado a su ubicación local."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/10/2016"
   ms.author="cherylmc" />

# <a name="configure-forced-tunneling-using-the-classic-deployment-model"></a>Configurar el túnel forzado utiliza el modelo de implementación clásica

> [AZURE.SELECTOR]
- [PowerShell - clásica](vpn-gateway-about-forced-tunneling.md)
- [PowerShell - Administrador de recursos](vpn-gateway-forced-tunneling-rm.md)

Túnel forzado le permite redirigir o "forzar" todo el tráfico de Internet enlazado volver a su ubicación local a través de un túnel VPN de sitio a sitio de inspección y auditoría. Se trata de un requisito de seguridad crítica de TI la mayoría de las empresas directivas. 

Sin túnel forzado, tráfico de Internet enlazado de sus máquinas virtuales en Azure pasarán por siempre de infraestructura de red Azure directamente fuera a Internet, sin la opción para que pueda inspeccionar o el tráfico de auditoría. Acceso no autorizado de Internet puede dar como resultado revelación de información u otros tipos de problemas de seguridad.

En este artículo le guiará a través de configuración se obliga túnel para redes virtuales creadas con el modelo de implementación clásico. 

**Acerca de los modelos de implementación de Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

**Modelos de implementación y herramientas para túnel forzado**

Se pueden configurar una conexión túnel forzada para el modelo de implementación clásico y el modelo de implementación de administrador de recursos. Consulte la tabla siguiente para obtener más información. Actualizamos esta tabla como nuevos artículos, nuevos modelos de implementación y otras herramientas no estarán disponibles para esta configuración. Cuando un artículo está disponible, nos vincularlo directamente desde la tabla.

[AZURE.INCLUDE [vpn-gateway-forcedtunnel](../../includes/vpn-gateway-table-forcedtunnel-include.md)] 


## <a name="requirements-and-considerations"></a>Consideraciones y requisitos

Túnel forzado en Azure está configurado a través de rutas de red virtual definidas por el usuario (UDR). Redirigir el tráfico a un sitio local se expresa como una ruta predeterminada a la puerta de enlace VPN de Azure. En la sección siguiente muestra el límite actual de la tabla y las rutas de enrutamiento para una red Virtual de Azure:


-  Cada subred de red virtual tiene una tabla de enrutamiento de sistema integradas. La tabla de enrutamiento de sistema tiene los siguientes tres grupos de rutas:

    - **VNet local redirige:** Directamente en las máquinas virtuales de destino en la misma red virtual
    
    - **En rutas de local:** La puerta de enlace VPN de Azure
    
    - **Ruta predeterminada:** Directamente a Internet. Se quitarán los paquetes destinados a las direcciones IP privadas no cubiertas por las rutas de dos anterior.


-  Con el lanzamiento de rutas definidas por el usuario, puede crear una tabla de enrutamiento para agregar una ruta predeterminada y, a continuación, asociar la tabla de enrutamiento a su subredes VNet habilitar túnel forzado en esas subredes.

- Debe establecer un "sitio de predeterminado" entre los sitios local de cruz local conectado a la red virtual.

- Túnel forzado debe estar asociado con un VNet que tenga una dinámica enrutamiento VPN puerta de enlace (no una puerta de enlace estática).
 
- ExpressRoute forzado túnel no está configurado a través de este mecanismo, pero está habilitada en su lugar, anuncia una ruta predeterminada a través de las sesiones de interconexión ExpressRoute BGP. Consulte la [Documentación de ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) para obtener más información.



## <a name="configuration-overview"></a>Introducción a la configuración

En el ejemplo siguiente, el cliente no se fuerza subred túnel. Pueden seguir las cargas de trabajo en la subred front-end Aceptar y responder a solicitudes de clientes de Internet directamente. Las subredes back-end y el nivel intermedio tienen túnel. Se obliga o redirige a un sitio local a través de uno de los túneles VPN S2S las conexiones salientes de estas dos subredes a Internet.

Esto le permite restringir y acceso a Internet desde sus máquinas virtuales de inspeccionar o cloud services en Azure, mientras sigue habilitar la arquitectura de servicio de varios niveles necesaria. También puede aplicar túnel forzado a las redes virtuales completas si no hay ningún cargas de trabajo a través de Internet en sus redes virtuales.


![Se obliga túnel](./media/vpn-gateway-about-forced-tunneling/forced-tunnel.png)



## <a name="before-you-begin"></a>Antes de empezar

Compruebe que tiene los siguientes elementos antes de comenzar la configuración.

- Una suscripción de Azure. Si todavía no tiene una suscripción de Azure, puede activar los [beneficios de suscriptor MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o signo hacia arriba para obtener una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial/).

- Una red virtual configurada. 

- La última versión de los cmdlets de PowerShell de Azure. Para obtener más información sobre la instalación de los cmdlets de PowerShell, vea [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) .


## <a name="configure-forced-tunneling"></a>Configurar el túnel forzado

El procedimiento siguiente le ayudará a especificar túnel forzado para una red virtual. Los pasos de configuración se corresponden con el archivo de configuración de red VNet.



    <VirtualNetworkSite name="MultiTier-VNet" Location="North Europe">
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
        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="DefaultSiteHQ">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch1">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch2">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch3">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
        </Gateway>
      </VirtualNetworkSite>
    </VirtualNetworkSite>

En este ejemplo, la red virtual "VNet de varios niveles" tiene tres subredes: subredes *front-end*, *nivel intermedio*y *back-end* , con conexiones de cuatro local cruzada: *DefaultSiteHQ*y tres *ramas*. 

Los pasos se la *DefaultSiteHQ* como la conexión de sitio predeterminado para forzado túnel y configurar nivel intermedio y back-end subredes usar forzado túnel.


1. Crear una tabla de enrutamiento. Use el cmdlet siguiente para crear la tabla de ruta.

        New-AzureRouteTable –Name "MyRouteTable" –Label "Routing Table for Forced Tunneling" –Location "North Europe"

2. Agregar una ruta predeterminada a la tabla de enrutamiento. 

    En el ejemplo siguiente se agrega una ruta predeterminada a la tabla de enrutamiento que creó en el paso 1. Tenga en cuenta que la ruta sola admite es el prefijo de destino de "0.0.0.0/0" a la siguiente salto "VPNGateway".
 
        Get-AzureRouteTable -Name "MyRouteTable" | Set-AzureRoute –RouteTable "MyRouteTable" –RouteName "DefaultRoute" –AddressPrefix "0.0.0.0/0" –NextHopType VPNGateway

3. Asociar la tabla de enrutamiento a las subredes. 

    Después de crea una tabla de enrutamiento y agregar una ruta, use el siguiente ejemplo para agregar o asociar la tabla de ruta para una subred VNet. En el ejemplo se agrega la tabla de ruta "MyRouteTable" a las subredes de nivel intermedio y back-end de varios niveles de VNet-VNet.

        Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Midtier" -RouteTableName "MyRouteTable"

        Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Backend" -RouteTableName "MyRouteTable"

4. Asignar un sitio predeterminado para forzado túnel. 

    En el paso anterior, las secuencias de comandos de cmdlet de ejemplo crean la tabla de enrutamiento y asociados a la tabla de rutas a dos subred VNet. El restante consiste en seleccionar un sitio local entre las conexiones múltiples sitios de la red virtual como el sitio predeterminado o túnel.

        $DefaultSite = @("DefaultSiteHQ")
        Set-AzureVNetGatewayDefaultSite –VNetName "MultiTier-VNet" –DefaultSite "DefaultSiteHQ"

## <a name="additional-powershell-cmdlets"></a>Cmdlets de PowerShell adicionales

### <a name="to-delete-a-route-table"></a>Para eliminar una tabla de ruta

    Remove-AzureRouteTable -Name <routeTableName>

### <a name="to-list-a-route-table"></a>Para mostrar una tabla de ruta

    Get-AzureRouteTable [-Name <routeTableName> [-DetailLevel <detailLevel>]]

### <a name="to-delete-a-route-from-a-route-table"></a>Para eliminar una ruta de una tabla de ruta

    Remove-AzureRouteTable –Name <routeTableName>

### <a name="to-remove-a-route-from-a-subnet"></a>Para quitar una ruta de una subred

    Remove-AzureSubnetRouteTable –VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>

### <a name="to-list-the-route-table-associated-with-a-subnet"></a>Para obtener una lista de la tabla de rutas asociada a una subred
    
    Get-AzureSubnetRouteTable -VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>

### <a name="to-remove-a-default-site-from-a-vnet-vpn-gateway"></a>Para quitar un sitio predeterminado de una puerta de enlace VPN VNet

    Remove-AzureVnetGatewayDefaultSite -VNetName <virtualNetworkName>






