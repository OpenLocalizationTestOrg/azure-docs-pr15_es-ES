<properties
   pageTitle="Implementar una arquitectura de red altamente disponible híbrido | Microsoft Azure"
   description="Cómo implementar una arquitectura de red de sitio a sitio segura que abarca una red virtual Azure y una red local conectado mediante ExpressRoute con migración tras error de puerta de enlace VPN."
   services="guidance,virtual-network,vpn-gateway,expressroute"
   documentationCenter="na"
   authors="telmosampaio"
   manager="christb"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="telmos"/>

# <a name="implementing-a-highly-available-hybrid-network-architecture"></a>Implementar una arquitectura de red híbrida altamente disponible

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

En este artículo se describe los procedimientos recomendados para conectar una red local a redes virtuales en Azure mediante ExpressRoute, con una sitio a otro red privada virtual (VPN) como una conexión de migración tras error. Flujo del tráfico entre la red local y una red virtual Azure (VNet) mediante una conexión de ExpressRoute.  Si hay una pérdida de conectividad en el circuito de ExpressRoute, el tráfico se se enrutan a través de un túnel VPN IPSec.

> [AZURE.NOTE] Azure tiene dos diferentes modelos de implementación: [El Administrador de recursos] [ resource-manager-overview] y clásica. Esta guía utiliza el Administrador de recursos, Microsoft recomienda para implementaciones nuevas.

Casos de uso común para esta arquitectura incluyen:

- Aplicaciones de híbridas donde las cargas de trabajo se distribuyen entre una red local y Azure.

- Aplicaciones que se ejecutan a gran escala, críticas cargas de trabajo que requieren un alto grado de escalabilidad.

- Grandes instalaciones de copia de seguridad y restauración de los datos que se deben guardar sin conexión.

- Manejar cargas de trabajo de datos grande.

- Uso de Azure como un sitio de recuperación.

Tenga en cuenta que si el circuito ExpressRoute no está disponible, la ruta VPN sólo controlará conexiones interconexión privadas. Interconexión pública y Microsoft interconexión conexiones pasarán a través de Internet.

## <a name="architecture-diagram"></a>Diagrama de arquitectura

>[AZURE.NOTE] El [servicio de puerta de enlace de Azure VPN] [ azure-vpn-gateway] implementa dos tipos de puertas de enlace de red virtual; Puertas de enlace de red virtual VPN y puertas de enlace de red virtual ExpressRoute. En este documento, el término a la *Puerta de enlace VPN* hace referencia al servicio de Azure, mientras las frases de *puerta de enlace de red virtual VPN* y *puerta de enlace de red virtual ExpressRoute* se usan para hacer referencia a las implementaciones VPN y ExpressRoute de la puerta de enlace respectivamente.

El siguiente diagrama resalta los componentes importantes en esta arquitectura:

> Un documento de Visio que incluye este diagrama de arquitectura está disponible para su descarga en el [Centro de descarga de Microsoft][visio-download]. Este diagrama es "red híbrida - RE VPN" página.

![[0]][0]

- **Redes virtuales Azure (VNets).** Cada VNet reside en una sola región de Azure y puede hospedar varios niveles de la aplicación. Niveles de la aplicación se pueden segmentar con subredes en cada VNet o grupos de seguridad (NSGs) de red.

- **Red corporativa local.** Se trata de una red de equipos y dispositivos, conectados a través de una red de área local privada ejecutando dentro de una organización.

- **Dispositivo VPN.** Se trata de un dispositivo o servicio que proporciona conectividad externa a la red local. El dispositivo VPN puede ser un dispositivo de hardware, o puede ser una solución de software como el enrutamiento y acceso remoto (RRAS) en Windows Server 2012.

> [AZURE.NOTE] Para obtener una lista de dispositivos de VPN compatibles e información sobre cómo configurar dispositivos de VPN seleccionados para conectarse a una puerta de enlace de VPN de Azure, consulte las instrucciones del dispositivo adecuado en la [lista de dispositivos VPN compatibles con Azure][vpn-appliance].

- **Puerta de enlace de red virtual VPN.** La puerta de enlace de red virtual VPN habilita la VNet para conectarse al dispositivo VPN en la red local. La puerta de enlace de red virtual VPN está configurado para aceptar las solicitudes de la red local sólo a través del dispositivo VPN. Para obtener más información, vea [Conectar una red local a una red virtual de Microsoft Azure][connect-to-an-Azure-vnet].

- **Puerta de enlace de red virtual ExpressRoute.** La puerta de enlace de red virtual ExpressRoute habilita la VNet conectar con el circuito de ExpressRoute usado para conectividad con su red local.

- **Subred de puerta de enlace.** Las puertas de enlace de red virtual se encuentran en la misma subred.

- **Conexión VPN.** La conexión tiene propiedades que especifican el tipo de conexión (IPSec) y la clave que se comparte con el dispositivo de VPN local para cifrar el tráfico.

- **Circuito de ExpressRoute.** Se trata de un nivel 2 o circuito de nivel 3 proporcionado por el proveedor de servicios de conectividad de red combinaciones la locales con Azure a través de los enrutadores de borde. El circuito utiliza la infraestructura de hardware administrada por el proveedor de servicios de conectividad.

- **Aplicación de nube de capas.** Esta es la aplicación hospedada en Azure. Es posible que incluya varios niveles, con varias subredes conectados a través de equilibradores de carga de Azure. El tráfico en cada subred puede ser sujeto a las reglas definidas mediante el uso de [Grupos de seguridad de red de Azure][azure-network-security-group](NSGs). Para obtener más información, vea [Introducción a la seguridad de Microsoft Azure][getting-started-with-azure-security].

## <a name="recommendations"></a>Recomendaciones

Azure ofrece numerosos recursos distintos y tipos de recursos, por lo que puede ser esta arquitectura de referencia se aprovisione varias formas diferentes. Proporcionamos una plantilla de administrador de recursos de Azure para instalar la arquitectura de referencia que sigue estas recomendaciones. Si elige crear su propia arquitectura de referencia debería seguir estas recomendaciones a menos que tenga un requisito específico que no se ajusta una recomendación.

### <a name="vnet-and-gatewaysubnet"></a>VNet y GatewaySubnet

Crear la puerta de enlace de red virtual ExpressRoute y la puerta de enlace de red virtual VPN en el mismo VNet. Esto significa que debe compartir la misma subred denominada **GatewaySubnet**

Si el VNet ya incluye una subred denominada **GatewaySubnet**, asegúrese de que tiene un /27 o más espacio de direcciones. Si la subred existente es muy pequeña, quitar de manera y cree uno nuevo como se muestra en la siguiente viñeta:

```powershell
$vnet = Get-AzureRmVirtualNetworkGateway -Name <yourvnetname> -ResourceGroupName <yourresourcegroup>
Remove-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet
```

Si el VNet no contiene una subred denominada **GatewaySubnet**, cree una nueva como sigue:

```powershell
$vnet = Get-AzureRmVirtualNetworkGateway -Name <yourvnetname> -ResourceGroupName <yourresourcegroup>
Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.224/27"
$vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

### <a name="vpn-and-expressroute-gateways"></a>Puertas de enlace VPN y ExpressRoute

Compruebe que su organización cumple los [requisitos previos de ExpressRoute] [ expressroute-prereq] para conectarse a Azure.

Si ya tiene una puerta de enlace de red virtual VPN en su VNet de Azure, elimine, tal como se muestra a continuación.

```powershell
Remove-AzureRmVirtualNetworkGateway -Name <yourgatewayname> -ResourceGroupName <yourresourcegroup>
```

Siga las instrucciones de [implementación de una arquitectura de red híbrido con Azure ExpressRoute] [ implementing-expressroute] para establecer la conexión de ExpressRoute.

Siga las instrucciones de [implementación de una arquitectura de red híbrido con Azure y VPN local] [ implementing-vpn] para establecer la conexión VPN de puerta de enlace de red virtual.

Después de establecer las conexiones de puerta de enlace de red virtual, pruebe el entorno como la siguiente:

1. Asegúrese de que puede conectarse a su VNet de Azure desde su red local.

2. Póngase en contacto con su proveedor para detener la conectividad de ExpressRoute para realizar pruebas.

3. Compruebe que la puede aún conectarse desde su red local a su VNet de Azure mediante la conexión VPN de puerta de enlace de red virtual.

4. Póngase en contacto con su proveedor de reestabilish ExpressRoute conectividad.

## <a name="considerations"></a>Consideraciones

Consideraciones al usar ExpressRoute, vea la [implementación de una arquitectura de red híbrido con Azure ExpressRoute] [ guidance-expressroute] orientación.

Por motivos de VPN de sitio a sitio, vea la [implementación de una arquitectura de red híbrido con Azure y VPN local] [ guidance-vpn] orientación.

Por motivos de seguridad de Azure general, vea [Servicios de nube de Microsoft y seguridad de la red][best-practices-security].

## <a name="solution-deployment"></a>Implementación de soluciones

Si tiene una infraestructura local existente ya está configurada con un dispositivo de red adecuado, puede implementar la arquitectura de referencia siguiendo estos pasos:

1. Haga clic con el botón secundario del mouse en el botón de abajo y seleccione uno "Abrir vínculo en una nueva pestaña" o "Abrir vínculo en ventana nueva":  
[![Implementar en Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-vpn-er%2Fazuredeploy.json)

2. Espere a que el vínculo para abrir en el portal de Azure, a continuación, siga estos pasos: 
  - El nombre del **grupo de recursos** ya está definido en el archivo de parámetros, así que seleccione **Crear nuevo** y escriba `ra-hybrid-vpn-er-rg` en el cuadro de texto.
  - Seleccione la región en el cuadro desplegable de **ubicación** .
  - No modifique la **Plantilla raíz Uri** o los cuadros de texto de **Uri raíz del parámetro** .
  - Revise los términos y condiciones y luego haga clic en la casilla de verificación **que acepto los términos y condiciones indicadas arriba** .
  - Haga clic en el botón de **compra** .

3. Espere a que la implementación completar.

4. Haga clic con el botón secundario del mouse en el botón de abajo y seleccione uno "Abrir vínculo en una nueva pestaña" o "Abrir vínculo en ventana nueva":  
[![Implementar en Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-vpn-er%2Fazuredeploy-expressRouteCircuit.json)

5. Espere a que el vínculo abrir en el portal de Azure, a continuación, escriba, a continuación, siga estos pasos: 
  - Seleccione **usar una existente** en la sección de **grupo de recursos** y escriba `ra-hybrid-vpn-er-rg` en el cuadro de texto.
  - Seleccione la región en el cuadro desplegable de **ubicación** .
  - No modifique la **Plantilla raíz Uri** o los cuadros de texto de **Uri raíz del parámetro** .
  - Revise los términos y condiciones y luego haga clic en la casilla de verificación **que acepto los términos y condiciones indicadas arriba** .
  - Haga clic en el botón de **compra** .

<!-- links -->

[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[vpn-appliance]: ../vpn-gateway/vpn-gateway-about-vpn-devices.md
[azure-vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[connect-to-an-Azure-vnet]: https://technet.microsoft.com/library/dn786406.aspx
[azure-network-security-group]: ../virtual-network/virtual-networks-nsg.md
[getting-started-with-azure-security]: ./../security/azure-security-getting-started.md
[expressroute-prereq]: ../expressroute/expressroute-prerequisites.md
[implementing-expressroute]: ./guidance-hybrid-network-expressroute.md#implementing-this-architecture
[implementing-vpn]: ./guidance-hybrid-network-vpn.md#implementing-this-architecture
[guidance-expressroute]: ./guidance-hybrid-network-expressroute.md
[guidance-vpn]: ./guidance-hybrid-network-vpn.md
[best-practices-security]: ../best-practices-network-security.md
[solution-script]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn-er/Deploy-ReferenceArchitecture.ps1
[solution-script-bash]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn-er/deploy-reference-architecture.sh
[vnet-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn-er/parameters/virtualNetwork.parameters.json
[virtualnetworkgateway-vpn-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn-er/parameters/virtualNetworkGateway-vpn.parameters.json
[virtualnetworkgateway-expressroute-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn-er/parameters/virtualNetworkGateway-expressRoute.parameters.json
[er-circuit-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn-er/parameters/expressRouteCircuit.parameters.json
[azure-powershell-download]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[naming conventions]: ./guidance-naming-conventions.md
[azure-cli]: https://azure.microsoft.com/documentation/articles/xplat-cli-install/
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[0]: ./media/blueprints/hybrid-network-expressroute-vpn-failover.png "Arquitectura de una arquitectura de red híbrida altamente disponible mediante VPN y ExpressRoute puerta de enlace"
[ARM-Templates]: https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/
