<properties
   pageTitle="Configurar el tiempo de espera de inactividad de TCP del equilibrador de carga | Microsoft Azure"
   description="Configurar el tiempo de espera de inactividad de TCP del equilibrador de carga"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor="" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="sewhee" />

# <a name="configure-tcp-idle-timeout-settings-for-azure-load-balancer"></a>Establecer la configuración de tiempo de espera de inactividad de TCP de equilibrador de carga de Azure

En su configuración predeterminada, equilibrador de carga de Azure tiene un valor de tiempo de espera inactivo de 4 minutos. Si un periodo de inactividad es mayor que el valor de tiempo de espera, no hay ninguna garantía de que se mantiene la sesión TCP o HTTP entre el cliente y el servicio de nube.

Cuando se cierra la conexión, la aplicación de cliente puede recibir el siguiente mensaje de error: "se ha terminado la conexión: se cerró una conexión que se esperaba que mantenerse activo por el servidor."

Una práctica habitual es usar un TCP de mantenimiento. Esta práctica mantiene la conexión activa durante un período más largo. Para obtener más información, vea estos [ejemplos de .NET](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx). Con habilitado de mantenimiento, se envían paquetes durante los períodos de inactividad en la conexión. Asegúrese de que los paquetes de mantenimiento que nunca se alcance el valor de tiempo de espera inactivo y la conexión se mantiene durante un período de tiempo.

Esta configuración funciona sólo las conexiones entrantes. Para evitar perder la conexión, debe configurar TCP de mantenimiento con un intervalo menor que el valor de tiempo de espera inactivo o aumente el valor de tiempo de espera inactivo. Para admitir estos escenarios, hemos agregado compatibilidad para un tiempo de espera de inactividad configurable. Ahora puede establecer una duración de 4 a 30 minutos.

TCP de mantenimiento funciona bien en escenarios donde la batería no es una restricción. No se recomienda para las aplicaciones móviles. Mediante un TCP de mantenimiento en una aplicación móvil puede consumir la batería del dispositivo con mayor rapidez.

![Tiempo de espera TCP](./media/load-balancer-tcp-idle-timeout/image1.png)

Las secciones siguientes describen cómo cambiar la configuración de tiempo de espera inactivo en máquinas virtuales y servicios de nube.

## <a name="configure-the-tcp-timeout-for-your-instance-level-public-ip-to-15-minutes"></a>Configurar el tiempo de espera TCP para su dirección IP público de nivel de instancia en 15 minutos

    Set-AzurePublicIP -PublicIPName webip -VM MyVM -IdleTimeoutInMinutes 15

`IdleTimeoutInMinutes`es opcional. Si no está configurada, el tiempo de espera predeterminado es 4 minutos. El intervalo de tiempo de espera aceptable es 4 a 30 minutos.

## <a name="set-the-idle-timeout-when-creating-an-azure-endpoint-on-a-virtual-machine"></a>Establecer el tiempo de espera inactivo al crear un extremo de Azure en una máquina virtual

Para cambiar la configuración de tiempo de espera de un extremo, use la siguiente:

    Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 -IdleTimeoutInMinutes 15| Update-AzureVM

Para recuperar la configuración de tiempo de espera inactivo, utilice el siguiente comando:

    PS C:\> Get-AzureVM -ServiceName "MyService" -Name "MyVM" | Get-AzureEndpoint
    VERBOSE: 6:43:50 PM - Completed Operation: Get Deployment
    LBSetName : MyLoadBalancedSet
    LocalPort : 80
    Name : HTTP
    Port : 80
    Protocol : tcp
    Vip : 65.52.xxx.xxx
    ProbePath :
    ProbePort : 80
    ProbeProtocol : tcp
    ProbeIntervalInSeconds : 15
    ProbeTimeoutInSeconds : 31
    EnableDirectServerReturn : False
    Acl : {}
    InternalLoadBalancerName :
    IdleTimeoutInMinutes : 15

## <a name="set-the-tcp-timeout-on-a-load-balanced-endpoint-set"></a>Establecer el tiempo de espera TCP en un conjunto de extremo de equilibrio de carga

Si los extremos son parte de un conjunto de extremo de equilibrio de carga, el tiempo de espera TCP se debe establecer en el conjunto de extremo de equilibrio de carga. Por ejemplo:

    Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 -IdleTimeoutInMinutes 15

## <a name="change-timeout-settings-for-cloud-services"></a>Cambiar la configuración de tiempo de espera de servicios de nube

Puede usar el SDK de Azure para actualizar el servicio de nube. Asegúrese de configuración de extremo de los servicios de nube en el archivo .csdef. Actualizar el tiempo de espera TCP para su implementación de un servicio de nube, requiere una actualización de la implementación. Una excepción es si el tiempo de espera TCP se especifica solo para una dirección IP pública. Configuración de IP pública se encuentra en el archivo .cscfg y actualizarlos a través de actualización y actualización de implementación.

Los cambios de .csdef configuración de extremo son:

    <WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
      <Endpoints>
        <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" idleTimeoutInMinutes="tcp-timeout" />
      </Endpoints>
    </WorkerRole>

Los cambios de .cscfg para la configuración de tiempo de espera de direcciones IP pública son:

    <NetworkConfiguration>
      <VirtualNetworkSite name="VNet"/>
      <AddressAssignments>
        <InstanceAddress roleName="VMRolePersisted">
        <PublicIPs>
          <PublicIP name="public-ip-name" idleTimeoutInMinutes="timeout-in-minutes"/>
        </PublicIPs>
        </InstanceAddress>
      </AddressAssignments>
    </NetworkConfiguration>

## <a name="rest-api-example"></a>Ejemplo de la API de REST

Puede configurar el tiempo de espera de inactividad de TCP mediante la API de administración de servicio. Asegúrese de que el `x-ms-version` encabezado se establece en versión `2014-06-01` o posterior. Actualizar la configuración de los extremos de entrada especificados equilibrio de carga en todos los equipos virtuales en una implementación.

### <a name="request"></a>Solicitar

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>

### <a name="response"></a>Respuesta

    <LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
      <InputEndpoint>
        <LoadBalancedEndpointSetName>endpoint-set-name</LoadBalancedEndpointSetName>
        <LocalPort>local-port-number</LocalPort>
        <Port>external-port-number</Port>
        <LoadBalancerProbe>
          <Path>path-of-probe</Path>
          <Port>port-assigned-to-probe</Port>
          <Protocol>probe-protocol</Protocol>
          <IntervalInSeconds>interval-of-probe</IntervalInSeconds>
          <TimeoutInSeconds>timeout-for-probe</TimeoutInSeconds>
        </LoadBalancerProbe>
        <LoadBalancerName>name-of-internal-loadbalancer</LoadBalancerName>
        <Protocol>endpoint-protocol</Protocol>
        <IdleTimeoutInMinutes>15</IdleTimeoutInMinutes>
        <EnableDirectServerReturn>enable-direct-server-return</EnableDirectServerReturn>
        <EndpointACL>
          <Rules>
            <Rule>
              <Order>priority-of-the-rule</Order>
              <Action>permit-rule</Action>
              <RemoteSubnet>subnet-of-the-rule</RemoteSubnet>
              <Description>description-of-the-rule</Description>
            </Rule>
          </Rules>
        </EndpointACL>
      </InputEndpoint>
    </LoadBalancedEndpointList>

## <a name="next-steps"></a>Pasos siguientes

[Información general del equilibrador de carga interno](load-balancer-internal-overview.md)

[Empezar a configurar un equilibrador de carga a través de Internet](load-balancer-get-started-internet-arm-ps.md)

[Configurar un modo de distribución del equilibrador de carga](load-balancer-distribution-mode.md)
