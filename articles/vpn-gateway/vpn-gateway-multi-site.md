<properties 
   pageTitle="Conectarse a una red virtual de varios sitios mediante la puerta de enlace VPN y PowerShell | Microsoft Azure"
   description="En este artículo le guiará a través de varios sitios local local a conectarse a una red virtual mediante una puerta de enlace VPN para el modelo de implementación clásico."
   services="vpn-gateway"
   documentationCenter="na"
   authors="yushwang"
   manager="rossort"
   editor=""
   tags="azure-service-management"/>

<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/11/2016"
   ms.author="yushwang" />

# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection"></a>Agregar una conexión de sitio a sitio a una VNet con una conexión de puerta de enlace VPN existente

> [AZURE.SELECTOR]
- [Administrador de recursos - Portal](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
- [Clásico: PowerShell](vpn-gateway-multi-site.md)

En este artículo le guiará por usar PowerShell para agregar conexiones de sitio a sitio (S2S) a una puerta de enlace VPN que tiene una conexión existente. Este tipo de conexión a menudo se conoce como una configuración de "múltiples sitios". 

En este artículo se aplica a redes virtuales creadas con el modelo de implementación clásica (también conocido como servicio de administración). Estos pasos no se aplican a las configuraciones de conexión coexistentes ExpressRoute o a sitios. Para obtener información acerca de las conexiones coexistentes, consulte [ExpressRoute/S2S coexistentes conexiones](../expressroute/expressroute-howto-coexist-classic.md) .

### <a name="deployment-models-and-methods"></a>Métodos y modelos de implementación

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

Actualizamos esta tabla como nuevos artículos y herramientas adicionales no estarán disponibles para esta configuración. Cuando un artículo está disponible, nos vincularlo directamente desde esta tabla.

[AZURE.INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)] 



## <a name="about-connecting"></a>Acerca de la conexión

Puede conectar varios sitios local a una única red virtual. Esto es especialmente atractiva para crear soluciones de nube híbrida. Crear una conexión de múltiples sitios a la puerta de enlace de red virtual Azure es muy similar a la creación de otras conexiones de sitio a otro. De hecho, puede usar una puerta de enlace de Azure VPN existente, como la puerta de enlace es dinámico (basada en ruta).

Si ya tiene una puerta de enlace estática conectado a su red virtual, puede cambiar el tipo de puerta de enlace a dinámico sin necesidad de volver a crear la red virtual para acomodar múltiples sitios. Antes de cambiar el tipo de distribución, asegúrese de que la puerta de enlace VPN local admite configuraciones de VPN basadas en ruta. 

![diagrama de múltiples sitios] (./media/vpn-gateway-multi-site/multisite.png "múltiples sitios")

## <a name="points-to-consider"></a>Puntos para tener en cuenta

**No podrá usar el Portal de Azure clásico para realizar cambios en esta red virtual.** En esta versión, debe realizar cambios en el archivo de configuración de red en lugar de usar el Portal de clásico de Azure. Si realiza cambios en el Portal de Azure clásica, deberá sobrescriben la configuración de referencia de múltiples sitios para esta red virtual. 

Debería sentirse bastante cómodo con el archivo de configuración de red en el momento en que se haya completado el procedimiento de múltiples sitios. Sin embargo, si tiene varias personas que trabajan en la configuración de red, debe asegurarse de que todo el mundo sabe acerca de esta limitación. Esto no significa que no puede usar el Portal de clásico de Azure en absoluto. Puede usarlo para todo lo demás, excepto realizar cambios en esta red virtual determinada configuración.

## <a name="before-you-begin"></a>Antes de empezar

Antes de comenzar la configuración, compruebe que tiene los siguientes:

- Una suscripción de Azure. Si todavía no tiene una suscripción de Azure, puede activar los [beneficios de suscriptor MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o signo hacia arriba para obtener una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial/).

- Hardware VPN compatible para cada local ubicación. Consulte [Acerca de los dispositivos VPN para la conectividad de red Virtual](vpn-gateway-about-vpn-devices.md) para comprobar si el dispositivo que desea usar es algo que sabe que son compatibles.

- Una dirección IP IPv4 pública orientada externamente para cada dispositivo VPN. No se puede encontrar la dirección IP tras un NAT. Este requisito es.

- Debe instalar la última versión de los cmdlets de PowerShell de Azure. Para obtener más información sobre la instalación de los cmdlets de PowerShell, vea [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) .

- Alguien expertos en configurar el hardware VPN. No podrá usar los scripts VPN generado automáticamente desde el Portal de clásico de Azure para configurar los dispositivos VPN. Esto significa que debe tener un mayor entendimiento de cómo configurar el dispositivo VPN o trabajar con alguien que sí.

- Los intervalos de direcciones IP que desea usar para su red virtual (si ya no ha creado una). 

- Los intervalos de direcciones IP para cada uno de los sitios de la red local que se va a conectar. Debe asegurarse de que la dirección IP rangos para cada uno de los sitios de la red local que desea conectarse para que no se superponen. En caso contrario, el Portal de Azure clásico o la API de REST rechaza la configuración que se está cargando. 

    Por ejemplo, si tiene dos sitios de la red local que contienen la IP dirección rango 10.2.3.0/24 y tiene un paquete con una dirección de destino 10.2.3.3, Azure no sabe qué sitio que desea enviar el paquete, ya que se superponen los intervalos de direcciones. Para evitar problemas de enrutamiento, Azure no le permite cargar un archivo de configuración que tiene intervalos superpuestos.



## <a name="1-create-a-site-to-site-vpn"></a>1. crear una VPN de sitio a otro

Si ya tiene una VPN de sitio a sitio con una puerta de enlace enrutamiento dinámico genial! Puede continuar para [exportar los valores de configuración de red virtual](#export). Si no es así, haga lo siguiente:

### <a name="if-you-already-have-a-site-to-site-virtual-network-but-it-has-a-static-policy-based-routing-gateway"></a>Si ya tiene una red virtual de sitio a otro, pero tiene una estático (según directiva) enrutamiento puerta de enlace:

1. Cambie el tipo de puerta de enlace enrutamiento dinámico. Una VPN múltiples sitios requiere un dinámico (también conocido como basada en ruta) enrutamiento puerta de enlace. Para cambiar el tipo de puerta de enlace, debe eliminar la puerta de enlace existente en primer lugar, a continuación, cree uno nuevo. Para obtener instrucciones, consulte [cómo cambiar el tipo de enrutamiento VPN de la puerta de enlace](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md#how-to-change-the-vpn-routing-type-for-your-gateway).  

2. Configurar la puerta de enlace nuevo y crear el túnel VPN. Para obtener instrucciones, vea [Configurar una puerta de enlace VPN en el Portal de clásico de Azure](vpn-gateway-configure-vpn-gateway-mp.md). En primer lugar, cambie el tipo de puerta de enlace enrutamiento dinámico. 

### <a name="if-you-dont-have-a-site-to-site-virtual-network"></a>Si no tiene una red virtual de sitio a sitio:

1. Crear su red virtual de sitio a sitio siguiendo estas instrucciones: [crear una red Virtual con una conexión VPN de sitio a sitio en el Portal de clásico de Azure](vpn-gateway-site-to-site-create.md).  

2. Configurar una puerta de enlace enrutamiento dinámico con estas instrucciones: [Configurar una puerta de enlace VPN](vpn-gateway-configure-vpn-gateway-mp.md). Asegúrese de seleccionar el **enrutamiento dinámico** para el tipo de puerta de enlace.

## <a name="export"></a>2. exportar el archivo de configuración de red 

Exportar el archivo de configuración de red. El archivo que exporte se utilizará para configurar su nuevo sitio múltiples. Si necesita instrucciones sobre cómo exportar un archivo, consulte la sección en el artículo: [cómo crear un VNet con un archivo de configuración de red en el Portal de Azure](../virtual-network/virtual-networks-create-vnet-classic-portal.md#how-to-create-a-vnet-using-a-network-config-file-in-the-azure-portal). 

## <a name="3-open-the-network-configuration-file"></a>3. Abra el archivo de configuración de red

Abra el archivo de configuración de red que ha descargado en el último paso. Utilice un editor xml que le guste. El archivo debe ser similar al siguiente:

        <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
          <VirtualNetworkConfiguration>
            <LocalNetworkSites>
              <LocalNetworkSite name="Site1">
                <AddressSpace>
                  <AddressPrefix>10.0.0.0/16</AddressPrefix>
                  <AddressPrefix>10.1.0.0/16</AddressPrefix>
                </AddressSpace>
                <VPNGatewayAddress>131.2.3.4</VPNGatewayAddress>
              </LocalNetworkSite>
              <LocalNetworkSite name="Site2">
                <AddressSpace>
                  <AddressPrefix>10.2.0.0/16</AddressPrefix>
                  <AddressPrefix>10.3.0.0/16</AddressPrefix>
                </AddressSpace>
                <VPNGatewayAddress>131.4.5.6</VPNGatewayAddress>
              </LocalNetworkSite>
            </LocalNetworkSites>
            <VirtualNetworkSites>
              <VirtualNetworkSite name="VNet1" AffinityGroup="USWest">
                <AddressSpace>
                  <AddressPrefix>10.20.0.0/16</AddressPrefix>
                  <AddressPrefix>10.21.0.0/16</AddressPrefix>
                </AddressSpace>
                <Subnets>
                  <Subnet name="FE">
                    <AddressPrefix>10.20.0.0/24</AddressPrefix>
                  </Subnet>
                  <Subnet name="BE">
                    <AddressPrefix>10.20.1.0/24</AddressPrefix>
                  </Subnet>
                  <Subnet name="GatewaySubnet">
                    <AddressPrefix>10.20.2.0/29</AddressPrefix>
                  </Subnet>
                </Subnets>
                <Gateway>
                  <ConnectionsToLocalNetwork>
                    <LocalNetworkSiteRef name="Site1">
                      <Connection type="IPsec" />
                    </LocalNetworkSiteRef>
                  </ConnectionsToLocalNetwork>
                </Gateway>
              </VirtualNetworkSite>
            </VirtualNetworkSites>
          </VirtualNetworkConfiguration>
        </NetworkConfiguration>

## <a name="4-add-multiple-site-references"></a>4. agregar varias referencias de sitio

Al agregar o quitar la información de referencia del sitio, deberá realizar cambios de configuración en el ConnectionsToLocalNetwork/LocalNetworkSiteRef. Agregar un nuevo desencadenadores de referencia Azure para crear un nuevo túnel de sitio local. En el ejemplo siguiente, la configuración de red es para una conexión de un solo sitio. Una vez que haya terminado de realizar los cambios, guarde el archivo.

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

    To add additional site references (create a multi-site configuration), simply add additional "LocalNetworkSiteRef" lines, as shown in the example below: 

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Site2"><Connection type="IPsec" /></LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

## <a name="5-import-the-network-configuration-file"></a>5. importar el archivo de configuración de red

Importar el archivo de configuración de red. Al importar este archivo con los cambios, se agregará la nuevos túneles. Los túneles utilizará la puerta de enlace dinámico que creó anteriormente. Si necesita instrucciones sobre cómo importar el archivo, consulte la sección en el artículo: [cómo crear un VNet con un archivo de configuración de red en el Portal de Azure](../virtual-network/virtual-networks-create-vnet-classic-portal.md#how-to-create-a-vnet-using-a-network-config-file-in-the-azure-portal). 

## <a name="6-download-keys"></a>6. descargar claves

Una vez que haya agregado la nuevos túneles, use el cmdlet de PowerShell `Get-AzureVNetGatewayKey` para obtener las claves previamente compartidas de IPsec/IKE para cada túnel.

Por ejemplo:

    Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site1"

    Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site2"

Si lo prefiere, también puede usar la API de REST de *Obtener Virtual red puerta de enlace clave compartida* para obtener las claves previamente compartidas.

## <a name="7-verify-your-connections"></a>7. Compruebe las conexiones

Comprobar el estado de túnel de múltiples sitios. Después de descargar las claves para cada túnel, desee comprobar las conexiones. Usar `Get-AzureVnetConnection` para obtener una lista de túneles de red virtual, tal como se muestra en el ejemplo siguiente. VNet1 es el nombre de la VNet.

    Get-AzureVnetConnection -VNetName VNET1
        
    ConnectivityState         : Connected
    EgressBytesTransferred    : 661530
    IngressBytesTransferred   : 519207
    LastConnectionEstablished : 5/2/2014 2:51:40 PM
    LastEventID               : 23401
    LastEventMessage          : The connectivity state for the local network site 'Site1' changed from Not Connected to Connected.
    LastEventTimeStamp        : 5/2/2014 2:51:40 PM
    LocalNetworkSiteName      : Site1
    OperationDescription      : Get-AzureVNetConnection
    OperationId               : 7f68a8e6-51e9-9db4-88c2-16b8067fed7f
    OperationStatus           : Succeeded
        
    ConnectivityState         : Connected
    EgressBytesTransferred    : 789398
    IngressBytesTransferred   : 143908
    LastConnectionEstablished : 5/2/2014 3:20:40 PM
    LastEventID               : 23401
    LastEventMessage          : The connectivity state for the local network site 'Site2' changed from Not Connected to Connected.
    LastEventTimeStamp        : 5/2/2014 2:51:40 PM
    LocalNetworkSiteName      : Site2
    OperationDescription      : Get-AzureVNetConnection
    OperationId               : 7893b329-51e9-9db4-88c2-16b8067fed7f
    OperationStatus           : Succeeded

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información acerca de las puertas de enlace VPN, consulte [Acerca de puertas de enlace VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

