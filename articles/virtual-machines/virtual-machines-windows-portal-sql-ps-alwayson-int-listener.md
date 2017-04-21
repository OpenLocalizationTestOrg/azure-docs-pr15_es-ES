<properties
   pageTitle="Configurar siempre en grupo disponibilidad escucha: Microsoft Azure"
   description="Configurar agentes de escucha de disponibilidad de grupo en el modelo de administrador de recursos de Azure, con un equilibrador de carga interno con una o más direcciones IP."
   services="virtual-machines"
   documentationCenter="na"
   authors="MikeRayMSFT"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows-sql-server"
   ms.workload="infrastructure-services"
   ms.date="10/20/2016"
   ms.author="MikeRayMSFT"/>

# <a name="configure-one-or-more-always-on-availability-group-listeners---resource-manager"></a>Configurar uno o más siempre en disponibilidad grupo escucha - Administrador de recursos 

En este tema se muestra cómo hacer dos cosas:

- Crear un equilibrador de carga interno para grupos de disponibilidad de SQL Server mediante los cmdlets de PowerShell.

- Agregar direcciones IP adicionales a un equilibrador de carga para admitir más de un grupo de disponibilidad de SQL Server. 

En SQL Server un detector de grupo de disponibilidad es un nombre de red virtual que se conectan los clientes a fin de obtener acceso a una base de datos de la réplica primaria o secundaria. En máquinas virtuales de Azure, un equilibrador de carga contiene la dirección IP para la escucha. El equilibrador de carga enruta el tráfico a la instancia de SQL Server que está escuchando en el puerto de sondeo. En la mayoría de los casos, un grupo de disponibilidad utiliza un equilibrador de carga interno. Un equilibrador de carga interno Azure puede hospedar una o varias direcciones IP. Cada dirección IP utiliza un puerto de sondeo específicos. Este documento muestra cómo usar PowerShell para crear un nuevo equilibrador de carga, o agregue direcciones IP a un equilibrador de carga existente para grupos de disponibilidad de SQL Server. 

La capacidad de asignar varias direcciones IP a un equilibrador de carga interno es nueva en Azure y solo está disponible en el modelo de administrador de recursos. Para completar esta tarea, debe tener un grupo de disponibilidad de SQL Server implementado en máquinas virtuales de Azure en el modelo de administrador de recursos. Ambas máquinas virtuales de SQL Server debe pertenecer al mismo conjunto de disponibilidad. Puede usar la [plantilla de Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) para crear automáticamente el grupo de disponibilidad en el Administrador de recursos de Azure. Esta plantilla crea automáticamente el grupo de disponibilidad, incluidos el equilibrador de carga interno para usted. Si lo prefiere, puede [configurar manualmente un grupo de disponibilidad AlwaysOn](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

En este tema se requiere que los grupos de disponibilidad ya están configurados.  

Temas relacionados se incluyen:

- [Configurar grupos de disponibilidad AlwaysOn en Azure VM (gráfico)](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)   

- [Configurar una conexión de VNet a VNet mediante PowerShell y el Administrador de recursos de Azure](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-vm-powershell.md)]

## <a name="configure-the-windows-firewall"></a>Configurar el Firewall de Windows

Configurar el Firewall de Windows para permitir el acceso de SQL Server. Debe configurar el firewall para permitir las conexiones de TCP para el uso de puertos por la instancia de SQL Server, así como el puerto usado por el sondeo de escucha. Para obtener instrucciones detalladas, consulte [configurar un Firewall de Windows para obtener acceso al motor de base de datos](http://msdn.microsoft.com/library/ms175043.aspx#Anchor_1). Crear una regla de entrada para el puerto de SQL Server y para el puerto de sondeo.

## <a name="example-script-create-an-internal-load-balancer-with-powershell"></a>Ejemplo Script: Crear un equilibrador de carga interno con PowerShell

> [AZURE.NOTE] Si ha creado su grupo de disponibilidad con la [plantilla de Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) la carga no es necesario completar este paso. 

El siguiente script de PowerShell crea un equilibrador de carga interno, configura las reglas de equilibrio de carga y establece una dirección IP para el equilibrador de carga. Para ejecutar la secuencia de comandos, abra Windows PowerShell ISE y pegue el script en el panel Script. Usar `Login-AzureRMAccount` para iniciar sesión en PowerShell. Si tiene varias suscripciones de Azure, use `Select-AzureRmSubscription ` para establecer la suscripción. 

```powershell
# Login-AzureRmAccount
# Select-AzureRmSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<Resource Group Name>" # Resource group name
$VNetName = "<Virtual Network Name>"         # Virtual network name
$SubnetName = "<Subnet Name>"                # Subnet name
$ILBName = "<Load Balancer Name>"            # ILB name
$Location = "<Azure Region>"                 # Azure location
$VMNames = "<VM1>","<VM2>"                   # Virtual machine names

$ILBIP = "<n.n.n.n>"                         # IP address
[int]$ListenerPort = "<nnnn>"                # AG listener port
[int]$ProbePort = "<nnnn>"                   # Probe port

$LBProbeName ="ILBPROBE_$ListenerPort"       # The Load balancer Probe Object Name              
$LBConfigRuleName = "ILBCR_$ListenerPort"    # The Load Balancer Rule Object Name

$FrontEndConfigurationName = "FE_SQLAGILB_1" # Object name for the Front End configuration 
$BackEndConfigurationName ="BE_SQLAGILB_1"   # Object name for the Back End configuration

$VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 

$Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName 

$FEConfig = New-AzureRMLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.id

$BEConfig = New-AzureRMLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName 

$SQLHealthProbe = New-AzureRmLoadBalancerProbeConfig -Name $LBProbeName -Protocol tcp -Port $ProbePort -IntervalInSeconds 15 -ProbeCount 2

$ILBRule = New-AzureRmLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP 

$ILB= New-AzureRmLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe 

$bepool = Get-AzureRmLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB 

foreach($VMName in $VMNames)
    {
        $VM = Get-AzureRmVM -ResourceGroupName $ResourceGroupName -Name $VMName 
        $NICName = ($VM.NetworkInterfaceIDs[0].Split('/') | select -last 1)
        $NIC = Get-AzureRmNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools = $BEPool
        Set-AzureRmNetworkInterface -NetworkInterface $NIC
        start-AzureRmVM -ResourceGroupName $ResourceGroupName -Name $VM.Name 
    }
```

## <a name="example-script-add-an-ip-address-to-an-existing-load-balancer-with-powershell"></a>Secuencia de comandos de ejemplo: agregar una dirección IP a un equilibrador de carga existente con PowerShell

Para usar más de un grupo de disponibilidad, usar PowerShell para agregar una dirección IP adicional a un equilibrador de carga existente. Cada dirección IP requiere su propia regla, el puerto de sondeo y puerto frontal de equilibrio de carga.

El puerto front-end es el que las aplicaciones que se utilizan para conectarse a la instancia de SQL Server. Direcciones IP para grupos de disponibilidad diferentes pueden utilizar el mismo puerto front-end.

>[AZURE.NOTE] Para los grupos de disponibilidad de SQL Server, cada dirección IP requiere un puerto de sondeo específicos. Por ejemplo, si una dirección IP en un equilibrador de carga, utiliza el puerto de sondeo 59999, no hay otras direcciones IP en ese equilibrador de carga pueden usar el puerto de sondeo 59999. 

- Para obtener información acerca del equilibrador de carga límites vea **privada frente finalizar IP por equilibrador de carga** en [Límites de redes - Administrador de recursos de Azure](../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits).

- Para obtener información sobre la disponibilidad de los límites de grupo vea [restricciones (grupos de disponibilidad)](http://msdn.microsoft.com/library/ff878487.aspx#RestrictionsAG).

La siguiente secuencia de comandos agrega una nueva dirección IP a un equilibrador de carga existente. Actualice las variables para su entorno. La ILB utiliza el puerto de escucha para el puerto de front-end de equilibrio de carga. Este puerto puede ser el puerto que está escuchando SQL Server. Para instancias predeterminadas de SQL Server, éste es el puerto 1433. La regla para un grupo de disponibilidad equilibrio de carga requiere una dirección IP flotante (servidor directo devuelto) para que el puerto de back-end es el mismo que el puerto front-end.

```powershell
# Login-AzureRmAccount
# Select-AzureRmSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<ResourceGroup>"          # Resource group name
$VNetName = "<VirtualNetwork>"                  # Virtual network name
$SubnetName = "<Subnet>"                        # Subnet name
$ILBName = "<ILBName>"                          # ILB name                      

$ILBIP = "<n.n.n.n>"                            # IP address
[int]$ListenerPort = "<nnnn>"                   # AG listener port
[int]$ProbePort = "<nnnnn>"                     # Probe port 

$ILB = Get-AzureRmLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName 

$count = $ILB.FrontendIpConfigurations.Count+1
$FrontEndConfigurationName ="FE_SQLAGILB_$count"  

$LBProbeName = "ILBPROBE_$count"
$LBConfigrulename = "ILBCR_$count"

$VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 
$Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

$ILB | Add-AzureRmLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id 

$ILB | Add-AzureRmLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 15  | Set-AzureRmLoadBalancer 

$ILB = Get-AzureRmLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

$FEConfig = get-AzureRMLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB

$SQLHealthProbe  = Get-AzureRmLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

$BEConfig = Get-AzureRmLoadBalancerBackendAddressPoolConfig -Name $ILB.BackendAddressPools[0].Name -LoadBalancer $ILB 

$ILB | Add-AzureRmLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort  $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP | Set-AzureRmLoadBalancer   
```



## <a name="configure-the-cluster-to-use-the-load-balancer-ip-address"></a>Configurar el clúster para usar la dirección IP del equilibrador de carga 

El siguiente paso es configurar la escucha en el clúster y a continuación, visualice el oyente en línea. Para ello, haga lo siguiente: 

1. Crear la escucha de grupo de disponibilidad en el clúster  

2. Traer el oyente en línea

## <a name="1-create-the-availability-group-listener-on-the-failover-cluster"></a>1. crear la escucha de grupo de disponibilidad en el clúster

En este paso, agregue un punto de acceso de cliente en el clúster con el Administrador de clúster de migración tras error y, a continuación, usar PowerShell para configurar el recurso de clúster para escuchar en el puerto de sondeo. 

1. Usar RDP para conectarse a la máquina virtual Azure que aloja la réplica principal. 

2. Abra el Administrador de clúster de migración tras error.

3. Seleccione el nodo de **redes** y anote el nombre de red de clúster. Usar este nombre en la `$ClusterNetworkName` variable en la secuencia de comandos de PowerShell.

4. Expanda el nombre del clúster y, a continuación, haga clic en **Roles**.

5. En el panel de **Roles** , haga clic en el nombre del grupo de disponibilidad y, a continuación, seleccione **Agregar recurso** > **Punto de acceso de cliente**.

6. En el cuadro **nombre** , crear un nombre para esta escucha de nuevo, a continuación, haga doble clic en **siguiente** y, a continuación, haga clic en **Finalizar**. No poner el oyente o recursos en línea en este momento.
 
    El nombre de la nueva escucha es el nombre de red que aplicaciones usará para conectarse a bases de datos en el grupo de disponibilidad de SQL Server.

7. Haga clic en la pestaña **recursos** , expanda el punto de acceso de cliente que acaba de crear. Haga clic en el recurso de IP y haga clic en Propiedades. Anote el nombre de la dirección IP. Usará este nombre en la `$IPResourceName` variable en la secuencia de comandos de PowerShell.

8. En **Dirección IP** haga clic en **Dirección IP estática** y establezca la dirección IP estática en la misma dirección que utilizó cuando se establece la dirección IP del equilibrador de carga en el portal de Azure. 

9. Deshabilitar NetBIOS para esta dirección y haga clic en **Aceptar**. Si su solución abarca varios VNets de Azure, repita este paso para cada recurso IP. 

10. Haga que el recurso de grupo de disponibilidad de SQL Server depende de la dirección IP. Haga clic derecho en el recurso en el Administrador de clúster, esto es en la pestaña **recursos** en **Otros recursos**. 

11. En el nodo que aloja actualmente la réplica principal, abra un PowerShell ISE elevados y pegue los siguientes comandos en una secuencia de comandos nuevo. En la ficha **dependencias** , haga clic en el nombre de la escucha.
 
    ```PowerShell
    $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
    $IPResourceName = "<IPResourceName>" # the IP Address resource name
    $ILBIP = “<n.n.n.n>” # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
    [int]$ProbePort = <nnnnn>

    Import-Module FailoverClusters
    
    Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
    ```
 
6. Actualice las variables y ejecute la secuencia de comandos de PowerShell para configurar la dirección IP y el puerto para la nueva escucha.

 >[AZURE.NOTE] Si los servidores de SQL en áreas independientes, debe ejecutar la secuencia de comandos de PowerShell dos veces. La primera vez que use el nombre de red clúster, nombre de recurso IP de clúster y la dirección IP del equilibrador de carga del primer grupo de recursos. La segunda vez que use el nombre de red clúster, nombre de recurso IP de clúster y la dirección IP del equilibrador de carga del segundo grupo de recursos.

Ahora el clúster tiene un recurso de escucha del grupo de disponibilidad.

## <a name="2-bring-the-listener-online"></a>2. Traer el oyente en línea

Con el recurso de escucha disponibilidad grupo configurado, puede poner en línea la escucha para que las aplicaciones pueden conectar a bases de datos en el grupo de disponibilidad con la escucha.

1. Desplácese atrás hasta migración tras error clúster administrador. Expanda **Roles** y, a continuación, resalte el grupo de disponibilidad. En la pestaña **recursos** , haga clic en el nombre de la escucha y haga clic en **Propiedades**.

1. Haga clic en la ficha **dependencias** . Si hay varios recursos que se muestran, compruebe que las direcciones IP que tienen o no AND, dependencias. Haga clic en **Aceptar**.

1. Haga clic en el nombre de la escucha y haga clic en **Conectar**.

1. Una vez que el oyente está en línea, en la ficha **recursos** , haga clic en el grupo de disponibilidad y haga clic en **Propiedades**.

1. Crear una dependencia en el recurso de nombre de escucha (no nombre de los recursos de direcciones IP). Haga clic en **Aceptar**.

1. Inicie SQL Server Management Studio y conectar con la réplica principal.

1. Vaya a **AlwaysOn alta disponibilidad** | **grupos de disponibilidad** | **escucha de disponibilidad de grupo**. 

1. Ahora debe ver el nombre de escucha que creó en el Administrador de clúster de migración tras error. Haga clic en el nombre de la escucha y haga clic en **Propiedades**.

1. En el cuadro **puerto** , especifique el número de puerto para la escucha de grupo de disponibilidad mediante el uso de la $EndpointPort que usa versiones anteriores (1433 era el valor predeterminado), a continuación, haga clic en **Aceptar**.

Ahora tiene un grupo de disponibilidad de SQL Server en máquinas virtuales de Azure ejecuta en modo de administrador de recursos. 

## <a name="test-the-connection-to-the-listener"></a>Probar la conexión a la escucha

Para probar la conexión:

1. RDP a SQL Server que está en la misma red virtual, pero no posee la réplica. Esto puede ser el otro servidor de SQL en el clúster.

2. Use la utilidad **sqlcmd** para probar la conexión. Por ejemplo, la siguiente secuencia de comandos establece una conexión de **sqlcmd** con la réplica principal a través de la escucha con autenticación de Windows:

    ```
    sqlmd -S <listenerName> -E
    ```

    Si la escucha está usando un puerto distinto del predeterminado (1433) el puerto, especifique el puerto en la cadena de conexión. Por ejemplo, el siguiente comando sqlcmd se conecta a una escucha en el puerto 1435: 
    
    ```
    sqlcmd -S <listenerName>,1435 -E
    ```

La conexión de SQLCMD se conectará automáticamente a la instancia de SQL Server aloja la réplica principal. 

>[AZURE.NOTE] Asegúrese de que el puerto que especifique está abierto en el firewall de ambos servidores SQL Server. Ambos servidores requieren una regla de entrada para el puerto TCP que usa. Para obtener más información, vea [Agregar o editar reglas de Firewall](http://technet.microsoft.com/library/cc753558.aspx) . 

## <a name="guidelines-and-limitations"></a>Directrices y limitaciones

Tenga en cuenta las siguientes directrices en escucha de grupo de disponibilidad en Azure con interno equilibrador de carga:

- Con un equilibrador de carga interno que solo tener acceso a la escucha desde dentro de la misma red virtual.

## <a name="for-more-information"></a>Para obtener más información

Para obtener más información, vea [Configurar siempre disponibilidad agrupar en Azure VM manualmente](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

### <a name="powershell-cmdlets"></a>Cmdlets de PowerShell

Use los siguientes cmdlets de PowerShell para crear un equilibrador de carga interno para máquinas virtuales de Windows Azure.

- `New-AzureRmLoadBalancer`crea un equilibrador de carga. Para obtener más información, consulte [AzureRmLoadBalancer de nuevo](http://msdn.microsoft.com/library/mt619450.aspx) . 

- `New-AzureRMLoadBalancerFrontendIpConfig`crea una configuración de IP front-end para un equilibrador de carga. Para obtener más información, consulte [AzureRMLoadBalancerFrontendIpConfig de nuevo](http://msdn.microsoft.com/library/mt603510.aspx) .

- `New-AzureRmLoadBalancerRuleConfig`crea una configuración de regla equilibradores de carga. Para obtener más información, consulte [AzureRmLoadBalancerRuleConfig de nuevo](http://msdn.microsoft.com/library/mt619391.aspx) . 

- `New-AzureRMLoadBalancerBackendAddressPoolConfig`crea una configuración de grupo de la dirección de back-end para un equilibrador de carga. Para obtener más información, consulte [AzureRmLoadBalancerBackendAddressPoolConfig de nuevo](http://msdn.microsoft.com/library/mt603791.aspx) . 

- `New-AzureRmLoadBalancerProbeConfig`crea una configuración de sondeo equilibradores de carga. Para obtener más información, consulte [AzureRmLoadBalancerProbeConfig de nuevo](http://msdn.microsoft.com/library/mt603847.aspx) .

Si necesita quitar un equilibrador de carga de un grupo de recursos de Azure, use `Remove-AzureRmLoadBalancer`. Para obtener más información, vea [Quitar AzureRmLoadBalancer](http://msdn.microsoft.com/library/mt603862.aspx).
