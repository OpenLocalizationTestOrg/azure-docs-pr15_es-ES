<properties
   pageTitle="Crear un sondeo personalizada de puerta de enlace de aplicación mediante PowerShell en el modelo de implementación clásica | Microsoft Azure"
   description="Aprenda a crear un sondeo personalizada de puerta de enlace de aplicación con PowerShell en el modelo de implementación clásica"
   services="application-gateway"
   documentationCenter="na"
   authors="georgewallace"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="gwallace" />

# <a name="create-a-custom-probe-for-azure-application-gateway-classic-by-using-powershell"></a>Crear un sondeo personalizada de puerta de enlace de aplicación de Azure (clásico) con PowerShell

> [AZURE.SELECTOR]
- [Portal de Azure](application-gateway-create-probe-portal.md)
- [Azure PowerShell de administrador de recursos](application-gateway-create-probe-ps.md)
- [Azure PowerShell clásica](application-gateway-create-probe-classic-ps.md)

[AZURE.INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]Obtenga información sobre cómo llevar a [cabo estos pasos con el modelo de administrador de recursos](application-gateway-create-probe-ps.md).

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-an-application-gateway"></a>Crear una puerta de enlace de aplicación

Para crear una puerta de enlace de aplicación:

1. Crear un recurso de puerta de enlace de aplicación.
2. Crear un archivo de configuración XML o un objeto de configuración.
3. Confirme la configuración para el recurso de puerta de enlace de aplicación recién creado.

### <a name="create-an-application-gateway-resource"></a>Crear un recurso de puerta de enlace de aplicación

Para crear la puerta de enlace, use el cmdlet de **Nuevo AzureApplicationGateway** , reemplazando los valores por la suya propia. Facturación de la puerta de enlace no se inicia en este momento. Facturación comienza en un paso más adelante, cuando se inicia correctamente la puerta de enlace.

En el ejemplo siguiente se crea una puerta de enlace de aplicación utilizando una red virtual denominado "testvnet1" y una subred denominado "subred-1".

    New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

Para validar que la puerta de enlace se ha creado, puede usar el cmdlet **Get-AzureApplicationGateway** .

    Get-AzureApplicationGateway AppGwTest

>[AZURE.NOTE]  El valor predeterminado de *InstanceCount* es 2, con un valor máximo de 10. El valor predeterminado de *GatewaySize* es medio. Puede elegir entre pequeño, mediano y grande.

 *VirtualIPs* y *DnsName* se muestran en blanco porque aún no ha iniciado la puerta de enlace. Se crean una vez que la puerta de enlace está en ejecución.

## <a name="configure-an-application-gateway"></a>Configurar una puerta de enlace de aplicación

Puede configurar la puerta de enlace de aplicación mediante XML o un objeto de configuración.

## <a name="configure-an-application-gateway-by-using-xml"></a>Configurar una puerta de enlace de aplicación mediante XML

En el ejemplo siguiente, se utiliza un archivo XML para configurar todos los valores de puerta de enlace de la aplicación y confirme que el recurso de puerta de enlace de aplicación.  

### <a name="step-1"></a>Paso 1

Copiar el texto siguiente en el Bloc de notas.

    <ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
    <FrontendIPConfigurations>
        <FrontendIPConfiguration>
            <Name>fip1</Name>
            <Type>Private</Type>
        </FrontendIPConfiguration>
    </FrontendIPConfigurations>    
    <FrontendPorts>
        <FrontendPort>
            <Name>port1</Name>
            <Port>80</Port>
        </FrontendPort>
    </FrontendPorts>
    <Probes>
        <Probe>
            <Name>Probe01</Name>
            <Protocol>Http</Protocol>
            <Host>contoso.com</Host>
            <Path>/path/custompath.htm</Path>
            <Interval>15</Interval>
            <Timeout>15</Timeout>
            <UnhealthyThreshold>5</UnhealthyThreshold>
        </Probe>
      </Probes>
     <BackendAddressPools>
        <BackendAddressPool>
            <Name>pool1</Name>
            <IPAddresses>
                <IPAddress>1.1.1.1</IPAddress>
                <IPAddress>2.2.2.2</IPAddress>
            </IPAddresses>
        </BackendAddressPool>
    </BackendAddressPools>
    <BackendHttpSettingsList>
        <BackendHttpSettings>
            <Name>setting1</Name>
            <Port>80</Port>
            <Protocol>Http</Protocol>
            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
            <RequestTimeout>120</RequestTimeout>
            <Probe>Probe01</Probe>
        </BackendHttpSettings>
    </BackendHttpSettingsList>
    <HttpListeners>
        <HttpListener>
            <Name>listener1</Name>
            <FrontendIP>fip1</FrontendIP>
        <FrontendPort>port1</FrontendPort>
            <Protocol>Http</Protocol>
        </HttpListener>
    </HttpListeners>
    <HttpLoadBalancingRules>
        <HttpLoadBalancingRule>
            <Name>lbrule1</Name>
            <Type>basic</Type>
            <BackendHttpSettings>setting1</BackendHttpSettings>
            <Listener>listener1</Listener>
            <BackendAddressPool>pool1</BackendAddressPool>
        </HttpLoadBalancingRule>
    </HttpLoadBalancingRules>
    </ApplicationGatewayConfiguration>


Edite los valores entre los paréntesis para los elementos de configuración. Guarde el archivo con la extensión .xml.

En el ejemplo siguiente se muestra cómo usar un archivo de configuración para configurar la puerta de enlace de aplicación, para el tráfico HTTP en el puerto público 80 de equilibrio de carga y enviar tráfico de red al puerto 80 de back-end entre dos direcciones IP con un sondeo personalizada.

>[AZURE.IMPORTANT] El elemento de protocolo Http o Https distingue mayúsculas de minúsculas.

Un nuevo elemento de configuración \<sondeo\> se agrega al configurar sondeos personalizados.

Los parámetros de configuración son:

- **Nombre** : nombre de referencia de sondeo personalizada.
- **Protocolo** - protocolo utilizado (valores posibles son HTTP o HTTPS).
- **Ruta de acceso** - dirección URL completa que se invoca la puerta de enlace de aplicación para determinar el estado de la instancia y el **host** . Por ejemplo, si tiene un sitio Web http://contoso.com/, se puede configurar el sondeo personalizada para "http://contoso.com/path/custompath.htm" para que los controles de sondeo tiene una respuesta HTTP correcta.
- **Intervalo** - configura las comprobaciones de intervalo de sondeo en segundos.
- **Tiempo de espera** - define el tiempo de espera de sondeo para una comprobación de respuesta HTTP.
- **UnhealthyThreshold** - el número de respuestas HTTP erróneas necesarias para marcar la instancia de back-end como *mal estado*.

El nombre del sondeo se hace referencia en el <BackendHttpSettings> configuración para asignar qué grupo back-end utiliza valores de sondeo personalizada.

## <a name="add-a-custom-probe-configuration-to-an-existing-application-gateway"></a>Agregar una configuración de sondeo personalizada a una puerta de enlace de aplicación existente

Cambiar la configuración actual de una puerta de enlace de aplicación requiere tres pasos: obtener el archivo de configuración XML actual, modificar para tener un sondeo personalizada y configurar la puerta de enlace de la aplicación con la nueva configuración de XML.

### <a name="step-1"></a>Paso 1

Obtener el archivo XML mediante get-AzureApplicationGatewayConfig. Exporta la configuración XML modificarse para agregar una configuración de sondeo.

    Get-AzureApplicationGatewayConfig -Name "<application gateway name>" -Exporttofile "<path to file>"


### <a name="step-2"></a>Paso 2

Abra el archivo XML en un editor de texto. Agregar un `<probe>` después de la sección `<frontendport>`.

    <Probes>
        <Probe>
            <Name>Probe01</Name>
            <Protocol>Http</Protocol>
            <Host>contoso.com</Host>
            <Path>/path/custompath.htm</Path>
            <Interval>15</Interval>
            <Timeout>15</Timeout>
            <UnhealthyThreshold>5</UnhealthyThreshold>
        </Probe>
    </Probes>

En la sección backendHttpSettings del XML, agregue el nombre del sondeo tal como se muestra en el ejemplo siguiente:

        <BackendHttpSettings>
            <Name>setting1</Name>
            <Port>80</Port>
            <Protocol>Http</Protocol>
            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
            <RequestTimeout>120</RequestTimeout>
            <Probe>Probe01</Probe>
        </BackendHttpSettings>

Guarde el archivo XML.

### <a name="step-3"></a>Paso 3

Actualizar la configuración de puerta de enlace de la aplicación con el nuevo archivo XML mediante el **Conjunto AzureApplicationGatewayConfig**. La puerta de enlace de la aplicación se actualiza con la nueva configuración.

    Set-AzureApplicationGatewayConfig -Name "<application gateway name>" -Configfile "<path to file>"


## <a name="next-steps"></a>Pasos siguientes

Si desea configurar la descarga de capa de Sockets seguros (SSL), vea [Configurar una puerta de enlace de aplicación para SSL de descarga](application-gateway-ssl.md).

Si desea configurar una puerta de enlace de la aplicación para usar con un equilibrador de carga interno, consulte [crear una puerta de enlace de la aplicación con un equilibrador de carga interno (ILB)](application-gateway-ilb.md).
