<properties
   pageTitle="Crear una puerta de enlace de aplicación mediante las reglas de enrutamiento de direcciones URL | Microsoft Azure"
   description="Esta página contiene instrucciones para crear, configurar una puerta de enlace de aplicación Azure usando las reglas de enrutamiento de direcciones URL"
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="jdial"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="gwallace"/>


# <a name="create-an-application-gateway-using-path-based-routing"></a>Crear una puerta de enlace de aplicación utilizando el enrutamiento basado en la ruta de acceso 

> [AZURE.SELECTOR]
- [Portal de Azure](application-gateway-create-url-route-portal.md)
- [Azure PowerShell de administrador de recursos](application-gateway-create-url-route-arm-ps.md)

Enrutamiento basado en la ruta de acceso de direcciones URL permite asociar rutas basándose en la dirección URL de la solicitud Http. Comprueba si hay una ruta a un grupo de back-end configurado para las listas de dirección URL de la puerta de enlace de aplicación y enviar el tráfico de red al grupo definido de back-end. Un uso común de enrutamiento basado en la dirección URL es la carga de solicitudes de saldo para distintos tipos de contenido a los grupos de otro servidor back-end.

Enrutamiento basado en la dirección URL introduce un nuevo tipo de regla a la puerta de enlace de aplicación. Puerta de enlace de aplicación tiene dos tipos de regla: básica y PathBasedRouting. Tipo de regla básica proporciona turnos de servicio para los grupos de back-end mientras PathBasedRouting además de distribución de turnos, también tiene trama de ruta de acceso de la dirección URL de la solicitud en cuenta al elegir la agrupación de back-end.

>[AZURE.IMPORTANT] PathPattern: La lista de los patrones de ruta para que coincida con. Cada uno debe empezar por / y el único lugar un "\*" se permite al final. Ejemplos válidos son /xyz /xyz* o /xyz/*. La cadena apto para el buscador de coincidencias de ruta de acceso no incluye cualquier texto después de la primera "?" o "#" y los caracteres no permitidos. 

## <a name="scenario"></a>Escenario
En el ejemplo siguiente, la puerta de enlace de aplicación sirve tráfico de contoso.com con dos agrupaciones de servidor back-end: grupo de servidores de video y servidor imagen.

Solicitudes para http://contoso.com/image* se enrutan a grupo de servidores de imagen (grupo 1) y http://contoso.com/video* se dirigen al grupo de servidores de video (pool2). Un grupo de servidores predeterminado del (grupo 1) está seleccionado si coincide con ninguno de los modelos de ruta de acceso.

![ruta de dirección URL](./media/application-gateway-create-url-route-arm-ps/figure1.png)

## <a name="before-you-begin"></a>Antes de empezar

1. Instalar la última versión de los cmdlets de PowerShell de Azure mediante el instalador de plataforma Web. Puede descargar e instalar la versión más reciente de la sección de **Windows PowerShell** de la [página de descargas](https://azure.microsoft.com/downloads/).
2. Crear una red virtual y subred de puerta de enlace de aplicación. Asegúrese de que no hay máquinas virtuales o implementaciones de nube utilizan la subred. La puerta de enlace de aplicación debe ser por sí mismo en una subred de una red virtual.
3. Los servidores que se agrega a la agrupación de back-end para usar la puerta de enlace de aplicación deben existir o han creado sus extremos de la red virtual o con una pública IP/VIP asignada.



## <a name="what-is-required-to-create-an-application-gateway"></a>¿Qué se necesita para crear una puerta de enlace de la aplicación?


- **Grupo de servidores de back-end:** La lista de direcciones IP de los servidores de back-end. Las direcciones IP enumeradas o bien deben pertenecer a la subred virtual o deben ser VIP o IP pública.
- **Configuración de la agrupación de back-end server:** Cada grupo tiene configuración como afinidad basada en cookies, protocolo y puerto. Estas opciones están vinculadas a un grupo y se aplican a todos los servidores dentro del grupo.
- **Puerto front-end:** Este puerto es el público que se abre en la puerta de enlace de aplicación. El tráfico llega a este puerto y, a continuación, se redirige a uno de los servidores de back-end.
- **Escucha:** La escucha tiene un puerto front-end, un protocolo (Http o Https, estos son distingue mayúsculas de minúsculas) y el nombre del certificado SSL (si se descarga la configuración de SSL).
- **Regla:** La regla enlaza la escucha, el grupo de servidor back-end y define el tráfico debe dirigirse a cuando llegue a una determinada escucha qué grupo de servidor back-end.

## <a name="create-an-application-gateway"></a>Crear una puerta de enlace de aplicación

La diferencia entre el uso de clásico de Azure y Azure Resource Manager es el orden en que se crea la puerta de enlace de la aplicación y los elementos que deben configurarse.

Con el Administrador de recursos, todos los elementos que hacen que una puerta de enlace de aplicaciones están configurados de forma individual y luego cargarla para crear el recurso de puerta de enlace de aplicación.


Estos son los pasos necesarios para crear una puerta de enlace de aplicación:

1. Crear un grupo de recursos para el Administrador de recursos.
2. Crear una red virtual y subred IP pública para la puerta de enlace de aplicación.
3. Crear un objeto de configuración de puerta de enlace de aplicación.
4. Crear un recurso de puerta de enlace de aplicación.

## <a name="create-a-resource-group-for-resource-manager"></a>Crear un grupo de recursos para el Administrador de recursos

Asegúrese de que está usando la versión más reciente de PowerShell de Azure. Más información está disponible en el [Uso de Windows PowerShell con el Administrador de recursos](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Paso 1

Inicie sesión en Azure

    Login-AzureRmAccount

Se le solicite para autenticar con sus credenciales.<BR>

### <a name="step-2"></a>Paso 2

Compruebe las suscripciones para la cuenta.

    Get-AzureRmSubscription

### <a name="step-3"></a>Paso 3

Elija cuál de las suscripciones de Azure usar. <BR>


    Select-AzureRmSubscription -Subscriptionid "GUID of subscription"

### <a name="step-4"></a>Paso 4

Crear un grupo de recursos (omitir este paso si está utilizando un grupo de recursos existente).

    New-AzureRmResourceGroup -Name appgw-RG -Location "West US"

También puede también puede crear etiquetas para un grupo de recursos de puerta de enlace de aplicación:
    
    $resourceGroup = New-AzureRmResourceGroup -Name appgw-RG -Location "West US" -Tags @{Name = "testtag"; Value = "Application Gateway URL routing"} 

Administrador de recursos de Azure requiere que todos los grupos de recursos especifican una ubicación. Se usa como ubicación predeterminada para los recursos de ese grupo de recursos. Asegúrese de que todos los comandos para crear una puerta de enlace de aplicación utilizan el mismo grupo de recursos.

En el ejemplo anterior, que se creó un grupo de recursos denominado "appgw-c" y la ubicación "Oeste nos".

>[AZURE.NOTE] Si necesita configurar un sondeo personalizado de la puerta de enlace de aplicación, consulte [crear una puerta de enlace de la aplicación con sondeos personalizados con PowerShell](application-gateway-create-probe-ps.md). Consulte [sondeos personalizados y la supervisión de estado](application-gateway-probe-overview.md) para obtener más información.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Crear una red virtual y una subred de la puerta de enlace de aplicación

En el ejemplo siguiente se muestra cómo crear una red virtual mediante el Administrador de recursos.

### <a name="step-1"></a>Paso 1

Asignar la 10.0.0.0/24 rango dirección a la variable de subred que se utilizará para crear una red virtual.

    $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24


### <a name="step-2"></a>Paso 2

Crear una red virtual denominada "appgwvnet" en el grupo de recursos "appgw-c" de la región Oeste US con el prefijo 10.0.0.0/16 subred 10.0.0.0/24.

    $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet


### <a name="step-3"></a>Paso 3

Asignar una variable de subred para los pasos siguientes, que crea una puerta de enlace de aplicación.

    $subnet=$vnet.Subnets[0]

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Crear una dirección IP pública para la configuración de front-end

Crear un recurso IP público "publicIP01" en recursos grupo "appgw-c" de la región Oeste US.

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -name publicIP01 -location "West US" -AllocationMethod Dynamic

Se asigna una dirección IP a la puerta de enlace de aplicación cuando se inicia el servicio.

## <a name="create-application-gateway-configuration"></a>Crear la configuración de puerta de enlace de aplicación

Todos los elementos de configuración se deben configurar antes de crear la puerta de enlace de aplicación. Los pasos siguientes crean los elementos de configuración que son necesarias para un recurso de puerta de enlace de aplicación.

### <a name="step-1"></a>Paso 1

Crear una configuración de IP de puerta de enlace de aplicación denominada "gatewayIP01". Cuando se inicia la puerta de enlace de aplicación, toma una dirección IP de la subred configurada y enrutar el tráfico de red a las direcciones IP en el grupo IP de back-end. Tenga en cuenta que cada instancia tiene una dirección IP.


    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet


### <a name="step-2"></a>Paso 2

Configurar el grupo de direcciones IP de back-end denominada "pool01" y "pool2" con las direcciones IP "134.170.185.46, 134.170.188.221,134.170.185.50" para el grupo "1" y "134.170.186.46, 134.170.189.221,134.170.186.50" para "pool2".


    $pool1 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

    $pool2 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool02 -BackendIPAddresses 134.170.186.46, 134.170.189.221,134.170.186.50

En este ejemplo, hay dos grupos de back-end para enrutar el tráfico de red en función de la dirección URL. Un grupo recibe tráfico de la dirección URL "/ vídeo" y otra agrupación recibir tráfico de ruta de acceso "/ image". Reemplazar las direcciones IP anteriores para agregar sus propios extremos de direcciones IP de aplicación. 

### <a name="step-3"></a>Paso 3

Configurar la configuración de puerta de enlace de aplicación "poolsetting01" y "poolsetting02" para el tráfico de red de equilibrio de carga en el grupo de back-end. En este ejemplo, configuración de agrupación de back-end diferente para los grupos de back-end. Cada conjunto de back-end puede tener su propia configuración de la agrupación de back-end.

    $poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled -RequestTimeout 120

    $poolSetting02 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting02" -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 240

### <a name="step-4"></a>Paso 4

Configurar la dirección IP front-end con público extremo IP.

    $fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip

### <a name="step-5"></a>Paso 5 

Configurar el puerto front-end de una puerta de enlace de aplicación.

    $fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "fep01" -Port 80
### <a name="step-6"></a>Paso 6

Configurar la escucha. Este paso configura la escucha para la dirección IP pública y puerto que se utiliza para recibir tráfico de red. 
 
    $listener = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol Http -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01

### <a name="step-7"></a>Paso 7 

Configurar rutas de acceso de reglas de dirección URL para los grupos de back-end. Este paso configura la ruta de acceso relativa utilizada puerta de enlace de aplicación para definir la asignación entre la dirección URL y se asigna a qué grupo de back-end para manejar el tráfico entrante.

El ejemplo siguiente crea dos reglas: uno para "/ imágenes /" ruta enrutar el tráfico a back-end "grupo 1" y otro para "/ vídeo /" ruta enrutar el tráfico back-end "pool2".
    
    $imagePathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "pathrule1" -Paths "/image/*" -BackendAddressPool $pool1 -BackendHttpSettings $poolSetting01

    $videoPathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "pathrule2" -Paths "/video/*" -BackendAddressPool $pool2 -BackendHttpSettings $poolSetting02

La configuración de mapa de ruta de acceso de regla, también configura un conjunto de direcciones de back-end de forma predeterminada si la ruta de acceso no coincide con cualquiera de las reglas de ruta predefinida. 

    $urlPathMap = New-AzureRmApplicationGatewayUrlPathMapConfig -Name "urlpathmap" -PathRules $videoPathRule, $imagePathRule -DefaultBackendAddressPool $pool1 -DefaultBackendHttpSettings $poolSetting02

### <a name="step-8"></a>Paso 8

Crear una configuración de regla. Este paso configura la puerta de enlace de la aplicación para usar el enrutamiento basado en la ruta de acceso de direcciones URL.

    $rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType PathBasedRouting -HttpListener $listener -UrlPathMap $urlPathMap

### <a name="step-9"></a>Paso 9

Configurar el número de instancias y el tamaño de la puerta de enlace de aplicación.

    $sku = New-AzureRmApplicationGatewaySku -Name "Standard_Small" -Tier Standard -Capacity 2

## <a name="create-application-gateway"></a>Crear la puerta de enlace de aplicación

Crear una puerta de enlace de la aplicación con todos los objetos de configuración de los pasos anteriores.

    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-RG -Location "West US" -BackendAddressPools $pool1,$pool2 -BackendHttpSettingsCollection $poolSetting01, $poolSetting02 -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener -UrlPathMaps $urlPathMap -RequestRoutingRules $rule01 -Sku $sku

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

Si desea obtener la descarga de capa de Sockets seguros (SSL), vea [Configurar una puerta de enlace de aplicación para SSL de descarga](application-gateway-ssl-arm.md).