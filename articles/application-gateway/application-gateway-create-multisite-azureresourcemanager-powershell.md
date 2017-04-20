<properties
   pageTitle="Crear una puerta de enlace de aplicación para hospedar varios sitios | Microsoft Azure"
   description="Esta página contiene instrucciones para crear, configurar una puerta de enlace de aplicación Azure para alojar varias aplicaciones web en la misma puerta de enlace."
   documentationCenter="na"
   services="application-gateway"
   authors="amsriva"
   manager="rossort"
   editor="amsriva"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="amsriva"/>


# <a name="create-an-application-gateway-for-hosting-multiple-web-applications"></a>Crear una puerta de enlace de aplicación para alojar varias aplicaciones web

> [AZURE.SELECTOR]
- [Portal de Azure](application-gateway-create-multisite-portal.md)
- [Azure PowerShell de administrador de recursos](application-gateway-create-multisite-azureresourcemanager-powershell.md)

Alojamiento de varios sitios le permite implementar más de una aplicación web en la misma puerta de enlace de aplicación. Se basa en la presencia del encabezado de host de la solicitud HTTP entrante, para determinar qué escucha recibiría tráfico. La escucha dirige a continuación, el tráfico al grupo de back-end adecuados tal como está configurado en la definición de reglas de la puerta de enlace. En aplicaciones web SSL habilitado, la puerta de enlace de aplicación se basa en la extensión de nombre de servidor indicación (SNI) para elegir el oyente correcto para el tráfico de web. Un uso común para el hospedaje de sitios múltiples es cargar equilibrar las solicitudes de los dominios de web diferentes para grupos de otro servidor back-end. Del mismo modo también se pueden hospedar varios subdominios del mismo dominio raíz en la misma puerta de enlace de aplicación.

## <a name="scenario"></a>Escenario

En el ejemplo siguiente, la puerta de enlace de aplicación sirve tráfico de contoso.com y fabrikam.com con dos agrupaciones de servidor back-end: contoso grupo de servidor y fabrikam server. Programa de instalación similar podría utilizarse para subdominios host como app.contoso.com y blog.contoso.com.

![imageURLroute](./media/application-gateway-create-multisite-azureresourcemanager-powershell/multisite.png)

## <a name="before-you-begin"></a>Antes de empezar

1. Instalar la última versión de los cmdlets de PowerShell de Azure mediante el instalador de plataforma Web. Puede descargar e instalar la versión más reciente de la sección de **Windows PowerShell** de la [página de descargas](https://azure.microsoft.com/downloads/).
2. Los servidores que se agrega a la agrupación de back-end para usar la puerta de enlace de aplicación deben existir o han creado sus extremos en una red virtual en una subred independiente o con una pública IP/VIP asignada.

## <a name="requirements"></a>Requisitos

- **Grupo de servidores de back-end:** La lista de direcciones IP de los servidores de back-end. Las direcciones IP enumeradas o bien deben pertenecer a la subred virtual o deben ser VIP o IP pública. También puede utilizarse el nombre de dominio completo.
- **Configuración de la agrupación de back-end server:** Cada grupo tiene configuración como afinidad basada en cookies, protocolo y puerto. Estas opciones están vinculadas a un grupo y se aplican a todos los servidores dentro del grupo.
- **Puerto front-end:** Este puerto es el público que se abre en la puerta de enlace de aplicación. El tráfico llega a este puerto y, a continuación, se redirige a uno de los servidores de back-end.
- **Escucha:** La escucha tiene un puerto front-end, un protocolo (Http o Https, estos valores distinguen mayúsculas de minúsculas) y el nombre del certificado SSL (si se descarga la configuración de SSL). Nombre de host e indicadores SNI también se agregan para puertas de enlace de aplicación habilitada de múltiples sitios.
- **Regla:** La regla enlaza la escucha, la agrupación de servidor back-end y define el tráfico debe dirigirse a cuando llegue a una determinada escucha qué grupo de servidor back-end.

## <a name="create-an-application-gateway"></a>Crear una puerta de enlace de aplicación

Los siguientes son los pasos necesarios para crear una puerta de enlace de aplicación:

1. Crear un grupo de recursos para el Administrador de recursos.
2. Crear una red virtual, subredes y IP pública para la puerta de enlace de aplicación.
3. Crear un objeto de configuración de puerta de enlace de aplicación.
4. Crear un recurso de puerta de enlace de aplicación.

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

    Select-AzureRmSubscription -SubscriptionName "Name of subscription"

### <a name="step-4"></a>Paso 4

Crear un grupo de recursos (omitir este paso si está utilizando un grupo de recursos existente).

    New-AzureRmResourceGroup -Name appgw-RG -location "West US"

También puede también puede crear etiquetas para un grupo de recursos de puerta de enlace de aplicación:

    $resourceGroup = New-AzureRmResourceGroup -Name appgw-RG -Location "West US" -Tags @{Name = "testtag"; Value = "Application Gateway multiple site"}

Administrador de recursos de Azure requiere que todos los grupos de recursos especifican una ubicación. Esta ubicación se usa como la ubicación predeterminada para los recursos de ese grupo de recursos. Asegúrese de que todos los comandos para crear una puerta de enlace de aplicación utilizan el mismo grupo de recursos.

En el ejemplo anterior, que se creó un grupo de recursos denominado "appgw-c" con una ubicación de "Oeste nosotros".

>[AZURE.NOTE] Si necesita configurar un sondeo personalizado de la puerta de enlace de aplicación, consulte [crear una puerta de enlace de la aplicación con sondeos personalizados con PowerShell](application-gateway-create-probe-ps.md). Visite [sondeos personalizados y la supervisión de estado](application-gateway-probe-overview.md) para obtener más información.

## <a name="create-a-virtual-network-and-subnets"></a>Crear una red virtual y subredes

En el ejemplo siguiente se muestra cómo crear una red virtual mediante el Administrador de recursos. En este paso, se crean dos subredes. La primera subred es para la puerta de enlace de aplicación. Puerta de enlace de aplicación requiere su propia subred para mantener sus instancias. Solo otras puertas de enlace de aplicación se pueden implementar en esa subred. La segunda subred se usa para mantener los servidores de back-end de la aplicación.

### <a name="step-1"></a>Paso 1

Asignar la 10.0.0.0/24 rango dirección a la variable de subred que se utiliza para mantener la puerta de enlace de aplicación.

    $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name appgatewaysubnet -AddressPrefix 10.0.0.0/24

### <a name="step-2"></a>Paso 2

Asigne el 10.0.1.0/24 intervalo dirección a la variable subred2 que se usará para los grupos de back-end.

    $subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name backendsubnet -AddressPrefix 10.0.1.0/24

### <a name="step-3"></a>Paso 3

Crear una red virtual denominada "appgwvnet" en el grupo de recursos "appgw-c" de la región Oeste US con el prefijo 10.0.0.0/16 subred 10.0.0.0/24 y 10.0.1.0/24.

    $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet,$subnet2


### <a name="step-4"></a>Paso 4

Asignar una variable de subred para los pasos siguientes, que crea una puerta de enlace de aplicación.

    $appgatewaysubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name appgatewaysubnet -VirtualNetwork $vnet
    $backendsubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name backendsubnet -VirtualNetwork $vnet

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Crear una dirección IP pública para la configuración de front-end

Crear un recurso IP público "publicIP01" en recursos grupo "appgw-c" de la región Oeste US.

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -name publicIP01 -location "West US" -AllocationMethod Dynamic

Se asigna una dirección IP a la puerta de enlace de aplicación cuando se inicia el servicio.

## <a name="create-application-gateway-configuration"></a>Crear la configuración de puerta de enlace de aplicación

Debe configurar todos los elementos de configuración antes de crear la puerta de enlace de aplicación. Los pasos siguientes crean los elementos de configuración que son necesarias para un recurso de puerta de enlace de aplicación.

### <a name="step-1"></a>Paso 1

Crear una configuración de IP de puerta de enlace de aplicación denominada "gatewayIP01". Cuando se inicia la puerta de enlace de aplicación, toma una dirección IP de la subred configurada y enrutar el tráfico de red a las direcciones IP en el grupo IP de back-end. Tenga en cuenta que cada instancia tiene una dirección IP.

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $appgatewaysubnet

### <a name="step-2"></a>Paso 2

Configurar el grupo de direcciones IP de back-end denominada "pool01" y "pool2" con las direcciones IP "10.0.1.100, 10.0.1.101,10.0.1.102" "grupo 1" y "10.0.1.103, 10.0.1.104, 10.0.1.105" para "pool2".

    $pool1 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 10.0.1.100, 10.0.1.101, 10.0.1.102
    $pool2 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool02 -BackendIPAddresses 10.0.1.103, 10.0.1.104, 10.0.1.105

En este ejemplo, hay dos grupos de back-end para enrutar el tráfico de red basado en el sitio solicitado. Un grupo recibe el tráfico de sitio "contoso.com" y otra agrupación recibe el tráfico de sitio "fabrikam.com". Debe reemplazar las direcciones IP anteriores para agregar sus propios extremos de direcciones IP de aplicación. En lugar de direcciones IP internas, también podría usar direcciones IP públicas, FQDN o NIC de la VM para instancias de back-end. Use "-BackendFQDNs" parámetro de PowerShell para especificar FQDN en lugar de direcciones IP.

### <a name="step-3"></a>Paso 3

Configurar la configuración de puerta de enlace de aplicación "poolsetting01" y "poolsetting02" para el tráfico de red de equilibrio de carga en el grupo de back-end. En este ejemplo, configuración de agrupación de back-end diferente para los grupos de back-end. Cada conjunto de back-end puede tener su propia configuración de la agrupación de back-end.

    $poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled -RequestTimeout 120
    $poolSetting02 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting02" -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 240

### <a name="step-4"></a>Paso 4

Configurar la dirección IP front-end con público extremo IP.

    $fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip

### <a name="step-5"></a>Paso 5

Configurar el puerto front-end de una puerta de enlace de aplicación.

    $fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "fep01" -Port 443

### <a name="step-6"></a>Paso 6

Configurar, vamos a soporte técnico en este ejemplo dos certificados SSL para los dos sitios Web. Un certificado es para el tráfico de contoso.com y el otro es para el tráfico de fabrikam.com. Estos certificados deben ser una entidad emisora de certificados emite certificados para los sitios Web. Certificados firmados son compatibles, pero no se recomienda para el tráfico de producción.

    $cert01 = New-AzureRmApplicationGatewaySslCertificate -Name contosocert -CertificateFile <file path> -Password <password>
    $cert02 = New-AzureRmApplicationGatewaySslCertificate -Name fabrikamcert -CertificateFile <file path> -Password <password>


### <a name="step-7"></a>Paso 7

En este ejemplo, configure dos escucha para los dos sitios web. Este paso configura la escucha para la dirección IP, puertos y host utilizado para recibir el tráfico entrante público. Parámetro de nombre de host es necesaria para varios sitios y se debe establecer en el sitio Web adecuado para el que se ha recibido el tráfico. Parámetro RequireServerNameIndication se debe establecer en true para sitios Web que necesita compatibilidad con SSL en un escenario con varios host. Si se requiere la compatibilidad con SSL, debe especificar el certificado SSL que se usa para proteger el tráfico para la aplicación web. La combinación de FrontendIPConfiguration, FrontendPort y nombre de host debe ser única en una escucha. Cada oyente puede admitir un certificado.

    $listener01 = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol Https -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -HostName "contoso11.com" -RequireServerNameIndication true  -SslCertificate $cert01
    $listener02 = New-AzureRmApplicationGatewayHttpListener -Name "listener02" -Protocol Https -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -HostName "fabrikam11.com" -RequireServerNameIndication true -SslCertificate $cert02

### <a name="step-8"></a>Paso 8

En este ejemplo, cree dos configuración de regla para las aplicaciones web de dos. Una regla reúne escucha, grupos de back-end y configuración de http. Este paso configura la puerta de enlace de la aplicación para usar la regla de enrutamiento básica, uno para cada sitio Web. El tráfico a cada sitio Web sea recibido por su escucha configurado y, a continuación, se dirige a su grupo configurado back-end, con las propiedades especificadas en el BackendHttpSettings.

    $rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule01" -RuleType Basic -HttpListener $listener01 -BackendHttpSettings $poolSetting01 -BackendAddressPool $pool1
    $rule02 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule02" -RuleType Basic -HttpListener $listener02 -BackendHttpSettings $poolSetting02 -BackendAddressPool $pool2

### <a name="step-9"></a>Paso 9

Configurar el número de instancias y el tamaño de la puerta de enlace de aplicación.

    $sku = New-AzureRmApplicationGatewaySku -Name "Standard_Medium" -Tier Standard -Capacity 2

## <a name="create-application-gateway"></a>Crear la puerta de enlace de aplicación

Crear una puerta de enlace de la aplicación con todos los objetos de configuración de los pasos anteriores.

    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-RG -Location "West US" -BackendAddressPools $pool1,$pool2 -BackendHttpSettingsCollection $poolSetting01, $poolSetting02 -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener01, $listener02 -RequestRoutingRules $rule01, $rule02 -Sku $sku -SslCertificates $cert01, $cert02


>[AZURE.IMPORTANT] Aprovisionamiento de la puerta de enlace en la aplicación es una operación larga y puede tardar algún tiempo en completarse.

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

Obtenga información sobre cómo proteger los sitios Web con la [Puerta de enlace de aplicaciones - servidor de aplicaciones Web](application-gateway-webapplicationfirewall-overview.md)