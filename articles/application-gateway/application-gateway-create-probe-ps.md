<properties
   pageTitle="Crear un sondeo personalizada de puerta de enlace de aplicación con PowerShell en el Administrador de recursos | Microsoft Azure"
   description="Aprenda a crear un sondeo personalizada de puerta de enlace de aplicación con PowerShell en el Administrador de recursos"
   services="application-gateway"
   documentationCenter="na"
   authors="georgewallace"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/06/2016"
   ms.author="gwallace" />

# <a name="create-a-custom-probe-for-azure-application-gateway-by-using-powershell-for-azure-resource-manager"></a>Crear un sondeo personalizada de puerta de enlace de aplicación de Azure con PowerShell para el Administrador de recursos de Azure

> [AZURE.SELECTOR]
- [Portal de Azure](application-gateway-create-probe-portal.md)
- [Azure PowerShell de administrador de recursos](application-gateway-create-probe-ps.md)
- [Azure PowerShell clásica](application-gateway-create-probe-classic-ps.md)

[AZURE.INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][modelo de implementación clásico](application-gateway-create-probe-classic-ps.md).

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

### <a name="step-1"></a>Paso 1

Utilizar AzureRmAccount de inicio de sesión para autenticar.

    Login-AzureRmAccount

### <a name="step-2"></a>Paso 2

Compruebe las suscripciones para la cuenta.

    Get-AzureRmSubscription

### <a name="step-3"></a>Paso 3

Elija cuál de las suscripciones de Azure usar. <BR>


    Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### <a name="step-4"></a>Paso 4

Crear un grupo de recursos (omitir este paso si está utilizando un grupo de recursos existente).

    New-AzureRmResourceGroup -Name appgw-rg -Location "West US"

Administrador de recursos de Azure requiere que todos los grupos de recursos especifican una ubicación. Esta ubicación se usa como la ubicación predeterminada para los recursos de ese grupo de recursos. Asegúrese de que todos los comandos para crear una puerta de enlace de aplicación utilizan el mismo grupo de recursos.

En el ejemplo anterior, que se creó un grupo de recursos denominado "appgw-c" y la ubicación "Oeste nos".

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Crear una red virtual y una subred de la puerta de enlace de aplicación

Los pasos siguientes crean una red virtual y una subred de la puerta de enlace de aplicación.

### <a name="step-1"></a>Paso 1


Asignar la 10.0.0.0/24 rango dirección a una variable de subred que se utilizará para crear una red virtual.

    $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

### <a name="step-2"></a>Paso 2

Crear una red virtual denominada "appgwvnet" en el grupo de recursos "appgw-c" de la región Oeste US con el prefijo 10.0.0.0/16 subred 10.0.0.0/24.

    $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet


### <a name="step-3"></a>Paso 3

Asignar una variable de subred para los pasos siguientes, que crear una puerta de enlace de aplicación.

    $subnet = $vnet.Subnets[0]

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Crear una dirección IP pública para la configuración de front-end


Crear un recurso IP público "publicIP01" en recursos grupo "appgw-c" de la región Oeste US.

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -Name publicIP01 -Location "West US" -AllocationMethod Dynamic


## <a name="create-an-application-gateway-configuration-object-with-a-custom-probe"></a>Crear un objeto de configuración de puerta de enlace de aplicación con un sondeo personalizada

Configurar todos los elementos de configuración antes de crear la puerta de enlace de aplicación. Los pasos siguientes crean los elementos de configuración que son necesarias para un recurso de puerta de enlace de aplicación.

### <a name="step-1"></a>Paso 1

Crear una configuración de IP de puerta de enlace de aplicación denominada "gatewayIP01". Cuando se inicia la puerta de enlace de aplicación, toma una dirección IP de la subred configurada y enrutar el tráfico de red a las direcciones IP en el grupo IP de back-end. Tenga en cuenta que cada instancia tiene una dirección IP.

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet


### <a name="step-2"></a>Paso 2


Configurar el grupo de direcciones IP de back-end denominado "pool01" con las direcciones IP "134.170.185.46, 134.170.188.221,134.170.185.50". Esos valores son las direcciones IP que reciban el tráfico de red que proceden del extremo IP front-end. Reemplazar las direcciones IP anteriores para agregar sus propios extremos de direcciones IP de aplicación.

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50


### <a name="step-3"></a>Paso 3


El sondeo personalizada está configurado en este paso.

Los parámetros utilizados son:

- **Intervalo** - configura las comprobaciones de intervalo de sondeo en segundos.
- **Tiempo de espera** - define el tiempo de espera de sondeo para una comprobación de respuesta HTTP.
- **Ruta y el nombre de host** : dirección URL completa que se invoca puerta de enlace de aplicación para determinar el estado de la instancia. Por ejemplo, si tiene un sitio Web http://contoso.com/, se puede configurar el sondeo personalizada para "http://contoso.com/path/custompath.htm" para que los controles de sondeo tiene una respuesta HTTP correcta.
- **UnhealthyThreshold** - el número de respuestas HTTP erróneas necesarias para marcar la instancia de back-end como *mal estado*.

<BR>

    $probe = New-AzureRmApplicationGatewayProbeConfig -Name probe01 -Protocol Http -HostName "contoso.com" -Path "/path/path.htm" -Interval 30 -Timeout 120 -UnhealthyThreshold 8

### <a name="step-4"></a>Paso 4

Configurar la configuración de puerta de enlace de aplicación "poolsetting01" para el tráfico en el grupo de back-end. Este paso también tiene una configuración de tiempo de espera de la respuesta del grupo de back-end a una solicitud de puerta de enlace de aplicación. Cuando una respuesta de back-end alcanza un límite de tiempo de espera, la puerta de enlace de aplicación cancela la solicitud. Este valor es diferente de un tiempo de espera de sondeo solo está disponible para que la respuesta de back-end a comprobaciones de sondeo.

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 80

### <a name="step-5"></a>Paso 5

Configurar el puerto front-end de IP denominado "frontendport01" del extremo IP pública.

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01 -Port 80

### <a name="step-6"></a>Paso 6

Crear la configuración de IP front-end denominada "fipconfig01" y asociar la dirección IP pública con la configuración de IP front-end.


    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

### <a name="step-7"></a>Paso 7

Crear la escucha listener01"nombre" y asociar el puerto front-end para la configuración de IP front-end.

    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

### <a name="step-8"></a>Paso 8

Crear la regla de enrutamiento de equilibrador de carga con el nombre "rule01" que configura el comportamiento del equilibrador de carga.

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

### <a name="step-9"></a>Paso 9

Configurar el tamaño de la instancia de la puerta de enlace de aplicación.

    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2


>[AZURE.NOTE]  El valor predeterminado de *InstanceCount* es 2, con un valor máximo de 10. El valor predeterminado de *GatewaySize* es medio. Puede elegir entre Standard_Small, Standard_Medium y Standard_Large.

## <a name="create-an-application-gateway-by-using-new-azurermapplicationgateway"></a>Crear una puerta de enlace de aplicación mediante AzureRmApplicationGateway de nuevo

Crear una puerta de enlace de la aplicación con todos los elementos de configuración de los pasos anteriores. En este ejemplo, la puerta de enlace de aplicación se denomina "appgwtest".

    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -Probes $probe -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku

## <a name="add-a-probe-to-an-existing-application-gateway"></a>Agregar un sondeo a una puerta de enlace de aplicación existente

Tiene cuatro pasos para agregar un sondeo personalizada a una puerta de enlace de aplicación existente.

### <a name="step-1"></a>Paso 1

Cargar el recurso de puerta de enlace de aplicación en una variable de PowerShell mediante **Get-AzureRmApplicationGateway**.

    $getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### <a name="step-2"></a>Paso 2

Agregar un sondeo a la configuración de puerta de enlace existente.

    $getgw = Add-AzureRmApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name probe01 -Protocol Http -HostName "contoso.com" -Path "/path/custompath.htm" -Interval 30 -Timeout 120 -UnhealthyThreshold 8


En el ejemplo, el sondeo personalizada está configurado para comprobar la dirección URL ruta contoso.com/path/custompath.htm cada 30 segundos. Un umbral de tiempo de espera de 120 segundos está configurado con un número máximo de solicitudes de sondeo error 8.

### <a name="step-3"></a>Paso 3

Agregar el sondeo a la configuración de grupo de back-end y el tiempo de espera mediante **-establecer-AzureRmApplicationGatewayBackendHttpSettings**.


     $getgw = Set-AzureRmApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 120

### <a name="step-4"></a>Paso 4

Guardar la configuración en la puerta de enlace de aplicación utilizando el **Conjunto AzureRmApplicationGateway**.

    Set-AzureRmApplicationGateway -ApplicationGateway $getgw

## <a name="remove-a-probe-from-an-existing-application-gateway"></a>Quitar un sondeo de una puerta de enlace de aplicación existente

Estos son los pasos para quitar un sondeo personalizada de una puerta de enlace de aplicación existente.

### <a name="step-1"></a>Paso 1

Cargar el recurso de puerta de enlace de aplicación en una variable de PowerShell mediante **Get-AzureRmApplicationGateway**.

    $getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg


### <a name="step-2"></a>Paso 2

Quitar la configuración de sondeo de la puerta de enlace de aplicación mediante **Quitar AzureRmApplicationGatewayProbeConfig**.

    $getgw = Remove-AzureRmApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name $getgw.Probes.name

### <a name="step-3"></a>Paso 3

Actualizar la configuración de la agrupación de back-end para quitar el sondeo y el tiempo de espera mediante **-establecer-AzureRmApplicationGatewayBackendHttpSettings**.


     $getgw = Set-AzureRmApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol http -CookieBasedAffinity Disabled

### <a name="step-4"></a>Paso 4

Guardar la configuración en la puerta de enlace de aplicación utilizando el **Conjunto AzureRmApplicationGateway**. 

    Set-AzureRmApplicationGateway -ApplicationGateway $getgw

## <a name="get-application-gateway-dns-name"></a>Obtener el nombre DNS de puerta de enlace de aplicación

Una vez creada la puerta de enlace, el siguiente paso es configurar el front-end para la comunicación. Cuando se usa una dirección IP pública, puerta de enlace de aplicación requiere un nombre DNS asignado dinámicamente, que no sea descriptivo. Para garantizar que los usuarios finales puede presionar la puerta de enlace de aplicación un registro CNAME puede utilizarse para que señalen al público extremo de la puerta de enlace de aplicación. [Configurar un nombre de dominio personalizado para en Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). Para ello, recuperar los detalles de la puerta de enlace de la aplicación y su nombre IP/DNS asociado con el elemento PublicIPAddress conectado a la puerta de enlace de aplicación. Nombre DNS de la aplicación la puerta de enlace debe utilizarse para crear un registro CNAME, que señala las aplicaciones web de dos a este nombre DNS. No se recomienda el uso de registros a ya que puede cambiar la dirección VIP en reinicio de puerta de enlace de aplicación.
    
    Get-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -Name publicIP01
        
    Name                     : publicIP01
    ResourceGroupName        : appgw-RG
    Location                 : westus
    Id                       : /subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/publicIPAddresses/publicIP01
    Etag                     : W/"00000d5b-54ed-4907-bae8-99bd5766d0e5"
    ResourceGuid             : 00000000-0000-0000-0000-000000000000
    ProvisioningState        : Succeeded
    Tags                     : 
    PublicIpAllocationMethod : Dynamic
    IpAddress                : xx.xx.xxx.xx
    PublicIpAddressVersion   : IPv4
    IdleTimeoutInMinutes     : 4
    IpConfiguration          : {
                                 "Id": "/subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/applicationGateways/appgwtest/frontendIP
                               Configurations/frontend1"
                               }
    DnsSettings              : {
                                 "Fqdn": "00000000-0000-xxxx-xxxx-xxxxxxxxxxxx.cloudapp.net"
                               }

## <a name="next-steps"></a>Pasos siguientes

Aprenda a configurar descarga SSL visitando [Configurar la descarga de SSL](application-gateway-ssl-arm.md)

