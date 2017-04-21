<properties
   pageTitle="Crear y modificar un circuito ExpressRoute mediante el modelo de implementación clásica y PowerShell | Microsoft Azure"
   description="En este artículo le guiará por los pasos necesarios para crear y aprovisionar un circuito ExpressRoute. También se muestra cómo activar su circuito y comprobar el estado, actualizar o eliminar."
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
   ms.author="ganesr;cherylmc"/>

# <a name="create-and-modify-an-expressroute-circuit"></a>Crear y modificar un circuito ExpressRoute

> [AZURE.SELECTOR]
[Portal de Azure - Administrador de recursos](expressroute-howto-circuit-portal-resource-manager.md)
[PowerShell - Administrador de recursos](expressroute-howto-circuit-arm.md)
[PowerShell - clásica](expressroute-howto-circuit-classic.md)

En este artículo le guiará por los pasos para crear un circuito de Azure ExpressRoute mediante cmdlets de PowerShell y el modelo de implementación clásico. En este artículo también le mostrará cómo activar un circuito ExpressRoute y comprobar el estado, actualizar o eliminar.

**Acerca de los modelos de implementación de Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]


## <a name="before-you-begin"></a>Antes de empezar

### <a name="1-review-the-prerequisites-and-workflow-articles"></a>1. revisar los requisitos previos y artículos de flujo de trabajo

Asegúrese de que ha revisado los [requisitos previos](expressroute-prerequisites.md) y [flujos de trabajo](expressroute-workflows.md) antes de comenzar la configuración.  


### <a name="2-install-the-latest-versions-of-the-azure-powershell-modules"></a>2. Instale las últimas versiones de los módulos de PowerShell de Azure 

Siga las instrucciones de [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) para obtener instrucciones paso a paso sobre cómo configurar su PC para usar los módulos de PowerShell de Azure.

### <a name="3-log-in-to-your-azure-account-and-select-a-subscription"></a>3. Inicie sesión en su cuenta de Azure y seleccione una suscripción

1. Ejecute el cmdlet siguiente con un símbolo del sistema con privilegios elevados de Windows PowerShell:

        Add-AzureAccount
2. En la pantalla Inicio de sesión que aparece, inicie sesión en su cuenta.

3. Obtener una lista de las suscripciones.

        Get-AzureSubscription
4. Seleccione la suscripción que desea usar.
    
        Select-AzureSubscription -SubscriptionName "mysubscriptionname"

## <a name="create-and-provision-an-expressroute-circuit"></a>Crear y aprovisionar un circuito ExpressRoute

### <a name="1-import-the-powershell-modules-for-expressroute"></a>1. importar los módulos de PowerShell para ExpressRoute

 Si no lo ha hecho ya, debe importar los módulos de Azure y ExpressRoute a la sesión de PowerShell para empezar a usar los cmdlets de ExpressRoute. Importar los módulos de la ubicación que se ha instalado en su equipo local. Según el método que se usó para instalar los módulos, la ubicación puede ser diferente al que se muestra en el ejemplo siguiente. Modificar el ejemplo si es necesario.  

    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

### <a name="2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2. obtener una lista de proveedores admitidos, las ubicaciones y anchos de banda

Antes de crear un circuito ExpressRoute, se necesita la lista de proveedores de conectividad admitidos, ubicaciones y opciones de ancho de banda.

El cmdlet de PowerShell `Get-AzureDedicatedCircuitServiceProvider` devuelve esta información, que deberá usar en los pasos siguientes:

    Get-AzureDedicatedCircuitServiceProvider

Compruebe si su proveedor de servicios de conectividad se muestra aquí. Anote la información siguiente porque lo necesitará más adelante cuando se crea un circuito:

- Nombre

- PeeringLocations

- BandwidthsOffered

Ahora ya está listo para crear un circuito ExpressRoute.         

### <a name="3-create-an-expressroute-circuit"></a>3. crear un circuito ExpressRoute

En el ejemplo siguiente se muestra cómo crear una Mbps 200 ExpressRoute circuito a través de Equinix en Silicon Valley. Si usa otro proveedor y diferentes configuraciones, sustituir dicha información al realizar la solicitud.

>[AZURE.IMPORTANT] Su circuito ExpressRoute se cargará desde el momento en que se envía una clave de servicio. Asegúrese de realizar esta operación cuando el proveedor de servicios de conectividad está listo para aprovisionar el circuito.


A continuación se muestra una solicitud de ejemplo para una nueva clave de servicio:

    $Bandwidth = 200
    $CircuitName = "MyTestCircuit"
    $ServiceProvider = "Equinix"
    $Location = "Silicon Valley"

    New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -sku Standard -BillingType MeteredData

O bien, si desea crear un circuito de ExpressRoute con el complemento premium, use el siguiente ejemplo. Consulte las [Preguntas más frecuentes de ExpressRoute](expressroute-faqs.md) para obtener más detalles sobre el complemento premium.

    New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -sku Premium - BillingType MeteredData


La respuesta contendrá la clave del servicio. Puede obtener una descripción detallada de todos los parámetros, ejecute lo siguiente:

    get-help new-azurededicatedcircuit -detailed

### <a name="4-list-all-the-expressroute-circuits"></a>4. todos los circuitos ExpressRoute de lista de

Puede ejecutar la `Get-AzureDedicatedCircuit` comando para obtener una lista de todos los circuitos ExpressRoute que ha creado:


    Get-AzureDedicatedCircuit

La respuesta será similar al siguiente ejemplo:

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : NotProvisioned
    Sku                              : Standard
    Status                           : Enabled

Puede recuperar esta información en cualquier momento mediante la `Get-AzureDedicatedCircuit` cmdlet. Realiza la llamada sin parámetros enumera todos los circuitos. La clave de servicio se mostrarán en el campo de *valor de ServiceKey* .

    Get-AzureDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : NotProvisioned
    Sku                              : Standard
    Status                           : Enabled

Puede obtener una descripción detallada de todos los parámetros, ejecute lo siguiente:

    get-help get-azurededicatedcircuit -detailed

### <a name="5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5. enviar la clave de servicio a su proveedor de servicios de conectividad de aprovisionamiento


*ServiceProviderProvisioningState* proporciona información sobre el estado actual de aprovisionamiento en el lado del proveedor de servicios. *Estado* proporciona el estado en el lado de Microsoft. Para obtener más información acerca de los Estados de aprovisionamiento de circuito, vea el artículo de [flujos de trabajo](expressroute-workflows.md#expressroute-circuit-provisioning-states) .

Cuando se crea un nuevo circuito de ExpressRoute, el circuito estarán en el estado de los siguiente:

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


El circuito pasará al siguiente estado cuando es el proveedor de servicios de conectividad en el proceso de habilitación para usted:

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled

Un circuito ExpressRoute debe estar en el estado siguiente para poder usarlo:

    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled


### <a name="6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6. periódicamente comprobar el estado y el estado de la tecla de circuito

Esto le permite saber cuándo su proveedor ha habilitado el circuito. Después de configurar el circuito *ServiceProviderProvisioningState* se mostrará como *Provisioned* tal como se muestra en el ejemplo siguiente:

    Get-AzureDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

### <a name="7-create-your-routing-configuration"></a>7. crear la configuración de enrutamiento

Consulte la [ExpressRoute circuito configuración de enrutamiento (crear y modificar circuito peerings)](expressroute-howto-routing-classic.md) artículo para obtener instrucciones paso a paso.

>[AZURE.IMPORTANT] Estas instrucciones solo se aplican a circuitos que se crean con proveedores de servicios que ofrecen servicios de conectividad 2 de capa. Si está usando un proveedor de servicios que ofrece administrado nivel 3 servicios (generalmente una IP VPN, como MPLS), configurará y administrar el enrutamiento para su proveedor de servicios de conectividad.

### <a name="8-link-a-virtual-network-to-an-expressroute-circuit"></a>8. vincular una red virtual a un circuito ExpressRoute

A continuación, vincular una red virtual a su circuito ExpressRoute. Para obtener instrucciones detalladas, consulte [circuitos ExpressRoute vinculación a redes virtuales](expressroute-howto-linkvnet-classic.md) . Si necesita crear una red virtual mediante el modelo de implementación clásico para ExpressRoute, consulte [crear una red virtual para ExpressRoute](expressroute-howto-vnet-portal-classic.md) para obtener instrucciones.

## <a name="getting-the-status-of-an-expressroute-circuit"></a>Obtener el estado de un circuito ExpressRoute

Puede recuperar esta información en cualquier momento mediante la `Get-AzureCircuit` cmdlet. Realiza la llamada sin parámetros enumera todos los circuitos.

    Get-AzureDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

    Bandwidth                        : 1000
    CircuitName                      : MyAsiaCircuit
    Location                         : Singapore
    ServiceKey                       : #################################
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

Puede obtener información sobre un circuito ExpressRoute específico pasando la clave del servicio como un parámetro a la llamada:

    Get-AzureDedicatedCircuit -ServiceKey "*********************************"

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled


Puede obtener una descripción detallada de todos los parámetros, ejecute lo siguiente:

    get-help get-azurededicatedcircuit -detailed

## <a name="modifying-an-expressroute-circuit"></a>Modificar un circuito ExpressRoute

Puede modificar determinadas propiedades de un circuito ExpressRoute sin afectar a la conectividad.

Puede hacer lo siguiente sin tiempo de inactividad:

- Habilitar o deshabilitar un complemento de premium ExpressRoute para su circuito ExpressRoute.
- Aumentar el ancho de banda de su circuito ExpressRoute. Tenga en cuenta que no se admite degradar el ancho de banda de un circuito.
- Cambiar el plan de medición de datos de uso medido a datos ilimitado. Tenga en cuenta que no será posible cambiar el plan de medición de datos ilimitado a los datos de uso medido.
- Puede habilitar y deshabilitar *Permitir operaciones clásico*.

Consulte las [Preguntas más frecuentes de ExpressRoute](expressroute-faqs.md) para obtener más información sobre los límites y limitaciones.

### <a name="to-enable-the-expressroute-premium-add-on"></a>Para habilitar el complemento de premium ExpressRoute

Puede habilitar el complemento de premium ExpressRoute para su circuito existente mediante el siguiente cmdlet de PowerShell:

    Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Premium

    Bandwidth                        : 1000
    CircuitName                      : TestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Premium
    Status                           : Enabled

Su circuito ahora tiene habilitadas las funciones de complemento ExpressRoute premium. Tenga en cuenta que se iniciará facturación para la capacidad de complemento premium tan pronto como se ha ejecutado correctamente el comando.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Para deshabilitar el complemento de premium ExpressRoute

>[AZURE.IMPORTANT] Esta operación puede fallar si está usando los recursos que sean superiores a lo que está permitido para el circuito estándar.

Tenga en cuenta lo siguiente:

- Debe asegurarse de que el número de redes virtuales vinculado al circuito es menos de 10 antes de degradar premium estándar. Si no es así, se producirá un error en la solicitud de actualización y estará facturado las tarifas.

- Debe desvincular todas las redes virtuales en las demás regiones geopolíticas. Si no es así, se producirá un error en la solicitud de actualización y estará facturado las tarifas.

- La tabla de ruta debe ser inferior a 4.000 rutas de interconexión privada. Si el tamaño de la tabla de ruta es mayor que 4.000 redirige, la sesión BGP, se quitará y no puede volver a habilitar hasta que el número de prefijos anunciados pasa por debajo de 4.000.

Puede deshabilitar el complemento de premium ExpressRoute para su circuito existente mediante el siguiente cmdlet de PowerShell:

    Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Standard

    Bandwidth                        : 1000
    CircuitName                      : TestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled



### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Para actualizar el ancho de banda de circuito ExpressRoute

Consulte las [Preguntas más frecuentes de ExpressRoute](expressroute-faqs.md) para opciones de ancho de banda compatibles para su proveedor de. Puede seleccionar cualquier tamaño es mayor que el tamaño de su circuito existente como permite el puerto físico (en el que se crea su circuito).

>[AZURE.IMPORTANT] No puede reducir el ancho de banda de un circuito ExpressRoute sin interrupciones. Bajar el nivel de ancho de banda requieren que se desactive el circuito ExpressRoute y, a continuación, vuelva a aprovisionar un nuevo circuito de ExpressRoute.

Después de decidir qué tamaño es necesario, puede usar el comando siguiente para cambiar el tamaño de su circuito:

    Set-AzureDedicatedCircuitProperties -ServiceKey ********************************* -Bandwidth 1000

    Bandwidth                        : 1000
    CircuitName                      : TestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

Su circuito habrá sido tamaño hacia arriba en el lado de Microsoft. Debe ponerse en contacto con su proveedor de servicios de conectividad para actualizar las configuraciones por su parte para que coincida con este cambio. Tenga en cuenta que se iniciará facturación para la opción de ancho de banda actualizado desde este punto en.

Si ve el siguiente error al aumentar el ancho de banda de circuito, hay significa no queda suficiente ancho de banda en el puerto físico donde se crea su circuito existente. Debe eliminar este circuito y crear un nuevo circuito del tamaño que necesita. 

    Set-AzureDedicatedCircuitProperties : InvalidOperation : Insufficient bandwidth available to perform this circuit
    update operation
    At line:1 char:1
    + Set-AzureDedicatedCircuitProperties -ServiceKey ********************* ...
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : CloseError: (:) [Set-AzureDedicatedCircuitProperties], CloudException
        + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.SetAzureDedicatedCircuitPropertiesCommand
    

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a>Baja y eliminar un circuito ExpressRoute

Tenga en cuenta lo siguiente:

- Debe desvincular todas las redes virtuales desde el circuito ExpressRoute para realizar esta operación correctamente. Compruebe si tiene cualquier redes virtuales que están vinculadas al circuito si se produce un error en esta operación.

- Si el estado aprovisionamiento del proveedor de servicio de ExpressRoute circuito es **aprovisionamiento** o **Provisioned** debe trabajar con su proveedor de servicio para activar el circuito por su parte. Seguimos reservar recursos y bill hasta que el proveedor de servicios completa baja el circuito y nos notifica.

- Si el proveedor de servicios ha aprovisionamiento anulado el circuito (el estado de aprovisionamiento del proveedor de servicio está establecido en **no se aprovisione**), a continuación, puede eliminar el circuito. Esto dejará de facturación para el circuito.

Puede eliminar el circuito ExpressRoute ejecutando el siguiente comando:

    Remove-AzureDedicatedCircuit -ServiceKey "*********************************"



## <a name="next-steps"></a>Pasos siguientes

Después de crear su circuito, asegúrese de que lo siguiente:

- [Crear y modificar el enrutamiento de su circuito ExpressRoute](expressroute-howto-routing-classic.md)
- [Vínculo de su red virtual a su circuito ExpressRoute](expressroute-howto-linkvnet-classic.md)
