<properties
   pageTitle="Configurar una puerta de enlace de la aplicación de descarga SSL mediante el Administrador de recursos de Azure | Microsoft Azure"
   description="Esta página contiene instrucciones para crear una puerta de enlace de la aplicación con SSL de descarga con el Administrador de recursos de Azure"
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
   ms.date="09/09/2016"
   ms.author="gwallace"/>

# <a name="configure-an-application-gateway-for-ssl-offload-by-using-azure-resource-manager"></a>Configurar una puerta de enlace de la aplicación de descarga SSL mediante el Administrador de recursos de Azure

> [AZURE.SELECTOR]
-[Portal de Azure](application-gateway-ssl-portal.md)
-[Azure PowerShell de administrador de recursos](application-gateway-ssl-arm.md)
-[Azure PowerShell clásica](application-gateway-ssl.md)

 La puerta de enlace de aplicación Azure se puede configurar para finalizar la sesión de Secure Sockets Layer (SSL) de la puerta de enlace para evitar costosas tareas de descifrado SSL para que se producen en el conjunto de servidores web. Descarga SSL también simplifica la configuración del servidor front-end y la administración de la aplicación web.

## <a name="before-you-begin"></a>Antes de empezar

1. Instalar la última versión de los cmdlets de PowerShell de Azure mediante el instalador de plataforma Web. Puede descargar e instalar la versión más reciente de la sección de **Windows PowerShell** de la [página de descargas](https://azure.microsoft.com/downloads/).
2. Crear una red virtual y una subred de la puerta de enlace de aplicación. Asegúrese de que no hay máquinas virtuales o implementaciones de nube utilizan la subred. Puerta de enlace de aplicación debe ser por sí mismo en una subred de una red virtual.
3. Los servidores que configurar para que utilice la puerta de enlace de aplicación deben existir o han asignado sus extremos creados en una red virtual o con VIP o IP pública.

## <a name="what-is-required-to-create-an-application-gateway"></a>¿Qué se necesita para crear una puerta de enlace de la aplicación?


- **Grupo de servidores de back-end:** La lista de direcciones IP de los servidores de back-end. Las direcciones IP enumeradas o bien deben pertenecer a la subred virtual o deben ser VIP o IP pública.
- **Configuración de la agrupación de back-end server:** Cada grupo tiene configuración como afinidad basada en cookies, protocolo y puerto. Estas opciones están vinculadas a un grupo y se aplican a todos los servidores dentro del grupo.
- **Puerto front-end:** Este puerto es el público que se abre en la puerta de enlace de aplicación. El tráfico llega a este puerto y, a continuación, se redirige a uno de los servidores de back-end.
- **Escucha:** La escucha tiene un puerto front-end, un protocolo (Http o Https, estos valores distinguen mayúsculas de minúsculas) y el nombre del certificado SSL (si se descarga la configuración de SSL).
- **Regla:** La regla enlaza la escucha y el grupo de servidor back-end y define el tráfico debe dirigirse a cuando llegue a una determinada escucha qué grupo de servidor back-end. Actualmente, solo la regla *básica* es compatible. La regla *básica* es la distribución de carga de turnos.

**Notas de configuración adicionales**

Configuración de certificados SSL, el protocolo en **HttpListener** debe cambiar a *Https* (distingue entre mayúsculas y minúsculas). Agregar el elemento **SslCertificate** a **HttpListener** con el valor de la variable configurado para el certificado SSL. El puerto front-end debe actualizarse a 443.

**Para habilitar la afinidad de cookie**: se pueden configurar una puerta de enlace de aplicaciones para asegurarse de que una solicitud de una sesión de cliente siempre se dirige a la misma VM en el conjunto de servidores web. Este escenario se realiza la inserción de una cookie de sesión que permite la puerta de enlace dirigir el tráfico adecuadamente. Para habilitar la afinidad basada en cookies, establezca **CookieBasedAffinity** en *habilitado* en el elemento **BackendHttpSettings** .


## <a name="create-an-application-gateway"></a>Crear una puerta de enlace de aplicación

La diferencia entre mediante el Administrador de recursos de Azure y Azure clásico modelo de implementación es el orden en que se crea una puerta de enlace de aplicaciones y los elementos que deben configurarse.

Con el Administrador de recursos, todos los componentes de una puerta de enlace de aplicaciones están configurados de forma individual y luego cargarla para crear un recurso de puerta de enlace de aplicación.


Estos son los pasos necesarios para crear una puerta de enlace de aplicación:

1. Crear un grupo de recursos para el Administrador de recursos
2. Crear una red virtual, subred y IP pública para la puerta de enlace de aplicación
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

Crear un grupo de recursos (omitir este paso si está utilizando un grupo de recursos existente).

    New-AzureRmResourceGroup -Name appgw-rg -Location "West US"

Administrador de recursos de Azure requiere que todos los grupos de recursos especifican una ubicación. Esta configuración se usa como la ubicación predeterminada para los recursos de ese grupo de recursos. Asegúrese de que todos los comandos para crear una puerta de enlace de aplicación utiliza el mismo grupo de recursos.

En el ejemplo anterior, que se creó un grupo de recursos denominado "appgw-c" y la ubicación "Oeste nos".

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Crear una red virtual y una subred de la puerta de enlace de aplicación

En el ejemplo siguiente se muestra cómo crear una red virtual mediante el Administrador de recursos:

### <a name="step-1"></a>Paso 1

    $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

Este ejemplo asigna la 10.0.0.0/24 rango dirección a una variable de subred que se utilizará para crear una red virtual.

### <a name="step-2"></a>Paso 2

    $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet

En este ejemplo se crea una red virtual denominada "appgwvnet" en el grupo de recursos "appgw-c" de la región Oeste US con el prefijo 10.0.0.0/16 subred 10.0.0.0/24.

### <a name="step-3"></a>Paso 3

    $subnet = $vnet.Subnets[0]

En este ejemplo se asigna el objeto de subred a variable $subnet para los pasos siguientes.

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Crear una dirección IP pública para la configuración de front-end

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic

Este ejemplo crea un recurso IP público "publicIP01" en recursos grupo "appgw-c" de la región Oeste US.


## <a name="create-an-application-gateway-configuration-object"></a>Crear un objeto de configuración de puerta de enlace de aplicación

### <a name="step-1"></a>Paso 1

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

En este ejemplo se crea una configuración de IP de puerta de enlace de aplicación denominada "gatewayIP01". Cuando se inicia la puerta de enlace de aplicación, toma una dirección IP de la subred configurada y enrutar el tráfico de red a las direcciones IP en el grupo IP de back-end. Tenga en cuenta que cada instancia tiene una dirección IP.

### <a name="step-2"></a>Paso 2

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

En este ejemplo se configura el conjunto de direcciones IP de back-end denominado "pool01" con las direcciones IP "134.170.185.46, 134.170.188.221,134.170.185.50." Esos valores son las direcciones IP que reciban el tráfico de red que proceden del extremo IP front-end. Reemplazar las direcciones IP del ejemplo anterior con las direcciones IP de los extremos de la aplicación web.

### <a name="step-3"></a>Paso 3

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Enabled

En este ejemplo configura la configuración de puerta de enlace de aplicación "poolsetting01" equilibrio de carga de tráfico de red en el grupo de back-end.

### <a name="step-4"></a>Paso 4

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 443

En este ejemplo se configura el puerto front-end de IP denominado "frontendport01" del extremo IP pública.

### <a name="step-5"></a>Paso 5

    $cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path for certificate file> -Password ‘<password>’

En este ejemplo se configura el certificado usado para la conexión SSL. El certificado debe estar en formato .pfx y la contraseña debe estar entre 4 a 12 caracteres.

### <a name="step-6"></a>Paso 6

    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

En este ejemplo se crea la configuración de IP front-end denominada "fipconfig01" y la dirección IP pública se asocia con la configuración de IP front-end.

### <a name="step-7"></a>Paso 7

    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert


En este ejemplo se crea el nombre de escucha "listener01" y se asocia el puerto front-end para la configuración de IP front-end y el certificado.

### <a name="step-8"></a>Paso 8

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

En este ejemplo se crea la regla de enrutamiento de equilibrador de carga con el nombre "rule01" que configura el comportamiento del equilibrador de carga.

### <a name="step-9"></a>Paso 9

    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

En este ejemplo se configura el tamaño de la instancia de la puerta de enlace de aplicación.

>[AZURE.NOTE]  El valor predeterminado de *InstanceCount* es 2, con un valor máximo de 10. El valor predeterminado de *GatewaySize* es medio. Puede elegir entre Standard_Small, Standard_Medium y Standard_Large.

## <a name="create-an-application-gateway-by-using-new-azureapplicationgateway"></a>Crear una puerta de enlace de aplicación mediante AzureApplicationGateway de nuevo

    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslCertificates $cert

En este ejemplo, se crea una puerta de enlace de la aplicación con todos los elementos de configuración de los pasos anteriores. En el ejemplo, la puerta de enlace de aplicación se denomina "appgwtest".

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

Si desea configurar una puerta de enlace de la aplicación para usar con un equilibrador de carga interno (ILB), consulte [crear una puerta de enlace de la aplicación con un equilibrador de carga interno (ILB)](application-gateway-ilb.md).

Si desea obtener más información sobre opciones de equilibrio de carga en general, vea:

- [Equilibrador de carga de Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Administrador de tráfico de Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)
