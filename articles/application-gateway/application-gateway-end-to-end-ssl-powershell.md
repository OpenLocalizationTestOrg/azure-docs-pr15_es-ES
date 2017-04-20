<properties
    pageTitle="Configurar la directiva de SSL y SSL de llevar a cabo con la puerta de enlace de aplicación | Microsoft Azure"
    description="Este artículo describe cómo configurar SSL de llevar a cabo con la puerta de enlace de aplicación con PowerShell del Administrador de recursos de Azure"
    services="application-gateway"
    documentationCenter="na"
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

# <a name="configure-ssl-policy-and-end-to-end-ssl-with-application-gateway-using-powershell"></a>Configurar la directiva de SSL y SSL de llevar a cabo con la puerta de enlace de aplicación con PowerShell

## <a name="overview"></a>Información general

Puerta de enlace de aplicación admite el cifrado de llevar a cabo de tráfico. Para ello, puerta de enlace de aplicación finaliza la conexión SSL en la puerta de enlace de aplicación. La puerta de enlace aplica las reglas de enrutamiento para el tráfico, vuelve a cifra el paquete y reenvía el paquete a la back-end adecuados en función de las reglas de enrutamiento definidas. Cualquier respuesta desde el servidor web pasa por el mismo proceso al usuario final.

Otra característica dicha puerta de enlace de la aplicación es compatible con es deshabilitar determinadas versiones del protocolo SSL. Puerta de enlace de aplicación es compatible con la deshabilitación de la versión del protocolo siguiente; TLSv1.0, TLSv1.1 y TLSv1.2.

> [AZURE.NOTE] SSL 2.0 y SSL 3.0 están deshabilitadas de forma predeterminada y no se puede habilitar. Que se consideran no seguras y no son pueden utilizarse con la puerta de enlace de aplicación

![imagen de escenario][scenario]

## <a name="scenario"></a>Escenario

En este escenario, aprenda a crear una puerta de enlace de la aplicación con SSL de llevar a cabo con PowerShell.

Este escenario hará lo siguiente:

- Crear un grupo de recursos denominado "appgw-c"
- Crear una red virtual denominada "appgwvnet" con un bloque CIDR reservado de 10.0.0.0/16.
- Cree dos subredes denominado "appgwsubnet" y "appsubnet".
- Crear una puerta de enlace de aplicación pequeña que admite el cifrado de SSL de llevar a cabo que desactiva determinados protocolos SSL.

## <a name="before-you-begin"></a>Antes de empezar

Para configurar SSL de llevar a cabo con una puerta de enlace de aplicación, es necesario para la puerta de enlace un certificado y los certificados son necesarios para los servidores back-end. El certificado de puerta de enlace se utiliza para cifrar y descifrar el tráfico enviado a través de SSL. El certificado de puerta de enlace debe estar en formato de intercambio de información Personal (pfx). Este formato de archivo permite para que la clave privada que se pueden exportar para realizar el cifrado y descifrado de tráfico, se requiere la puerta de enlace de aplicación.

Para el cifrado ssl de llevar a cabo el back-end debe ser lista blanca con puerta de enlace de aplicación. Para ello, cargar el certificado de la back-ends público a la puerta de enlace de aplicación. Así se asegura de que la puerta de enlace de aplicación sólo se comunica con instancias conocidos back-end. Esto protege aún más la comunicación de llevar a cabo.

Este proceso se describe en los pasos siguientes:

## <a name="create-the-resource-group"></a>Crear el grupo de recursos

Esta sección le guiará a través de la creación de un grupo de recursos, que contiene la puerta de enlace de aplicación.

### <a name="step-1"></a>Paso 1

Inicie sesión en su cuenta de Azure.

    Login-AzureRmAccount

### <a name="step-2"></a>Paso 2

Seleccione la suscripción para este escenario.

    Select-AzureRmsubscription -SubscriptionName "<Subscription name>"

### <a name="step-3"></a>Paso 3

Crear un grupo de recursos (omitir este paso si está utilizando un grupo de recursos existente).

    New-AzureRmResourceGroup -Name appgw-rg -Location "West US"

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Crear una red virtual y una subred de la puerta de enlace de aplicación

En el ejemplo siguiente se crea una red virtual y dos subredes. Una subred se utiliza para guardar la puerta de enlace de aplicación. La otra subred se usa para el back-ends hospeda la aplicación web.

### <a name="step-1"></a>Paso 1

Asignar un intervalo de direcciones de la subred se utiliza para la puerta de enlace de aplicación.

    $gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24

> [AZURE.NOTE] Subredes configuradas de puerta de enlace de aplicación deben ajustarse correctamente. Una puerta de enlace de la aplicación se puede configurar para instancias de hasta 10. Cada instancia tiene 1 dirección IP de la subred. Demasiado pequeña de una subred puede afectar el escalado de una puerta de enlace de aplicación.

### <a name="step-2"></a>Paso 2

Asignar un intervalo de direcciones que se utilizará para el grupo de direcciones de back-end.

    $nicSubnet = New-AzureRmVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24

### <a name="step-3"></a>Paso 3

Crear una red virtual con cada subred definido en los pasos anteriores.

    $vnet = New-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet

### <a name="step-4"></a>Paso 4

Recuperar el recurso de red virtual y los recursos de la subred que se utilizará en los siguientes pasos:

    $vnet = Get-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
    $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
    $nicSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Crear una dirección IP pública para la configuración de front-end

Crear un recurso IP público que se usará para la puerta de enlace de aplicación. Se usa esta dirección IP pública un paso siguiente.

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -Name 'publicIP01' -Location "West US" -AllocationMethod Dynamic

> [AZURE.IMPORTANT] Puerta de enlace de aplicación no admite el uso de una dirección IP pública creada con una etiqueta de dominio definida. Solo una dirección IP pública con una etiqueta de dominio creado dinámicamente es compatible. Si establece un nombre descriptivo de dns para la puerta de enlace de aplicación, se recomienda usar un registro cname como un alias.

## <a name="create-an-application-gateway-configuration-object"></a>Crear un objeto de configuración de puerta de enlace de aplicación

Debe configurar todos los elementos de configuración antes de crear la puerta de enlace de aplicación. Los pasos siguientes crean los elementos de configuración que son necesarias para un recurso de puerta de enlace de aplicación.

### <a name="step-1"></a>Paso 1

Crear una configuración de IP de la puerta de enlace de aplicación, esta opción configura qué subred utiliza la puerta de enlace de aplicación. Cuando se inicia la puerta de enlace de aplicación, toma una dirección IP de la subred configurada y enruta el tráfico de red a las direcciones IP en el grupo IP de back-end. Tenga en cuenta que cada instancia tiene una dirección IP.

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet

### <a name="step-2"></a>Paso 2

Crear una configuración de IP front-end, esta configuración asigna una dirección ip privada o pública a front-end de la puerta de enlace de aplicación. El paso siguiente asocia la dirección IP pública en el paso anterior a la configuración de IP front-end.

    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip

### <a name="step-3"></a>Paso 3

Configurar el grupo de direcciones IP de back-end con las direcciones IP de los servidores web de back-end. Estas direcciones IP están las direcciones IP que reciban el tráfico de red que proceden del extremo IP front-end. Reemplazar las siguientes direcciones IP para agregar sus propios extremos de direcciones IP de aplicación.

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3

> [AZURE.NOTE] Un nombre de dominio completo (FQDN) también es un valor válido en lugar de una dirección ip para los servidores de back-end mediante el modificador - BackendFqdns.

### <a name="step-4"></a>Paso 4

Configurar el puerto IP front-end del extremo IP pública. Este puerto es la que los usuarios finales conectar a.

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443

### <a name="step-5"></a>Paso 5

Configurar el certificado para la puerta de enlace de aplicación. Este certificado se usa para descifrar y volver a cifrar el tráfico de la puerta de enlace de aplicación.

    $cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password <password for certificate file>

> [AZURE.NOTE] En este ejemplo se configura el certificado usado para la conexión SSL. El certificado debe estar en formato .pfx y la contraseña debe estar entre 4 a 12 caracteres.

### <a name="step-6"></a>Paso 6

Crear la escucha HTTP de la puerta de enlace de aplicación. Asignar el certificado de configuración, puertos y ssl de ip front-end para usar.

    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert

### <a name="step-7"></a>Paso 7

Cargar el certificado que se utilizará en los recursos del grupo de back-end de ssl habilitado.

> [AZURE.NOTE] El sondeo de forma predeterminada Obtiene la clave pública desde el enlace de SSL de **forma predeterminada** en la dirección IP del back-end y compara el valor de clave pública que recibe a proporciona aquí el valor de clave pública. La clave pública recuperada no puede ser necesariamente al sitio deseado para que el tráfico fluirá **Si** está utilizando encabezados de host y SNI en back-end. En caso de duda, visite https://127.0.0.1/ en los extremos de atrás para confirmar que el certificado que se usa para el enlace de SSL de **forma predeterminada** . Use la clave pública de esa solicitud de esta sección. Si está utilizando encabezados de host y SNI de enlaces HTTPS y no recibe una respuesta y un certificado de una solicitud de explorador manual a https://127.0.0.1/ en el back-end, debe configurar un enlace de SSL predeterminado en el back-end. Si no lo hace, se producirá un error sondeos y back-end no se estará en la lista blanca.

    $authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile C:\users\gwallace\Desktop\cert.cer

> [AZURE.NOTE] El certificado que se proporcionan en este paso debe ser la clave pública de los certificados de pfx presentes en el servidor. Exporte el certificado (no el certificado raíz) instalado en el servidor back-end. CER dar formato y utilizar en este paso. Este paso se blancas el back-end con la puerta de enlace de aplicación.

### <a name="step-8"></a>Paso 8

Establecer la configuración de http back-end de puerta de enlace de aplicación. Asignar el certificado cargado en el paso anterior a la configuración de http.

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert

### <a name="step-9"></a>Paso 9

Crear una regla de enrutamiento de equilibrador de carga que configura el comportamiento del equilibrador de carga. En este ejemplo, se crea una regla de turnos básica.

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

### <a name="step-10"></a>Paso 10

Configurar el tamaño de la instancia de la puerta de enlace de aplicación.  Los tamaños disponibles son **estándar\_pequeña**, **estándar\_medio**, y **estándar\_grande**.  Capacidad, los valores disponibles son 1 al 10.

    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

>[AZURE.NOTE] Puede elegir un recuento de la instancia de 1 con fines de pruebas. Es importante conocer que cualquier número de instancias en dos instancias no está cubierto por el SLA y, por tanto, no se recomienda. Pequeñas puertas de enlace van a utilizar para pruebas de desarrollo y no con fines de producción.

### <a name="step-11"></a>Paso 11

Configurar la directiva SSL que se utilizará en la puerta de enlace de aplicación. Puerta de enlace de aplicación es compatible con la capacidad de deshabilitar determinadas versiones del protocolo SSL.

Los valores siguientes son una lista de versiones del protocolo que pueden estar deshabilitados.

- **TLSv1_0**
- **TLSv1_1**
- **TLSv1_2**

En el ejemplo siguiente se deshabilita TLSv1\_0.

    $sslPolicy = New-AzureRmApplicationGatewaySslPolicy -DisabledSslProtocols TLSv1_0

## <a name="create-the-application-gateway"></a>Crear la puerta de enlace de aplicación

Con todos los pasos anteriores, cree la puerta de enlace de aplicación. La creación de la puerta de enlace es un proceso de ejecución larga.

    $appgw = New-AzureRmApplicationGateway -Name appgateway -SslCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslPolicy $sslPolicy -AuthenticationCertificates $authcert -Verbose

## <a name="disable-ssl-protocol-versions-on-an-existing-application-gateway"></a>Deshabilitar las versiones del protocolo SSL de una puerta de enlace de aplicación existente

Los pasos anteriores llevarán mediante la creación de una aplicación con ssl de llevar a cabo y deshabilitar determinadas versiones del protocolo SSL. En el ejemplo siguiente se deshabilita ciertas directivas de SSL de una puerta de enlace de aplicación existente.

### <a name="step-1"></a>Paso 1

Recuperar la puerta de enlace de aplicación para actualizar.

    $gw = Get-AzureRmApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG

### <a name="step-2"></a>Paso 2

Definir una directiva SSL. En el ejemplo siguiente, se deshabilitan TLSv1.0 y TLSv1.1.

    Set-AzureRmApplicationGatewaySslPolicy -DisabledSslProtocols TLSv1_0, TLSv1_1 -ApplicationGateway $gw

### <a name="step-3"></a>Paso 3

Por último, actualice la puerta de enlace. Es importante que tenga en cuenta que este último paso es una tarea de ejecución larga. Cuando haya terminado, llevar a cabo ssl está configurado en la puerta de enlace de aplicación.

    $gw | Set-AzureRmApplicationGateway

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

Obtenga más información sobre la seguridad de las aplicaciones web con el servidor de aplicaciones Web a través de la puerta de enlace de aplicación de consolidación con una visita a [Información general sobre el Firewall de aplicación Web](application-gateway-webapplicationfirewall-overview.md)

[scenario]: ./media/application-gateway-end-to-end-ssl-powershell/scenario.png
