<properties
   pageTitle="Configurar una puerta de enlace de la aplicación de descarga SSL con la implementación clásica | Microsoft Azure"
   description="Este artículo ofrece instrucciones para crear una puerta de enlace de la aplicación con SSL de descarga mediante el modelo de implementación clásica Azure."
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

# <a name="configure-an-application-gateway-for-ssl-offload-by-using-the-classic-deployment-model"></a>Configurar una puerta de enlace de la aplicación de descarga SSL mediante el modelo de implementación clásica

> [AZURE.SELECTOR]
-[Portal de Azure](application-gateway-ssl-portal.md)
-[PowerShell del Administrador de recursos de Azure](application-gateway-ssl-arm.md)
-[PowerShell clásico de Azure](application-gateway-ssl.md)

La puerta de enlace de aplicación Azure se puede configurar para finalizar la sesión de Secure Sockets Layer (SSL) de la puerta de enlace para evitar costosas tareas de descifrado SSL para que se producen en el conjunto de servidores web. Descarga SSL también simplifica la configuración del servidor front-end y la administración de la aplicación web.

## <a name="before-you-begin"></a>Antes de empezar

1. Instalar la última versión de los cmdlets de PowerShell de Azure mediante el instalador de plataforma Web. Puede descargar e instalar la versión más reciente de la sección de **Windows PowerShell** de la [página de descargas](https://azure.microsoft.com/downloads/).
2. Compruebe que tiene una red virtual de trabajar con una subred válida. Asegúrese de que no hay máquinas virtuales o implementaciones de nube utilizan la subred. La puerta de enlace de aplicación debe ser por sí mismo en una subred de una red virtual.
3. Los servidores que configurar para que utilice la puerta de enlace de aplicación deben existir o han asignado sus extremos creados en una red virtual o con VIP o IP pública.

Para configurar la descarga SSL en una puerta de enlace de aplicación, siga los pasos siguientes en el orden indicado:

1. [Crear una puerta de enlace de aplicación](#create-an-application-gateway)
2. [Cargar certificados SSL](#upload-ssl-certificates)
3. [Configurar la puerta de enlace](#configure-the-gateway)
4. [Establecer la configuración de puerta de enlace](#set-the-gateway-configuration)
5. [Iniciar la puerta de enlace](#start-the-gateway)
6. [Comprobar el estado de la puerta de enlace](#verify-the-gateway-status)


## <a name="create-an-application-gateway"></a>Crear una puerta de enlace de aplicación

Para crear la puerta de enlace, use el cmdlet de **Nuevo AzureApplicationGateway** , reemplazando los valores por la suya propia. Facturación de la puerta de enlace no se inicia en este momento. Facturación comienza en un paso más adelante, cuando se inicia correctamente la puerta de enlace.

    New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

Para validar que la puerta de enlace se ha creado, puede usar el cmdlet **Get-AzureApplicationGateway** .

En el ejemplo, la *Descripción*, *InstanceCount*y *GatewaySize* son parámetros opcionales. El valor predeterminado de *InstanceCount* es 2, con un valor máximo de 10. El valor predeterminado de *GatewaySize* es medio. Pequeñas y grandes son otros valores disponibles. *VirtualIPs* y *DnsName* se muestran en blanco porque aún no ha iniciado la puerta de enlace. Estos valores se crean una vez que la puerta de enlace está en ejecución.

    Get-AzureApplicationGateway AppGwTest

## <a name="upload-ssl-certificates"></a>Cargar certificados SSL

Usar **Agregar AzureApplicationGatewaySslCertificate** para cargar el certificado de servidor en formato *pfx* en la puerta de enlace de aplicación. El nombre del certificado es un nombre de usuario seleccionado y debe ser único dentro de la puerta de enlace de aplicación. Este certificado se conoce con este nombre en todas las operaciones de administración de certificados de la puerta de enlace de aplicación.

Este ejemplo siguiente muestra el cmdlet, reemplace los valores de los datos de ejemplo con su propio.

    Add-AzureApplicationGatewaySslCertificate  -Name AppGwTest -CertificateName GWCert -Password <password> -CertificateFile <full path to pfx file>

A continuación, validar la carga de certificado. Usar el cmdlet **Get-AzureApplicationGatewayCertificate** .

El cmdlet se muestra en la primera línea, seguido de la salida.

    Get-AzureApplicationGatewaySslCertificate AppGwTest

    VERBOSE: 5:07:54 PM - Begin Operation: Get-AzureApplicationGatewaySslCertificate
    VERBOSE: 5:07:55 PM - Completed Operation: Get-AzureApplicationGatewaySslCertificate
    Name           : SslCert
    SubjectName    : CN=gwcert.app.test.contoso.com
    Thumbprint     : AF5ADD77E160A01A6......EE48D1A
    ThumbprintAlgo : sha1RSA
    State..........: Provisioned

>[AZURE.NOTE] La contraseña de certificado debe estar entre caracteres, letras o números de 4 a 12. No se aceptan caracteres especiales.

## <a name="configure-the-gateway"></a>Configurar la puerta de enlace

Una configuración de puerta de enlace de aplicación consta de varios valores. Los valores pueden estar vinculados entre sí para crear la configuración.

Los valores son:

- **Grupo de servidores de back-end:** La lista de direcciones IP de los servidores de back-end. Las direcciones IP enumeradas o bien deben pertenecer a la subred virtual o deben ser VIP o IP pública.
- **Configuración de la agrupación de back-end server:** Cada grupo tiene configuración como afinidad basada en cookies, protocolo y puerto. Estas opciones están vinculadas a un grupo y se aplican a todos los servidores dentro del grupo.
- **Puerto front-end:** Este puerto es el público que se abre en la puerta de enlace de aplicación. El tráfico llega a este puerto y, a continuación, se redirige a uno de los servidores de back-end.
- **Escucha:** La escucha tiene un puerto front-end, un protocolo (Http o Https, estos valores distinguen mayúsculas de minúsculas) y el nombre del certificado SSL (si se descarga la configuración de SSL).
- **Regla:** La regla enlaza la escucha y el grupo de servidor back-end y define el tráfico debe dirigirse a cuando llegue a una determinada escucha qué grupo de servidor back-end. Actualmente, solo la regla *básica* es compatible. La regla *básica* es la distribución de carga de turnos.

**Notas de configuración adicionales**

Configuración de certificados SSL, el protocolo en **HttpListener** debe cambiar a *Https* (distingue entre mayúsculas y minúsculas). El elemento **SslCert** se agrega a **HttpListener** con el valor establecido en el mismo nombre que se utiliza en la carga de la sección de certificados SSL anterior. El puerto front-end debe actualizarse a 443.

**Para habilitar la afinidad de cookie**: se pueden configurar una puerta de enlace de aplicaciones para asegurarse de que una solicitud de una sesión de cliente siempre se dirige a la misma VM en el conjunto de servidores web. Este escenario se realiza la inserción de una cookie de sesión que permite la puerta de enlace dirigir el tráfico adecuadamente. Para habilitar la afinidad basada en cookies, establezca **CookieBasedAffinity** en *habilitado* en el elemento **BackendHttpSettings** .



Puede crear su configuración mediante la creación de un objeto de configuración o mediante un archivo XML de configuración.
Para crear su configuración mediante un archivo XML de configuración, utilice el siguiente ejemplo:

**Ejemplo XML de configuración**

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
        <FrontendIPConfigurations />
        <FrontendPorts>
            <FrontendPort>
                <Name>FrontendPort1</Name>
                <Port>443</Port>
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
                <Protocol>Https</Protocol>
                <SslCert>GWCert</SslCert>
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


## <a name="set-the-gateway-configuration"></a>Establecer la configuración de puerta de enlace

A continuación, establezca la puerta de enlace de aplicación. Puede usar el cmdlet **Set-AzureApplicationGatewayConfig** con un objeto de configuración o con un archivo XML de configuración.

    Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml

## <a name="start-the-gateway"></a>Iniciar la puerta de enlace

Una vez que se ha configurado la puerta de enlace, use el cmdlet **AzureApplicationGateway de inicio** para iniciar la puerta de enlace. Facturación de una puerta de enlace de aplicación comienza después de la puerta de enlace se ha iniciado correctamente.


**Nota:** El cmdlet **Start AzureApplicationGateway** puede tardar hasta 15-20 minutos en Finalizar.

    Start-AzureApplicationGateway AppGwTest

## <a name="verify-the-gateway-status"></a>Comprobar el estado de la puerta de enlace

Use el cmdlet **Get-AzureApplicationGateway** para comprobar el estado de la puerta de enlace. Si **Inicio AzureApplicationGateway** correcta en el paso anterior, debe estar ejecutándose *estado* y *VirtualIPs* y *DnsName* deberían tener entradas válidas.

Este ejemplo muestra una puerta de enlace de aplicación que está activo y está preparado para tomar el tráfico.

    Get-AzureApplicationGateway AppGwTest

    Name          : AppGwTest2
    Description   :
    VnetName      : testvnet1
    Subnets       : {Subnet-1}
    InstanceCount : 2
    GatewaySize   : Medium
    State         : Running
    VirtualIPs    : {23.96.22.241}
    DnsName       : appgw-4c960426-d1e6-4aae-8670-81fd7a519a43.cloudapp.net


## <a name="next-steps"></a>Pasos siguientes


Si desea obtener más información sobre opciones de equilibrio de carga en general, vea:

- [Equilibrador de carga de Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Administrador de tráfico de Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)