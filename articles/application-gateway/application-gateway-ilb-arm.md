<properties
   pageTitle="Crear y configurar una puerta de enlace de la aplicación con un equilibrador de carga interno (ILB) mediante el Administrador de recursos de Azure | Microsoft Azure"
   description="Esta página contiene instrucciones para crear, configurar, iniciar y eliminar una puerta de enlace de aplicación Azure con equilibrador de carga interno (ILB) para el Administrador de recursos de Azure"
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/19/2016"
   ms.author="gwallace"/>


# <a name="create-an-application-gateway-with-an-internal-load-balancer-ilb-by-using-azure-resource-manager"></a>Crear una puerta de enlace de la aplicación con un equilibrador de carga interno (ILB) mediante el Administrador de recursos de Azure

> [AZURE.SELECTOR]
- [Pasos de clásicos de Azure](application-gateway-ilb.md)
- [Instrucciones de PowerShell de administrador de recursos](application-gateway-ilb-arm.md)

La puerta de enlace de aplicación Azure puede configurarse con una VIP a través de Internet o con un extremo interno que no se expone en Internet, también conocido como un extremo de (ILB) del equilibrador de carga interno. Configuración de la puerta de enlace con un ILB es útil para las aplicaciones de línea de negocio internas que no se exponen en Internet. También es útil para los servicios y niveles en una aplicación de varios niveles que sentarse en un límite de seguridad que no se expone en Internet, pero todavía necesitan turnos cargar distribución, se pega sesión o finalización de la capa de Sockets seguros (SSL).

En este artículo le guiará por los pasos para configurar una puerta de enlace de la aplicación con una ILB.

## <a name="before-you-begin"></a>Antes de empezar

1. Instalar la última versión de los cmdlets de PowerShell de Azure mediante el instalador de plataforma Web. Puede descargar e instalar la versión más reciente de la sección de **Windows PowerShell** de la [página de descargas](https://azure.microsoft.com/downloads/).
2. Crear una red virtual y una subred de puerta de enlace de aplicación. Asegúrese de que no hay máquinas virtuales o implementaciones de nube utilizan la subred. Puerta de enlace de aplicación debe ser por sí mismo en una subred de una red virtual.
3. Los servidores que configurar para que utilice la puerta de enlace de aplicación deben existir o han asignado sus extremos creados en una red virtual o con VIP o IP pública.

## <a name="what-is-required-to-create-an-application-gateway"></a>¿Qué se necesita para crear una puerta de enlace de la aplicación?


- **Grupo de servidores de back-end:** La lista de direcciones IP de los servidores de back-end. Las direcciones IP enumeradas o bien deben pertenecer a la red virtual, pero en una subred diferente de la puerta de enlace de aplicación o deben ser VIP o IP pública.
- **Configuración de la agrupación de back-end server:** Cada grupo tiene configuración como afinidad basada en cookies, protocolo y puerto. Estas opciones están vinculadas a un grupo y se aplican a todos los servidores dentro del grupo.
- **Puerto front-end:** Este puerto es el público que se abre en la puerta de enlace de aplicación. El tráfico llega a este puerto y, a continuación, se redirige a uno de los servidores de back-end.
- **Escucha:** La escucha tiene un puerto front-end, un protocolo (Http o Https, estos son distingue mayúsculas de minúsculas) y el nombre del certificado SSL (si se descarga la configuración de SSL).
- **Regla:** La regla enlaza la escucha y el grupo de servidor back-end y define el tráfico debe dirigirse a cuando llegue a una determinada escucha qué grupo de servidor back-end. Actualmente, solo la regla *básica* es compatible. La regla *básica* es la distribución de carga de turnos.



## <a name="create-an-application-gateway"></a>Crear una puerta de enlace de aplicación

La diferencia entre el uso de clásico de Azure y Azure Resource Manager es el orden en que se crea la puerta de enlace de la aplicación y los elementos que deben configurarse.
Con el Administrador de recursos, todos los elementos que hacen que una puerta de enlace de la aplicación está configurado de forma individual y luego cargarla para crear el recurso de puerta de enlace de aplicación.


Estos son los pasos necesarios para crear una puerta de enlace de aplicación:

1. Crear un grupo de recursos para el Administrador de recursos
2. Crear una red virtual y una subred de la puerta de enlace de aplicación
3. Crear un objeto de configuración de puerta de enlace de aplicación
4. Crear un recurso de puerta de enlace de aplicación


## <a name="create-a-resource-group-for-resource-manager"></a>Crear un grupo de recursos para el Administrador de recursos

Asegúrese de que cambiar el modo de PowerShell para usar los cmdlets del Administrador de recursos de Azure. Más información está disponible en el [Uso de Windows PowerShell con el Administrador de recursos](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Paso 1

    Login-AzureRmAccount

### <a name="step-2"></a>Paso 2

Compruebe las suscripciones para la cuenta.

    Get-AzureRmSubscription

Se le solicite para autenticar con sus credenciales.<BR>

### <a name="step-3"></a>Paso 3

Elija cuál de las suscripciones de Azure usar. <BR>


    Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### <a name="step-4"></a>Paso 4

Crear un nuevo grupo de recursos (omitir este paso si está utilizando un grupo de recursos existente).

    New-AzureRmResourceGroup -Name appgw-rg -location "West US"

Administrador de recursos de Azure requiere que todos los grupos de recursos especifican una ubicación. Se usa como ubicación predeterminada para los recursos de ese grupo de recursos. Asegúrese de que todos los comandos para crear una puerta de enlace de aplicación utiliza el mismo grupo de recursos.

En el ejemplo anterior, que se creó un grupo de recursos denominado "appgw-c" y la ubicación "Oeste nos".

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Crear una red virtual y una subred de la puerta de enlace de aplicación

En el ejemplo siguiente se muestra cómo crear una red virtual mediante el Administrador de recursos:

### <a name="step-1"></a>Paso 1

    $subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

Esto asigna la 10.0.0.0/24 rango dirección a una variable de subred que se utilizará para crear una red virtual.

### <a name="step-2"></a>Paso 2

    $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnetconfig

Esto crea una red virtual denominada "appgwvnet" en el grupo de recursos "appgw-c" de la región Oeste US con el prefijo 10.0.0.0/16 subred 10.0.0.0/24.

### <a name="step-3"></a>Paso 3

    $subnet = $vnet.subnets[0]

Se asigna el objeto de subred a variable $subnet para los pasos siguientes.

## <a name="create-an-application-gateway-configuration-object"></a>Crear un objeto de configuración de puerta de enlace de aplicación

### <a name="step-1"></a>Paso 1

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

Esto crea una configuración de IP de puerta de enlace de aplicación denominada "gatewayIP01". Cuando se inicia la puerta de enlace de aplicación, toma una dirección IP de la subred configurada y enrutar el tráfico de red a las direcciones IP en el grupo IP de back-end. Tenga en cuenta que cada instancia tiene una dirección IP.


### <a name="step-2"></a>Paso 2

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

Esto configura el conjunto de direcciones IP de back-end denominado "pool01" con las direcciones IP "134.170.185.46, 134.170.188.221,134.170.185.50". Son las direcciones IP que reciban el tráfico de red que proceden del extremo IP front-end. Reemplazar las direcciones IP anteriores para agregar sus propios extremos de direcciones IP de aplicación.

### <a name="step-3"></a>Paso 3

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled

Configura el tráfico de red de puerta de enlace configuración "poolsetting01" para la carga equilibrada de aplicación en el grupo de back-end.

### <a name="step-4"></a>Paso 4

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80

Configura el puerto front-end de IP denominado "frontendport01" para la ILB.

### <a name="step-5"></a>Paso 5

    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -Subnet $subnet

Esto crea la configuración de IP front-end denominada "fipconfig01" y asocia un IP privada de la subred de una red virtual actual.

### <a name="step-6"></a>Paso 6

    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

Esto crea el oyente denominado "listener01" y asocia el puerto front-end para la configuración de IP front-end.

### <a name="step-7"></a>Paso 7

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

Esto crea la regla de enrutamiento de equilibrador de carga denominada "rule01" que configura el comportamiento del equilibrador de carga.

### <a name="step-8"></a>Paso 8

    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

Configura el tamaño de la instancia de la puerta de enlace de aplicación.

>[AZURE.NOTE]  El valor predeterminado de *InstanceCount* es 2, con un valor máximo de 10. El valor predeterminado de *GatewaySize* es medio. Puede elegir entre Standard_Small, Standard_Medium y Standard_Large.

## <a name="create-an-application-gateway-by-using-new-azureapplicationgateway"></a>Crear una puerta de enlace de aplicación mediante AzureApplicationGateway de nuevo

Crea una puerta de enlace de la aplicación con todos los elementos de configuración de los pasos anteriores. En este ejemplo, la puerta de enlace de aplicación se denomina "appgwtest".


    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku

Esto crea una puerta de enlace de la aplicación con todos los elementos de configuración de los pasos anteriores. En el ejemplo, la puerta de enlace de aplicación se denomina "appgwtest".


## <a name="delete-an-application-gateway"></a>Eliminar una puerta de enlace de aplicación

Para eliminar una puerta de enlace de aplicación, debe hacer lo siguiente en orden:

1. Usar el cmdlet **AzureRmApplicationGateway detener** para detener la puerta de enlace.
2. Use el cmdlet **AzureRmApplicationGateway quitar** para quitar la puerta de enlace.
3. Compruebe que la puerta de enlace se ha quitado mediante el cmdlet **Get-AzureApplicationGateway** .


### <a name="step-1"></a>Paso 1

Obtener el objeto de la puerta de enlace de aplicación y asociar a una variable "$getgw".

    $getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### <a name="step-2"></a>Paso 2

Utilice **AzureRmApplicationGateway detener** para detener la puerta de enlace de aplicación. El cmdlet **AzureRmApplicationGateway detener** se muestra en la primera línea, seguido de la salida.

    PS C:\> Stop-AzureRmApplicationGateway -ApplicationGateway $getgw  

    VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway
    VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8

Una vez que la puerta de enlace de la aplicación está en un estado de detención, use el cmdlet de **AzureRmApplicationGateway quitar** para quitar el servicio.


    PS C:\> Remove-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Force

    VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway
    VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301

>[AZURE.NOTE] La **-Forzar** conmutador puede usarse para suprimir el mensaje de confirmación de eliminar.


Para comprobar que se ha quitado el servicio, puede usar el cmdlet **Get-AzureRmApplicationGateway** . Este paso no es necesario.


    PS C:\>Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

    VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway

    Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist.
    .....

## <a name="next-steps"></a>Pasos siguientes

Si desea configurar la descarga SSL, vea [Configurar una puerta de enlace de aplicación para SSL de descarga](application-gateway-ssl.md).

Si desea configurar una puerta de enlace de la aplicación para usar con un ILB, vea [crear una puerta de enlace de la aplicación con un equilibrador de carga interno (ILB)](application-gateway-ilb.md).

Si desea obtener más información sobre opciones de equilibrio de carga en general, vea:

- [Equilibrador de carga de Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Administrador de tráfico de Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)
