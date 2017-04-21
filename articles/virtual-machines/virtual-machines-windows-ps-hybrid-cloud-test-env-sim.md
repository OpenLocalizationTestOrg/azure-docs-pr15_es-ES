<properties 
    pageTitle="Entorno de prueba de nube híbrida simulada | Microsoft Azure" 
    description="Crear un entorno de nube híbrida simulada para TI pro o pruebas de desarrollo, con dos redes virtuales Azure y una conexión de VNet a VNet." 
    services="virtual-machines-windows" 
    documentationCenter="" 
    authors="JoeDavies-MSFT" 
    manager="timlt" 
    editor=""
    tags="azure-resource-manager"/>

<tags 
    ms.service="virtual-machines-windows" 
    ms.workload="infrastructure-services" 
    ms.tgt_pltfrm="vm-windows" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/30/2016" 
    ms.author="josephd"/>

# <a name="set-up-a-simulated-hybrid-cloud-environment-for-testing"></a>Configurar un entorno de nube híbrida simulada para realizar pruebas

En este artículo le guiará en el proceso de creación de un entorno de nube híbrida simulado con Microsoft Azure con dos redes virtuales Azure. Aquí tiene la configuración resultante.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph4.png)

Esto simula un entorno de producción de nube híbrida y consta de:

- Una red simulada y simplificada local hospedada en una red virtual Azure (la red virtual de práctica de prueba).
- Una red virtual local entre simulada hospedada en Azure (TestVNET).
- Una conexión de VNet a VNet entre las dos redes virtuales.
- Controlador de dominio secundario de la red virtual TestVNET.

Esto proporciona que un base partida común punto y que se puede:

- Desarrollar y probar aplicaciones en un entorno de nube híbrida simulada.
- Crear configuraciones de prueba de equipos, algunos dentro de la red virtual de práctica de prueba y otros dentro de la red virtual TestVNET para simular cargas de trabajo en la nube de TI de híbrido.

Hay cuatro fases principales para configurar este entorno híbrido de prueba de nube:

1.  Configurar la red virtual de práctica de prueba.
2.  Crear la red virtual entre local.
3.  Crear la conexión VPN VNet a VNet.
4.  Configurar DC2. 

Esta configuración requiere una suscripción de Azure. Si tiene una suscripción a MSDN o Visual Studio, vea [crédito Azure mensuales para los suscriptores de Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

>[AZURE.NOTE] Máquinas virtuales y puertas de enlace de red virtual en Azure provocar un costo monetario en curso cuando se están ejecutando. Este costo había facturado contra su MSDN o suscripción de pago. Una puerta de enlace VPN de Azure se implementa como un conjunto de dos máquinas virtuales de Azure. Para minimizar los costos, cree el entorno de prueba y realizar las pruebas y demostración necesarios lo antes posible.

## <a name="phase-1-configure-the-testlab-virtual-network"></a>Fase 1: Configurar la red virtual de práctica de prueba

Use las instrucciones en el tema de la [configuración básica de entorno de prueba](https://technet.microsoft.com/library/mt771177.aspx) para configurar los equipos DC1, APP1 y CLIENTE1 en la red virtual Azure denominada práctica de prueba. 

A continuación, inicie un símbolo del sistema de PowerShell de Azure.

> [AZURE.NOTE] El siguiente comando establece usar PowerShell Azure 1.0 y posterior.

Inicie sesión en su cuenta.

    Login-AzureRMAccount

Obtener el nombre de suscripción mediante el siguiente comando.

    Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName

Configurar la suscripción de Azure. Use la misma suscripción que usó para crear la configuración básica en la fase 1. Reemplazar todo el contenido de las comillas, incluyendo la < y > caracteres, con el nombre correcto.

    $subscr="<subscription name>"
    Get-AzureRmSubscription –SubscriptionName $subscr | Select-AzureRmSubscription

A continuación, agregue una subred de puerta de enlace a la red virtual de práctica de prueba de la configuración básica, que se usará para hospedar la puerta de enlace de Azure.

    $rgName="<name of your resource group that you used for your TestLab virtual network>"
    $locName="<Azure location name where you placed the TestLab virtual network, such as West US>"
    $vnet=Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name TestLab
    Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix 10.255.255.248/29 -VirtualNetwork $vnet
    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

A continuación, solicitar una dirección IP pública para asignar a la puerta de enlace de la red virtual de práctica de prueba.

    $gwpip=New-AzureRmPublicIpAddress -Name TestLab_pip -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic

A continuación, cree la puerta de enlace.

    $vnet=Get-AzureRmVirtualNetwork -Name TestLab -ResourceGroupName $rgName
    $subnet=Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
    $gwipconfig=New-AzureRmVirtualNetworkGatewayIpConfig -Name TestLab_GWConfig -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 
    New-AzureRmVirtualNetworkGateway -Name TestLab_GW -ResourceGroupName $rgName -Location $locName -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

Tenga en cuenta que pueden realizar 20 minutos o más para crear las puertas de enlace nuevos.

Desde el portal de Azure en el equipo local, conéctese a DC1 con las credenciales CORP\User1. Para configurar el dominio CORP para que los usuarios y equipos de usan el controlador de dominio local para la autenticación, ejecute estos comandos desde un símbolo de Windows PowerShell de nivel de administrador en DC1.

    New-ADReplicationSite -Name "TestLab" 
    New-ADReplicationSite -Name "TestVNET"
    New-ADReplicationSubnet -Name "10.0.0.0/8" -Site "TestLab"
    New-ADReplicationSubnet -Name "192.168.0.0/16" -Site "TestVNET"

Esta es la configuración actual.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph1.png)
 
## <a name="phase-2-create-the-testvnet-virtual-network"></a>Fase 2: Crear la red virtual TestVNET

Primero, cree la red virtual de TestVNET y proteger con un grupo de seguridad de la red.

    $rgName="<name of the resource group that you used for your TestLab virtual network>"
    $locName="<Azure location name where you placed the TestLab virtual network, such as West US>"
    $locShortName="<Azure location name from $locName in all lowercase letters with spaces removed. Example:  westus>"
    $testSubnet=New-AzureRMVirtualNetworkSubnetConfig -Name "TestSubnet" -AddressPrefix 192.168.0.0/24
    $gatewaySubnet=New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix 192.168.255.248/29
    New-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName -Location $locName -AddressPrefix 192.168.0.0/16 -Subnet $testSubnet,$gatewaySubnet –DNSServer 10.0.0.4
    $rule1=New-AzureRMNetworkSecurityRuleConfig -Name "RDPTraffic" -Description "Allow RDP to all VMs on the subnet" -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 -SourceAddressPrefix Internet -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389
    New-AzureRMNetworkSecurityGroup -Name "TestSubnet" -ResourceGroupName $rgName -Location $locShortName -SecurityRules $rule1
    $vnet=Get-AzureRMVirtualNetwork -ResourceGroupName $rgName -Name TestVNET
    $nsg=Get-AzureRMNetworkSecurityGroup -Name "TestSubnet" -ResourceGroupName $rgName
    Set-AzureRMVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet" -AddressPrefix 192.168.0.0/24 -NetworkSecurityGroup $nsg

A continuación, solicitar una dirección IP pública se debe asignar a la puerta de enlace de la red virtual de TestVNET y crear la puerta de enlace.

    $gwpip=New-AzureRmPublicIpAddress -Name TestVNET_pip -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
    $vnet=Get-AzureRmVirtualNetwork -Name TestVNET -ResourceGroupName $rgName
    $subnet=Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
    $gwipconfig=New-AzureRmVirtualNetworkGatewayIpConfig -Name "TestVNET_GWConfig" -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
    New-AzureRmVirtualNetworkGateway -Name "TestVNET_GW" -ResourceGroupName $rgName -Location $locName -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

Esta es la configuración actual.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph2.png)
 
##<a name="phase-3-create-the-vnet-to-vnet-connection"></a>Fase 3: Crear la conexión de VNet a VNet

En primer lugar, obtenga una clave de previamente compartida aleatoria, cifrado fuerte, 32 caracteres desde el Administrador de red o de seguridad. Como alternativa, use la información en [crear una cadena aleatoria de una clave compartida de IPsec](http://social.technet.microsoft.com/wiki/contents/articles/32330.create-a-random-string-for-an-ipsec-preshared-key.aspx) para obtener una clave previamente compartida.

A continuación, utilice estos comandos para crear la conexión VPN VNet a VNet, que puede tardar algún tiempo en completarse.

    $sharedKey="<pre-shared key value>"
    $gwTestLab=Get-AzureRmVirtualNetworkGateway -Name TestLab_GW -ResourceGroupName $rgName
    $gwTestVNET=Get-AzureRmVirtualNetworkGateway -Name TestVNET_GW -ResourceGroupName $rgName
    New-AzureRmVirtualNetworkGatewayConnection -Name TestLab_to_TestVNET -ResourceGroupName $rgName -VirtualNetworkGateway1 $gwTestLab -VirtualNetworkGateway2 $gwTestVNET -Location $locName -ConnectionType Vnet2Vnet -SharedKey $sharedKey
    New-AzureRmVirtualNetworkGatewayConnection -Name TestVNET_to_TestLab -ResourceGroupName $rgName -VirtualNetworkGateway1 $gwTestVNET -VirtualNetworkGateway2 $gwTestLab -Location $locName -ConnectionType Vnet2Vnet -SharedKey $sharedKey

Después de unos minutos, se debe establecer la conexión.

Esta es la configuración actual.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph3.png)
 
## <a name="phase-4-configure-dc2"></a>Fase 4: Configurar DC2

Primero, cree una máquina virtual para DC2. Ejecutar estos comandos en el símbolo del sistema de PowerShell de Azure en el equipo local.

    $rgName="<your resource group name>"
    $locName="<your Azure location, such as West US>"
    $saName="<the storage account name for the base configuration>"
    $vnet=Get-AzureRMVirtualNetwork -Name TestVNET -ResourceGroupName $rgName
    $pip=New-AzureRMPublicIpAddress -Name DC2-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
    $nic=New-AzureRMNetworkInterface -Name DC2-NIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -PrivateIpAddress 192.168.0.4
    $vm=New-AzureRMVMConfig -VMName DC2 -VMSize Standard_A1
    $storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
    $vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DC2-TestVNET-ADDSDisk.vhd"
    Add-AzureRMVMDataDisk -VM $vm -Name ADDS-Data -DiskSizeInGB 20 -VhdUri $vhdURI  -CreateOption empty
    $cred=Get-Credential -Message "Type the name and password of the local administrator account for DC2."
    $vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName DC2 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
    $vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
    $osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DC2-TestLab-OSDisk.vhd"
    $vm=Set-AzureRMVMOSDisk -VM $vm -Name DC2-TestVNET-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
    New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

A continuación, conectarse a la nueva máquina virtual de DC2 desde el portal de Azure.

A continuación, configurar una regla de Firewall de Windows para permitir el tráfico de pruebas de conectividad básica. Desde una línea de comandos de Windows PowerShell nivel de administrador en DC2, ejecute estos comandos.

    Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
    ping dc1.corp.contoso.com

El comando ping debe aparecer en cuatro respuestas desde dirección IP 10.0.0.4. Esto es una prueba de tráfico a través de la conexión de VNet a VNet.

A continuación, agregue el disco de datos adicionales en DC2 como un nuevo volumen con la letra de unidad F:.

1.  En el panel izquierdo del administrador del servidor, haga clic en **archivo y servicios de almacenamiento**y, a continuación, haga clic en **discos**.
2.  En el panel de contenido, en el grupo de **discos** , haga clic en **el disco 2** (con el conjunto de **partición** **desconocido**).
3.  Haga clic en **tareas**y, a continuación, haga clic en **Nuevo volumen**.
4.  En la antes de comenzar la página del Asistente de volumen nuevo, haga clic en **siguiente**.
5.  En la página Seleccionar el servidor y disco, haga clic en el **disco 2**y, a continuación, haga clic en **siguiente**. Cuando se le solicite, haga clic en **Aceptar**.
6.  Especificar el tamaño de la página de volumen, haga clic en **siguiente**.
7.  En asignar a una página de unidad letra o una carpeta, haga clic en **siguiente**.
8.  En la página de configuración del sistema seleccione archivo, haga clic en **siguiente**.
9.  En la página de selecciones confirme, haga clic en **crear**.
10. Cuando haya terminado, haga clic en **Cerrar**.

A continuación, configure DC2 como un controlador de dominio de réplica del dominio corp.contoso.com. Ejecutar estos comandos desde el símbolo del sistema de Windows PowerShell en DC2.

    Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
    Install-ADDSDomainController -Credential (Get-Credential CORP\User1) -DomainName "corp.contoso.com" -InstallDns:$true -DatabasePath "F:\NTDS" -LogPath "F:\Logs" -SysvolPath "F:\SYSVOL"

Tenga en cuenta que se le pide que proporcione la contraseña de CORP\User1 y una contraseña de modo de restauración de servicios de directorio (DSRM) y reiniciar DC2.

Ahora que la red virtual de TestVNET tiene su propio servidor DNS (DC2), debe configurar la red virtual TestVNET para usar este servidor DNS.

1.  En el panel izquierdo del portal de Azure, haga clic en el icono de redes virtuales y, a continuación, haga clic en **TestVNET**.
2.  En la pestaña **configuración** , haga clic en **los servidores DNS**.
3.  En el **servidor DNS principal**, escriba **192.168.0.4** para reemplazar 10.0.0.4.
4.  Haga clic en **Guardar**.

Esta es la configuración actual. 

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph4.png)
 
Su entorno de nube híbrida simulada ahora está listo para probarlo.

## <a name="next-step"></a>Siguiente paso

- Configurar una [línea basados en web de aplicación de empresa](virtual-machines-windows-ps-hybrid-cloud-test-env-lob.md) en este entorno.
