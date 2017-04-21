<properties 
    pageTitle="Entorno de prueba de aplicación LOB | Microsoft Azure" 
    description="Obtenga información sobre cómo crear una línea basados en web de aplicación de empresa en un entorno híbrido de nube de TI pro o pruebas de desarrollo." 
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

# <a name="set-up-a-web-based-lob-application-in-a-hybrid-cloud-for-testing"></a>Configurar una aplicación de LOB basada en web en una nube híbrida para realizar pruebas

Este tema le guiará en el proceso de creación de un entorno de nube híbrida simulada para probar una línea basados en web de aplicación de negocio (LOB) alojado en Microsoft Azure. Aquí tiene la configuración resultante.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph3.png)

Esta configuración se compone de:

- Una red local simulada hospedada en Azure (la TestLab VNet).
- Una red virtual local entre hospedada en Azure (TestVNET).
- Una conexión VPN VNet a VNet.
- Un servidor LOB basado en web, SQL server y el controlador de dominio secundario de la red virtual TestVNET.

Esta configuración proporciona una base y el punto de partida común desde el que se puede:

- Desarrollar y probar aplicaciones LOB hospedadas en Internet Information Services (IIS) con un servidor de base de datos de SQL Server 2014 en Azure.
- Realizar pruebas de esta simulada híbrido en la nube de trabajo de TI.

Hay tres fases principales para configurar este entorno híbrido de prueba de nube:

1.  Configurar el entorno de nube híbrida simulada.
2.  Configurar el equipo SQL server (SQL1).
3.  Configurar el servidor de línea de negocio (LOB1).

Esta carga de trabajo requiere una suscripción de Azure. Si tiene una suscripción a MSDN o Visual Studio, vea [crédito Azure mensuales para los suscriptores de Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

Para obtener un ejemplo de una aplicación de LOB hospedado en Azure de producción, consulte la Guía de arquitectura de **aplicaciones empresariales** en [diagramas de arquitectura de Software de Microsoft y planos](http://msdn.microsoft.com/dn630664).

## <a name="phase-1-set-up-the-simulated-hybrid-cloud-environment"></a>Fase 1: Configurar el entorno de nube híbrida simulada

Crear la [simulación de entorno de prueba de nube híbrida](virtual-machines-windows-ps-hybrid-cloud-test-env-sim.md). Dado que este entorno de prueba no requiere la presencia del servidor APP1 en la subred de la red corporativa, puede cerrarlo por ahora.

Esta es la configuración actual.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph1.png)
 
## <a name="phase-2-configure-the-sql-server-computer-sql1"></a>Fase 2: Configurar el equipo SQL server (SQL1)

Desde el portal de Azure, inicie el equipo de DC2 si es necesario.

A continuación, cree una máquina virtual para SQL1 con estos comandos en un símbolo del sistema de PowerShell de Azure en el equipo local. Antes de ejecutar estos comandos, rellene los valores de variables y quite los caracteres < y >.

    $rgName="<your resource group name>"
    $locName="<the Azure location of your resource group>"
    $saName="<your storage account name>"
    
    $vnet=Get-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName
    $subnet=Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet"
    $pip=New-AzureRMPublicIpAddress -Name SQL1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
    $nic=New-AzureRMNetworkInterface -Name SQL1-NIC -ResourceGroupName $rgName -Location $locName -Subnet $subnet -PublicIpAddress $pip
    $vm=New-AzureRMVMConfig -VMName SQL1 -VMSize Standard_A4
    $storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
    $vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/SQL1-SQLDataDisk.vhd"
    Add-AzureRMVMDataDisk -VM $vm -Name "Data" -DiskSizeInGB 100 -VhdUri $vhdURI  -CreateOption empty
    
    $cred=Get-Credential -Message "Type the name and password of the local administrator account for the SQL Server computer." 
    $vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName SQL1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftSQLServer -Offer SQL2014-WS2012R2 -Skus Standard -Version "latest"
    $vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
    $storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
    $osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/SQL1-OSDisk.vhd"
    $vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
    New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

Usar el portal de Azure para conectarse a SQL1 con la cuenta de administrador local de SQL1.

A continuación, configure las reglas de Firewall de Windows para permitir el tráfico de SQL Server y pruebas de conectividad básica. Desde una línea de comandos de Windows PowerShell nivel de administrador en SQL1, ejecute estos comandos.

    New-NetFirewallRule -DisplayName "SQL Server" -Direction Inbound -Protocol TCP -LocalPort 1433,1434,5022 -Action allow 
    Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
    ping dc2.corp.contoso.com

El comando ping debe aparecer en cuatro respuestas desde la dirección IP 192.168.0.4.

A continuación, agregue el disco de datos adicionales en SQL1 como un nuevo volumen con la letra de unidad F:.

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

Ejecutar estos comandos en el símbolo del sistema de Windows PowerShell en SQL1:

    md f:\Data
    md f:\Log
    md f:\Backup

A continuación, unir SQL1 al dominio CORP Windows Server Active Directory con estos comandos en el símbolo del sistema de Windows PowerShell en SQL1.

    Add-Computer -DomainName corp.contoso.com
    Restart-Computer

Usar la cuenta de CORP\User1 cuando se le pida que proporcione credenciales de cuenta de dominio para el comando **Agregar equipo** .

Después de reiniciar, usar el portal de Azure para conectarse a SQL1 *con la cuenta de administrador local de SQL1*.

A continuación, configure SQL Server 2014 para usar la unidad F: para nuevas bases de datos y de permisos de la cuenta de usuario.

1.  Desde la pantalla de inicio, escriba **Administración de SQL Server**y, a continuación, haga clic en **2014 de SQL Server Management Studio**.
2.  En **Conectar con el servidor**, haga clic en **Conectar**.
3.  En el panel de árbol del explorador de objetos, haga clic con el botón **SQL1**y, a continuación, haga clic en **Propiedades**.
4.  En la ventana de **Propiedades del servidor** , haga clic en **Configuración de la base de datos**.
5.  Busque las **ubicaciones predeterminadas de base de datos** y configurar estos valores: 
    - Para los **datos**, escriba la ruta de acceso **f:\Data**.
    - Para el **registro**, escriba la ruta de acceso **f:\Log**.
    - Para **copia de seguridad**, escriba la ruta de acceso **f:\Backup**.
    - Nota: Solo nuevas bases de datos use estas ubicaciones.
6.  Haga clic en **Aceptar** para cerrar la ventana.
7.  En el panel de árbol del **Explorador de objetos** , abra la **seguridad**.
8.  Haga clic en **inicios de sesión** y, a continuación, haga clic en **Nuevo inicio de sesión**.
9.  En **nombre de inicio de sesión**, escriba **CORP\User1**.
10. En la página de **Roles del servidor** , haga clic en **sysadmin**y, a continuación, haga clic en **Aceptar**.
11. Cierre Microsoft SQL Server Management Studio.

Esta es la configuración actual.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph2.png)
 
## <a name="phase-3-configure-the-lob-server-lob1"></a>Fase 3: Configurar el servidor de línea de negocio (LOB1)

Primero, cree una máquina virtual para LOB1 con estos comandos en el símbolo del sistema de PowerShell de Azure en el equipo local.

    $rgName="<your resource group name>"
    $locName="<your Azure location, such as West US>"
    $saName="<your storage account name>"
    
    $vnet=Get-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName
    $subnet=Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet"
    $pip=New-AzureRMPublicIpAddress -Name LOB1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
    $nic=New-AzureRMNetworkInterface -Name LOB1-NIC -ResourceGroupName $rgName -Location $locName -Subnet $subnet -PublicIpAddress $pip
    $vm=New-AzureRMVMConfig -VMName LOB1 -VMSize Standard_A2
    $storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
    $cred=Get-Credential -Message "Type the name and password of the local administrator account for LOB1."
    $vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName LOB1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
    $vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
    $osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/LOB1-TestLab-OSDisk.vhd"
    $vm=Set-AzureRMVMOSDisk -VM $vm -Name LOB1-TestVNET-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
    New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

A continuación, usar el portal de Azure para conectarse a LOB1 con las credenciales de la cuenta de administrador local de LOB1.

A continuación, configurar una regla de Firewall de Windows para permitir el tráfico de pruebas de conectividad básica. Desde una línea de comandos de Windows PowerShell nivel de administrador en LOB1, ejecute estos comandos.

    Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
    ping dc2.corp.contoso.com

El comando ping debe aparecer en cuatro respuestas desde la dirección IP 192.168.0.4.

A continuación, unirse a LOB1 en el dominio de Active Directory de CORP con estos comandos en el símbolo del sistema de Windows PowerShell.

    Add-Computer -DomainName corp.contoso.com
    Restart-Computer

Usar la cuenta de CORP\User1 cuando se le pida que proporcione credenciales de cuenta de dominio para el comando **Agregar equipo** .

Después de reiniciar, use el portal de Azure para conectarse a LOB1 con la cuenta CORP\User1 y la contraseña.

A continuación, configurar LOB1 para IIS y probar el acceso desde CLIENTE1.

1.  Desde el administrador del servidor, haga clic en **funciones y agregar**.
2.  En la página **antes de empezar** , haga clic en **siguiente**.
3.  En la página **Seleccionar tipo de instalación** , haga clic en **siguiente**.
4.  En la página **Seleccionar el servidor de destino** , haga clic en **siguiente**.
5.  En la página de **roles del servidor** , haga clic en el **Servidor Web (IIS)** en la lista de **funciones**.
6.  Cuando se le solicite, haga clic en **Agregar características**y, a continuación, haga clic en **siguiente**.
7.  En la página **Seleccionar características** , haga clic en **siguiente**.
8.  En la página de **Servidor Web (IIS)** , haga clic en **siguiente**.
9.  En la página **Seleccionar servicios de rol** , seleccione o desactive las casillas de verificación de los servicios que necesita para probar la aplicación de LOB y, a continuación, haga clic en **siguiente**.
10. En la página **Confirmar selecciones de instalación** , haga clic en **instalar**.
11. Espere hasta que haya finalizado la instalación de componentes y, a continuación, haga clic en **Cerrar**.
12. Desde el portal de Azure, conectarse a CLIENTE1 con las credenciales de cuenta de CORP\User1 y, a continuación, inicie Internet Explorer.
13. En la barra de direcciones, escriba **http://lob1/** y, a continuación, presione ENTRAR. Verá la página web de IIS 8 de forma predeterminada.

Esta es la configuración actual.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph3.png)
 
Este entorno ahora está listo para implementar la aplicación basada en web en LOB1 y probar la funcionalidad de CLIENTE1 en la subred de la red corporativa.

## <a name="next-step"></a>Siguiente paso

- Agregar una nueva máquina virtual con el [portal de Azure](virtual-machines-windows-hero-tutorial.md).
