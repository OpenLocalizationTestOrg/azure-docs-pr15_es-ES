<properties
   pageTitle="Implementar una arquitectura de red híbrido con Azure ExpressRoute | Arquitectura de referencia | Microsoft Azure"
   description="¿Cómo implementar una arquitectura de red de sitio a sitio segura abarca una red virtual Azure y una red local se conecta a través de Azure ExpressRoute."
   services=""
   documentationCenter="na"
   authors="telmosampaio"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="telmos"/>

# <a name="implementing-a-hybrid-network-architecture-with-azure-expressroute"></a>Implementar una arquitectura de red híbrido con Azure ExpressRoute

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Este artículo describe los procedimientos recomendados para conectar una red local a redes virtuales en Azure mediante ExpressRoute. ExpressRoute conexiones se realizan mediante una conexión privada dedicada a través de un proveedor de servicios de conectividad de terceros. La conexión privada extiende la red local en Azure proporcionando acceso a su propia infraestructura de IaaS en Azure, públicos extremos utilizados en los servicios de PaaS y SaaS de Office 365. Este documento se centra en usar ExpressRoute para conectarse a una sola Azure una red virtual (VNet) con lo que se denomina interconexión privada.

> [AZURE.NOTE] Azure tiene dos diferentes modelos de implementación: [El Administrador de recursos] [ resource-manager-overview] y clásica. Esta guía utiliza el Administrador de recursos, Microsoft recomienda para implementaciones nuevas.

Casos de uso común para esta arquitectura incluyen:

- Aplicaciones de híbridas donde las cargas de trabajo se distribuyen entre una red local y Azure.

- Aplicaciones que se ejecutan a gran escala, críticas cargas de trabajo que requieren un alto grado de escalabilidad.

- Grandes instalaciones de copia de seguridad y restauración de los datos que se deben guardar sin conexión.

- Manejar cargas de trabajo de datos grande.

- Uso de Azure como un sitio de recuperación.

> [AZURE.NOTE] [Información general técnica de ExpressRoute] [ expressroute-technical-overview] proporciona una introducción a ExpressRoute.

## <a name="architecture-diagram"></a>Diagrama de arquitectura

El siguiente diagrama resalta los componentes importantes en esta arquitectura:

> Un documento de Visio que incluye este diagrama de arquitectura está disponible para su descarga en el [Centro de descarga de Microsoft][visio-download]. Este diagrama muestra en la página "Híbrido de red - ro".

![[0]][0]

- **Redes virtuales Azure (VNets).** Cada VNet reside en una sola región de Azure y puede hospedar varios niveles de la aplicación. Niveles de la aplicación se pueden segmentar con subredes en cada VNet o grupos de seguridad (NSGs) de red. 

- **Servicios de Azure públicos.** Estos son los servicios de Azure que pueden utilizarse dentro de una aplicación híbrida. Estos servicios también están disponibles a través de Internet pública, pero obtener acceso a ellos a través de un circuito ExpressRoute proporciona baja latencia y rendimiento más predecible ya que el tráfico no vaya a través de Internet. Las conexiones se realizan mediante **interconexión pública**, con las direcciones que pertenecen a su organización o le proporcionó su proveedor de servicios de conectividad. 

- **Servicios de Office 365.** Estos son las aplicaciones de Office 365 esté disponibles públicamente y los servicios proporcionados por Microsoft. Las conexiones se realizan mediante **interconexión de Microsoft**, con las direcciones que pertenecen a su organización o le proporcionó su proveedor de servicios de conectividad.

>[AZURE.NOTE] También puede conectar directamente a Microsoft CRM Online a través de una interconexión de Microsoft.

- **Red corporativa local.** Se trata de una red de equipos y dispositivos, conectados a través de una red de área local privada ejecutando dentro de una organización.

- **Enrutadores de borde local.** Estos son enrutadores que conecta la red local al circuito administrado por el proveedor. Dependiendo de cómo se aprovisiona la conexión, debe proporcionar las direcciones IP públicas usadas por los enrutadores. 

- **Enrutadores de borde de Microsoft.** Estos son dos enrutadores en una configuración de activo activo altamente disponible. Estos enrutadores habilitar un proveedor de servicios de conectividad conectar sus circuitos directamente a su centro de datos. Dependiendo de cómo se aprovisiona la conexión, debe proporcionar las direcciones IP públicas usadas por los enrutadores.

- **Circuito de ExpressRoute.** Se trata de un nivel 2 o circuito de nivel 3 proporcionado por el proveedor de servicios de conectividad de red combinaciones la locales con Azure a través de los enrutadores de borde. El circuito utiliza la infraestructura de hardware administrada por el proveedor de servicios de conectividad.

- **Proveedores de conectividad.** Estos son compañías que proporcionan una conexión a través nivel 2 o conectividad de nivel 3 entre su centro de datos y un centro de datos de Azure.

## <a name="recommendations"></a>Recomendaciones

Azure ofrece numerosos recursos distintos y tipos de recursos, por lo que puede ser esta arquitectura de referencia se aprovisione varias formas diferentes. Proporcionamos una plantilla de administrador de recursos de Azure para instalar la arquitectura de referencia que sigue estas recomendaciones. Si elige crear su propia arquitectura de referencia debería seguir estas recomendaciones a menos que tenga un requisito específico que no se ajusta una recomendación.

### <a name="connectivity-providers"></a>Proveedores de conectividad

Seleccione un proveedor de servicios de conectividad ExpressRoute adecuado para su ubicación. Para obtener una lista de proveedores de conectividad disponibles en su ubicación, use el siguiente comando PowerShell Azure:

```powershell
Get-AzureRmExpressRouteServiceProvider
```

Proveedores de conectividad de ExpressRoute conectan su centro de datos a Microsoft de las siguientes maneras:

- **Ubicado en un cambio de la nube.** Si se encuentra conjunta en una instalación con un cambio de la nube, puede solicitar virtuales conexiones cruzadas en la nube de Microsoft a través de exchange de Ethernet del proveedor de la ubicación. Proveedores de ubicación compañeros pueden ofrecer conexiones cruzadas de nivel 2 o administrado Layer 3 conexiones cruzadas entre su infraestructura de la función de la ubicación y la nube de Microsoft...

- **Conexiones de punto a punto Ethernet.** Puede conectar los centros de datos locales/oficinas en la nube de Microsoft a través de vínculos de Ethernet de punto a punto. Proveedores de Ethernet de punto a punto pueden ofrecer conexiones de nivel 2 o administrado Layer 3 conexiones entre el sitio y la nube de Microsoft.

- **Redes de y a cualquier (IPVPN).** Puede integrar su WAN con la nube de Microsoft. Proveedores de IPVPN (normalmente MPLS VPN) ofrecen-cualquier conectividad entre sus sucursales y centros de datos. La nube de Microsoft puede estar interconectada a su WAN para darle el aspecto de cualquier sucursal. Proveedores de WAN normalmente ofrecen conectividad de nivel 3 administrado.

Para obtener más información acerca de los proveedores de conectividad, consulte [ExpressRoute circuitos y dominios de enrutamiento][connectivity-providers].

### <a name="expressroute-circuit"></a>ExpressRoute circuito

Asegurarse de que su organización cumple los [requisitos previos de ExpressRoute] [ expressroute-prereqs] para conectarse a Azure.

Si aún no lo ha hecho, agregue una subred denominada `GatewaySubnet` a su VNet de Azure y crear una puerta de enlace de red virtual ExpressRoute usando el servicio de puerta de enlace de Azure VPN. Para obtener más información sobre este proceso, vea [ExpressRoute flujos de trabajo para el aprovisionamiento de circuito y Estados de circuito][ExpressRoute-provisioning].

Crear un circuito ExpressRoute como sigue:

1. Ejecute el siguiente comando PowerShell:

    ```powershell
    New-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>> -Location <<location>> -SkuTier <<sku-tier>> -SkuFamily <<sku-family>> -ServiceProviderName <<service-provider-name>> -PeeringLocation <<peering-location>> -BandwidthInMbps <<bandwidth-in-mbps>>
    ```

2. Enviar la `ServiceKey` para el nuevo circuito al proveedor de servicios.

3. Espere a que el proveedor de aprovisionar el circuito. Puede comprobar el estado de aprovisionamiento de un circuito mediante el siguiente comando PowerShell:

    ```powershell
    Get-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>
    ```

La `Provisioning state` campo la `Service Provider` sección del resultado se cambiará de `NotProvisioned` a `Provisioned` si el circuito está listo.

>[AZURE.NOTE]Si está usando una conexión de nivel 3, el proveedor debe configurar y administrar el enrutamiento proporcionar la información necesaria para habilitar el proveedor implementar las rutas adecuadas.

Si está usando una conexión de nivel 2, siga los pasos siguientes:

1. Reserva dos/30 subredes se componen de direcciones IP públicas válidas para cada tipo de interconexión que desea implementar. Estos/30 subredes se utilizará para proporcionar direcciones IP para el enrutador utilizado para el circuito. Si está implementando privado, público e interconexión de Microsoft, deberá /30 6 subredes con direcciones IP públicas.     

2. Configurar el enrutamiento para el circuito ExpressRoute. Debe ejecutar el comando debajo de cada tipo de interconexión que desea configurar (privado, público y Microsoft).

    >[AZURE.NOTE]Consulte [crear y modificar el enrutamiento de un circuito ExpressRoute] [ configure-expressroute-routing] para obtener más detalles. Utilice los siguientes comandos de PowerShell para agregar una interconexión para enrutar el tráfico de red:

    ```powershell
    Set-AzureRmExpressRouteCircuitPeeringConfig -Name <<peering-name>> -Circuit <<circuit-name>> -PeeringType <<peering-type>> -PeerASN <<peer-asn>> -PrimaryPeerAddressPrefix <<primary-peer-address-prefix>> -SecondaryPeerAddressPrefix <<secondary-peer-address-prefix>> -VlanId <<vlan-id>>

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit <<circuit-name>>
    ```

3. Reservar otro grupo de direcciones IP públicas que usar para NAT en público e interconexión de Microsoft. Se recomienda tener una agrupación diferente para cada interconexión. Especificar el grupo a su proveedor de servicios de conectividad, por lo que pueden configurar anuncios BGP para los rangos.

[Vincular su VNet(s) privado en la nube al circuito ExpressRoute][link-vnet-to-expressroute]. Use los siguientes comandos de PowerShell:

```
$circuit = Get-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>
$gw = Get-AzureRmVirtualNetworkGateway -Name <<gateway-name>> -ResourceGroupName <<resource-group>>
New-AzureRmVirtualNetworkGatewayConnection -Name <<connection-name>> -ResourceGroupName <<resource-group>> -Location <<location> -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute
```

Tenga en cuenta los siguientes puntos:

- ExpressRoute usa el protocolo de puerta de enlace de borde (BGP) para intercambiar información de enrutamiento entre la red y Azure.

- Puede conectar varios VNets que se encuentra en diferentes regiones hasta el mismo circuito ExpressRoute mientras se encuentran dentro de la misma región geopolíticas VNets todos y el circuito de ExpressRoute.

## <a name="scalability-considerations"></a>Consideraciones de escalabilidad

ExpressRoute circuitos proporcionan una ruta de ancho de banda alto entre redes. En general, cuanto mayor sea el ancho de banda mayor será el costo. Aunque algunos proveedores permiten cambiar el ancho de banda, asegúrese de que elegir un ancho de banda inicial que supera a sus necesidades y proporciona espacio para el crecimiento. En caso de que necesite aumentar el ancho de banda en el futuro, se queda con dos opciones.

Aumentar el ancho de banda. Recuerde que no todos los proveedores permiten hacerlo dinámicamente. Y debe evitar tener que realizar este máximo posible. Pero, si es necesario, después de comprobar con su proveedor, ejecute los comandos siguientes.

```powershell
$ckt = Get-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>
$ckt.ServiceProviderProperties.BandwidthInMbps = <<bandwidth-in-mbps>>
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

Puede aumentar el ancho de banda sin pérdida de conectividad. Cambio del ancho de banda da interrupción de conectividad. Tendrá que eliminar el circuito y volver a crearla con la nueva configuración.

Si está utilizando la SKU estándar para ExpressRoute y necesita para actualizar a Premium o cambiar están los precios planear (uso medido o ilimitado), ejecute los comandos siguientes. La `Sku.Tier` propiedad puede `Standard` o `Premium`; la `Sku.Name` propiedad puede `MeteredData` o `UnlimitedData`.

```powershell
$ckt = Get-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>

$ckt.Sku.Tier = "Premium"
$ckt.Sku.Family = "MeteredData"
$ckt.Sku.Name = "Premium_MeteredData"

 Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

>[AZURE.IMPORTANT] Asegúrese de que el `Sku.Name` propiedad coincidencias la `Sku.Tier` y `Sku.Family`. Si cambia la familia y nivel, pero no el nombre, se deshabilitará la conexión.

Puede actualizar el SKU sin interrupciones, pero no se puede cambiar el plan de precio ilimitado para uso medido. Cuando cambio el SKU, el consumo de ancho de banda debe permanecer dentro del límite de forma predeterminada de la SKU estándar.

> [AZURE.NOTE] ExpressRoute ofrece dos planes de precios a los clientes, en función de los datos de medición o ilimitados. Consulte [precios ExpressRoute] [ expressroute-pricing] para obtener más detalles. Cargos varían según el ancho de banda de circuito. Ancho de banda disponible es probable que pueden variar de un proveedor a otro. Usar el `Get-AzureRmExpressRouteServiceProvider` cmdlet para ver los proveedores disponibles en su región y los anchos de banda ofrecen.

Un solo circuito ExpressRoute puede admitir un número de vínculos de VNet y peerings. Consulte [los límites de ExpressRoute] [ expressroute-limits] para obtener más información.

Un costo adicional, ExpressRoute Premium complemento proporciona:

- Hasta 10.000 rutas por circuito. Sin ExpressRoute Premium complemento, el límite es de 4.000 rutas por circuito.

- Conectividad global, lo que permite un circuito ExpressRoute ubicado en cualquier parte del mundo acceso a los recursos de cualquier región en lugar de simplemente regiones en el mismo continente.

- Un aumento en el número de vínculos de VNet por circuito de 10 a un límite mayor, según el ancho de banda del circuito.

ExpressRoute circuitos están diseñados para permitir ráfagas de red temporal hasta dos veces el ancho de banda límite que adquirido para sin costo adicional. Esto se logra mediante vínculos redundantes. Sin embargo, no todos los proveedores de conectividad admiten esta característica; Compruebe que su proveedor de servicios de conectividad permite esta característica antes dependen de él.

## <a name="availability-considerations"></a>Consideraciones de disponibilidad

Puede configurar alta disponibilidad para la conexión de Azure de diferentes maneras, dependiendo del tipo de proveedor que utilice y el número de circuitos ExpressRoute y está dispuesto a configurar las conexiones de puerta de enlace de red virtual. Los puntos siguientes resumen las opciones de disponibilidad:

- ExpressRoute no admite protocolos de redundancia de enrutador como HSRP y VRRP para implementar alta disponibilidad. En su lugar, utiliza un par de sesiones BGP por interconexión redundante. Para facilitar conexiones altamente disponibles para su red, Microsoft Azure aprovisiona dos puertos redundantes en dos enrutadores (parte de Microsoft edge) en una configuración de activo / activo.

- Si está usando una conexión de nivel 2, implementar enrutadores redundantes de su red local en una configuración de activo / activo. Conecte el circuito principal a un enrutador y el secundario circuito a otra. Esto le proporcionará una conexión altamente disponible en ambos extremos de la conexión. Esto es necesario si establece el SLA de ExpressRoute. Vea [SLA para Azure ExpressRoute] [ sla-for-expressroute] para obtener más detalles.

El diagrama siguiente muestra una configuración con enrutadores redundantes local conectada a los circuitos primario y secundarios. Cada circuito controla el tráfico para un público interconexión y una interconexión privada (cada interconexión se designa un par de /30 dirección espacios, tal como se describe en la sección anterior).

![[1]][1]

Si está usando una conexión de nivel 3, compruebe que proporciona BGP redundante sesiones que controlan disponibilidad para usted.

Redes virtuales se pueden conectar a varios circuitos ExpressRoute y se pueden proporcionar cada circuitos por proveedores de servicios diferente. Esta estrategia proporciona capacidades de recuperación de desastres y alta disponibilidad adicional.

Configurar una VPN de sitio a sitio como una ruta de migración tras error para ExpressRoute. Esto solo es aplicable a interconexión privada. Para los servicios de Azure y Office 365, Internet es la ruta de acceso de solo migración tras error.

De forma predeterminada, las sesiones BGP usan un valor de tiempo de espera inactivo de 60 segundos. Una vez una sesión agotado 3 veces, la ruta está marcada como no disponible y todo el tráfico se redirige al enrutador restante. Este tiempo de espera de 180 segundos podría ser demasiado largo para las aplicaciones críticas. En este caso, puede cambiar la configuración de tiempo de espera BGP en el enrutador local a un valor más pequeño.

## <a name="manageability-considerations"></a>Consideraciones de capacidad de administración

Puede usar el [Kit de herramientas de conectividad de Azure (AzureCT)] [ azurect] para supervisar la conectividad entre el centro de datos local y Azure.

## <a name="security-considerations"></a>Consideraciones de seguridad

Puede configurar las opciones de seguridad para la conexión de Azure de maneras diferentes, según sus necesidades de cumplimiento y problemas de seguridad. Los puntos siguientes resumen las opciones de seguridad.

ExpressRoute funciona en el nivel 3. Pueden evitar amenazas en el nivel de aplicación utilizando un dispositivo de seguridad de red que restringe el tráfico a recursos legítimos. Además, solo se pueden iniciar conexiones de ExpressRoute mediante interconexión pública locales. Esto evita que un servicio de rogue obtengan acceso y poner en peligro datos locales desde Internet.

Para maximizar la seguridad, agregue los dispositivos de seguridad de red entre la red local y los enrutadores de borde de proveedor. Esto le ayudará a restringir la entrada de tráfico no autorizado desde el VNet:

![[2]][2]

Para fines de auditoría o cumplimiento, puede ser necesario prohibir el acceso directo de componentes se ejecutan en el VNet a Internet e implementar [forzado túnel][forced-tuneling]. En esta situación, se debería redirigir el tráfico de Internet a través de un proxy de ejecución local donde pueden auditar. El servidor proxy se puede configurar para bloquear el tráfico no autorizado procedente y filtrar el tráfico potencialmente malintencionado.

![[3]][3]

De forma predeterminada, máquinas virtuales de Azure exponer extremos utilizados para proporcionar acceso de inicio de sesión para propósitos de administración - RDP y Powershell remoto para Windows VM y SSH para máquinas virtuales basados en Linux cuando se implementa a través del portal de Azure. Para maximizar la seguridad, habilitar una dirección IP pública para sus máquinas virtuales y utilizar NSGs para asegurarse de que estas máquinas virtuales no están accesibles públicamente. Máquinas virtuales sólo deben estar disponibles con la dirección IP interna. Estas direcciones pueden realizarse accesibles a través de la red ExpressRoute, habilitar personal de DevOps local realizar cualquier configuración necesaria o mantenimiento.

Si debe exponer los extremos de la administración de máquinas virtuales a una red externa, use NSGs o acceder a las listas de control para restringir la visibilidad de estos puertos a una lista de direcciones IP o redes.

## <a name="troubleshooting-considerations"></a>Consideraciones de solución de problemas

Si un circuito ExpressRoute previamente funcionando ahora no consigue conectarse, en ausencia de cualquier configuración cambios local o en su VNet privado, deberá ponerse en contacto con el proveedor de servicios de conectividad y trabajar con ellos para corregir el problema. También puede usar los siguientes comandos de PowerShell de Azure para realizar alguna comprobación limitado y ayudar a determinar donde podrían están los problemas:

- Compruebe que se haya aprovisionado el circuito:

```powershell
Get-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>
```

El resultado de este comando muestra varias propiedades para su circuito, incluidos `ProvisioningState`, `CircuitProvisioningState`, y `ServiceProviderProvisioningState` tal como se muestra a continuación.

```
ProvisioningState                : Succeeded
Sku                              : {
                                     "Name": "Standard_MeteredData",
                                     "Tier": "Standard",
                                     "Family": "MeteredData"
                                   }
CircuitProvisioningState         : Enabled
ServiceProviderProvisioningState : NotProvisioned
```

Si la `ProvisioningState` no está configurada para `Succeeded` después de que ha intentado crear un nuevo circuito, quitar el circuito mediante el comando siguiente y pruebe a crear de nuevo.

```powershell
Remove-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>
```

Si su proveedor ya había creado el circuito y la `ProvisioningState` se establece en `Failed`, o la `CircuitProvisioningState` no `Enabled`, póngase en contacto con su proveedor para obtener más ayuda.

## <a name="solution-deployment"></a>Implementación de soluciones

Si tiene una infraestructura local existente ya está configurada con un dispositivo de red adecuado, puede implementar la arquitectura de referencia siguiendo estos pasos:

Si tiene una infraestructura local existente ya está configurada con un dispositivo VPN, puede implementar la arquitectura de referencia siguiendo estos pasos:

1. Haga clic con el botón secundario del mouse en el botón de abajo y seleccione uno "Abrir vínculo en una nueva pestaña" o "Abrir vínculo en ventana nueva":  
[![Implementar en Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-er%2Fazuredeploy.json)

2. Espere a que el vínculo para abrir en el portal de Azure, a continuación, siga estos pasos: 
  - El nombre del **grupo de recursos** ya está definido en el archivo de parámetros, así que seleccione **Crear nuevo** y escriba `ra-hybrid-er-rg` en el cuadro de texto.
  - Seleccione la región en el cuadro desplegable de **ubicación** .
  - No modifique la **Plantilla raíz Uri** o los cuadros de texto de **Uri raíz del parámetro** .
  - Revise los términos y condiciones y luego haga clic en la casilla de verificación **que acepto los términos y condiciones indicadas arriba** .
  - Haga clic en el botón de **compra** .

3. Espere a que la implementación completar.

4. Haga clic con el botón secundario del mouse en el botón de abajo y seleccione uno "Abrir vínculo en una nueva pestaña" o "Abrir vínculo en ventana nueva":  
[![Implementar en Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-er%2Fazuredeploy-expressRouteCircuit.json)

5. Espere a que el vínculo para abrir en el portal de Azure, a continuación, siga estos pasos: 
  - Seleccione **usar una existente** en la sección de **grupo de recursos** y escriba `ra-hybrid-er-rg` en el cuadro de texto.
  - Seleccione la región en el cuadro desplegable de **ubicación** .
  - No modifique la **Plantilla raíz Uri** o los cuadros de texto de **Uri raíz del parámetro** .
  - Revise los términos y condiciones y luego haga clic en la casilla de verificación **que acepto los términos y condiciones indicadas arriba** .
  - Haga clic en el botón de **compra** .

6. Espere a que la implementación completar.

## <a name="next-steps"></a>Pasos siguientes

- Vea [implementar una arquitectura de red altamente disponible híbrido] [ highly-available-network-architecture] para obtener información acerca de cómo aumentar la disponibilidad de una red híbrida basado en ExpressRoute por error sobre a una conexión VPN.

<!-- links -->
[expressroute-technical-overview]: ../expressroute/expressroute-introduction.md
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[azure-powershell]: ../powershell-azure-resource-manager.md
[expressroute-prereqs]: ../expressroute/expressroute-prerequisites.md
[configure-expressroute-routing]: ../expressroute/expressroute-howto-routing-arm.md
[sla-for-expressroute]: https://azure.microsoft.com/support/legal/sla/expressroute/v1_0/
[link-vnet-to-expressroute]: ../expressroute/expressroute-howto-linkvnet-arm.md
[ExpressRoute-provisioning]: ../expressroute/expressroute-workflows.md
[expressroute-pricing]: https://azure.microsoft.com/pricing/details/expressroute/
[expressroute-limits]: ../azure-subscription-service-limits.md#networking-limits
[sample-script]: #sample-solution-script
[connectivity-providers]: ../expressroute/expressroute-introduction.md#how-can-i-connect-my-network-to-microsoft-using-expressroute
[azurect]: https://github.com/Azure/NetworkMonitoring/tree/master/AzureCT
[forced-tuneling]: ./guidance-iaas-ra-secure-vnet-hybrid.md
[highly-available-network-architecture]: ./guidance-hybrid-network-expressroute-vpn-failover.md
[arm-templates]: ../resource-group-authoring-templates.md
[naming-conventions]: ./guidance-naming-conventions.md
[solution-script]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-er/Deploy-ReferenceArchitecture.ps1
[solution-script-bash]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-er/deploy-reference-architecture.sh
[vnet-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-er/parameters/virtualNetwork.parameters.json
[virtualnetworkgateway-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-er/parameters/virtualNetworkGateway.parameters.json
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[er-circuit-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-er/parameters/expressRouteCircuit.parameters.json
[azure-powershell-download]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[azure-cli]: https://azure.microsoft.com/documentation/articles/xplat-cli-install/
[0]: ./media/guidance-hybrid-network-expressroute/figure1.png "Arquitectura de red híbrida con Azure ExpressRoute"
[1]: ./media/guidance-hybrid-network-expressroute/figure2.png "Usar enrutadores redundantes con circuitos ExpressRoute de primaria y secundarias"
[2]: ./media/guidance-hybrid-network-expressroute/figure3.png "Agregar dispositivos de seguridad a la red local"
[3]: ./media/guidance-hybrid-network-expressroute/figure4.png "Usar forzado túnel para el tráfico de Internet enlazado de auditoría"
[4]: ./media/guidance-hybrid-network-expressroute/figure5.png "Buscar el valor de ServiceKey de un circuito ExpressRoute"
