<properties
   pageTitle="Vincular una red virtual a un circuito ExpressRoute mediante el modelo de implementación clásica y PowerShell | Microsoft Azure"
   description="Este documento proporciona información general sobre cómo vincular redes virtuales (VNets) a circuitos ExpressRoute mediante el modelo de implementación clásica y PowerShell."
   services="expressroute"
   documentationCenter="na"
   authors="ganesr"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>
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



En este artículo le ayudará a vincular redes virtuales (VNets) a Azure ExpressRoute circuitos mediante el modelo de implementación clásica y PowerShell. Redes virtuales pueden ser en la misma suscripción o pueden ser parte de otra suscripción.

**Acerca de los modelos de implementación de Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>Requisitos previos de configuración

1. Necesita la última versión de los módulos de PowerShell de Azure. Puede descargar los últimos módulos de PowerShell desde la sección de PowerShell de la [página de descargas de Azure](https://azure.microsoft.com/downloads/). Siga las instrucciones de [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) para obtener instrucciones paso a paso sobre cómo configurar su PC para usar los módulos de PowerShell de Azure.
2. Es necesario revisar los [requisitos previos](expressroute-prerequisites.md), [requisitos enrutamiento](expressroute-routing.md)y [flujos de trabajo](expressroute-workflows.md) antes de comenzar la configuración.
3. Debe tener un circuito ExpressRoute activo.
    - Siga las instrucciones para [crear un circuito ExpressRoute](expressroute-howto-circuit-classic.md) y tener su proveedor de servicios de conectividad habilitar el circuito.
    - Asegúrese de que haya configurado para su circuito la función de interconexión privada de Azure. Vea el artículo [configurar enrutamiento](expressroute-howto-routing-classic.md) para las instrucciones de enrutamiento.
    - Asegúrese de que interconexión privada Azure está configurada y la interconexión BGP entre la red y Microsoft para que puede habilitar la conectividad de llevar a cabo.
    - Debe tener una red virtual y una puerta de enlace de red virtual creado y configurarán por completo. Siga las instrucciones para [Configurar una red virtual para ExpressRoute](expressroute-howto-vnet-portal-classic.md).

Puede vincular hasta 10 redes virtuales a un circuito de ExpressRoute. Todas las redes virtuales deben estar en la misma región geopolíticas. Puede vincular un mayor número de redes virtuales a su circuito ExpressRoute o redes virtuales del vínculo que se encuentran en otras regiones geopolíticas Si habilita el complemento de premium ExpressRoute. Consulte las [preguntas más frecuentes](expressroute-faqs.md) para obtener más detalles sobre el complemento premium.

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Conectar una red virtual en la misma suscripción a un circuito

Puede vincular una red virtual a un circuito ExpressRoute mediante el siguiente cmdlet. Asegúrese de que la puerta de enlace de red virtual se crea y está listo para vincular antes de ejecutar el cmdlet.

    New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
    Provisioned

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Conectarse a una red virtual en una suscripción diferente de un circuito

Puede compartir un circuito ExpressRoute entre varias suscripciones. La figura siguiente muestra una sencilla esquemático de cómo compartir funciona para circuitos ExpressRoute a través de varias suscripciones.

Cada una de las nubes más pequeñas dentro de la nube grande se usa para representar las suscripciones que pertenecen a diferentes departamentos dentro de una organización. Cada uno de los departamentos de la organización puede usar su propia suscripción para implementar los servicios, pero los departamentos puede compartir un único circuito ExpressRoute volver a conectarse a su red local. Un solo departamento (en este ejemplo: TI) puede posee el circuito ExpressRoute. Otras suscripciones dentro de la organización pueden usar el circuito ExpressRoute.

>[AZURE.NOTE] Cargos de conectividad y de ancho de banda para el circuito dedicado se aplicarán al propietario de circuito ExpressRoute. Todas las redes virtuales comparten el mismo ancho de banda.

![Conectividad de suscripción de cruz](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration"></a>Administración

El *propietario de circuito* es el administrador o coadministrator de la suscripción en la que se creó el circuito ExpressRoute. El propietario de circuito puede autorizar los administradores o coadministrators de otras suscripciones, denominados *usuarios circuito*, para usar el circuito dedicado que es propietario. Los usuarios de circuito que están autorizados a utilizar circuito de ExpressRoute de la organización pueden vincular la red virtual de su suscripción al circuito ExpressRoute después de que están autorizados.

El propietario de circuito tiene la posibilidad de modificar y revocar autorizaciones en cualquier momento. Revocar la autorización se traducirán en todos los vínculos que se elimina de la suscripción cuyo acceso rechazó.

### <a name="circuit-owner-operations"></a>Operaciones de propietario de circuito

#### <a name="creating-an-authorization"></a>Creación de una autorización

El propietario de circuito autoriza a los administradores de otras suscripciones para usar el circuito especificado. En el ejemplo siguiente, el administrador del circuito (TI de Contoso) permite al administrador de otra suscripción (desarrollo de prueba) para vincular dos redes virtuales al circuito. El Administrador de TI de Contoso habilita especificando el identificador de prueba de desarrollo de Microsoft. El cmdlet no enviar correo electrónico al identificador de Microsoft especificado. El propietario de circuito debe notificar explícitamente el propietario de la suscripción que la autorización está completa.

    New-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -Description "Dev-Test Links" -Limit 2 -MicrosoftIds 'devtest@contoso.com'

    Description         : Dev-Test Links
    Limit               : 2
    LinkAuthorizationId : **********************************
    MicrosoftIds        : devtest@contoso.com
    Used                : 0

#### <a name="reviewing-authorizations"></a>Revisar las autorizaciones

El propietario de circuito puede revisar todas las autorizaciones que se emiten en un determinado circuito ejecutando el siguiente cmdlet:

    Get-AzureDedicatedCircuitLinkAuthorization -ServiceKey: "**************************"

    Description         : EngineeringTeam
    Limit               : 3
    LinkAuthorizationId : ####################################
    MicrosoftIds        : engadmin@contoso.com
    Used                : 1

    Description         : MarketingTeam
    Limit               : 1
    LinkAuthorizationId : @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
    MicrosoftIds        : marketingadmin@contoso.com
    Used                : 0

    Description         : Dev-Test Links
    Limit               : 2
    LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
    MicrosoftIds        : salesadmin@contoso.com
    Used                : 2


#### <a name="updating-authorizations"></a>Actualizar las autorizaciones

El propietario de circuito puede modificar las autorizaciones mediante el siguiente cmdlet:

    Set-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -AuthorizationId "&&&&&&&&&&&&&&&&&&&&&&&&&&&&"-Limit 5

    Description         : Dev-Test Links
    Limit               : 5
    LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
    MicrosoftIds        : devtest@contoso.com
    Used                : 0


#### <a name="deleting-authorizations"></a>Eliminar autorizaciones

El propietario de circuito puede revocar o eliminar autorizaciones para el usuario, ejecute el siguiente cmdlet:

    Remove-AzureDedicatedCircuitLinkAuthorization -ServiceKey "*****************************" -AuthorizationId "###############################"


### <a name="circuit-user-operations"></a>Operaciones de usuario de circuito

#### <a name="reviewing-authorizations"></a>Revisar las autorizaciones

El usuario de circuito puede revisar las autorizaciones mediante el siguiente cmdlet:

    Get-AzureAuthorizedDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : ContosoIT
    Location                         : Washington DC
    MaximumAllowedLinks              : 2
    ServiceKey                       : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled
    UsedLinks                        : 0

#### <a name="redeeming-link-authorizations"></a>Canjear autorizaciones de vínculo

El usuario de circuito puede ejecutar el siguiente cmdlet para canjear una autorización de vínculo:

    New-AzureDedicatedCircuitLink –servicekey "&&&&&&&&&&&&&&&&&&&&&&&&&&" –VnetName 'SalesVNET1'

    State VnetName
    ----- --------
    Provisioned SalesVNET1

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre ExpressRoute, consulte las [Preguntas más frecuentes de ExpressRoute](expressroute-faqs.md).
