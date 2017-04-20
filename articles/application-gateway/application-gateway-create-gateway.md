<properties
   pageTitle="Crear, iniciar o eliminar una puerta de enlace de aplicación | Microsoft Azure"
   description="Esta página contiene instrucciones para crear, configurar, iniciar y eliminar una puerta de enlace de aplicación de Azure"
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

# <a name="create-start-or-delete-an-application-gateway"></a>Crear, iniciar o eliminar una puerta de enlace de aplicación

> [AZURE.SELECTOR]
- [Portal de Azure](application-gateway-create-gateway-portal.md)
- [Azure PowerShell de administrador de recursos](application-gateway-create-gateway-arm.md)
- [Azure PowerShell clásica](application-gateway-create-gateway.md)
- [Azure plantilla de administrador de recursos](application-gateway-create-gateway-arm-template.md)
- [CLI de Azure](application-gateway-create-gateway-cli.md)

La puerta de enlace de aplicación Azure es un equilibrador de carga de nivel 7. Proporciona migración tras error, las solicitudes HTTP enrutamiento de rendimiento entre diferentes servidores, independientemente de si están en la nube o local. Puerta de enlace de aplicación proporciona muchas características de controlador de entrega de la aplicación (ADC) incluye Equilibrio de carga HTTP, afinidad sesión basada en cookies, descargar Secure Sockets Layer (SSL), sondeos de estado personalizados, compatibilidad con varios sitios y muchas otras. Para obtener una lista completa de características compatibles, visite [Introducción de puerta de enlace de aplicaciones](application-gateway-introduction.md)

En este artículo le guiará por los pasos para crear, configurar, iniciar y eliminar una puerta de enlace de aplicación.

## <a name="before-you-begin"></a>Antes de empezar

1. Instalar la última versión de los cmdlets de PowerShell de Azure mediante el instalador de plataforma Web. Puede descargar e instalar la versión más reciente de la sección de **Windows PowerShell** de la [página de descargas](https://azure.microsoft.com/downloads/).
2. Si tiene una red virtual existente, seleccione una subred vacía existente o crear una nueva subred en su red virtual existente únicamente para usar la puerta de enlace de aplicación. No se puede implementar la puerta de enlace de aplicación a una red virtual distinta a los recursos que desee distribuir detrás de la puerta de enlace de aplicación a menos que se usa vnet interconexión. Para obtener más información visite [Vnet interconexión](../virtual-network/virtual-network-peering-overview.md)
3. Compruebe que tiene una red virtual de trabajar con una subred válida. Asegúrese de que no hay máquinas virtuales o implementaciones de nube utilizan la subred. La puerta de enlace de aplicación debe ser por sí mismo en una subred de una red virtual.
3. Los servidores que configurar para que utilice la puerta de enlace de aplicación deben existir o han asignado sus extremos creados en una red virtual o con VIP o IP pública.

## <a name="what-is-required-to-create-an-application-gateway"></a>¿Qué se necesita para crear una puerta de enlace de la aplicación?

Al usar el comando **Nuevo AzureApplicationGateway** para crear la puerta de enlace de aplicación, no se ha configurado en este momento y se han configurado el recurso recién creado mediante XML o un objeto de configuración.

Los valores son:

- **Grupo de servidores de back-end:** La lista de direcciones IP de los servidores de back-end. Las direcciones IP enumeradas o bien deben pertenecer a la subred virtual o deben ser VIP o IP pública.
- **Configuración de la agrupación de back-end server:** Cada grupo tiene configuración como afinidad basada en cookies, protocolo y puerto. Estas opciones están vinculadas a un grupo y se aplican a todos los servidores dentro del grupo.
- **Puerto front-end:** Este puerto es el público que se abre en la puerta de enlace de aplicación. El tráfico llega a este puerto y, a continuación, se redirige a uno de los servidores de back-end.
- **Escucha:** La escucha tiene un puerto front-end, un protocolo (Http o Https, estos valores distinguen mayúsculas de minúsculas) y el nombre del certificado SSL (si se descarga la configuración de SSL).
- **Regla:** La regla enlaza la escucha y el grupo de servidor back-end y define el tráfico debe dirigirse a cuando llegue a una determinada escucha qué grupo de servidor back-end.

## <a name="create-an-application-gateway"></a>Crear una puerta de enlace de aplicación

Para crear una puerta de enlace de aplicación:

1. Crear un recurso de puerta de enlace de aplicación.
2. Crear un archivo de configuración XML o un objeto de configuración.
3. Confirme la configuración para el recurso de puerta de enlace de aplicación recién creado.

>[AZURE.NOTE] Si necesita configurar un sondeo personalizado de la puerta de enlace de aplicación, consulte [crear una puerta de enlace de la aplicación con sondeos personalizados con PowerShell](application-gateway-create-probe-classic-ps.md). Consulte [sondeos personalizados y la supervisión de estado](application-gateway-probe-overview.md) para obtener más información.

![Ejemplo de escenario][scenario]

### <a name="create-an-application-gateway-resource"></a>Crear un recurso de puerta de enlace de aplicación

Para crear la puerta de enlace, use el cmdlet de **Nuevo AzureApplicationGateway** , reemplazando los valores por la suya propia. Facturación de la puerta de enlace no se inicia en este momento. Facturación comienza en un paso más adelante, cuando se inicia correctamente la puerta de enlace.

En el ejemplo siguiente se crea una puerta de enlace de aplicación utilizando una red virtual denominado "testvnet1" y una subred denominado "subred-1".


    New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

    VERBOSE: 4:31:35 PM - Begin Operation: New-AzureApplicationGateway
    VERBOSE: 4:32:37 PM - Completed Operation: New-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   55ef0460-825d-2981-ad20-b9a8af41b399


 *Descripción*, *InstanceCount*y *GatewaySize* son parámetros opcionales.

Para validar que la puerta de enlace se ha creado, puede usar el cmdlet **Get-AzureApplicationGateway** .

    Get-AzureApplicationGateway AppGwTest
    Name          : AppGwTest
    Description   :
    VnetName      : testvnet1
    Subnets       : {Subnet-1}
    InstanceCount : 2
    GatewaySize   : Medium
    State         : Stopped
    VirtualIPs    : {}
    DnsName       :

>[AZURE.NOTE]  El valor predeterminado de *InstanceCount* es 2, con un valor máximo de 10. El valor predeterminado de *GatewaySize* es medio. Puede elegir entre pequeño, mediano y grande.

*VirtualIPs* y *DnsName* se muestran en blanco porque aún no ha iniciado la puerta de enlace. Se crean una vez que la puerta de enlace está en ejecución.

## <a name="configure-the-application-gateway"></a>Configurar la puerta de enlace de aplicación

Puede configurar la puerta de enlace de aplicación mediante XML o un objeto de configuración.

## <a name="configure-the-application-gateway-by-using-xml"></a>Configurar la puerta de enlace de aplicación mediante XML

En el ejemplo siguiente, se utiliza un archivo XML para configurar todos los valores de puerta de enlace de la aplicación y confirme que el recurso de puerta de enlace de aplicación.  

### <a name="step-1"></a>Paso 1  

Copiar el texto siguiente en el Bloc de notas.

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
        <FrontendPorts>
            <FrontendPort>
                <Name>(name-of-your-frontend-port)</Name>
                <Port>(port number)</Port>
            </FrontendPort>
        </FrontendPorts>
        <BackendAddressPools>
            <BackendAddressPool>
                <Name>(name-of-your-backend-pool)</Name>
                <IPAddresses>
                    <IPAddress>(your-IP-address-for-backend-pool)</IPAddress>
                    <IPAddress>(your-second-IP-address-for-backend-pool)</IPAddress>
                </IPAddresses>
            </BackendAddressPool>
        </BackendAddressPools>
        <BackendHttpSettingsList>
            <BackendHttpSettings>
                <Name>(backend-setting-name-to-configure-rule)</Name>
                <Port>80</Port>
                <Protocol>[Http|Https]</Protocol>
                <CookieBasedAffinity>Enabled</CookieBasedAffinity>
            </BackendHttpSettings>
        </BackendHttpSettingsList>
        <HttpListeners>
            <HttpListener>
                <Name>(name-of-the-listener)</Name>
                <FrontendPort>(name-of-your-frontend-port)</FrontendPort>
                <Protocol>[Http|Https]</Protocol>
            </HttpListener>
        </HttpListeners>
        <HttpLoadBalancingRules>
            <HttpLoadBalancingRule>
                <Name>(name-of-load-balancing-rule)</Name>
                <Type>basic</Type>
                <BackendHttpSettings>(backend-setting-name-to-configure-rule)</BackendHttpSettings>
                <Listener>(name-of-the-listener)</Listener>
                <BackendAddressPool>(name-of-your-backend-pool)</BackendAddressPool>
            </HttpLoadBalancingRule>
        </HttpLoadBalancingRules>
    </ApplicationGatewayConfiguration>

Edite los valores entre los paréntesis para los elementos de configuración. Guarde el archivo con la extensión .xml.

>[AZURE.IMPORTANT] El elemento de protocolo Http o Https distingue mayúsculas de minúsculas.

En el ejemplo siguiente se muestra cómo usar un archivo de configuración para configurar la puerta de enlace de aplicación. La carga de ejemplo equilibra el tráfico HTTP en el puerto público 80 y envía el tráfico de red al back-end puerto 80 entre dos direcciones IP.

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
        <FrontendPorts>
            <FrontendPort>
                <Name>FrontendPort1</Name>
                <Port>80</Port>
            </FrontendPort>
        </FrontendPorts>
        <BackendAddressPools>
            <BackendAddressPool>
                <Name>BackendPool1</Name>
                <IPAddresses>
                    <IPAddress>10.0.0.1</IPAddress>
                    <IPAddress>10.0.0.2</IPAddress>
                </IPAddresses>
            </BackendAddressPool>
        </BackendAddressPools>
        <BackendHttpSettingsList>
            <BackendHttpSettings>
                <Name>BackendSetting1</Name>
                <Port>80</Port>
                <Protocol>Http</Protocol>
                <CookieBasedAffinity>Enabled</CookieBasedAffinity>
            </BackendHttpSettings>
        </BackendHttpSettingsList>
        <HttpListeners>
            <HttpListener>
                <Name>HTTPListener1</Name>
                <FrontendPort>FrontendPort1</FrontendPort>
                <Protocol>Http</Protocol>
            </HttpListener>
        </HttpListeners>
        <HttpLoadBalancingRules>
            <HttpLoadBalancingRule>
                <Name>HttpLBRule1</Name>
                <Type>basic</Type>
                <BackendHttpSettings>BackendSetting1</BackendHttpSettings>
                <Listener>HTTPListener1</Listener>
                <BackendAddressPool>BackendPool1</BackendAddressPool>
            </HttpLoadBalancingRule>
        </HttpLoadBalancingRules>
    </ApplicationGatewayConfiguration>


### <a name="step-2"></a>Paso 2

A continuación, establezca la puerta de enlace de aplicación. Usar el cmdlet **Set-AzureApplicationGatewayConfig** con un archivo XML de configuración.


    Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile "D:\config.xml"

    VERBOSE: 7:54:59 PM - Begin Operation: Set-AzureApplicationGatewayConfig
    VERBOSE: 7:55:32 PM - Completed Operation: Set-AzureApplicationGatewayConfig
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   9b995a09-66fe-2944-8b67-9bb04fcccb9d

## <a name="configure-the-application-gateway-by-using-a-configuration-object"></a>Configurar la puerta de enlace de aplicación mediante un objeto de configuración

En el ejemplo siguiente se muestra cómo configurar la puerta de enlace de aplicación mediante el uso de objetos de configuración. Todos los elementos de configuración se deben configurar de forma individual y, a continuación, se agrega a un objeto de configuración de puerta de enlace de aplicación. Después de crear el objeto de configuración, utilice el comando **Conjunto AzureApplicationGateway** para confirmar la configuración para el recurso de puerta de enlace de aplicación creado previamente.

>[AZURE.NOTE] Antes de asignar un valor a cada objeto de configuración, debe declarar ¿qué tipo de objeto de PowerShell que se usa para el almacenamiento. La primera línea para crear los elementos individuales define qué Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model (nombre de objeto) se usan.

### <a name="step-1"></a>Paso 1

Crear todos los elementos de configuración individuales.

Crear la dirección IP front-end tal como se muestra en el ejemplo siguiente.

    $fip = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendIPConfiguration
    $fip.Name = "fip1"
    $fip.Type = "Private"
    $fip.StaticIPAddress = "10.0.0.5"

Crear el puerto front-end, como se muestra en el ejemplo siguiente.

    $fep = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendPort
    $fep.Name = "fep1"
    $fep.Port = 80

Crear el grupo de servidor back-end.

 Definir las direcciones IP que se agregan a la agrupación de servidor back-end, como se muestra en el ejemplo siguiente.


    $servers = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendServerCollection
    $servers.Add("10.0.0.1")
    $servers.Add("10.0.0.2")

 Utilice el objeto $server para agregar los valores para el objeto de grupo de back-end ($pool).

    $pool = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendAddressPool
    $pool.BackendServers = $servers
    $pool.Name = "pool1"

Crear la configuración de la agrupación de servidor back-end.

    $setting = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendHttpSettings
    $setting.Name = "setting1"
    $setting.CookieBasedAffinity = "enabled"
    $setting.Port = 80
    $setting.Protocol = "http"

Crear la escucha.

    $listener = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpListener
    $listener.Name = "listener1"
    $listener.FrontendPort = "fep1"
    $listener.FrontendIP = "fip1"
    $listener.Protocol = "http"
    $listener.SslCert = ""

Cree la regla.

    $rule = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpLoadBalancingRule
    $rule.Name = "rule1"
    $rule.Type = "basic"
    $rule.BackendHttpSettings = "setting1"
    $rule.Listener = "listener1"
    $rule.BackendAddressPool = "pool1"

### <a name="step-2"></a>Paso 2

Asignar todos los elementos de configuración individuales a un objeto de configuración de puerta de enlace de aplicación ($appgwconfig).

Agregar la dirección IP front-end a la configuración.

    $appgwconfig = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.ApplicationGatewayConfiguration
    $appgwconfig.FrontendIPConfigurations = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendIPConfiguration]"
    $appgwconfig.FrontendIPConfigurations.Add($fip)

Agregar el puerto front-end a la configuración.

    $appgwconfig.FrontendPorts = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendPort]"
    $appgwconfig.FrontendPorts.Add($fep)

Agregue la agrupación de servidor back-end de configuración.

    $appgwconfig.BackendAddressPools = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendAddressPool]"
    $appgwconfig.BackendAddressPools.Add($pool)

Agregar la configuración de la agrupación de back-end a la configuración.

    $appgwconfig.BackendHttpSettingsList = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendHttpSettings]"
    $appgwconfig.BackendHttpSettingsList.Add($setting)

Agregar el agente de escucha a la configuración.

    $appgwconfig.HttpListeners = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpListener]"
    $appgwconfig.HttpListeners.Add($listener)

Agregar la regla a la configuración.

    $appgwconfig.HttpLoadBalancingRules = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpLoadBalancingRule]"
    $appgwconfig.HttpLoadBalancingRules.Add($rule)

### <a name="step-3"></a>Paso 3

Confirmar el objeto de configuración en el recurso de puerta de enlace de aplicación utilizando el **Conjunto AzureApplicationGatewayConfig**.

    Set-AzureApplicationGatewayConfig -Name AppGwTest -Config $appgwconfig

## <a name="start-the-gateway"></a>Iniciar la puerta de enlace

Una vez que se ha configurado la puerta de enlace, use el cmdlet **AzureApplicationGateway de inicio** para iniciar la puerta de enlace. Facturación de una puerta de enlace de aplicación comienza después de la puerta de enlace se ha iniciado correctamente.

> [AZURE.NOTE] El cmdlet **Start AzureApplicationGateway** puede tardar hasta 15-20 minutos en Finalizar.

    Start-AzureApplicationGateway AppGwTest

    VERBOSE: 7:59:16 PM - Begin Operation: Start-AzureApplicationGateway
    VERBOSE: 8:05:52 PM - Completed Operation: Start-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   fc592db8-4c58-2c8e-9a1d-1c97880f0b9b

## <a name="verify-the-gateway-status"></a>Comprobar el estado de la puerta de enlace

Use el cmdlet **Get-AzureApplicationGateway** para comprobar el estado de la puerta de enlace. Si **Inicio AzureApplicationGateway** correcta en el paso anterior, debe estar ejecutándose *estado* y *Vip* y *DnsName* deberían tener entradas válidas.

En el ejemplo siguiente se muestra una puerta de enlace de aplicación que está arriba, ejecutar, y destinados a preparado para tomar el tráfico `http://<generated-dns-name>.cloudapp.net`.

    Get-AzureApplicationGateway AppGwTest

    VERBOSE: 8:09:28 PM - Begin Operation: Get-AzureApplicationGateway
    VERBOSE: 8:09:30 PM - Completed Operation: Get-AzureApplicationGateway
    Name          : AppGwTest
    Description   :
    VnetName      : testvnet1
    Subnets       : {Subnet-1}
    InstanceCount : 2
    GatewaySize   : Medium
    State         : Running
    Vip           : 138.91.170.26
    DnsName       : appgw-1b8402e8-3e0d-428d-b661-289c16c82101.cloudapp.net


## <a name="delete-an-application-gateway"></a>Eliminar una puerta de enlace de aplicación

Para eliminar una puerta de enlace de aplicación:

1. Usar el cmdlet **AzureApplicationGateway detener** para detener la puerta de enlace.
2. Use el cmdlet **AzureApplicationGateway quitar** para quitar la puerta de enlace.
3. Compruebe que la puerta de enlace se ha quitado mediante el cmdlet **Get-AzureApplicationGateway** .

En el ejemplo siguiente se muestra el cmdlet **Stop AzureApplicationGateway** en la primera línea, seguida de la salida.

    Stop-AzureApplicationGateway AppGwTest

    VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway
    VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8

Una vez que la puerta de enlace de la aplicación está en un estado de detención, use el cmdlet de **AzureApplicationGateway quitar** para quitar el servicio.


    Remove-AzureApplicationGateway AppGwTest

    VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway
    VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301

Para comprobar que se ha quitado el servicio, puede usar el cmdlet **Get-AzureApplicationGateway** . Este paso no es necesario.


    Get-AzureApplicationGateway AppGwTest

    VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway

    Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist.
    .....

## <a name="next-steps"></a>Pasos siguientes

Si desea configurar la descarga SSL, vea [Configurar una puerta de enlace de aplicación para SSL de descarga](application-gateway-ssl.md).

Si desea configurar una puerta de enlace de la aplicación para usar con un equilibrador de carga interno, consulte [crear una puerta de enlace de la aplicación con un equilibrador de carga interno (ILB)](application-gateway-ilb.md).

Si desea obtener más información sobre opciones de equilibrio de carga en general, vea:

- [Equilibrador de carga de Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Administrador de tráfico de Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)

[scenario]: ./media/application-gateway-create-gateway/scenario.png