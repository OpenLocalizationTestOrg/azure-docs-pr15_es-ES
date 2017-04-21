<properties
   pageTitle="Crear un equilibrador de carga interno con PowerShell en el modelo de implementación clásica | Microsoft Azure"
   description="Aprenda a crear un equilibrador de carga interno con PowerShell en el modelo de implementación clásica"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/09/2016"
   ms.author="sewhee" />

# <a name="get-started-creating-an-internal-load-balancer-classic-using-powershell"></a>Empezar a crear un equilibrador de carga interno (clásico) con PowerShell

[AZURE.INCLUDE [load-balancer-get-started-ilb-classic-selectors-include.md](../../includes/load-balancer-get-started-ilb-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]Obtenga información sobre cómo llevar a [cabo estos pasos con el modelo de administrador de recursos](load-balancer-get-started-ilb-arm-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]


[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]


## <a name="create-an-internal-load-balancer-set-for-virtual-machines"></a>Crear un equilibrador de carga interno para máquinas virtuales

Para crear un conjunto de equilibrador de carga interno y los servidores que va a enviar el tráfico a él, tiene que hacer lo siguiente:

1. Crear una instancia de interno equilibrio de carga que será el extremo de tráfico entrante carga equilibrada en todos los servidores de un conjunto de equilibrio de carga.

1. Agregar extremos correspondiente a los equipos virtuales que va a recibir el tráfico entrante.

1. Configure los servidores que va a enviar el tráfico de carga equilibrada para enviar el tráfico a la dirección IP (VIP) virtual de la instancia de equilibrio de carga interno.


### <a name="step-1-create-an-internal-load-balancing-instance"></a>Paso 1: Crear una instancia de equilibrio de carga interno

Para un servicio de nube existente o un servicio de nube implementado en una red virtual regional, puede crear una instancia de equilibrio de carga interna con los siguientes comandos de Windows PowerShell:

    $svc="<Cloud Service Name>"
    $ilb="<Name of your ILB instance>"
    $subnet="<Name of the subnet within your virtual network>"
    $IP="<The IPv4 address to use on the subnet-optional>"

    Add-AzureInternalLoadBalancer -ServiceName $svc -InternalLoadBalancerName $ilb –SubnetName $subnet –StaticVNetIPAddress $IP


Tenga en cuenta que este uso del cmdlet de Windows PowerShell de [Agregar AzureEndpoint](https://msdn.microsoft.com/library/dn495300.aspx) utiliza el parámetro DefaultProbe establecido. Para obtener más información sobre los conjuntos de parámetro adicional, vea [Agregar AzureEndpoint](https://msdn.microsoft.com/library/dn495300.aspx).

### <a name="step-2-add-endpoints-to-the-internal-load-balancing-instance"></a>Paso 2: Agregar extremos a la instancia de equilibrio de carga interno

Aquí tenemos un ejemplo:

    $svc="mytestcloud"
    $vmname="DB1"
    $epname="TCP-1433-1433"
    $lbsetname="lbset"
    $prot="tcp"
    $locport=1433
    $pubport=1433
    $ilb="ilbset"
    Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -Lbset $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM


### <a name="step-3-configure-your-servers-to-send-their-traffic-to-the-new-internal-load-balancing-endpoint"></a>Paso 3: Configurar los servidores para enviar tráfico al nuevo extremo interno equilibrio de carga

Tiene que configurar los servidores cuyo tráfico va a ser de carga equilibrada para usar la nueva dirección IP (la dirección VIP) de la instancia de equilibrio de carga interno. Esta es la dirección en que está escuchando la instancia de equilibrio de carga interno. En la mayoría de los casos, necesitará acaba de agregar o modificar un registro DNS para la dirección VIP de la instancia de equilibrio de carga interno.

Si especifica la dirección IP durante la creación de la instancia de equilibrio de carga interno, ya dispone de la dirección VIP. En caso contrario, puede ver a la dirección VIP de los siguientes comandos:

    $svc="<Cloud Service Name>"
    Get-AzureService -ServiceName $svc | Get-AzureInternalLoadBalancer



Para usar estos comandos, rellene los valores y quitar la < y >. Aquí tenemos un ejemplo:

    $svc="mytestcloud"
    Get-AzureService -ServiceName $svc | Get-AzureInternalLoadBalancer


En la pantalla del comando Get-AzureInternalLoadBalancer, anote la dirección IP y realice los cambios necesarios en los servidores o los registros DNS para asegurarse de que el tráfico se envía a la dirección VIP.

>[AZURE.NOTE]La plataforma de Microsoft Azure, utiliza una dirección de IPv4 estática, públicamente enrutable para una gran variedad de escenarios de administración. La dirección IP es 168.63.129.16. Esta dirección IP no debería ser bloqueada por los servidores de seguridad, ya que puede provocar un comportamiento inesperado.
>Con respecto a Azure interno equilibrio de carga, se usa esta dirección IP mediante la supervisión de sondeos desde el equilibrador de carga para determinar el estado de mantenimiento de máquinas virtuales de un conjunto de equilibrio de carga. Si un grupo de seguridad de red se utiliza para restringir el tráfico a Azure máquinas virtuales de Windows en un conjunto de equilibrio de carga internamente o se aplica a una subred de red Virtual, asegúrese de que se agrega una regla de seguridad de la red para permitir el tráfico de 168.63.129.16.


## <a name="example-of-internal-load-balancing"></a>Ejemplo de equilibrio de carga interno

Para recorrer el proceso de fin a fin de crear un conjunto de equilibrio de carga para las dos configuraciones de ejemplo, vea las secciones siguientes.

### <a name="an-internet-facing-multi-tier-application"></a>Un opuestas, la aplicación de varios niveles de Internet

Desea proporcionar un servicio de base de datos de equilibrio de carga de un conjunto de servidores de web a través de Internet. Ambos conjuntos de servidores se hospedan en un servicio de nube de Azure único. Tráfico del servidor Web para el puerto TCP 1433 debe distribuirse entre las dos máquinas virtuales en el nivel de base de datos. Figura 1 muestra la configuración.

![Conjunto de equilibrio de carga interno para el nivel de base de datos](./media/load-balancer-internal-getstarted/IC736321.png)


La configuración consta de las siguientes acciones:

- El servicio de nube existente alojan los equipos virtuales se denomina mytestcloud.

- Los dos servidores de base de datos existente se denominan DB1, DB2.

- Servidores Web en el nivel de web conectan a los servidores de base de datos en el nivel de base de datos usando la dirección IP privada. Otra opción es usar su propio DNS de la red virtual y registrar manualmente un registro para el conjunto de equilibrador de carga interno.

Los siguientes comandos configurar una nueva instancia de equilibrio de carga interno denominada **ILBset** y agregar extremos a las máquinas virtuales correspondientes a los servidores de base de datos de dos:

    $svc="mytestcloud"
    $ilb="ilbset"
    Add-AzureInternalLoadBalancer -ServiceName $svc -InternalLoadBalancerName $ilb
    $prot="tcp"
    $locport=1433
    $pubport=1433
    $epname="TCP-1433-1433"
    $lbsetname="lbset"
    $vmname="DB1"
    Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -LbSetName $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM

    $epname="TCP-1433-1433-2"
    $vmname="DB2"
    Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -LbSetName $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM


## <a name="remove-an-internal-load-balancing-configuration"></a>Quitar una configuración de equilibrio de carga interno

Para quitar una máquina virtual como un punto final de una instancia de equilibrador de carga interno, use los comandos siguientes:

    $svc="<Cloud service name>"
    $vmname="<Name of the VM>"
    $epname="<Name of the endpoint>"
    Get-AzureVM -ServiceName $svc -Name $vmname | Remove-AzureEndpoint -Name $epname | Update-AzureVM

Para usar estos comandos, rellene los valores, quitando el < y >.

Aquí tenemos un ejemplo:

    $svc="mytestcloud"
    $vmname="DB1"
    $epname="TCP-1433-1433"
    Get-AzureVM -ServiceName $svc -Name $vmname | Remove-AzureEndpoint -Name $epname | Update-AzureVM

Para quitar una instancia de equilibrador de carga interno de un servicio de nube, use los comandos siguientes:

    $svc="<Cloud service name>"
    Remove-AzureInternalLoadBalancer -ServiceName $svc

Para usar estos comandos, escriba el valor y quitar la < y >.

Aquí tenemos un ejemplo:

    $svc="mytestcloud"
    Remove-AzureInternalLoadBalancer -ServiceName $svc



## <a name="additional-information-about-internal-load-balancer-cmdlets"></a>Información adicional sobre los cmdlets del equilibrador de carga interno


Para obtener información adicional sobre los cmdlets de equilibrio de carga interno, ejecute los comandos siguientes en un símbolo del sistema de Windows PowerShell:

- Obtener ayuda para el nuevo AzureInternalLoadBalancerConfig-completo

- Get-help AzureInternalLoadBalancer agregar-completo

- Obtener ayuda Get-AzureInternalLoadbalancer-completo

- Get-help quitar AzureInternalLoadBalancer-completo

## <a name="next-steps"></a>Pasos siguientes

[Configurar un modo de distribución del equilibrador de carga mediante la afinidad IP de origen](load-balancer-distribution-mode.md)

[Configurar los valores de tiempo de espera TCP inactivas para el equilibrador de carga](load-balancer-tcp-idle-timeout.md)