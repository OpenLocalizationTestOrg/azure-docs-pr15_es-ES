<properties
   pageTitle="Crear, iniciar o eliminar una puerta de enlace de aplicación mediante el Administrador de recursos de Azure | Microsoft Azure"
   description="Esta página contiene instrucciones para crear, configurar, iniciar y eliminar una puerta de enlace de aplicación Azure mediante el Administrador de recursos de Azure"
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="gwallace"/>


# <a name="create-start-or-delete-an-application-gateway-by-using-azure-resource-manager"></a>Crear, iniciar o eliminar una puerta de enlace de aplicación mediante el Administrador de recursos de Azure

> [AZURE.SELECTOR]
- [Portal de Azure](application-gateway-create-gateway-portal.md)
- [Azure PowerShell de administrador de recursos](application-gateway-create-gateway-arm.md)
- [Azure PowerShell clásica](application-gateway-create-gateway.md)
- [Azure plantilla de administrador de recursos](application-gateway-create-gateway-arm-template.md)
- [CLI de Azure](application-gateway-create-gateway-cli.md)

La puerta de enlace de aplicación Azure es un equilibrador de carga de nivel 7. Proporciona migración tras error, las solicitudes HTTP enrutamiento de rendimiento entre diferentes servidores, independientemente de si están en la nube o local. Puerta de enlace de aplicación proporciona muchas características de controlador de entrega de la aplicación (ADC) incluye Equilibrio de carga HTTP, afinidad sesión basada en cookies, descargar Secure Sockets Layer (SSL), sondeos de estado personalizados, compatibilidad con varios sitios y muchas otras. Para obtener una lista completa de características compatibles, visite [Introducción de puerta de enlace de aplicaciones](application-gateway-introduction.md)

En este artículo le guiará por los pasos para crear, configurar, iniciar y eliminar una puerta de enlace de aplicación.

>[AZURE.IMPORTANT] Antes de trabajar con recursos Azure, es importante saber que Azure actualmente tiene dos modelos de implementación: el Administrador de recursos y clásica. Asegúrese de que comprende [Herramientas y los modelos de implementación](../azure-classic-rm.md) antes de trabajar con los recursos de Azure. Puede ver la documentación de distintas herramientas haciendo clic en las pestañas en la parte superior de este artículo. Este documento trata sobre la creación de una puerta de enlace de aplicación mediante el Administrador de recursos de Azure. Para usar la versión básica, vaya a [crear una implementación clásico de puerta de enlace de aplicación con PowerShell](application-gateway-create-gateway.md).


## <a name="before-you-begin"></a>Antes de empezar

1. Instalar la última versión de los cmdlets de PowerShell de Azure mediante el instalador de plataforma Web. Puede descargar e instalar la versión más reciente de la sección de **Windows PowerShell** de la [página de descargas](https://azure.microsoft.com/downloads/).
2. Si tiene una red virtual existente, seleccione una subred vacía existente o crear una subred de su red virtual existente únicamente para usar la puerta de enlace de aplicación. No se puede implementar la puerta de enlace de aplicación a una red virtual distinta a los recursos que desee distribuir detrás de la puerta de enlace de aplicación.
3. Los servidores que configurar para que utilice la puerta de enlace de aplicación deben existir o han asignado sus extremos creados en una red virtual o con VIP o IP pública.

## <a name="what-is-required-to-create-an-application-gateway"></a>¿Qué se necesita para crear una puerta de enlace de la aplicación?

- **Grupo de servidores de back-end:** La lista de direcciones IP de los servidores de back-end. Las direcciones IP enumeradas o bien deben pertenecer a la subred virtual o deben ser VIP o IP pública.
- **Configuración de la agrupación de back-end server:** Cada grupo tiene configuración como afinidad basada en cookies, protocolo y puerto. Estas opciones están vinculadas a un grupo y se aplican a todos los servidores dentro del grupo.
- **Puerto front-end:** Este puerto es el público que se abre en la puerta de enlace de aplicación. El tráfico llega a este puerto y, a continuación, se redirige a uno de los servidores de back-end.
- **Escucha:** La escucha tiene un puerto front-end, un protocolo (Http o Https, estos valores distinguen mayúsculas de minúsculas) y el nombre del certificado SSL (si se descarga la configuración de SSL).
- **Regla:** La regla enlaza la escucha, el grupo de servidor back-end y define el tráfico debe dirigirse a cuando llegue a una determinada escucha qué grupo de servidor back-end.

## <a name="create-an-application-gateway"></a>Crear una puerta de enlace de aplicación

La diferencia entre el uso de clásico de Azure y Azure Resource Manager es el orden en que se crea la puerta de enlace de la aplicación y los elementos que deben configurarse.

Con el Administrador de recursos, todos los elementos que hacen que una puerta de enlace de aplicaciones están configurados de forma individual y luego cargarla para crear el recurso de puerta de enlace de aplicación.

Los siguientes son los pasos necesarios para crear una puerta de enlace de aplicación.

## <a name="create-a-resource-group-for-resource-manager"></a>Crear un grupo de recursos para el Administrador de recursos

Asegúrese de que está usando la versión más reciente de PowerShell de Azure. Más información está disponible en el [Uso de Windows PowerShell con el Administrador de recursos](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Paso 1

Inicie sesión en Azure

    Login-AzureRmAccount

Se le solicite para autenticar con sus credenciales.

### <a name="step-2"></a>Paso 2

Compruebe las suscripciones para la cuenta.

    Get-AzureRmSubscription

### <a name="step-3"></a>Paso 3

Elija cuál de las suscripciones de Azure usar.

    Select-AzureRmSubscription -Subscriptionid "GUID of subscription"

### <a name="step-4"></a>Paso 4

Crear un grupo de recursos (omitir este paso si está utilizando un grupo de recursos existente).

    New-AzureRmResourceGroup -Name appgw-rg -Location "West US"

Administrador de recursos de Azure requiere que todos los grupos de recursos especifican una ubicación. Esta ubicación se usa como la ubicación predeterminada para los recursos de ese grupo de recursos. Asegúrese de que todos los comandos para crear una puerta de enlace de aplicación utiliza el mismo grupo de recursos.

En el ejemplo anterior, que se creó un grupo de recursos denominado "appgw-c" y la ubicación "Oeste nos".

>[AZURE.NOTE] Si necesita configurar un sondeo personalizado de la puerta de enlace de aplicación, consulte [crear una puerta de enlace de la aplicación con sondeos personalizados con PowerShell](application-gateway-create-probe-ps.md). Consulte [sondeos personalizados y la supervisión de estado](application-gateway-probe-overview.md) para obtener más información.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Crear una red virtual y una subred de la puerta de enlace de aplicación

En el ejemplo siguiente se muestra cómo crear una red virtual mediante el Administrador de recursos.

### <a name="step-1"></a>Paso 1

Asignar la 10.0.0.0/24 rango dirección a la variable de subred que se utilizará para crear una red virtual.

    $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

### <a name="step-2"></a>Paso 2

Crear una red virtual denominada "appgwvnet" en el grupo de recursos "appgw-c" de la región Oeste US con el prefijo 10.0.0.0/16 subred 10.0.0.0/24.

    $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet

### <a name="step-3"></a>Paso 3

Asignar una variable de subred para los pasos siguientes, que crear una puerta de enlace de aplicación.

    $subnet=$vnet.Subnets[0]

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Crear una dirección IP pública para la configuración de front-end

Crear un recurso IP público "publicIP01" en recursos grupo "appgw-c" de la región Oeste US.

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic


## <a name="create-an-application-gateway-configuration-object"></a>Crear un objeto de configuración de puerta de enlace de aplicación

Debe configurar todos los elementos de configuración antes de crear la puerta de enlace de aplicación. Los pasos siguientes crean los elementos de configuración que son necesarias para un recurso de puerta de enlace de aplicación.

### <a name="step-1"></a>Paso 1

Crear una configuración de IP de puerta de enlace de aplicación denominada "gatewayIP01". Cuando se inicia la puerta de enlace de aplicación, toma una dirección IP de la subred configurada y enruta el tráfico de red a las direcciones IP en el grupo IP de back-end. Tenga en cuenta que cada instancia tiene una dirección IP.

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

### <a name="step-2"></a>Paso 2

Configurar el grupo de direcciones IP de back-end denominado "pool01" con las direcciones IP "134.170.185.46, 134.170.188.221,134.170.185.50". Estas direcciones IP están las direcciones IP que reciban el tráfico de red que proceden del extremo IP front-end. Reemplazar las direcciones IP anteriores para agregar sus propios extremos de direcciones IP de aplicación.

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

### <a name="step-3"></a>Paso 3

Configurar la configuración de puerta de enlace de aplicación "poolsetting01" para el tráfico de red de equilibrio de carga en el grupo de back-end.

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled

### <a name="step-4"></a>Paso 4

Configurar el puerto front-end de IP denominado "frontendport01" del extremo IP pública.

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80

### <a name="step-5"></a>Paso 5

Crear la configuración de IP front-end denominada "fipconfig01" y asociar la dirección IP pública con la configuración de IP front-end.

    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

### <a name="step-6"></a>Paso 6

Crear la escucha listener01"nombre" y asociar el puerto front-end para la configuración de IP front-end.

    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

### <a name="step-7"></a>Paso 7

Crear la regla de enrutamiento de equilibrador de carga con el nombre "rule01" que configura el comportamiento del equilibrador de carga.

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

### <a name="step-8"></a>Paso 8

Configurar el tamaño de la instancia de la puerta de enlace de aplicación.

    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

>[AZURE.NOTE]  El valor predeterminado de *InstanceCount* es 2, con un valor máximo de 10. El valor predeterminado de *GatewaySize* es medio. Puede elegir entre Standard_Small, Standard_Medium y Standard_Large.

## <a name="create-an-application-gateway-by-using-new-azurermapplicationgateway"></a>Crear una puerta de enlace de aplicación mediante AzureRmApplicationGateway de nuevo

Crear una puerta de enlace de la aplicación con todos los elementos de configuración de los pasos anteriores. En este ejemplo, la puerta de enlace de aplicación se denomina "appgwtest".

    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku

### <a name="step-9"></a>Paso 9

Recuperar DNS y VIP los detalles de la puerta de enlace de la aplicación desde el recurso IP público adjuntado a la puerta de enlace de aplicación.

    Get-AzureRmPublicIpAddress -Name publicIP01 -ResourceGroupName appgw-rg  

## <a name="delete-an-application-gateway"></a>Eliminar una puerta de enlace de aplicación

Para eliminar una puerta de enlace de aplicación, siga estos pasos:

### <a name="step-1"></a>Paso 1

Obtener el objeto de la puerta de enlace de aplicación y asociar a una variable "$getgw".

    $getgw = Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### <a name="step-2"></a>Paso 2

Utilice **AzureRmApplicationGateway detener** para detener la puerta de enlace de aplicación.

    Stop-AzureRmApplicationGateway -ApplicationGateway $getgw  

Una vez que la puerta de enlace de la aplicación está en un estado de detención, use el cmdlet de **AzureRmApplicationGateway quitar** para quitar el servicio.

    Remove-AzureRmApplicationGateway -Name $appgwtest -ResourceGroupName appgw-rg -Force

>[AZURE.NOTE] La **-Forzar** conmutador puede usarse para suprimir el mensaje de confirmación de eliminar.

Para comprobar que se ha quitado el servicio, puede usar el cmdlet **Get-AzureRmApplicationGateway** . Este paso no es necesario.

    Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

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

Si desea configurar la descarga SSL, vea [Configurar una puerta de enlace de aplicación para SSL de descarga](application-gateway-ssl.md).

Si desea configurar una puerta de enlace de la aplicación para usar con un equilibrador de carga interno, consulte [crear una puerta de enlace de la aplicación con un equilibrador de carga interno (ILB)](application-gateway-ilb.md).

Si desea obtener más información sobre opciones de equilibrio de carga en general, vea:

- [Equilibrador de carga de Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Administrador de tráfico de Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)
