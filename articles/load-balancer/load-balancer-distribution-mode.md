<properties
   pageTitle="Configurar el modo de distribución del equilibrador de carga | Microsoft Azure"
   description="Cómo configurar el modo de distribución de equilibrador de carga de Azure para admitir afinidad IP de origen"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="sewhee" />


# <a name="configure-the-distribution-mode-for-load-balancer"></a>Configurar el modo de distribución para equilibrador de carga

## <a name="hash-based-distribution-mode"></a>Modo de distribución basado en hash

El algoritmo de distribución predeterminado es una tupla de 5 (IP, puerto de origen de origen IP de destino, puerto de destino, el tipo de protocolo) hash para asignar el tráfico a servidores disponibles. Proporciona se pega únicamente dentro de una sesión de transporte. Paquetes en la misma sesión se dirigirá a la misma instancia de IP (DIP) del centro de datos tras el extremo de equilibrio de carga. Cuando inicia una nueva sesión en el cliente desde la misma IP de origen, el puerto de origen cambia y hace que el tráfico ir a un extremo DIP diferente.

![equilibrador de carga de hash basado](./media/load-balancer-distribution-mode/load-balancer-distribution.png)

Figura 1-5-tupla distribución

## <a name="source-ip-affinity-mode"></a>Modo de afinidad IP de origen

Tenemos otro modo de distribución que se denomina afinidad de IP de origen (también conocido como afinidad de la sesión o afinidad IP del cliente). Equilibrador de carga de Azure puede estar configurado para usar una tupla de 2 (dirección IP de origen, dirección IP de destino) o tupla de 3 (protocolo IP de origen, dirección IP de destino,) para asignar el tráfico a los servidores disponibles. Mediante la afinidad de IP de origen, conexiones iniciadas desde el mismo equipo cliente va al mismo extremo DIP.

En el diagrama siguiente muestra una configuración de tupla de 2. Observe la ejecución de la tupla de 2 a través del equilibrador de carga para máquina virtual 1 (VM1) que, a continuación, se copia VM2 y VM3.

![afinidad de la sesión](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

Ilustración 2: distribución de tupla de 2

Afinidad IP de origen resuelve una incompatibilidad entre el equilibrador de carga de Azure y la puerta de enlace de escritorio remoto (RD). Ahora puede crear una granja de puerta de enlace de escritorio remoto en un servicio de nube solo.

Otro escenario de caso de uso es la carga de medios donde la carga de datos se pasa a través de UDP, pero el plano de control se logra a través de TCP:

- Un cliente primero inicia una sesión TCP en la dirección pública de equilibrio de carga, obtiene dirige a un DIP específico, este canal permanece activo para supervisar el estado de conexión
- Una nueva sesión UDP desde el mismo equipo cliente se inicia en el mismo extremo pública de equilibrio de carga, las expectativas aquí es que esta conexión también se dirige al mismo extremo DIP como la conexión de TCP anterior para que cargue multimedia puede ejecutar en alto rendimiento conservando también un canal de control a través de TCP.

>[AZURE.NOTE] Cuando cambia un conjunto de equilibrio de carga (quitando o agregando una máquina virtual), la distribución de solicitudes de cliente se vuelve a calcular. No pueden depender nuevas conexiones de los clientes existentes terminando hacia arriba en el mismo servidor. Además, con la dirección IP de origen modo de distribución de afinidad puede causar una distribución desigual de tráfico. Los clientes que ejecutan con servidores proxy pueden verse como una aplicación de cliente únicos.

## <a name="configuring-source-ip-affinity-settings-for-load-balancer"></a>Establecer la configuración de afinidad de IP de origen para equilibrador de carga

Para máquinas virtuales de Windows, puede usar PowerShell para cambiar la configuración de tiempo de espera:

Agregar un extremo de Azure a una máquina Virtual y configurar el modo de distribución del equilibrador de carga

    Get-AzureVM -ServiceName mySvc -Name MyVM1 | Add-AzureEndpoint -Name HttpIn -Protocol TCP -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution sourceIP | Update-AzureVM

LoadBalancerDistribution se puede establecer en sourceIP tupla de 2 (dirección IP de origen, dirección IP de destino) equilibrio de carga, sourceIPProtocol de equilibrio de carga de tupla de 3 (protocolo IP de origen, dirección IP de destino,) o ninguno si desea que el comportamiento predeterminado de equilibrio de carga de tupla 5.

Para recuperar una configuración de modo extremo distribución de equilibrador de carga, utilice la siguiente:

    PS C:\> Get-AzureVM –ServiceName MyService –Name MyVM | Get-AzureEndpoint

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
    LoadBalancerDistribution : sourceIP

Si el elemento LoadBalancerDistribution no está presente el equilibrador de carga de Azure usa el algoritmo de tupla 5 predeterminado.

### <a name="set-the-distribution-mode-on-a-load-balanced-endpoint-set"></a>Establecer el modo de distribución en un conjunto de extremo de equilibrio de carga

Si extremos forman parte de un conjunto de extremo de equilibrio de carga, se debe establecer el modo de distribución en el conjunto de extremo de equilibrio de carga:

    Set-AzureLoadBalancedEndpoint -ServiceName MyService -LBSetName LBSet1 -Protocol TCP -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 –LoadBalancerDistribution sourceIP

### <a name="cloud-service-configuration-to-change-distribution-mode"></a>Configuración del servicio de nube para cambiar el modo de distribución.

Puede aprovechar el SDK de Azure para .NET 2.5 (que se lanzará en noviembre) para actualizar el servicio de nube. Configuración de extremo de los servicios de nube se realiza en la .csdef. Para actualizar el modo de distribución del equilibrador de carga para una implementación de servicios de nube, se requiere una actualización de la implementación.
Aquí tiene un ejemplo de cambios de .csdef de configuración de extremo:

    <WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
      <Endpoints>
        <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancerDistribution="sourceIP" />
      </Endpoints>
    </WorkerRole>
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

## <a name="api-example"></a>Ejemplo de la API

Puede configurar la distribución de equilibrador de carga con la API de administración de servicio. Asegúrese de agregar el `x-ms-version` encabezado se establece en versión `2014-09-01` o superior.

### <a name="update-the-configuration-of-the-specified-load-balanced-set-in-a-deployment"></a>Actualizar la configuración del conjunto de equilibrio de carga especificado en una implementación

#### <a name="request-example"></a>Ejemplo de una solicitud

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>?comp=UpdateLbSet    x-ms-version: 2014-09-01
    Content-Type: application/xml

    <LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
      <InputEndpoint>
        <LoadBalancedEndpointSetName> endpoint-set-name </LoadBalancedEndpointSetName>
        <LocalPort> local-port-number </LocalPort>
        <Port> external-port-number </Port>
        <LoadBalancerProbe>
          <Port> port-assigned-to-probe </Port>
          <Protocol> probe-protocol </Protocol>
          <IntervalInSeconds> interval-of-probe </IntervalInSeconds>
          <TimeoutInSeconds> timeout-for-probe </TimeoutInSeconds>
        </LoadBalancerProbe>
        <Protocol> endpoint-protocol </Protocol>
        <EnableDirectServerReturn> enable-direct-server-return </EnableDirectServerReturn>
        <IdleTimeoutInMinutes>idle-time-out</IdleTimeoutInMinutes>
        <LoadBalancerDistribution>sourceIP</LoadBalancerDistribution>
      </InputEndpoint>
    </LoadBalancedEndpointList>

El valor de LoadBalancerDistribution puede ser sourceIP para afinidad de tupla de 2, sourceIPProtocol para la afinidad de tupla de 3 o ninguna (sin afinidad. es decir, tupla 5)

#### <a name="response"></a>Respuesta

    HTTP/1.1 202 Accepted
    Cache-Control: no-cache
    Content-Length: 0
    Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0
    x-ms-servedbyregion: ussouth2
    x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af
    Date: Thu, 16 Oct 2014 22:49:21 GMT

## <a name="next-steps"></a>Pasos siguientes

[Información general del equilibrador de carga interno](load-balancer-internal-overview.md)

[Empezar a configurar una Internet opuestas equilibrador de carga](load-balancer-get-started-internet-arm-ps.md)

[Configurar los valores de tiempo de espera TCP inactivas para el equilibrador de carga](load-balancer-tcp-idle-timeout.md)
