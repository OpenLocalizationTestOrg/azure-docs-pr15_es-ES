<properties
   pageTitle="Cómo configurar el enrutamiento para un circuito ExpressRoute | Microsoft Azure"
   description="En este artículo le guiará por los pasos necesarios para crear y aprovisionar el privado, público y Microsoft interconexión de un circuito ExpressRoute. En este artículo también muestra cómo comprobar el estado, actualizar o eliminar peerings para su circuito."
   documentationCenter="na"
   services="expressroute"
   authors="ganesr"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="hero-article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/05/2016"
   ms.author="ganesr"/>

# <a name="create-and-modify-routing-for-an-expressroute-circuit"></a>Crear y modificar el enrutamiento de un circuito ExpressRoute


> [AZURE.SELECTOR]
[Portal de Azure - Administrador de recursos](expressroute-howto-routing-portal-resource-manager.md)
[PowerShell - Administrador de recursos](expressroute-howto-routing-arm.md)
[PowerShell - clásica](expressroute-howto-routing-classic.md)



En este artículo le guiará por los pasos para crear y administrar la configuración de enrutamiento para un circuito ExpressRoute usando PowerShell y el modelo de implementación de administrador de recursos de Azure.  Los pasos siguientes también le mostrará cómo activar peerings para un circuito ExpressRoute y comprobar el estado, actualizar o eliminar. 


**Acerca de los modelos de implementación de Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## <a name="configuration-prerequisites"></a>Requisitos previos de configuración

- Necesitará la versión más reciente de los módulos de PowerShell de Azure, versión 1.0 o posterior. 
- Asegúrese de que ha revisado la página [requisitos previos](expressroute-prerequisites.md) , la página de [requisitos de enrutamiento](expressroute-routing.md) y la página de [flujos de trabajo](expressroute-workflows.md) antes de comenzar la configuración.
- Debe tener un circuito ExpressRoute activo. Siga las instrucciones para [crear un circuito ExpressRoute](expressroute-howto-circuit-arm.md) y tiene el circuito habilitado por su proveedor de servicios de conectividad antes de continuar. Circuito de ExpressRoute debe estar en un estado de preparación y están habilitado para poder ejecutar los cmdlets que se describe a continuación.

Estas instrucciones solo se aplican a circuitos creados con proveedores de servicios que ofrece servicios de conectividad de nivel 2. Si está utilizando un proveedor de servicios que ofrece servicios de nivel 3 administrados (normalmente un IPVPN, como MPLS), su proveedor de servicios de conectividad configurará y administrar el enrutamiento para usted.

>[AZURE.IMPORTANT] Nos actualmente no se anuncia peerings configurado por proveedores de servicios a través del portal de administración de servicio. Estamos trabajando sobre cómo habilitar esta capacidad pronto. Consulte con su proveedor de servicios antes de configurar peerings BGP.

Puede configurar una, dos o todas las tres peerings (público privado, Azure Azure y Microsoft) para un circuito ExpressRoute. Puede configurar peerings en el orden que desee. Sin embargo, debe asegurarse de que complete la configuración de cada uno de ellos interconexión a la vez. 

## <a name="azure-private-peering"></a>Interconexión privada de Azure

Esta sección proporciona instrucciones sobre cómo crear, obtener, actualizar y eliminar la configuración de interconexión privada Azure para un circuito ExpressRoute. 

### <a name="to-create-azure-private-peering"></a>Para crear la interconexión privada de Azure

1. Importar el módulo de PowerShell para ExpressRoute.
    
    Debe instalar al instalador de PowerShell más reciente de la [Galería de PowerShell](http://www.powershellgallery.com/) e importar los módulos del Administrador de recursos de Azure en la sesión de PowerShell para empezar a usar los cmdlets de ExpressRoute. Debe ejecutar PowerShell como administrador.

        Install-Module AzureRM

        Install-AzureRM

    Importar todos los módulos AzureRM.* dentro del intervalo semántico conocidos

        Import-AzureRM

    También puede importar un módulo select dentro del intervalo semántico conocidos 
        
        Import-Module AzureRM.Network 

    Iniciar sesión en su cuenta

        Login-AzureRmAccount

    Seleccione la suscripción que desea crear circuito ExpressRoute
        
        Select-AzureRmSubscription -SubscriptionId "<subscription ID>"

2. Crear un circuito de ExpressRoute.
    
    Siga las instrucciones para crear un [circuito ExpressRoute](expressroute-howto-circuit-arm.md) y ha proporcionado por el proveedor de servicios de conectividad. 

    Si su proveedor de servicios de conectividad ofrece servicios de nivel 3 administrados, puede solicitar su proveedor de conectividad para habilitar interconexión privada Azure para usted. En ese caso, no tendrá que siga las instrucciones que se indican en las siguientes secciones. Sin embargo, si su proveedor de servicios de conectividad no administrar el enrutamiento, después de crear su circuito, siga las instrucciones siguientes. 

3. Compruebe el circuito de ExpressRoute para asegurarse de que se aprovisiona.

    En primer lugar, debe comprobar si el circuito ExpressRoute se aprovisiona y también habilitado. Consulte el ejemplo siguiente.

        Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    La respuesta será similar del siguiente ejemplo:

        Name                             : ExpressRouteARMCircuit
        ResourceGroupName                : ExpressRouteResourceGroup
        Location                         : westus
        Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
        Etag                             : W/"################################"
        ProvisioningState                : Succeeded
        Sku                              : {
                                             "Name": "Standard_MeteredData",
                                             "Tier": "Standard",
                                             "Family": "MeteredData"
                                           }
        CircuitProvisioningState         : Enabled
        ServiceProviderProvisioningState : Provisioned
        ServiceProviderNotes             : 
        ServiceProviderProperties        : {
                                             "ServiceProviderName": "Equinix",
                                             "PeeringLocation": "Silicon Valley",
                                             "BandwidthInMbps": 200
                                           }
        ServiceKey                       : **************************************
        Peerings                         : []


4. Configurar interconexión privada Azure para el circuito.

    Asegúrese de que tiene los siguientes elementos antes de continuar con los siguientes pasos:

    - Un /30 subred del vínculo principal. No debe ser parte de cualquier espacio de direcciones reservado para redes virtuales.
    - Un /30 subred del vínculo secundario. No debe ser parte de cualquier espacio de direcciones reservado para redes virtuales.
    - Un ID válido para establecer esta interconexión en. Asegúrese de que ningún otro interconexión circuito utiliza el mismo identificador de VLAN.
    - COMO número de interconexión. Puede utilizar 2 bytes y 4 bytes como números. Puede usar una privada como número para este interconexión. Asegúrese de que no está usando 65515.
    - Un hash MD5 si decide usar uno. **Esto es opcional**.
    
    Puede ejecutar el siguiente cmdlet para configurar interconexión privada Azure para su circuito.

        Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

    Puede usar el cmdlet siguiente si decide usar un hash MD5.

        Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200  -SharedKey "A1B2C3D4"

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

    >[AZURE.IMPORTANT] Asegúrese de que especifica el número de AS como ASN interconexión, no el cliente ASN.

### <a name="to-view-azure-private-peering-details"></a>Para ver los detalles de interconexión privadas Azure

Puede obtener los detalles de configuración con el siguiente cmdlet

        $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

        Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt   


### <a name="to-update-azure-private-peering-configuration"></a>Actualizar la configuración de interconexión privada Azure

Puede actualizar cualquier parte de la configuración con el siguiente cmdlet. En el ejemplo siguiente, el identificador de VLAN del circuito se está actualizando desde 100 a 500.

    Set-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


### <a name="to-delete-azure-private-peering"></a>Para eliminar la interconexión privada de Azure

Puede quitar la configuración de interconexión, ejecute el cmdlet siguiente.

>[AZURE.WARNING] Debe asegurarse de que todas las redes virtuales son no vinculadas desde el circuito ExpressRoute antes de ejecutar este cmdlet. 

    Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt



## <a name="azure-public-peering"></a>Interconexión pública de Azure

Esta sección proporciona instrucciones sobre cómo crear, obtener, actualizar y eliminar la configuración de interconexión pública Azure para un circuito ExpressRoute.

### <a name="to-create-azure-public-peering"></a>Para crear la interconexión pública de Azure

1. Importar el módulo de PowerShell para ExpressRoute.
    
    Debe instalar al instalador de PowerShell más reciente de la [Galería de PowerShell](http://www.powershellgallery.com/) e importar los módulos del Administrador de recursos de Azure en la sesión de PowerShell para empezar a usar los cmdlets de ExpressRoute. Debe ejecutar PowerShell como administrador.

        Install-Module AzureRM

        Install-AzureRM

    Importar todos los módulos AzureRM.* dentro del intervalo semántico conocidos

        Import-AzureRM

    También puede importar un módulo select dentro del intervalo semántico conocidos 
        
        Import-Module AzureRM.Network 

    Iniciar sesión en su cuenta

        Login-AzureRmAccount

    Seleccione la suscripción que desea crear circuito ExpressRoute
        
        Select-AzureRmSubscription -SubscriptionId "<subscription ID>"

2. Crear un circuito de ExpressRoute.
    
    Siga las instrucciones para crear un [circuito ExpressRoute](expressroute-howto-circuit-arm.md) y ha proporcionado por el proveedor de servicios de conectividad. 

    Si su proveedor de servicios de conectividad ofrece servicios de nivel 3 administrados, puede solicitar su proveedor de conectividad para habilitar interconexión pública Azure para usted. En ese caso, no tendrá que siga las instrucciones que se indican en las siguientes secciones. Sin embargo, si su proveedor de servicios de conectividad no administrar el enrutamiento, después de crear su circuito, siga las instrucciones siguientes.

3. Compruebe el circuito de ExpressRoute para asegurarse de que se aprovisiona.

    En primer lugar, debe comprobar si el circuito ExpressRoute se aprovisiona y también habilitado. Consulte el ejemplo siguiente.

        Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    La respuesta será similar del siguiente ejemplo:

        Name                             : ExpressRouteARMCircuit
        ResourceGroupName                : ExpressRouteResourceGroup
        Location                         : westus
        Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
        Etag                             : W/"################################"
        ProvisioningState                : Succeeded
        Sku                              : {
                                             "Name": "Standard_MeteredData",
                                             "Tier": "Standard",
                                             "Family": "MeteredData"
                                           }
        CircuitProvisioningState         : Enabled
        ServiceProviderProvisioningState : Provisioned
        ServiceProviderNotes             : 
        ServiceProviderProperties        : {
                                             "ServiceProviderName": "Equinix",
                                             "PeeringLocation": "Silicon Valley",
                                             "BandwidthInMbps": 200
                                           }
        ServiceKey                       : **************************************
        Peerings                         : []   

4. Configurar interconexión pública Azure para el circuito.

    Asegurarse de que tiene la información siguiente antes de continuar más.

    - Un /30 subred del vínculo principal. Debe ser un prefijo IPv4 público válido.
    - Un /30 subred del vínculo secundario. Debe ser un prefijo IPv4 público válido.
    - Un ID válido para establecer esta interconexión en. Asegúrese de que ningún otro interconexión circuito utiliza el mismo identificador de VLAN.
    - COMO número de interconexión. Puede utilizar 2 bytes y 4 bytes como números.
    - Un hash MD5 si decide usar uno. **Esto es opcional**.
    
    Puede ejecutar el siguiente cmdlet para configurar interconexión pública Azure para su circuito

        Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

    Puede usar el cmdlet siguiente si decide usar un hash MD5

        Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100  -SharedKey "A1B2C3D4"

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


    >[AZURE.IMPORTANT] Asegúrese de que especifica el número de AS como interconexión ASN y no el cliente ASN.

### <a name="to-view-azure-public-peering-details"></a>Para ver los detalles de interconexión públicas Azure

Puede obtener los detalles de configuración con el siguiente cmdlet

        $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

        Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt


### <a name="to-update-azure-public-peering-configuration"></a>Actualizar la configuración de interconexión pública Azure

Puede actualizar cualquier parte de la configuración con el siguiente cmdlet

    Set-AzureRmExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 600 

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

El identificador de VLAN del circuito se está actualizando desde 200 a 600 en el ejemplo anterior.

### <a name="to-delete-azure-public-peering"></a>Para eliminar la interconexión pública de Azure

Puede quitar la configuración de interconexión ejecutando el siguiente cmdlet

    Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

## <a name="microsoft-peering"></a>Interconexión de Microsoft

Esta sección proporciona instrucciones sobre cómo crear, obtener, actualizar y eliminar la configuración de interconexión de Microsoft para un circuito ExpressRoute. 

### <a name="to-create-microsoft-peering"></a>Para crear Microsoft interconexión

1. Importar el módulo de PowerShell para ExpressRoute.
    
    Debe instalar al instalador de PowerShell más reciente de la [Galería de PowerShell](http://www.powershellgallery.com/) e importar los módulos del Administrador de recursos de Azure en la sesión de PowerShell para empezar a usar los cmdlets de ExpressRoute. Debe ejecutar PowerShell como administrador.

        Install-Module AzureRM

        Install-AzureRM

    Importar todos los módulos AzureRM.* dentro del intervalo semántico conocidos

        Import-AzureRM

    También puede importar un módulo select dentro del intervalo semántico conocidos 
        
        Import-Module AzureRM.Network 

    Iniciar sesión en su cuenta

        Login-AzureRmAccount

    Seleccione la suscripción que desea crear circuito ExpressRoute
        
        Select-AzureRmSubscription -SubscriptionId "<subscription ID>"

2. Crear un circuito de ExpressRoute.
    
    Siga las instrucciones para crear un [circuito ExpressRoute](expressroute-howto-circuit-arm.md) y ha proporcionado por el proveedor de servicios de conectividad. 

    Si su proveedor de servicios de conectividad ofrece servicios de nivel 3 administrados, puede solicitar su proveedor de conectividad para habilitar interconexión privada Azure para usted. En ese caso, no tendrá que siga las instrucciones que se indican en las siguientes secciones. Sin embargo, si su proveedor de servicios de conectividad no administrar el enrutamiento, después de crear su circuito, siga las instrucciones siguientes.

3. Compruebe el circuito de ExpressRoute para asegurarse de que se aprovisiona.

    En primer lugar, debe comprobar si el circuito ExpressRoute se aprovisiona y también habilitado. Consulte el ejemplo siguiente.

        Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    La respuesta será similar del siguiente ejemplo:

        Name                             : ExpressRouteARMCircuit
        ResourceGroupName                : ExpressRouteResourceGroup
        Location                         : westus
        Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
        Etag                             : W/"################################"
        ProvisioningState                : Succeeded
        Sku                              : {
                                             "Name": "Standard_MeteredData",
                                             "Tier": "Standard",
                                             "Family": "MeteredData"
                                           }
        CircuitProvisioningState         : Enabled
        ServiceProviderProvisioningState : Provisioned
        ServiceProviderNotes             : 
        ServiceProviderProperties        : {
                                             "ServiceProviderName": "Equinix",
                                             "PeeringLocation": "Silicon Valley",
                                             "BandwidthInMbps": 200
                                           }
        ServiceKey                       : **************************************
        Peerings                         : []   
4. Configurar Microsoft interconexión para el circuito.

    Asegúrese de que tiene la información siguiente antes de continuar.

    - Un /30 subred del vínculo principal. Debe ser un prefijo de IPv4 público válido posea y registrado en un RIR / TIR.
    - Un /30 subred del vínculo secundario. Debe ser un prefijo de IPv4 público válido posea y registrado en un RIR / TIR.
    - Un ID válido para establecer esta interconexión en. Asegúrese de que ningún otro interconexión circuito utiliza el mismo identificador de VLAN.
    - COMO número de interconexión. Puede utilizar 2 bytes y 4 bytes como números.
    - Anuncia prefijos: debe proporcionar una lista de todos los prefijos planea anunciar sobre la sesión BGP. Se aceptan solo prefijos de direcciones IP públicos. Puede enviar una lista separada por comas si va a enviar un conjunto de prefijos. Estos prefijos deben estar registrados para usted en un RIR / TIR.
    - Cliente ASN: Si son prefijos de publicidad que no están registrados para la interconexión como número, puede especificar el número de AS a los que están registrados. **Esto es opcional**.
    - Nombre de ruta del registro: Puede especificar el RIR / TIR frente a los que el número y prefijos se registrado.
    - Un hash MD5, si decide usar uno. **Esto es opcional.**
    
    Puede ejecutar el siguiente cmdlet para configurar Microsoft interconexión para su circuito

        Add-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "123.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


### <a name="to-get-microsoft-peering-details"></a>Para obtener detalles de interconexión de Microsoft

Puede obtener los detalles de configuración con el siguiente cmdlet.

        $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

        Get-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt


### <a name="to-update-microsoft-peering-configuration"></a>Actualizar la configuración de interconexión de Microsoft

Puede actualizar cualquier parte de la configuración con el siguiente cmdlet.

        Set-AzureRmExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "124.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
        

### <a name="to-delete-microsoft-peering"></a>Para eliminar interconexión de Microsoft

Puede quitar la configuración de interconexión, ejecute el cmdlet siguiente.

    Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

## <a name="next-steps"></a>Pasos siguientes

Siguiente paso, [vínculo un VNet a un circuito de ExpressRoute](expressroute-howto-linkvnet-arm.md).

-  Para obtener más información sobre los flujos de trabajo de ExpressRoute, consulte [ExpressRoute flujos de trabajo](expressroute-workflows.md).

-  Para obtener más información acerca de circuito interconexión, vea [ExpressRoute circuitos y dominios de enrutamiento](expressroute-circuit-peerings.md).

-  Para obtener más información sobre cómo trabajar con las redes virtuales, vea [Introducción a la red Virtual](../virtual-network/virtual-networks-overview.md).

