<properties
   pageTitle="Cómo configurar el enrutamiento para un circuito ExpressRoute para el modelo de implementación clásica con PowerShell | Microsoft Azure"
   description="En este artículo le guiará por los pasos necesarios para crear y aprovisionar el privado, público y Microsoft interconexión de un circuito ExpressRoute. En este artículo también muestra cómo comprobar el estado, actualizar o eliminar peerings para su circuito."
   documentationCenter="na"
   services="expressroute"
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
   ms.author="ganesr"/>

# <a name="create-and-modify-routing-for-an-expressroute-circuit"></a>Crear y modificar el enrutamiento de un circuito ExpressRoute

> [AZURE.SELECTOR]
[Portal de Azure - Administrador de recursos](expressroute-howto-routing-portal-resource-manager.md)
[PowerShell - Administrador de recursos](expressroute-howto-routing-arm.md)
[PowerShell - clásica](expressroute-howto-routing-classic.md)



En este artículo le guiará por los pasos para crear y administrar la configuración de enrutamiento de un circuito de ExpressRoute con PowerShell y el modelo de implementación clásico. Los pasos siguientes también le mostrará cómo activar peerings para un circuito ExpressRoute y comprobar el estado, actualizar o eliminar.


**Acerca de los modelos de implementación de Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## <a name="configuration-prerequisites"></a>Requisitos previos de configuración

- Necesitará la versión más reciente de los módulos de PowerShell de Azure. Puede descargar el módulo de PowerShell más reciente de la sección de PowerShell de la [página de descargas de Azure](https://azure.microsoft.com/downloads/). Siga las instrucciones de la página de [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) para obtener instrucciones paso a paso sobre cómo configurar su PC para usar los módulos de PowerShell de Azure. 
- Asegúrese de que ha revisado la página [requisitos previos](expressroute-prerequisites.md) , la página de [requisitos de enrutamiento](expressroute-routing.md) y la página de [flujos de trabajo](expressroute-workflows.md) antes de comenzar la configuración.
- Debe tener un circuito ExpressRoute activo. Siga las instrucciones para [crear un circuito ExpressRoute](expressroute-howto-circuit-classic.md) y tiene el circuito habilitado por su proveedor de servicios de conectividad antes de continuar. Circuito de ExpressRoute debe estar en un estado de preparación y están habilitado para poder ejecutar los cmdlets que se describe a continuación.

>[AZURE.IMPORTANT] Estas instrucciones solo se aplican a circuitos creados con proveedores de servicios que ofrece servicios de conectividad de nivel 2. Si está utilizando un proveedor de servicios que ofrece servicios de nivel 3 administrados (normalmente un IPVPN, como MPLS), su proveedor de servicios de conectividad configurará y administrar el enrutamiento para usted.

Puede configurar una, dos o todas las tres peerings (público privado, Azure Azure y Microsoft) para un circuito ExpressRoute. Puede configurar peerings en el orden que desee. Sin embargo, debe asegurarse de que complete la configuración de cada uno de ellos interconexión a la vez. 

## <a name="azure-private-peering"></a>Interconexión privada de Azure

Esta sección proporciona instrucciones sobre cómo crear, obtener, actualizar y eliminar la configuración de interconexión privada Azure para un circuito ExpressRoute. 

### <a name="to-create-azure-private-peering"></a>Para crear la interconexión privada de Azure

1. **Importar el módulo de PowerShell para ExpressRoute.**
    
    Debe importar los módulos de Azure y ExpressRoute a la sesión de PowerShell para empezar a usar los cmdlets de ExpressRoute. Ejecute los comandos siguientes para importar los módulos de Azure y ExpressRoute a la sesión de PowerShell.  

        Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
        Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

2. **Crear un circuito de ExpressRoute.**
    
    Siga las instrucciones para crear un [circuito ExpressRoute](expressroute-howto-circuit-classic.md) y ha proporcionado por el proveedor de servicios de conectividad. Si su proveedor de servicios de conectividad ofrece servicios de nivel 3 administrados, puede solicitar su proveedor de conectividad para habilitar interconexión privada Azure para usted. En ese caso, no tendrá que siga las instrucciones que se indican en las siguientes secciones. Sin embargo, si su proveedor de servicios de conectividad no administrar el enrutamiento, después de crear su circuito, siga las instrucciones siguientes. 

3. **Compruebe el circuito de ExpressRoute para asegurarse de que se aprovisiona.**

    En primer lugar, debe comprobar si el circuito ExpressRoute se aprovisiona y también habilitado. Consulte el ejemplo siguiente.

        PS C:\> Get-AzureDedicatedCircuit -ServiceKey "*********************************"

        Bandwidth                        : 200
        CircuitName                      : MyTestCircuit
        Location                         : Silicon Valley
        ServiceKey                       : *********************************
        ServiceProviderName              : equinix
        ServiceProviderProvisioningState : Provisioned
        Sku                              : Standard
        Status                           : Enabled

    Asegúrese de que el circuito se muestra como Provisioned y habilitado. Si no es así, trabajar con su proveedor de conectividad para obtener su circuito al estado obligatorio y estado.

        ServiceProviderProvisioningState : Provisioned
        Status                           : Enabled


4. **Configurar interconexión privada Azure para el circuito.**

    Asegúrese de que tiene los siguientes elementos antes de continuar con los siguientes pasos:

    - Un /30 subred del vínculo principal. No debe ser parte de cualquier espacio de direcciones reservado para redes virtuales.
    - Un /30 subred del vínculo secundario. No debe ser parte de cualquier espacio de direcciones reservado para redes virtuales.
    - Un ID válido para establecer esta interconexión en. Asegúrese de que ningún otro interconexión circuito utiliza el mismo identificador de VLAN.
    - COMO número de interconexión. Puede utilizar 2 bytes y 4 bytes como números. Puede usar una privada como número para este interconexión. Asegúrese de que no está usando 65515.
    - Un hash MD5 si decide usar uno. **Esto es opcional**.
    
    Puede ejecutar el siguiente cmdlet para configurar interconexión privada Azure para su circuito.

        New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100

    Puede usar el cmdlet siguiente si decide usar un hash MD5.

        New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100 -SharedKey "A1B2C3D4"

    >[AZURE.IMPORTANT] Asegúrese de que especifica el número de AS como ASN interconexión, no el cliente ASN.

### <a name="to-view-azure-private-peering-details"></a>Para ver los detalles de interconexión privadas Azure

Puede obtener los detalles de configuración con el siguiente cmdlet

    Get-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"
    
    AdvertisedPublicPrefixes       : 
    AdvertisedPublicPrefixesState  : Configured
    AzureAsn                       : 12076
    CustomerAutonomousSystemNumber : 
    PeerAsn                        : 1234
    PrimaryAzurePort               : 
    PrimaryPeerSubnet              : 10.0.0.0/30
    RoutingRegistryName            : 
    SecondaryAzurePort             : 
    SecondaryPeerSubnet            : 10.0.0.4/30
    State                          : Enabled
    VlanId                         : 100


### <a name="to-update-azure-private-peering-configuration"></a>Actualizar la configuración de interconexión privada Azure

Puede actualizar cualquier parte de la configuración con el siguiente cmdlet. En el ejemplo siguiente, el identificador de VLAN del circuito se está actualizando desde 100 a 500.

    Set-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 500 -SharedKey "A1B2C3D4"

### <a name="to-delete-azure-private-peering"></a>Para eliminar la interconexión privada de Azure

Puede quitar la configuración de interconexión, ejecute el cmdlet siguiente.

>[AZURE.WARNING] Debe asegurarse de que todas las redes virtuales son no vinculadas desde el circuito ExpressRoute antes de ejecutar este cmdlet. 

    Remove-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"


## <a name="azure-public-peering"></a>Interconexión pública de Azure

Esta sección proporciona instrucciones sobre cómo crear, obtener, actualizar y eliminar la configuración de interconexión pública Azure para un circuito ExpressRoute.

### <a name="to-create-azure-public-peering"></a>Para crear la interconexión pública de Azure

1. **Importar el módulo de PowerShell para ExpressRoute.**
    
    Debe importar los módulos de Azure y ExpressRoute a la sesión de PowerShell para empezar a usar los cmdlets de ExpressRoute. Ejecute los comandos siguientes para importar los módulos de Azure y ExpressRoute a la sesión de PowerShell. 

        Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
        Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

2. **Crear un circuito ExpressRoute**
    
    Siga las instrucciones para crear un [circuito ExpressRoute](expressroute-howto-circuit-classic.md) y ha proporcionado por el proveedor de servicios de conectividad. Si su proveedor de servicios de conectividad ofrece servicios de nivel 3 administrados, puede solicitar su proveedor de conectividad para habilitar interconexión pública Azure para usted. En ese caso, no tendrá que siga las instrucciones que se indican en las siguientes secciones. Sin embargo, si su proveedor de servicios de conectividad no administrar el enrutamiento, después de crear su circuito, siga las instrucciones siguientes.

3. **Compruebe el circuito de ExpressRoute para asegurarse de que se aprovisiona**

    En primer lugar, debe comprobar si el circuito ExpressRoute se aprovisiona y también habilitado. Consulte el ejemplo siguiente.

        PS C:\> Get-AzureDedicatedCircuit -ServiceKey "*********************************"

        Bandwidth                        : 200
        CircuitName                      : MyTestCircuit
        Location                         : Silicon Valley
        ServiceKey                       : *********************************
        ServiceProviderName              : equinix
        ServiceProviderProvisioningState : Provisioned
        Sku                              : Standard
        Status                           : Enabled

    Asegúrese de que el circuito se muestra como Provisioned y habilitado. Si no es así, trabajar con su proveedor de conectividad para obtener su circuito al estado obligatorio y estado.

        ServiceProviderProvisioningState : Provisioned
        Status                           : Enabled

    

4. **Configurar interconexión pública Azure para el circuito**

    Asegurarse de que tiene la información siguiente antes de continuar más.

    - Un /30 subred del vínculo principal. Debe ser un prefijo IPv4 público válido.
    - Un /30 subred del vínculo secundario. Debe ser un prefijo IPv4 público válido.
    - Un ID válido para establecer esta interconexión en. Asegúrese de que ningún otro interconexión circuito utiliza el mismo identificador de VLAN.
    - COMO número de interconexión. Puede utilizar 2 bytes y 4 bytes como números.
    - Un hash MD5 si decide usar uno. **Esto es opcional**.
    
    Puede ejecutar el siguiente cmdlet para configurar interconexión pública Azure para su circuito

        New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200

    Puede usar el cmdlet siguiente si decide usar un hash MD5

        New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200 -SharedKey "A1B2C3D4"

    >[AZURE.IMPORTANT] Asegúrese de que especifica el número de AS como interconexión ASN y no el cliente ASN.

### <a name="to-view-azure-public-peering-details"></a>Para ver los detalles de interconexión públicas Azure

Puede obtener los detalles de configuración con el siguiente cmdlet

    Get-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"
    
    AdvertisedPublicPrefixes       : 
    AdvertisedPublicPrefixesState  : Configured
    AzureAsn                       : 12076
    CustomerAutonomousSystemNumber : 
    PeerAsn                        : 1234
    PrimaryAzurePort               : 
    PrimaryPeerSubnet              : 131.107.0.0/30
    RoutingRegistryName            : 
    SecondaryAzurePort             : 
    SecondaryPeerSubnet            : 131.107.0.4/30
    State                          : Enabled
    VlanId                         : 200


### <a name="to-update-azure-public-peering-configuration"></a>Actualizar la configuración de interconexión pública Azure

Puede actualizar cualquier parte de la configuración con el siguiente cmdlet

    Set-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 600 -SharedKey "A1B2C3D4"

El identificador de VLAN del circuito se está actualizando desde 200 a 600 en el ejemplo anterior.

### <a name="to-delete-azure-public-peering"></a>Para eliminar la interconexión pública de Azure

Puede quitar la configuración de interconexión ejecutando el siguiente cmdlet

    Remove-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"

## <a name="microsoft-peering"></a>Interconexión de Microsoft

Esta sección proporciona instrucciones sobre cómo crear, obtener, actualizar y eliminar la configuración de interconexión de Microsoft para un circuito ExpressRoute. 

### <a name="to-create-microsoft-peering"></a>Para crear Microsoft interconexión

1. **Importar el módulo de PowerShell para ExpressRoute.**
    
    Debe importar los módulos de Azure y ExpressRoute a la sesión de PowerShell para empezar a usar los cmdlets de ExpressRoute. Ejecute los comandos siguientes para importar los módulos de Azure y ExpressRoute a la sesión de PowerShell.  

        Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
        Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

2. **Crear un circuito ExpressRoute**
    
    Siga las instrucciones para crear un [circuito ExpressRoute](expressroute-howto-circuit-classic.md) y ha proporcionado por el proveedor de servicios de conectividad. Si su proveedor de servicios de conectividad ofrece servicios de nivel 3 administrados, puede solicitar su proveedor de conectividad para habilitar interconexión privada Azure para usted. En ese caso, no tendrá que siga las instrucciones que se indican en las siguientes secciones. Sin embargo, si su proveedor de servicios de conectividad no administrar el enrutamiento, después de crear su circuito, siga las instrucciones siguientes.

3. **Compruebe el circuito de ExpressRoute para asegurarse de que se aprovisiona**

    En primer lugar debe comprobar si el circuito ExpressRoute está en estado Provisioned y habilitado.

        PS C:\> Get-AzureDedicatedCircuit -ServiceKey "*********************************"

        Bandwidth                        : 200
        CircuitName                      : MyTestCircuit
        Location                         : Silicon Valley
        ServiceKey                       : *********************************
        ServiceProviderName              : equinix
        ServiceProviderProvisioningState : Provisioned
        Sku                              : Standard
        Status                           : Enabled

    Asegúrese de que el circuito se muestra como Provisioned y habilitado. Si no es así, trabajar con su proveedor de conectividad para obtener su circuito al estado obligatorio y estado.

        ServiceProviderProvisioningState : Provisioned
        Status                           : Enabled


4. **Configurar Microsoft interconexión para el circuito**

    Asegúrese de que tiene la información siguiente antes de continuar.

    - Un /30 subred del vínculo principal. Debe ser un prefijo de IPv4 público válido posea y registrado en un RIR / TIR.
    - Un /30 subred del vínculo secundario. Debe ser un prefijo de IPv4 público válido posea y registrado en un RIR / TIR.
    - Un ID válido para establecer esta interconexión en. Asegúrese de que ningún otro interconexión circuito utiliza el mismo identificador de VLAN.
    - COMO número de interconexión. Puede utilizar 2 bytes y 4 bytes como números.
    - Anuncia prefijos: debe proporcionar una lista de todos los prefijos planea anunciar sobre la sesión BGP. Se aceptan solo prefijos de direcciones IP públicos. Puede enviar una lista separada por comas si va a enviar un conjunto de prefijos. Estos prefijos deben estar registrados para usted en un RIR / TIR.
    - Cliente ASN: Si son prefijos de publicidad que no están registrados para la interconexión como número, puede especificar el número de AS a los que están registrados. **Esto es opcional**.
    - Nombre de ruta del registro: Puede especificar el RIR / TIR frente a los que el número y prefijos se registrado.
    - Un hash MD5, si decide usar uno. **Esto es opcional.**
    
    Puede ejecutar el siguiente cmdlet para configurar pering de Microsoft para su circuito

        New-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"


### <a name="to-view-microsoft-peering-details"></a>Para ver los detalles de interconexión de Microsoft

Puede obtener los detalles de configuración con el siguiente cmdlet.

    Get-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"
    
    AdvertisedPublicPrefixes       : 123.0.0.0/30
    AdvertisedPublicPrefixesState  : Configured
    AzureAsn                       : 12076
    CustomerAutonomousSystemNumber : 2245
    PeerAsn                        : 1234
    PrimaryAzurePort               : 
    PrimaryPeerSubnet              : 10.0.0.0/30
    RoutingRegistryName            : ARIN
    SecondaryAzurePort             : 
    SecondaryPeerSubnet            : 10.0.0.4/30
    State                          : Enabled
    VlanId                         : 300


### <a name="to-update-microsoft-peering-configuration"></a>Actualizar la configuración de interconexión de Microsoft

Puede actualizar cualquier parte de la configuración con el siguiente cmdlet.

        Set-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"

### <a name="to-delete-microsoft-peering"></a>Para eliminar interconexión de Microsoft

Puede quitar la configuración de interconexión, ejecute el cmdlet siguiente.

    Remove-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"

## <a name="next-steps"></a>Pasos siguientes

A continuación, [vincular un VNet a un circuito de ExpressRoute](expressroute-howto-linkvnet-classic.md).


-  Para obtener más información sobre los flujos de trabajo, consulte [ExpressRoute flujos de trabajo](expressroute-workflows.md).
-  Para obtener más información acerca de circuito interconexión, vea [ExpressRoute circuitos y dominios de enrutamiento](expressroute-circuit-peerings.md).

