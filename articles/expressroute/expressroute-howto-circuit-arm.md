<properties
   pageTitle="Crear y modificar un circuito ExpressRoute mediante el Administrador de recursos y PowerShell | Microsoft Azure"
   description="En este artículo se describe cómo crear, aprovisionar, comprobar, actualizar, eliminar y activar un circuito ExpressRoute."
   documentationCenter="na"
   services="expressroute"
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
   ms.author="ganesr"/>


# <a name="create-and-modify-an-expressroute-circuit"></a>Crear y modificar un circuito ExpressRoute

> [AZURE.SELECTOR]
[Portal de Azure - Administrador de recursos](expressroute-howto-circuit-portal-resource-manager.md)
[PowerShell - Administrador de recursos](expressroute-howto-circuit-arm.md)
[PowerShell - clásica](expressroute-howto-circuit-classic.md)


En este artículo se describe cómo crear un circuito de Azure ExpressRoute mediante cmdlets de Windows PowerShell y el modelo de implementación de administrador de recursos de Azure. En este artículo también le mostrará cómo comprobar el estado del circuito, actualizar, o eliminar y anular.

**Acerca de los modelos de implementación de Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-you-begin"></a>Antes de empezar


- Obtener la última versión de los módulos de Azure PowerShell (al menos versión 1.0). Para obtener instrucciones paso a paso sobre cómo configurar su PC para usar los módulos de PowerShell, siga las instrucciones de [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md).

- Revise los [requisitos previos](expressroute-prerequisites.md) y [flujos de trabajo](expressroute-workflows.md) antes de comenzar la configuración.

## <a name="create-and-provision-an-expressroute-circuit"></a>Crear y aprovisionar un circuito ExpressRoute

### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1. Inicie sesión en su cuenta de Azure y seleccione la suscripción

Para empezar la configuración, inicie sesión en su cuenta de Azure. Para obtener más información acerca de PowerShell, vea [Usar Windows PowerShell con el Administrador de recursos](../powershell-azure-resource-manager.md). Utilice los siguientes ejemplos para ayudarle a conectarse:

    Login-AzureRmAccount

Compruebe las suscripciones para la cuenta:

    Get-AzureRmSubscription

Seleccione la suscripción que desea crear un circuito ExpressRoute para:

    Select-AzureRmSubscription -SubscriptionId "<subscription ID>"

### <a name="2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2. obtener una lista de proveedores admitidos, las ubicaciones y anchos de banda

Antes de crear un circuito ExpressRoute, se necesita la lista de proveedores de conectividad admitidos, ubicaciones y opciones de ancho de banda.

El cmdlet de PowerShell `Get-AzureRmExpressRouteServiceProvider` devuelve esta información, que deberá usar en los pasos siguientes:

    Get-AzureRmExpressRouteServiceProvider

Compruebe si su proveedor de servicios de conectividad se muestra aquí. Anote la información siguiente porque lo necesitará más adelante cuando se crea un circuito:

- Nombre

- PeeringLocations

- BandwidthsOffered

Ahora ya está listo para crear un circuito ExpressRoute.   

### <a name="3-create-an-expressroute-circuit"></a>3. crear un circuito ExpressRoute

Si todavía no tiene un grupo de recursos, debe crearla antes de crear su circuito ExpressRoute. Puede hacerlo ejecutando el siguiente comando:


    New-AzureRmResourceGroup -Name "ExpressRouteResourceGroup" -Location "West US"


En el ejemplo siguiente se muestra cómo crear una Mbps 200 ExpressRoute circuito a través de Equinix en Silicon Valley. Si usa otro proveedor y diferentes configuraciones, sustituir dicha información al realizar la solicitud. A continuación se muestra una solicitud de ejemplo para una nueva clave de servicio:

    New-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200

Asegúrese de que se especifique el nivel SKU correcta y familia SKU:

- Nivel SKU determina si se habilita un estándar de ExpressRoute o un complemento de premium ExpressRoute. Puede especificar *estándar* para obtener el SKU o *Premium* estándar para el complemento premium.

- Familia SKU determina el tipo de facturación. Puede especificar *Metereddata* para un plan de datos de uso medido y *Unlimiteddata* a un plan de datos ilimitado. Tenga en cuenta que puede cambiar el tipo de facturación de *Metereddata* a *Unlimiteddata*, pero no puede cambiar el tipo de *Unlimiteddata* a *Metereddata*.


>[AZURE.IMPORTANT] Su circuito ExpressRoute se cargará desde el momento en que se envía una clave de servicio. Asegúrese de realizar esta operación cuando el proveedor de servicios de conectividad está listo para aprovisionar el circuito.

La respuesta contiene la clave del servicio. Puede obtener una descripción detallada de todos los parámetros, ejecute lo siguiente:


    get-help New-AzureRmExpressRouteCircuit -detailed


### <a name="4-list-all-expressroute-circuits"></a>4. todos los circuitos ExpressRoute de lista de

Para obtener una lista de todos los circuitos ExpressRoute que ha creado, ejecute el `Get-AzureRmExpressRouteCircuit` comando:


    Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

La respuesta tendrá un aspecto similar al siguiente ejemplo:


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
    CircuitProvisioningState          : Enabled
    ServiceProviderProvisioningState  : NotProvisioned
    ServiceProviderNotes              :
    ServiceProviderProperties         : {
                                          "ServiceProviderName": "Equinix",
                                          "PeeringLocation": "Silicon Valley",
                                          "BandwidthInMbps": 200
                                        }
    ServiceKey                        : **************************************
    Peerings                          : []

Puede recuperar esta información en cualquier momento mediante la `Get-AzureRmExpressRouteCircuit` cmdlet. Realiza la llamada sin parámetros enumera todos los circuitos. La clave de servicio se mostrarán en el campo de *valor de ServiceKey* :


    Get-AzureRmExpressRouteCircuit


La respuesta tendrá un aspecto similar al siguiente ejemplo:


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
    ServiceProviderProvisioningState : NotProvisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                         "ServiceProviderName": "Equinix",
                                         "PeeringLocation": "Silicon Valley",
                                         "BandwidthInMbps": 200
                                       }
    ServiceKey                       : **************************************
    Peerings                         : []


Puede obtener una descripción detallada de todos los parámetros, ejecute lo siguiente:


    get-help Get-AzureRmExpressRouteCircuit -detailed

### <a name="5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5. enviar la clave de servicio a su proveedor de servicios de conectividad de aprovisionamiento

*ServiceProviderProvisioningState* proporciona información sobre el estado actual de aprovisionamiento en el lado del proveedor de servicios. Estado proporciona el estado en el lado de Microsoft. Para obtener más información acerca de los Estados de aprovisionamiento de circuito, vea el artículo de [flujos de trabajo](expressroute-workflows.md#expressroute-circuit-provisioning-states) .

Cuando se crea un nuevo circuito de ExpressRoute, el circuito estarán en el estado de los siguiente:


    ServiceProviderProvisioningState : NotProvisioned
    CircuitProvisioningState         : Enabled



El circuito cambiará al siguiente estado cuando es el proveedor de servicios de conectividad en el proceso de habilitación para usted:

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled

Para que pueda usar un circuito ExpressRoute, debe estar en el estado de los siguiente:

    ServiceProviderProvisioningState : Provisioned
    CircuitProvisioningState         : Enabled

### <a name="6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6. periódicamente comprobar el estado y el estado de la tecla de circuito

Comprobar el estado y el estado de la tecla de circuito permite saber cuándo su proveedor ha habilitado el circuito. Tras el circuito se ha configurado, *ServiceProviderProvisioningState* aparece como *Provisioned*, tal como se muestra en el ejemplo siguiente:


    Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"


La respuesta tendrá un aspecto similar al siguiente ejemplo:


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

### <a name="7-create-your-routing-configuration"></a>7. crear la configuración de enrutamiento

Para obtener instrucciones detalladas, vea el artículo de la [configuración de enrutamiento de circuito ExpressRoute](expressroute-howto-routing-arm.md) para crear y modificar circuito peerings.


>[AZURE.IMPORTANT] Estas instrucciones solo se aplican a circuitos que se crean con proveedores de servicios que ofrecen servicios de conectividad 2 de capa. Si está usando un proveedor de servicios que ofrece administrado nivel 3 servicios (generalmente una IP VPN, como MPLS), configurará y administrar el enrutamiento para su proveedor de servicios de conectividad.

### <a name="8-link-a-virtual-network-to-an-expressroute-circuit"></a>8. vincular una red virtual a un circuito ExpressRoute

A continuación, vincular una red virtual a su circuito ExpressRoute. Utilice el artículo de [redes virtuales de vinculación a circuitos ExpressRoute](expressroute-howto-linkvnet-arm.md) cuando se trabaja con el modelo de implementación de administrador de recursos.

## <a name="getting-the-status-of-an-expressroute-circuit"></a>Obtener el estado de un circuito ExpressRoute

Puede recuperar esta información en cualquier momento mediante la `Get-AzureRmExpressRouteCircuit` cmdlet. Realiza la llamada sin parámetros enumera todos los circuitos.

    Get-AzureRmExpressRouteCircuit


La respuesta será similar al siguiente ejemplo:


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


Puede obtener información sobre un circuito ExpressRoute específico pasando el nombre del grupo de recursos y circuito como un parámetro a la llamada:


    Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"


La respuesta tendrá un aspecto similar al siguiente ejemplo:


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


Puede obtener una descripción detallada de todos los parámetros, ejecute lo siguiente:

    get-help get-azurededicatedcircuit -detailed


## <a name="modify"></a>Modificar un circuito ExpressRoute

Puede modificar determinadas propiedades de un circuito ExpressRoute sin afectar a la conectividad.

Puede hacer lo siguiente sin tiempo de inactividad:

- Habilitar o deshabilitar un complemento de premium ExpressRoute para su circuito ExpressRoute.
- Aumentar el ancho de banda de su circuito ExpressRoute. Tenga en cuenta que no se admite degradar el ancho de banda de un circuito.
- Cambiar el plan de medición de datos de uso medido a datos ilimitado. Tenga en cuenta que no será posible cambiar el plan de medición de datos ilimitado a los datos de uso medido.
-  Puede habilitar y deshabilitar *Permitir operaciones clásico*.

Para obtener más información sobre los límites y limitaciones, consulte las [Preguntas más frecuentes de ExpressRoute](expressroute-faqs.md).

### <a name="to-enable-the-expressroute-premium-add-on"></a>Para habilitar el complemento de premium ExpressRoute

Puede habilitar el complemento de premium ExpressRoute para su circuito existente mediante el siguiente fragmento de PowerShell:

    $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    $ckt.Sku.Tier = "Premium"
    $ckt.sku.Name = "Premium_MeteredData"

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


El circuito ahora tiene habilitadas las funciones de complemento ExpressRoute premium. Tenga en cuenta que se iniciará facturación para la capacidad de complemento premium tan pronto como se ha ejecutado correctamente el comando.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Para deshabilitar el complemento de premium ExpressRoute

>[AZURE.IMPORTANT] Esta operación puede fallar si está usando los recursos que sean superiores a lo que está permitido para el circuito estándar.

Tenga en cuenta lo siguiente:

- Antes de degradar premium a estándar, debe asegurarse de que el número de redes virtuales que están vinculados al circuito es menos de 10. Si no es así, se produce un error en la solicitud de actualización y se facturará en tarifas.

- Debe desvincular todas las redes virtuales en las demás regiones geopolíticas. Si no es así, se producirá un error en la solicitud de actualización y se facturará en tarifas.

- La tabla de ruta debe ser inferior a 4.000 rutas de interconexión privada. Si el tamaño de la tabla de ruta es mayor que 4.000 redirige, la sesión BGP quita y no puede volver a habilitar hasta que el número de prefijos anunciados pasa por debajo de 4.000.

Puede deshabilitar el complemento de premium ExpressRoute para el circuito existente mediante el siguiente cmdlet de PowerShell:


    $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    $ckt.Sku.Tier = "Standard"
    $ckt.sku.Name = "Standard_MeteredData"

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Para actualizar el ancho de banda de circuito ExpressRoute

Para opciones de ancho de banda compatibles para el proveedor, consulte las [Preguntas más frecuentes de ExpressRoute](expressroute-faqs.md). Puede seleccionar cualquier tamaño mayor que el tamaño de su circuito existente.

>[AZURE.IMPORTANT] No puede reducir el ancho de banda de un circuito ExpressRoute sin interrupciones. Bajar el nivel de ancho de banda requiere que desactive el circuito ExpressRoute y, a continuación, vuelva a aprovisionar un nuevo circuito de ExpressRoute.

Después de decidir qué tamaño es necesario, utilice el comando siguiente para cambiar su circuito:


    $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    $ckt.ServiceProviderProperties.BandwidthInMbps = 1000

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


El circuito se tamaño hacia arriba en el lado de Microsoft. A continuación, deberá ponerse en contacto con su proveedor de servicios de conectividad para actualizar las configuraciones por su parte para que coincida con este cambio. Después de realizar esta notificación, empezaremos facturación para la opción de ancho de banda actualizado.


### <a name="to-move-the-sku-from-metered-to-unlimited"></a>Para mover el SKU de uso medido ilimitada

Puede cambiar el SKU de un circuito ExpressRoute mediante el siguiente fragmento de PowerShell:

    $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    $ckt.Sku.Family = "UnlimitedData"
    $ckt.sku.Name = "Premium_UnlimitedData"

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>Para controlar el acceso a entornos de administrador de recursos y clásico  

Revisar las instrucciones de [circuitos ExpressRoute mover desde el estándar para el modelo de implementación de administrador de recursos](expressroute-howto-move-arm.md).  


## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a>Baja y eliminar un circuito ExpressRoute

Tenga en cuenta lo siguiente:

- Debe desvincular todas las redes virtuales desde el circuito ExpressRoute. Si esta operación falla, compruebe si las redes virtuales están vinculadas al circuito.

- Si el estado aprovisionamiento del proveedor de servicio de ExpressRoute circuito es **aprovisionamiento** o **Provisioned** debe trabajar con su proveedor de servicio para activar el circuito por su parte. Seguimos reservar recursos y bill hasta que el proveedor de servicios completa baja el circuito y nos notifica.

- Si el proveedor de servicios ha aprovisionamiento anulado el circuito (el estado de aprovisionamiento del proveedor de servicio está establecido en **no se aprovisione**), a continuación, puede eliminar el circuito. Esto dejará de facturación para el circuito

Puede eliminar el circuito ExpressRoute ejecutando el siguiente comando:

    Remove-AzureRmExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit"



## <a name="next-steps"></a>Pasos siguientes

Después de crear su circuito, asegúrese de que lo siguiente:

- [Crear y modificar el enrutamiento de su circuito ExpressRoute](expressroute-howto-routing-arm.md)
- [Vínculo de su red virtual a su circuito ExpressRoute](expressroute-howto-linkvnet-arm.md)
