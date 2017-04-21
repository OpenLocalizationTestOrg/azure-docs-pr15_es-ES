<properties
    pageTitle="Configurar una escucha externa para siempre en grupos de disponibilidad | Microsoft Azure"
    description="Este tutorial le guiará a través de los pasos para crear un siempre en disponibilidad grupo escucha en Azure que es accesible externamente mediante la dirección IP Virtual pública del servicio de nube asociado."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="MikeRayMSFT"
    manager="jhubbard"
    editor=""
    tags="azure-service-management" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="07/12/2016"
    ms.author="MikeRayMSFT" />

# <a name="configure-an-external-listener-for-always-on-availability-groups-in-azure"></a>Configurar una escucha externa para siempre en grupos de disponibilidad de Azure

> [AZURE.SELECTOR]
- [Escucha interno](virtual-machines-windows-classic-ps-sql-int-listener.md)
- [Escucha externa](virtual-machines-windows-classic-ps-sql-ext-listener.md)

En este tema se muestra cómo configurar una escucha un siempre en disponibilidad de grupo que es accesible externamente en internet. Esto se hace posible mediante la asociación de dirección de **IP Virtual público (VIP)** del servicio de nube con la escucha.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


El grupo de disponibilidad pueden contener réplicas que sean local solo, Azure, o span local y Azure configuraciones híbridas. Azure réplicas pueden encontrarse en la misma región o en diferentes regiones con varias redes virtuales (VNets). Los pasos siguientes se suponen que ya ha [configurado un grupo de disponibilidad](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md) , pero no ha configurado una escucha.

## <a name="guidelines-and-limitations-for-external-listeners"></a>Directrices y limitaciones para escucha externo

Tenga en cuenta las siguientes directrices sobre la escucha de grupo de disponibilidad en Azure al implementar usando la dirección VIP púbica del servicio de nube:

- La escucha de grupo de disponibilidad es compatible con Windows Server 2008 R2, Windows Server 2012 y Windows Server 2012 R2.

- La aplicación cliente debe residen en un servicio de nube diferente a la que contiene el grupo de disponibilidad máquinas virtuales. Azure no admite retorno de servidor directo con el servidor y cliente en el mismo servicio de nube.

- De forma predeterminada, los pasos de este artículo muestran cómo configurar una escucha para usar la dirección IP Virtual (VIP) de servicio de nube. Sin embargo, es posible reservar y crear varias direcciones VIP de su servicio de nube. Esto le permite usar los pasos de este artículo para crear varios procesos de escucha asociados a una dirección VIP diferente. Para obtener información sobre cómo crear varias direcciones VIP, vea [Varios VIP por cada servicio de nube](../load-balancer/load-balancer-multivip.md).

- Si va a crear una escucha para un entorno híbrido, la red local debe tener conexión a Internet pública además de la VPN de sitio a sitio con una red virtual Azure. Cuando se encuentra en la subred Azure, la escucha de grupo de disponibilidad es solo de llegar a la dirección IP pública del servicio de nube respectivos.

- No se admite para crear una escucha externa en el mismo servicio de nube que también tenga un detector interno usando el equilibrador de carga interno (ILB).

## <a name="determine-the-accessibility-of-the-listener"></a>Determinar la accesibilidad de la escucha

[AZURE.INCLUDE [ag-listener-accessibility](../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

En este artículo se centra en la creación de un agente de escucha que usa el **Equilibrio de carga externa**. Si desea que un agente de escucha es privado para su red virtual, consulte la versión de este artículo proporciona los pasos necesarios para configurar un [agente de escucha con ILB](virtual-machines-windows-classic-ps-sql-int-listener.md)

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Crear extremos de máquina virtual de equilibrio de carga con el servidor directo devuelto

Equilibrio de carga externo utiliza el virtual la Virtual dirección IP pública del servicio de nube que hospeda sus máquinas virtuales. Así que no necesita crear o configurar el equilibrador de carga en este caso.

[AZURE.INCLUDE [load-balanced-endpoints](../../includes/virtual-machines-ag-listener-load-balanced-endpoints.md)]

1. Copie la secuencia de comandos de PowerShell debajo en un editor de texto y establecer los valores de variables para su entorno (ha proporcionado valores predeterminados para algunos parámetros). Tenga en cuenta que si su grupo de disponibilidad abarca áreas de Azure, debe ejecutar la secuencia de comandos una vez en cada centro de datos para el servicio en la nube y los nodos que residen en ese centro de datos.

        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas

        # Configure a load balanced endpoint for each node in $AGNodes, with direct server return enabled
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -Protocol "TCP" -PublicPort 1433 -LocalPort 1433 -LBSetName "ListenerEndpointLB" -ProbePort 59999 -ProbeProtocol "TCP" -DirectServerReturn $true | Update-AzureVM
        }

1. Una vez que ha establecido las variables, copie la secuencia de comandos desde el editor de texto en la sesión de Azure PowerShell para ejecutarlo. Si el mensaje se siguen viendo >>, escriba ENTRAR otra vez para asegurarse de que la secuencia de comandos comienza a ejecutarse.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Compruebe que está instalado KB2854082 si es necesario

[AZURE.INCLUDE [kb2854082](../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Abra los puertos del firewall de nodos de grupo de disponibilidad

[AZURE.INCLUDE [firewall](../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>Crear la escucha de grupo de disponibilidad

[AZURE.INCLUDE [firewall](../../includes/virtual-machines-ag-listener-create-listener.md)]

1. Equilibrio de carga externo, debe obtener la dirección IP virtual pública del servicio de nube que contiene sus réplicas. Inicie sesión en el portal de clásico de Azure. Vaya al servicio de nube que contiene su grupo de disponibilidad VM. Abrir la vista de **paneles** .

3. Tenga en cuenta la dirección que aparece en la **dirección IP Virtual público (VIP)**. Si su solución abarca VNets, repita este paso para cada servicio de nube que contiene una máquina virtual que aloja una réplica.

4. En una de las máquinas virtuales, copie la secuencia de comandos de PowerShell debajo en un editor de texto y establecer las variables de los valores que se indicó anteriormente.

        # Define variables
        $ClusterNetworkName = "<ClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $CloudServiceIP = "<X.X.X.X>" # Public Virtual IP (VIP) address of your cloud service

        Import-Module FailoverClusters

        # If you are using Windows Server 2012 or higher, use the Get-Cluster Resource command. If you are using Windows Server 2008 R2, use the cluster res command. Both commands are commented out. Choose the one applicable to your environment and remove the # at the beginning of the line to convert the comment to an executable line of code.

        # Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$CloudServiceIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"OverrideAddressMatch"=1;"EnableDhcp"=0}
        # cluster res $IPResourceName /priv enabledhcp=0 overrideaddressmatch=1 address=$CloudServiceIP probeport=59999  subnetmask=255.255.255.255


1. Una vez han establecido las variables, abra una ventana de Windows PowerShell elevado, a continuación, copie la secuencia de comandos en el editor de texto y pegar en su sesión de Azure PowerShell para ejecutarlo. Si el mensaje se siguen viendo >>, escriba ENTRAR otra vez para asegurarse de que la secuencia de comandos comienza a ejecutarse.

1. Repita este procedimiento en cada máquina virtual. Esta secuencia de comandos configura el recurso Dirección IP con la dirección IP del servicio de nube y establece otros parámetros como el puerto de sondeo. Cuando se conecte el recurso de dirección IP, a continuación, puede responder a la exploración en el puerto de sondeo desde el extremo de equilibrio de carga que creó anteriormente en este tutorial.

## <a name="bring-the-listener-online"></a>Traer el oyente en línea

[AZURE.INCLUDE [Bring-Listener-Online](../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Elementos de seguimiento

[AZURE.INCLUDE [Follow-up](../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-vnet"></a>Probar la escucha de grupo de disponibilidad (dentro de la misma VNet)

[AZURE.INCLUDE [Test-Listener-Within-VNET](../../includes/virtual-machines-ag-listener-test.md)]

## <a name="test-the-availability-group-listener-over-the-internet"></a>Probar la escucha de grupo de disponibilidad (a través de internet)

Para acceder a la escucha desde fuera de la red virtual, debe utilizar Equilibrio de carga de públicos externo (descritas en este tema) en lugar de ILB, que es accesible únicamente dentro de la misma VNet. En la cadena de conexión, especifique el nombre de servicio de nube. Por ejemplo, si tiene un servicio de nube con el nombre *mycloudservice*, la instrucción de sqlcmd sería como sigue:

    sqlcmd -S "mycloudservice.cloudapp.net,<EndpointPort>" -d "<DatabaseName>" -U "<LoginId>" -P "<Password>"  -Q "select @@servername, db_name()" -l 15

A diferencia del ejemplo anterior, se debe utilizar autenticación de SQL, porque el llamador no puede utilizar autenticación de windows a través de internet. Para obtener más información, vea [siempre de en grupo de disponibilidad en Azure VM: escenarios de conectividad de cliente](http://blogs.msdn.com/b/sqlcat/archive/2014/02/03/alwayson-availability-group-in-windows-azure-vm-client-connectivity-scenarios.aspx). Cuando utiliza la autenticación de SQL, asegúrese de que crear el mismo inicio de sesión en ambas réplicas. Para obtener más información acerca de cómo solucionar inicios de sesión con grupos de disponibilidad, vea [cómo asignar inicios de sesión o usar contenido de usuario de la base de datos SQL para conectarse a otras réplicas y asignar a bases de datos de disponibilidad](http://blogs.msdn.com/b/alwaysonpro/archive/2014/02/19/how-to-map-logins-or-use-contained-sql-database-user-to-connect-to-other-replicas-and-map-to-availability-databases.aspx).

Si las réplicas siempre están en subredes diferentes, deben especificar clientes **MultisubnetFailover = True** en la cadena de conexión. El resultado de intentos de conexión paralelo a réplicas en las diferentes subredes. Tenga en cuenta que este escenario incluye una implementación de siempre en el grupo de disponibilidad entre región.

## <a name="next-steps"></a>Pasos siguientes

[AZURE.INCLUDE [Listener-Next-Steps](../../includes/virtual-machines-ag-listener-next-steps.md)]
