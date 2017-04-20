<properties
   pageTitle="Configurar el servidor de aplicaciones Web de puerta de enlace de aplicación nuevo o existente | Microsoft Azure"
   description="Este artículo proporciona instrucciones sobre cómo empezar a usar el servidor de aplicaciones web de una puerta de enlace de aplicación nuevo o existente."
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
   ms.date="10/25/2016"
   ms.author="gwallace"/>

# <a name="configure-web-application-firewall-on-a-new-or-existing-application-gateway"></a>Configurar el servidor de aplicaciones Web de una puerta de enlace de aplicación nuevo o existente

> [AZURE.SELECTOR]
- [Portal de Azure](application-gateway-web-application-firewall-portal.md)
- [Azure PowerShell de administrador de recursos](application-gateway-web-application-firewall-powershell.md)

El servidor de aplicaciones web (WAFS) en la puerta de enlace de aplicaciones de Azure protege aplicaciones web de ataques basados en web comunes como la inserción de SQL, ataques de scripting entre sitios y apropiaciones de sesión.

La puerta de enlace de aplicación Azure es un equilibrador de carga de nivel 7. Proporciona migración tras error, las solicitudes HTTP enrutamiento de rendimiento entre diferentes servidores, independientemente de si están en la nube o local. Aplicación proporciona muchas características de controlador de entrega de la aplicación (ADC) incluye Equilibrio de carga HTTP, afinidad sesión basada en cookies, descargar Secure Sockets Layer (SSL), sondeos de estado personalizados, compatibilidad con varios sitios y muchas otras. Para obtener una lista completa de características compatibles, visite introducción de puerta de enlace de aplicaciones

El artículo siguiente muestra cómo [Agregar el servidor de aplicaciones web a una puerta de enlace de aplicación existente](#add-web-application-firewall-to-an-existing-application-gateway) y [crear una puerta de enlace de aplicación que utiliza el servidor de aplicaciones web](#create-an-application-gateway-with-web-application-firewall).

![imagen de escenario][scenario]

## <a name="waf-configuration-differences"></a>Diferencias de configuración WAFS

Si ha leído [crear una puerta de enlace de la aplicación con PowerShell](application-gateway-create-gateway-arm.md), comprender la configuración de SKU configurar al crear una puerta de enlace de aplicación. WAFS proporcionan opciones adicionales para definir al configurar el SKU en una puerta de enlace de aplicación. No hay ningún cambio adicional que realice en la puerta de enlace de aplicación.

**SKU** : una puerta de enlace de aplicación normal sin WAFS es compatible con **estándar\_pequeña**, **estándar\_medio**, y **estándar\_grande** tamaños. Con la introducción de WAFS, hay dos SKU adicionales, **WAFS\_medio** y **WAFS\_grande**. WAFS no es compatible en puertas de enlace de aplicación pequeña.

**Nivel** : los valores disponibles son **estándar** o **WAFS**. Cuando se usa el servidor de aplicaciones Web, se debe elegir **WAFS** .

**Modo** : esta opción es el modo de WAFS. los valores permitidos son **detección** y **prevención**. Cuando WAFS está configurado en modo de detección, todas las amenazas se almacenan en un archivo de registro. En el modo Prevención de eventos siguen registrados pero el intruso recibe una respuesta 403 no autorizado de la puerta de enlace de aplicación.

## <a name="add-web-application-firewall-to-an-existing-application-gateway"></a>Agregar el servidor de aplicaciones web a una puerta de enlace de aplicación existente

Asegúrese de que está usando la versión más reciente de PowerShell de Azure. Más información está disponible en el [Uso de Windows PowerShell con el Administrador de recursos](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Paso 1

Inicie sesión en su cuenta de Azure.

    Login-AzureRmAccount

### <a name="step-2"></a>Paso 2

Seleccione la suscripción para este escenario.

    Select-AzureRmSubscription -SubscriptionName "<Subscription name>"

### <a name="step-3"></a>Paso 3

Recuperar la puerta de enlace que está agregando Firewall de aplicación Web.

    $gw = Get-AzureRmApplicationGateway -Name "AdatumGateway" -ResourceGroupName "MyResourceGroup"


### <a name="step-4"></a>Paso 4

Configurar el sku de firewall de aplicación web. Los tamaños disponibles son **WAFS\_grande** y **WAFS\_medio**. Cuando se utiliza el servidor de aplicaciones web el nivel debe ser **WAFS**, debe confirmar la capacidad al establecer el sku.

    $gw | Set-AzureRmApplicationGatewaySku -Name WAF_Large -Tier WAF -Capacity 2

### <a name="step-5"></a>Paso 5

Configurar las opciones de WAFS según se define en el ejemplo siguiente:

Para la configuración de **WafMode** los valores disponibles son la detección y prevención.

    $gw | Set-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode Prevention

### <a name="step-6"></a>Paso 6

Actualice la puerta de enlace de la aplicación con la configuración definida en el paso anterior.

    Set-AzureRmApplicationGateway -ApplicationGateway $gw

Este comando actualiza la puerta de enlace de la aplicación con el servidor de aplicaciones Web. Se recomienda ver [Diagnósticos de puerta de enlace de aplicaciones](application-gateway-diagnostics.md) para comprender cómo ver registros de la puerta de enlace de aplicación. Debido a la naturaleza de seguridad de WAFS, registros deben revisarse periódicamente para conocer la posición de seguridad de las aplicaciones web.

## <a name="create-an-application-gateway-with-web-application-firewall"></a>Crear una puerta de enlace de la aplicación con el servidor de aplicaciones Web

Los siguientes pasos lo guiarán a través de todo el proceso de principio a fin de crear una puerta de enlace de la aplicación con el servidor de aplicaciones Web.

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

    Select-AzureRmsubscription -SubscriptionName "<Subscription name>"

### <a name="step-4"></a>Paso 4

Crear un grupo de recursos (omitir este paso si está utilizando un grupo de recursos existente).

    New-AzureRmResourceGroup -Name appgw-rg -Location "West US"

Administrador de recursos de Azure requiere que todos los grupos de recursos especifican una ubicación. Esta ubicación se usa como la ubicación predeterminada para los recursos de ese grupo de recursos. Asegúrese de que todos los comandos para crear una puerta de enlace de aplicación utiliza el mismo grupo de recursos.

En el ejemplo anterior, que se creó un grupo de recursos denominado "appgw-c" y la ubicación "Oeste nos".

>[AZURE.NOTE] Si necesita configurar un sondeo personalizado de la puerta de enlace de aplicación, consulte [crear una puerta de enlace de la aplicación con sondeos personalizados con PowerShell](application-gateway-create-probe-ps.md). Consulte [sondeos personalizados y la supervisión de estado](application-gateway-probe-overview.md) para obtener más información.

### <a name="step-5"></a>Paso 5

Asignar un intervalo de direcciones de la subred se utiliza para la puerta de enlace de aplicación.

    $gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24

> [AZURE.NOTE] Una subred para una aplicación debe tener un mínimo de 28 bits de máscara. Este valor deja 10 direcciones disponibles en la subred para instancias de puerta de enlace de aplicación. Con una subred más pequeña, es podrán que no pueda agregar más instancias de la puerta de enlace de aplicaciones en el futuro.

### <a name="step-6"></a>Paso 6

Asignar un intervalo de direcciones que se utilizará para el grupo de direcciones de back-end.

    $nicSubnet = New-AzureRmVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24

### <a name="step-7"></a>Paso 7

Crear una red virtual con cada subred anterior en el grupo de recursos que se creó en el paso: [crear el grupo de recursos](#create-the-resource-group)

    $vnet = New-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet

### <a name="step-8"></a>Paso 8

Recuperar el recurso de red virtual y los recursos de la subred que se utilizará en los siguientes pasos:

    $vnet = Get-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
    $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
    $nicSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet

### <a name="step-9"></a>Paso 9

Crear un recurso IP público que se usará para la puerta de enlace de aplicación. Esta dirección IP pública se utiliza en uno de los siguientes pasos:

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name 'appgwpip' -Location "West US" -AllocationMethod Dynamic

> [AZURE.IMPORTANT] Puerta de enlace de aplicación no admite el uso de una dirección IP pública creada con una etiqueta de dominio definida. Solo una dirección IP pública con una etiqueta de dominio creado dinámicamente es compatible. Si establece un nombre descriptivo de dns para la puerta de enlace de aplicación, se recomienda usar un registro cname como un alias.

### <a name="step-10"></a>Paso 10

Debe configurar todos los elementos de configuración antes de crear la puerta de enlace de aplicación. Los pasos siguientes crean los elementos de configuración que son necesarias para un recurso de puerta de enlace de aplicación.

Crear una configuración de IP de la puerta de enlace de aplicación, Esto configura qué subred utiliza la puerta de enlace de aplicación. Cuando se inicia la puerta de enlace de aplicación, toma una dirección IP de la subred configurada y enruta el tráfico de red a las direcciones IP en el grupo IP de back-end. Tenga en cuenta que cada instancia tiene una dirección IP.

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet

### <a name="step-11"></a>Paso 11

Configurar el grupo de direcciones IP de back-end con las direcciones IP de los servidores web de back-end. Estas direcciones IP están las direcciones IP que reciban el tráfico de red que proceden del extremo IP front-end. Reemplazar las siguientes direcciones IP para agregar sus propios extremos de direcciones IP de aplicación.

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3

### <a name="step-12"></a>Paso 12

Cargar el certificado que se utilizará en los recursos del grupo de back-end de ssl habilitado.

    $authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile <full path to .cer file>

### <a name="step-13"></a>Paso 13

Establecer la configuración de http de back-end de puerta de enlace de aplicación. Asignar el certificado cargado en el paso anterior a la configuración de http.

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert

### <a name="step-14"></a>Paso 14

Configurar el puerto IP front-end del extremo IP pública. Este puerto es la que los usuarios finales conectar a.

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443

### <a name="step-15"></a>Paso 15

Crear una configuración de IP front-end, esta configuración asigna una dirección ip privada o pública a front-end de la puerta de enlace de aplicación. El paso siguiente asocia la dirección IP pública en el paso anterior a la configuración de IP front-end.

    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip

### <a name="step-16"></a>Paso 16

Configurar el certificado para la puerta de enlace de aplicación. Este certificado se usa para descifrar y volver a cifrar el tráfico de la puerta de enlace de aplicación.

    $cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password <password for certificate file>

### <a name="step-17"></a>Paso 17

Crear la escucha HTTP de la puerta de enlace de aplicación. Asignar el certificado de configuración, puertos y ssl de ip front-end para usar.

    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert

### <a name="step-18"></a>Paso 18

Crear una regla de enrutamiento de equilibrador de carga que configura el comportamiento del equilibrador de carga. En este ejemplo, se crea una regla de turnos básica.

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
   
### <a name="step-19"></a>Paso 19

Configurar el tamaño de la instancia de la puerta de enlace de aplicación.

    $sku = New-AzureRmApplicationGatewaySku -Name WAF_Medium -Tier WAF -Capacity 2

>[AZURE.NOTE]  Puede elegir entre **WAFS\_medio** y **WAFS\_grande**, el nivel de uso WAFS siempre es **WAFS**. La capacidad es un número comprendido entre 1 y 10.

### <a name="step-20"></a>Paso 20

Configurar el modo de WAFS, los valores aceptables son **la detección**y **prevención** .

    $config = New-AzureRmApplicationGatewayWafConfig -Enabled $true -WafMode "Prevention"

### <a name="step-21"></a>Paso 21

Crear una puerta de enlace de la aplicación con todos los elementos de configuración de los pasos anteriores. En este ejemplo, la puerta de enlace de aplicación se denomina "appgwtest".

    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert -AuthenticationCertificates $authcert

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

Obtenga información sobre cómo configurar el registro de diagnóstico para registrar los eventos que se hayan detectado o impide con el servidor de aplicaciones Web con una visita a [Diagnósticos de puerta de enlace de aplicaciones](application-gateway-diagnostics.md)


[scenario]: ./media/application-gateway-web-application-firewall-powershell/scenario.png