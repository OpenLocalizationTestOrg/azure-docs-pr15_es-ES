<properties
   pageTitle="Configurar conexiones VPN Expressroute y a sitios que pueden coexistencia | Microsoft Azure"
   description="En este artículo se explica cómo configurar ExpressRoute y una conexión VPN de sitio a sitio que puede coexistencia para el modelo de implementación clásico."
   documentationCenter="na"
   services="expressroute"
   authors="charwen"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="charwen"/>

# <a name="configure-expressroute-and-site-to-site-coexisting-connections-for-the-classic-deployment-model"></a>Configurar conexiones de coexistentes ExpressRoute y sitio a otro para el modelo de implementación clásica


> [AZURE.SELECTOR]
- [PowerShell - Administrador de recursos](expressroute-howto-coexist-resource-manager.md)
- [PowerShell - clásica](expressroute-howto-coexist-classic.md)

Tener la capacidad de configurar VPN de sitio a sitio y ExpressRoute tiene varias ventajas. Puede configurar VPN de sitio a sitio como una ruta de acceso seguro migración tras error para ExressRoute o usar VPN de sitio a sitio para conectarse a los sitios que no están conectados a través de ExpressRoute. Trataremos los pasos para configurar ambos escenarios en este artículo. En este artículo se aplica al modelo de implementación clásico. Esta configuración no está disponible en el portal.

**Acerca de los modelos de implementación de Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

>[AZURE.IMPORTANT] Circuitos ExpressRoute deben estar configurados previamente antes de seguir las instrucciones a continuación. Asegúrese de que ha seguido a las guías para [crear un circuito ExpressRoute](expressroute-howto-circuit-classic.md) y [Configurar el enrutamiento](expressroute-howto-routing-classic.md) antes de seguir los pasos siguientes.

## <a name="limits-and-limitations"></a>Límites y limitaciones

- **No se admite el enrutamiento de tránsito.** No se pueden enrutar (a través de Azure) entre su red local conectado a través de VPN de sitio a sitio y su red local conectado a través de ExpressRoute.
- **No se admite el punto al sitio.** No se pueden habilitar las conexiones de VPN punto al sitio para el mismo VNet que está conectado a ExpressRoute. No pueden coexistencia punto al sitio VPN y ExpressRoute para el mismo VNet.
- **Túnel forzado no se puede habilitar en la puerta de enlace VPN de sitio a otro.** Solo "forzar" todo el tráfico de Internet enlazado a su red local a través de ExpressRoute.
- **No se admite la puerta de enlace SKU básica.** Debe utilizar una puerta de enlace no SKU básica para la [puerta de enlace de ExpressRoute](expressroute-about-virtual-network-gateways.md) y la [puerta de enlace VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).
- **Sólo basado en ruta puerta de enlace VPN es compatible.** Debe usar un basado en la ruta de [Puerta de enlace VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).
- **Ruta estática debe configurarse de la puerta de enlace VPN.** Si su red local está conectada a ExpressRoute y una VPN de sitio a sitio, debe tener una ruta estática configurada en su red local para enrutar la conexión VPN de sitio a sitio a Internet.
- **ExpressRoute puerta de enlace debe estar configurado en primer lugar.** En primer lugar debe crear la puerta de enlace ExpressRoute antes de agregar la puerta de enlace VPN de sitio a otro.

## <a name="configuration-designs"></a>Diseños de configuración

### <a name="configure-a-site-to-site-vpn-as-a-failover-path-for-expressroute"></a>Configurar una VPN de sitio a sitio como una ruta de migración tras error para ExpressRoute

Puede configurar una conexión VPN de sitio a sitio como una copia de seguridad para ExpressRoute. Esto se aplica solo a redes virtuales vinculadas a la ruta de interconexión privada Azure. No hay ninguna solución basado en VPN de migración tras error para los servicios accesible a través de Azure público y peerings de Microsoft. Circuito de ExpressRoute siempre es el vínculo principal. Datos fluirá a través de la ruta de acceso VPN de sitio a sitio solo si se produce un error en el circuito de ExpressRoute. 

![Coexistencia](media/expressroute-howto-coexist-classic/scenario1.jpg)

### <a name="configure-a-site-to-site-vpn-to-connect-to-sites-not-connected-through-expressroute"></a>Configurar una conexión VPN para conectarse a los sitios que no está conectados a través de ExpressRoute para sitios

Puede configurar su red donde algunos sitios se conectan directamente a Azure sobre VPN de sitio a sitio y algunos sitios a través de ExpressRoute. 

![Coexistencia](media/expressroute-howto-coexist-classic/scenario2.jpg)

>[AZURE.NOTE] No puede configurar una red virtual como enrutador tránsito.

## <a name="selecting-the-steps-to-use"></a>Selección de los pasos para usar

Hay dos conjuntos de procedimientos para elegir para configurar las conexiones que pueden coexistencia diferentes. El procedimiento de configuración que seleccione dependen de si tiene una red virtual existente que desea conectarse o que desea crear una nueva red virtual.


- No tiene un VNet y necesita crear uno.
    
    Si todavía no tiene una red virtual, este procedimiento le guiará a través de creación de una red virtual nueva mediante el modelo de implementación clásica y la creación de nuevas conexiones VPN ExpressRoute y sitio a otro. Para configurar, siga los pasos de la sección de artículo [para crear una nueva red virtual y coexistentes conexiones](#new).

- Ya tengo un modelo de implementación clásica VNet.

    Ya puede tener una red virtual en lugar de con una conexión VPN de sitio a sitio existente o una conexión de ExpressRoute. El artículo de la sección [Configurar conexiones de coexsiting para un VNet existente](#add) le guiará a través de eliminar la puerta de enlace y, a continuación, crear nuevas conexiones VPN ExpressRoute y sitio a otro. Tenga en cuenta que al crear nuevas conexiones, deben completar los pasos en un orden muy específico. No use las instrucciones que aparecen en otros artículos para crear las conexiones y puertas de enlace.

    En este procedimiento, crear conexiones que pueden coexistencia se requieren que se va a eliminar la puerta de enlace y a continuación, configure las puertas de enlace nuevos. Esto significa que tendrá el tiempo de inactividad para las conexiones entre local al eliminar y volver a crear la puerta de enlace y conexiones, pero no necesitará migrar máquinas virtuales o servicios a una red virtual nueva. Las máquinas virtuales y servicios aún podrán comunicarse fuera a través del equilibrador de carga mientras configura la puerta de enlace si están configurados para ello.


## <a name="new"></a>Para crear una nueva red virtual y conexiones coexistentes

Este procedimiento se le guíe durante la creación de un VNet y crear sitio a sitio y las conexiones de ExpressRoute se coexisten.

1. Debe instalar la última versión de los cmdlets de PowerShell de Azure. Para obtener más información sobre la instalación de los cmdlets de PowerShell, vea [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) . Tenga en cuenta que los cmdlets que se usará para esta configuración puede ser un poco distintos de lo que es posible que esté familiarizado con. Asegúrese de usar los cmdlets especificados en estas instrucciones. 

2. Crear un esquema para su red virtual. Para obtener más información sobre el esquema de configuración, vea [esquema de configuración de red Virtual de Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx).

    Cuando se crea el esquema, asegúrese de que usar los siguientes valores:

    - La subred de puerta de enlace de la red virtual debe ser /27 o un prefijo más corto (como /26 o /25).
    - El tipo de conexión de la puerta de enlace está "dedicado".

              <VirtualNetworkSite name="MyAzureVNET" Location="Central US">
                <AddressSpace>
                  <AddressPrefix>10.17.159.192/26</AddressPrefix>
                </AddressSpace>
                <Subnets>
                  <Subnet name="Subnet-1">
                    <AddressPrefix>10.17.159.192/27</AddressPrefix>
                  </Subnet>
                  <Subnet name="GatewaySubnet">
                    <AddressPrefix>10.17.159.224/27</AddressPrefix>
                  </Subnet>
                </Subnets>
                <Gateway>
                  <ConnectionsToLocalNetwork>
                    <LocalNetworkSiteRef name="MyLocalNetwork">
                      <Connection type="Dedicated" />
                    </LocalNetworkSiteRef>
                  </ConnectionsToLocalNetwork>
                </Gateway>
              </VirtualNetworkSite>

3. Después de crear y configurar el archivo de esquema xml, cargue el archivo. Esta opción creará su red virtual.

    Usar el siguiente cmdlet para cargar el archivo, reemplace el valor por su propio.

        Set-AzureVNetConfig -ConfigurationPath 'C:\NetworkConfig.xml'

4. <a name="gw"></a>Crear una puerta de enlace ExpressRoute. Asegúrese de especificar el GatewaySKU como *estándar*, de *alto rendimiento*, o *UltraPerformance* y la GatewayType como *DynamicRouting*.

    Use el siguiente ejemplo, la sustitución de los valores de sus propios.

        New-AzureVNetGateway -VNetName MyAzureVNET -GatewayType DynamicRouting -GatewaySKU HighPerformance

5. Vínculo de la puerta de enlace ExpressRoute al circuito ExpressRoute. Una vez se haya completado este paso, se establece la conexión entre su red local y Azure a través de ExpressRoute.

        New-AzureDedicatedCircuitLink -ServiceKey <service-key> -VNetName MyAzureVNET

6. <a name="vpngw"></a>A continuación, cree la puerta de enlace VPN de sitio a otro. El GatewaySKU debe ser *estándar*, de *alto rendimiento*, o *UltraPerformance* y la GatewayType deben ser *DynamicRouting*.

        New-AzureVirtualNetworkGateway -VNetName MyAzureVNET -GatewayName S2SVPN -GatewayType DynamicRouting -GatewaySKU  HighPerformance

    Para recuperar la configuración de puerta de enlace de red virtual, incluido el ID de puerta de enlace y la dirección IP pública, utilice la `Get-AzureVirtualNetworkGateway` cmdlet.

        Get-AzureVirtualNetworkGateway

        GatewayId            : 348ae011-ffa9-4add-b530-7cb30010565e
        GatewayName          : S2SVPN
        LastEventData        :
        GatewayType          : DynamicRouting
        LastEventTimeStamp   : 5/29/2015 4:41:41 PM
        LastEventMessage     : Successfully created a gateway for the following virtual network: GNSDesMoines
        LastEventID          : 23002
        State                : Provisioned
        VIPAddress           : 104.43.x.y
        DefaultSite          :
        GatewaySKU           : HighPerformance
        Location             :
        VnetId               : 979aabcf-e47f-4136-ab9b-b4780c1e1bd5
        SubnetId             :
        EnableBgp            : False
        OperationDescription : Get-AzureVirtualNetworkGateway
        OperationId          : 42773656-85e1-a6b6-8705-35473f1e6f6a
        OperationStatus      : Succeeded

7. Crear un sitio local entidad de puerta de enlace VPN. Este comando no configura la puerta de enlace VPN local. En su lugar, permita que proporcione la configuración de puerta de enlace local, como la dirección IP pública y local en el espacio de direcciones, para que la puerta de enlace de Azure VPN puedan conectarse a él.

    >[AZURE.IMPORTANT] El sitio local para la VPN de sitio a sitio no está definido en la netcfg. En su lugar, debe usar este cmdlet para especificar los parámetros de sitio local. No se puede definir mediante portal o el archivo netcfg.

    Use el siguiente ejemplo, reemplazando los valores con su propio.

        New-AzureLocalNetworkGateway -GatewayName MyLocalNetwork -IpAddress <MyLocalGatewayIp> -AddressSpace <MyLocalNetworkAddress>

    > [AZURE.NOTE] Si su red local tiene varias rutas, puede pasar en las que como una matriz.  $MyLocalNetworkAddress =@("10.1.2.0/24","10.1.3.0/24","10.2.1.0/24")  


    Para recuperar la configuración de puerta de enlace de red virtual, incluido el ID de puerta de enlace y la dirección IP pública, utilice la `Get-AzureVirtualNetworkGateway` cmdlet. Vea el siguiente ejemplo.

        Get-AzureLocalNetworkGateway

        GatewayId            : 532cb428-8c8c-4596-9a4f-7ae3a9fcd01b
        GatewayName          : MyLocalNetwork
        IpAddress            : 23.39.x.y
        AddressSpace         : {10.1.2.0/24}
        OperationDescription : Get-AzureLocalNetworkGateway
        OperationId          : ddc4bfae-502c-adc7-bd7d-1efbc00b3fe5
        OperationStatus      : Succeeded


8. Configurar el dispositivo VPN local para conectarse a la nueva puerta de enlace. Use la información que haya encontrado en el paso 6 al configurar el dispositivo VPN. Para obtener más información sobre la configuración de dispositivo VPN, consulte [Configuración de dispositivo VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md).

9. Vincular la puerta de enlace VPN de sitio a sitio en Azure a la puerta de enlace local.

    En este ejemplo, connectedEntityId es el identificador de la puerta de enlace local, que puede encontrar ejecutando `Get-AzureLocalNetworkGateway`. Puede buscar virtualNetworkGatewayId mediante la `Get-AzureVirtualNetworkGateway` cmdlet. Después de este paso, se establece la conexión entre su red local y Azure a través de la conexión VPN de sitio a otro.


        New-AzureVirtualNetworkGatewayConnection -connectedEntityId <local-network-gateway-id> -gatewayConnectionName Azure2Local -gatewayConnectionType IPsec -sharedKey abc123 -virtualNetworkGatewayId <azure-s2s-vpn-gateway-id>

## <a name="add"></a>Configurar conexiones de coexsiting para un VNet ya existente

Si tiene una red virtual existente, compruebe el tamaño de la subred de puerta de enlace. Si la subred de puerta de enlace es /28 o /29, primero debe eliminar la puerta de enlace de red virtual y aumentar el tamaño de la subred de puerta de enlace. Los pasos descritos en esta sección le mostrará cómo hacerlo.

Si la subred de puerta de enlace es /27 o mayor y la red virtual está conectada a través de ExpressRoute, puede omitir los pasos siguientes y continúe en ["Paso 6: crear una puerta de enlace VPN de sitio a sitio"](#vpngw) en la sección anterior.

>[AZURE.NOTE] Al eliminar la puerta de enlace existente, sus instalaciones locales perderán la conexión con su red virtual mientras está trabajando en esta configuración.

1. Debe instalar la última versión de los cmdlets de PowerShell de administrador de recursos de Azure. Para obtener más información sobre la instalación de los cmdlets de PowerShell, vea [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) . Tenga en cuenta que los cmdlets que se usará para esta configuración puede ser un poco distintos de lo que es posible que esté familiarizado con. Asegúrese de usar los cmdlets especificados en estas instrucciones. 

2. Eliminar la puerta de enlace VPN de sitio a otro o ExpressRoute existente. Usar el cmdlet siguiente, reemplazando los valores por la suya propia.

        Remove-AzureVNetGateway –VnetName MyAzureVNET

3. Exportar el esquema de una red virtual. Use el siguiente cmdlet de PowerShell, reemplazando los valores por la suya propia.

        Get-AzureVNetConfig –ExportToFile “C:\NetworkConfig.xml”

4. Editar el esquema de archivo de configuración de red para que la subred de puerta de enlace es /27 o un prefijo más corto (como /26 o /25). Vea el siguiente ejemplo. 
>[AZURE.NOTE] Si no tiene suficientes direcciones IP a la izquierda de su red virtual para aumentar el tamaño de la subred de puerta de enlace, debe agregar más espacio de direcciones IP. Para obtener más información sobre el esquema de configuración, vea [esquema de configuración de red Virtual de Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx).

          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.17.159.224/27</AddressPrefix>
          </Subnet>

5. Si la puerta de enlace anterior era una VPN de sitio a otro, también debe cambiar el tipo de conexión **dedicado**.

                 <Gateway>
                  <ConnectionsToLocalNetwork>
                    <LocalNetworkSiteRef name="MyLocalNetwork">
                      <Connection type="Dedicated" />
                    </LocalNetworkSiteRef>
                  </ConnectionsToLocalNetwork>
                </Gateway>

6. En este momento, tendrá un VNet con ningún puertas de enlace. Para crear nuevas puertas de enlace y completar las conexiones, puede continuar con el [paso 4: crear una puerta de enlace de ExpressRoute](#gw), que se encuentra en el conjunto de pasos anterior.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre ExpressRoute, consulte las [Preguntas más frecuentes de ExpressRoute](expressroute-faqs.md)
