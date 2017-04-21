<properties 
   pageTitle="Vincular una red virtual a un circuito de ExpressRoute con PowerShell | Microsoft Azure"
   description="Este documento proporciona información general sobre cómo vincular redes virtuales (VNets) a circuitos ExpressRoute mediante el modelo de implementación de administrador de recursos y PowerShell."
   services="expressroute"
   documentationCenter="na"
   authors="ganesr"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="ganesr" />

# <a name="link-a-virtual-network-to-an-expressroute-circuit"></a>Vincular una red virtual a un circuito ExpressRoute

> [AZURE.SELECTOR]
- [Portal de Azure - Administrador de recursos](expressroute-howto-linkvnet-portal-resource-manager.md)
- [PowerShell - Administrador de recursos](expressroute-howto-linkvnet-arm.md)
- [PowerShell - clásica](expressroute-howto-linkvnet-classic.md)


En este artículo le ayudará a vincular redes virtuales (VNets) a Azure ExpressRoute circuitos mediante el modelo de implementación de administrador de recursos y PowerShell. Redes virtuales pueden ser de la misma suscripción o parte de otra suscripción.

**Acerca de los modelos de implementación de Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## <a name="configuration-prerequisites"></a>Requisitos previos de configuración

- Necesita la última versión de los módulos de Azure PowerShell (al menos versión 1.0). Para obtener más información sobre la instalación de los cmdlets de PowerShell, vea [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) .
- Es necesario revisar los [requisitos previos](expressroute-prerequisites.md), [requisitos enrutamiento](expressroute-routing.md)y [flujos de trabajo](expressroute-workflows.md) antes de comenzar la configuración.
- Debe tener un circuito ExpressRoute activo. 
    - Siga las instrucciones para [crear un circuito ExpressRoute](expressroute-howto-circuit-arm.md) y tiene el circuito habilitado por su proveedor de servicios de conectividad. 
    - Asegúrese de que haya configurado para su circuito la función de interconexión privada de Azure. Vea el artículo [Configurar el enrutamiento](expressroute-howto-routing-arm.md) de las instrucciones de enrutamiento. 
    - Asegúrese de que interconexión privada Azure está configurada y la interconexión BGP entre la red y Microsoft para que puede habilitar la conectividad de llevar a cabo.
    - Asegúrese de que dispone de una red virtual y una puerta de enlace de red virtual creado y configurarán por completo. Siga las instrucciones para crear una [puerta de enlace VPN](../articles/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md), pero asegúrese de usar `-GatewayType ExpressRoute`.

Puede vincular hasta 10 redes virtuales a un circuito de ExpressRoute estándar. Todas las redes virtuales deben estar en la misma región geopolíticas cuando se usa un circuito ExpressRoute estándar. 

Puede vincular un redes virtuales fuera de la región del circuito ExpressRoute geopolítica o conectar un mayor número de redes virtuales con su circuito ExpressRoute Si habilita el complemento de premium ExpressRoute. Consulte las [preguntas más frecuentes](expressroute-faqs.md) para obtener más detalles sobre el complemento premium.

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Conectar una red virtual en la misma suscripción a un circuito

Puede conectar una puerta de enlace de red virtual a un circuito ExpressRoute mediante el siguiente cmdlet. Asegúrese de que la puerta de enlace de red virtual se crea y está listo para vincular antes de ejecutar el cmdlet:

    $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    $gw = Get-AzureRmVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
    $connection = New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "MyRG" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Conectarse a una red virtual en una suscripción diferente de un circuito

Puede compartir un circuito ExpressRoute entre varias suscripciones. La figura siguiente muestra una sencilla esquemático de cómo compartir funciona para circuitos ExpressRoute a través de varias suscripciones.

Cada una de las nubes más pequeñas dentro de la nube grande se usa para representar las suscripciones que pertenecen a diferentes departamentos dentro de una organización. Cada uno de los departamentos de la organización puede usar su propia suscripción para implementar los servicios, pero puede compartir un único circuito ExpressRoute volver a conectarse a su red local. Un solo departamento (en este ejemplo: TI) puede posee el circuito ExpressRoute. Otras suscripciones dentro de la organización pueden usar el circuito ExpressRoute.

>[AZURE.NOTE] Cargos de conectividad y de ancho de banda para el circuito dedicado se aplicarán al propietario de circuito ExpressRoute. Todas las redes virtuales comparten el mismo ancho de banda.

![Conectividad de suscripción de cruz](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration"></a>Administración

El *propietario de circuito* es un usuario de power autorizados del recurso de circuito ExpressRoute. El propietario de circuito puede crear las autorizaciones que se pueden canjear por *usuarios de circuito*. *Los usuarios de circuito* son los propietarios de puertas de enlace de red virtual (que no están en la misma suscripción como el circuito ExpressRoute). *Los usuarios de circuito* puede canjear autorizaciones (una autorización por una red virtual).

El *propietario de circuito* tiene la capacidad para modificar y revocar autorizaciones en cualquier momento. Revocar el resultado de una autorización en todas las conexiones de vínculo que se elimina de la suscripción se ha caducado cuyo acceso.

### <a name="circuit-owner-operations"></a>Operaciones de propietario de circuito 

#### <a name="creating-an-authorization"></a>Creación de una autorización
    
El propietario de circuito crea una autorización. El resultado es la creación de una clave de autorización puede ser usada por un usuario de circuito conectar sus puertas de enlace de red virtual al circuito ExpressRoute. Autorización es válida para sólo una conexión.

El fragmento de cmdlet siguiente muestra cómo crear una autorización:

    $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit

        $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    $auth1 = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
        

La respuesta a esta contendrá la clave de autorización y el estado:

    Name                   : MyAuthorization1
    Id                     : /subscriptions/&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/CrossSubTest/authorizations/MyAuthorization1
    Etag                   : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
    AuthorizationKey       : ####################################
    AuthorizationUseStatus : Available
    ProvisioningState      : Succeeded

        

#### <a name="reviewing-authorizations"></a>Revisar las autorizaciones

El propietario de circuito puede revisar todas las autorizaciones que se emiten en un determinado circuito ejecutando el siguiente cmdlet:

    $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    $authorizations = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
    

#### <a name="adding-authorizations"></a>Agregar autorizaciones

El propietario de circuito puede agregar autorizaciones mediante el siguiente cmdlet:

    $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization2"
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit
    
    $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    $authorizations = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit

    
#### <a name="deleting-authorizations"></a>Eliminar autorizaciones

El propietario de circuito puede revocar o eliminar autorizaciones para el usuario, ejecute el siguiente cmdlet:

    Remove-AzureRmExpressRouteCircuitAuthorization -Name "MyAuthorization2" -ExpressRouteCircuit $circuit
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit    

### <a name="circuit-user-operations"></a>Operaciones de usuario de circuito

El usuario de circuito necesita el identificador del mismo nivel y una clave de autorización del propietario de circuito. La clave de autorización es un GUID.

Es el identificador del mismo nivel, puede comprobar desde el siguiente comando.

    Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"

#### <a name="redeeming-connection-authorizations"></a>Canjear las autorizaciones de conexión

El usuario de circuito puede ejecutar el siguiente cmdlet para canjear una autorización de vínculo:

    $id = "/subscriptions/********************************/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/MyCircuit"  
    $gw = Get-AzureRmVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
    $connection = New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "RemoteResourceGroup" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $id -ConnectionType ExpressRoute -AuthorizationKey "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"

#### <a name="releasing-connection-authorizations"></a>Liberar las autorizaciones de conexión

Puede liberar una autorización eliminando la conexión que se vincula el circuito ExpressRoute a la red virtual.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre ExpressRoute, consulte las [Preguntas más frecuentes de ExpressRoute](expressroute-faqs.md).
