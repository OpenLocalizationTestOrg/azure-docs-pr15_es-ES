<properties 
   pageTitle="Crear y configurar una puerta de enlace de la aplicación con equilibrador de carga interno (ILB) en una red Virtual | Microsoft Azure"
   description="Esta página contiene instrucciones para configurar una puerta de enlace de aplicación de Azure con un extremo interno equilibrio de carga"
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
   ms.date="08/19/2016"
   ms.author="gwallace"/>

# <a name="create-an-application-gateway-with-an-internal-load-balancer-ilb"></a>Crear una puerta de enlace de la aplicación con un equilibrador de carga interno (ILB)

> [AZURE.SELECTOR]
- [Pasos de clásicos de Azure](application-gateway-ilb.md)
- [Instrucciones de Powershell de administrador de recursos](application-gateway-ilb-arm.md)

Puerta de enlace de aplicación puede estar configurado con un opuestas IP virtual de internet o con extremo interno no expuesta en internet, también conocido como extremo de equilibrador de carga interno (ILB). Configuración de la puerta de enlace con un ILB es útil para las aplicaciones de línea de negocio internas no expuestas en internet. También es útil para los servicios y niveles en una aplicación de varios niveles, que se encuentra en un límite de seguridad no expuesto en internet, pero todavía necesita turnos distribución de la carga, se pega sesión o terminación SSL. En este artículo le guiará por los pasos para configurar una puerta de enlace de la aplicación con una ILB.

## <a name="before-you-begin"></a>Antes de empezar

1. Instalar la última versión de los cmdlets de PowerShell de Azure mediante el instalador de plataforma Web. Puede descargar e instalar la versión más reciente de la sección de **Windows PowerShell** de la [página de descarga](https://azure.microsoft.com/downloads/).
2. Compruebe que tiene una red virtual de trabajar con subred válida.
3. Compruebe que tiene servidores back-end de la red virtual o con una pública IP/VIP asignada.


Para crear una puerta de enlace de aplicación, realice los pasos siguientes en el orden indicado. 

1. [Crear una puerta de enlace de aplicación](#create-a-new-application-gateway)
2. [Configurar la puerta de enlace](#configure-the-gateway)
3. [Establecer la configuración de puerta de enlace](#set-the-gateway-configuration)
4. [Iniciar la puerta de enlace](#start-the-gateway)
4. [Comprobar la puerta de enlace](#verify-the-gateway-status)



## <a name="create-an-application-gateway"></a>Crear una puerta de enlace de aplicación:

**Para crear la puerta de enlace**, use la `New-AzureApplicationGateway` cmdlet, reemplazando los valores por la suya propia. Tenga en cuenta que la facturación de la puerta de enlace no se inicia en este momento. Facturación comienza en un paso más adelante, cuando se inicia correctamente la puerta de enlace.

    PS C:\> New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

    VERBOSE: 4:31:35 PM - Begin Operation: New-AzureApplicationGateway 
    VERBOSE: 4:32:37 PM - Completed Operation: New-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error 
    ----       ----------------     ------------                             ----
    Successful OK                   55ef0460-825d-2981-ad20-b9a8af41b399

**Para validar** que la puerta de enlace se ha creado, puede utilizar el `Get-AzureApplicationGateway` cmdlet. 

En el ejemplo, la *Descripción*, *InstanceCount*y *GatewaySize* son parámetros opcionales. El valor predeterminado de *InstanceCount* es 2, con un valor máximo de 10. El valor predeterminado de *GatewaySize* es medio. Pequeñas y grandes son otros valores disponibles. *VIP* y *DnsName* se muestran en blanco porque aún no ha iniciado la puerta de enlace. Se crean una vez que la puerta de enlace está en ejecución. 

    PS C:\> Get-AzureApplicationGateway AppGwTest

    VERBOSE: 4:39:39 PM - Begin Operation:
    Get-AzureApplicationGateway VERBOSE: 4:39:40 PM - Completed 
    Operation: Get-AzureApplicationGateway
    Name: AppGwTest 
    Description: 
    VnetName: testvnet1 
    Subnets: {Subnet-1} 
    InstanceCount: 2 
    GatewaySize: Medium 
    State: Stopped 
    VirtualIPs: 
    DnsName:


## <a name="configure-the-gateway"></a>Configurar la puerta de enlace

Una configuración de puerta de enlace de aplicación consta de varios valores. Los valores pueden estar vinculados entre sí para crear la configuración.
 
Los valores son:

- **Grupo de servidores de back-end:** La lista de direcciones IP de los servidores de back-end. Las direcciones IP enumeradas o bien deben pertenecer a la subred VNet o deben ser VIP o IP pública. 
- **Configuración de la agrupación de back-end server:** Cada grupo tiene configuración como afinidad basada en cookies, protocolo y puerto. Estas opciones están vinculadas a un grupo y se aplican a todos los servidores dentro del grupo.
- **Puerto front-end:** Este puerto es el público abierto en la puerta de enlace de aplicación. El tráfico llega a este puerto y, a continuación, se redirige a uno de los servidores de back-end.
- **Escucha:** La escucha tiene un puerto front-end, un protocolo (Http o Https, estos son distingue mayúsculas de minúsculas) y el nombre del certificado SSL (si se descarga la configuración de SSL). 
- **Regla:** La regla enlaza la escucha y el grupo del servidor back-end y define el tráfico debe dirigirse a cuando llegue a una determinada escucha qué grupo de servidores de back-end. Actualmente, solo la regla *básica* es compatible. La regla *básica* es la distribución de carga de turnos.

Puede crear su configuración mediante la creación de un objeto de configuración, o mediante un archivo XML de configuración. Para crear su configuración mediante un archivo XML de configuración, use el siguiente ejemplo.



Tenga en cuenta lo siguiente:


- El elemento *FrontendIPConfigurations* describe los detalles ILB relevantes para la configuración de puerta de enlace de aplicación con una ILB. 

- Se debe establecer el *tipo* de IP Frontend 'Private'

- La *StaticIPAddress* se debe establecer en el que quiera IP interno en el que la puerta de enlace recibe el tráfico. Tenga en cuenta que el elemento *StaticIPAddress* es opcional. Si no conjunto, que se elige una IP interna disponible desde la subred implementada. 

- El valor del *nombre del* elemento especificado en *FrontendIPConfiguration* debe usarse en elemento de *FrontendIP* de HTTPListener para hacer referencia a la FrontendIPConfiguration.

 **Ejemplo XML de configuración**

 

        <?xml version="1.0" encoding="utf-8"?>
        <ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
            <FrontendIPConfigurations>
                <FrontendIPConfiguration>
                    <Name>fip1</Name> 
                    <Type>Private</Type> 
                    <StaticIPAddress>10.0.0.10</StaticIPAddress> 
                </FrontendIPConfiguration>
            </FrontendIPConfigurations>
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
                    <FrontendIP>fip1</FrontendIP>
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
    


## <a name="set-the-gateway-configuration"></a>Establecer la configuración de puerta de enlace

A continuación, configure la puerta de enlace de aplicación. Puede usar el `Set-AzureApplicationGatewayConfig` cmdlet con un objeto de configuración, o con un archivo XML de configuración. 

    PS C:\> Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml

    VERBOSE: 7:54:59 PM - Begin Operation: Set-AzureApplicationGatewayConfig 
    VERBOSE: 7:55:32 PM - Completed Operation: Set-AzureApplicationGatewayConfig
    Name       HTTP Status Code     Operation ID                             Error 
    ----       ----------------     ------------                             ----
    Successful OK                   9b995a09-66fe-2944-8b67-9bb04fcccb9d

## <a name="start-the-gateway"></a>Iniciar la puerta de enlace

Una vez que se ha configurado la puerta de enlace, utilice la `Start-AzureApplicationGateway` cmdlet para iniciar la puerta de enlace. Facturación de una puerta de enlace de aplicación comienza después de la puerta de enlace se ha iniciado correctamente. 


> [AZURE.NOTE] La `Start-AzureApplicationGateway` cmdlet puede tardar hasta 15-20 minutos en completarse. 
   
    PS C:\> Start-AzureApplicationGateway AppGwTest 

    VERBOSE: 7:59:16 PM - Begin Operation: Start-AzureApplicationGateway 
    VERBOSE: 8:05:52 PM - Completed Operation: Start-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error 
    ----       ----------------     ------------                             ----
    Successful OK                   fc592db8-4c58-2c8e-9a1d-1c97880f0b9b

## <a name="verify-the-gateway-status"></a>Comprobar el estado de la puerta de enlace

Usar el `Get-AzureApplicationGateway` cmdlet para comprobar el estado de la puerta de enlace. Si *Inicio AzureApplicationGateway* correcta en el paso anterior, el estado debe ser *ejecutando*y la dirección Vip y DnsName deberían tener entradas válidas. El cmdlet se muestra en la primera línea, seguido de la salida. En este ejemplo, la puerta de enlace se está ejecutando y está preparado para tomar el tráfico. 

> [AZURE.NOTE] La puerta de enlace de la aplicación está configurado para que acepte tráfico en el extremo ILB configurado 10.0.0.10 en este ejemplo.

    PS C:\> Get-AzureApplicationGateway AppGwTest 

    VERBOSE: 8:09:28 PM - Begin Operation: Get-AzureApplicationGateway 
    VERBOSE: 8:09:30 PM - Completed Operation: Get-AzureApplicationGateway
    Name          : AppGwTest
    Description   : 
    VnetName      : testvnet1
    Subnets       : {Subnet-1}
    InstanceCount : 2
    GatewaySize   : Medium
    State         : Running
    VirtualIPs    : {10.0.0.10}
    DnsName       : appgw-b2a11563-2b3a-4172-a4aa-226ee4c23eed.cloudapp.net

## <a name="next-steps"></a>Pasos siguientes


Si desea obtener más información sobre opciones de equilibrio de carga en general, vea:

- [Equilibrador de carga de Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Administrador de tráfico de Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)
