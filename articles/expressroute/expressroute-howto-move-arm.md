<properties
   pageTitle="Mover ExpressRoute circuitos de clásico para el Administrador de recursos | Microsoft Azure"
   description="Esta página describe cómo mover un circuito clásico al modelo de implementación de administrador de recursos."
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


# <a name="move-expressroute-circuits-from-the-classic-to-the-resource-manager-deployment-model"></a>Mover ExpressRoute circuitos de clásico al modelo de implementación de administrador de recursos

## <a name="configuration-prerequisites"></a>Requisitos previos de configuración

- Necesita la última versión de los módulos de Azure PowerShell (al menos versión 1.0).
- Asegúrese de que ha revisado los [requisitos previos](expressroute-prerequisites.md), [requisitos de enrutamiento](expressroute-routing.md)y [flujos de trabajo](expressroute-workflows.md) antes de comenzar la configuración.
- Antes de proceder aún más, revise la información que se proporciona en [mover un circuito ExpressRoute de clásico para el Administrador de recursos](expressroute-move.md). Asegúrese de que ha comprendido totalmente los límites y limitaciones de lo que es posible.
- Si desea mover un circuito de Azure ExpressRoute del modelo de implementación clásico al modelo de implementación de administrador de recursos de Azure, debe tener el circuito completamente configurado y operativo en el modelo de implementación clásico.
- Asegurarse de que tiene un grupo de recursos que se creó en el modelo de implementación de administrador de recursos.

## <a name="move-the-expressroute-circuit-to-the-resource-manager-deployment-model"></a>Mueva el circuito ExpressRoute al modelo de implementación de administrador de recursos

Debe mover un circuito ExpressRoute al modelo de implementación de administrador de recursos de modo que puede usar en el estándar y los modelos de implementación de administrador de recursos. Para ello, ejecute los siguientes comandos de PowerShell.

### <a name="step-1-gather-circuit-details-from-the-classic-deployment-model"></a>Paso 1: Recopilar detalles de circuito del modelo de implementación clásica

Debe recopilar información sobre su circuito ExpressRoute en primer lugar.

Inicie sesión en el entorno clásico Azure y recopile la clave del servicio. Puede usar el siguiente fragmento de PowerShell para recopilar la información:

    # Sign in to your Azure account
    Add-AzureAccount

    # Select the appropriate Azure subscription
    Select-AzureSubscription "<Enter Subscription Name here>"

    # Import the PowerShell modules for Azure and ExpressRoute
    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

    # Get the service keys of all your ExpressRoute circuits
    Get-AzureDedicatedCircuit

Copie la **clave de servicio** del circuito que desee mover al modelo de implementación de administrador de recursos.

### <a name="step-2-sign-in-to-the-resource-manager-environment-and-create-a-new-resource-group"></a>Paso 2: Inicie sesión en el entorno del Administrador de recursos y crear un nuevo grupo de recursos

Puede crear un nuevo grupo de recursos mediante el fragmento de código siguiente:

    # Sign in to your Azure Resource Manager environment
    Login-AzureRmAccount

    # Select the appropriate Azure subscription
    Get-AzureRmSubscription -SubscriptionName "<Enter Subscription Name here>" | Select-AzureRmSubscription

    #Create a new resource group if you don't already have one
    New-AzureRmResourceGroup -Name "DemoRG" -Location "West US"

También puede usar un grupo de recursos existente si ya tiene una.

### <a name="step-3-move-the-expressroute-circuit-to-the-resource-manager-deployment-model"></a>Paso 3: Mueva el circuito ExpressRoute al modelo de implementación de administrador de recursos

Ya está listo para mover sobre su circuito ExpressRoute de clásico al modelo de implementación de administrador de recursos. Revise la información provista en [mover un circuito ExpressRoute desde el estándar para el modelo de implementación de administrador de recursos](expressroute-move.md) antes de continuar.

Para ello, puede ejecutar el fragmento de código siguiente:

    Move-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "DemoRG" -Location "West US" -ServiceKey "<Service-key>"

>[AZURE.NOTE] Cuando haya finalizado el movimiento, el nuevo nombre que aparece en el cmdlet anterior se utilizará para el recurso de direcciones. Básicamente, se cambiará el circuito.

## <a name="enable-an-expressroute-circuit-for-both-deployment-models"></a>Habilitar un circuito ExpressRoute para ambos modelos de implementación

Debe mover su circuito ExpressRoute para el modelo de implementación de administrador de recursos antes de controlar el acceso al modelo de implementación.

Ejecute el cmdlet siguiente para habilitar el acceso a los modelos de implementación:

    # Get details of the ExpressRoute circuit
    $ckt = Get-AzureRmExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"

    #Set "Allow Classic Operations" to TRUE
    $ckt.AllowClassicOperations = $true

    # Update circuit
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

Después de esta operación finalizó correctamente, podrá ver el circuito en el modelo de implementación clásico.

Ejecute el siguiente procedimiento para obtener los detalles del circuito ExpressRoute:

    get-azurededicatedcircuit

Debe poder ver la clave de servicio que se muestran. Ahora pueden administrar vínculos al circuito ExpressRoute con los comandos de modelo de implementación clásica estándar para VNets clásica y sus comandos BRAZO estándares para BRAZO VNETs. Los siguientes artículos le guiará durante el proceso administrar vínculos a circuito de ExpressRoute:

- [Vínculo de su red virtual a su circuito ExpressRoute en el modelo de implementación de administrador de recursos](expressroute-howto-linkvnet-arm.md)
- [Vínculo de su red virtual a su circuito ExpressRoute en el modelo de implementación clásica](expressroute-howto-linkvnet-classic.md)


## <a name="disable-the-expressroute-circuit-to-the-classic-deployment-model"></a>Deshabilitar el circuito de ExpressRoute para el modelo de implementación clásica

Ejecute el cmdlet siguiente para deshabilitar el acceso al modelo de implementación clásica:

    # Get details of the ExpressRoute circuit
    $ckt = Get-AzureRmExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"

    #Set "Allow Classic Operations" to FALSE
    $ckt.AllowClassicOperations = $false

    # Update circuit
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

Después de esta operación finalizó correctamente, no podrá ver el circuito en el modelo de implementación clásico.

## <a name="next-steps"></a>Pasos siguientes

Después de crear su circuito, asegúrese de que lo siguiente:

- [Crear y modificar el enrutamiento de su circuito ExpressRoute](expressroute-howto-routing-arm.md)
- [Vínculo de su red virtual a su circuito ExpressRoute](expressroute-howto-linkvnet-arm.md)
