<properties 
   pageTitle="Aceleración de la red para una máquina virtual - PowerShell | Microsoft Azure"
   description="Obtenga información sobre cómo configurar redes acelera para una máquina virtual Azure con PowerShell."
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/23/2016"
   ms.author="jdial" />

# <a name="accelerated-networking-for-a-virtual-machine"></a>Redes acelerada para una máquina virtual

> [AZURE.SELECTOR]
- [Portal de Azure](virtual-network-accelerated-networking-portal.md)
- [PowerShell](virtual-network-accelerated-networking-powershell.md)

Redes acelerada permite la única raíz i/OS virtualización (SR IOV) a una máquina virtual (VM), mejorando considerablemente el rendimiento de red. Esta ruta de acceso de alto rendimiento que omita el host de la datapath reducción de latencia, vibración y el uso de CPU para su uso con las cargas de trabajo de red más exigentes en tipos VM compatibles. En este artículo se explica cómo usar PowerShell de Azure para configurar redes acelerado en el modelo de implementación de administrador de recursos de Azure. También puede crear una máquina virtual con acelerado de red con el Portal de Azure. Para obtener información sobre cómo hacerlo, haga clic en el cuadro de Portal de Azure en la parte superior de este artículo.

La siguiente imagen muestra la comunicación entre dos máquinas virtuales (VM) con y sin acelerado a redes:

![Comparación](./media/virtual-network-accelerated-networking-powershell/image1.png)

Sin acelerado redes, todo el tráfico de red dentro y fuera de la máquina virtual debe recorrer el host y el modificador virtual. El modificador virtual proporciona todas las aplicación de directivas, por ejemplo, los grupos de seguridad de red, las listas de control de acceso, aislamiento y otros servicios de red virtualizado para el tráfico de red. Para obtener más información, lea el artículo [virtualización de red Hyper-V y Switch Virtual](https://technet.microsoft.com/library/jj945275.aspx) .

Con acelerado de red, el tráfico de red llega a la tarjeta de red (NIC) y, a continuación, se reenviará a la máquina virtual. Todas las directivas de red que se aplica el modificador virtual sin acelerado redes se descarga y se aplica en hardware. Aplicar la directiva de hardware permite la NIC reenvíe el tráfico de red directamente a la máquina virtual, omitiendo el host y el modificador virtual, manteniendo todas la directiva que se aplica en el host.

Las ventajas de redes acelerado solo se aplican a la máquina virtual que está activada. Para obtener los mejores resultados, es ideal para habilitar esta característica en al menos dos máquinas virtuales conectado a la misma VNet.  Cuando se comunica a través de VNets o conexión local, esta característica tiene un impacto mínimo a latencia general.

[AZURE.INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

##<a name="benefits"></a>Ventajas

- **Menor latencia / superior paquetes por segundo (pps):** Quitando el modificador virtual la datapath quita el tiempo que dedican a paquetes en el host de procesamiento de directiva y aumenta el número de paquetes que puede procesar dentro de la máquina virtual.
- **Reducido vibración:** Procesamiento de switch virtual depende de la cantidad de directiva que debe aplicarse y la carga de trabajo de la CPU que está realizando el procesamiento. Descarga la aplicación de directivas para el hardware quita los cambios en esa proporcionando paquetes directamente a la máquina virtual, quitando el host de comunicación VM y todas las interrupciones de software y los cambios de contexto.
- **Disminuye la CPU:** Omitir el modificador virtual en el host lleva a menos la CPU para el procesamiento de tráfico de red.

## <a name="limitations"></a>Limitaciones

Al usar esta función, existen las siguientes limitaciones:
 
- **Creación de la interfaz de red:** Redes acelerada sólo se puede habilitar para una nueva interfaz de red.  No se pueden habilitar en una interfaz de red existente.
- **Creación de VM:** Una interfaz de red con las redes acelerada habilitada solo puede estar asociada a una máquina virtual cuando se crea la máquina virtual. La interfaz de red no se puede adjuntar a una máquina virtual existente.
- **Regiones:** Disponible en las regiones oeste Central de nosotros y Oeste Europe Azure solo. Expandir el conjunto de regiones en el futuro.
- **Sistema operativo de compatibles:** Microsoft Windows Server 2012 R2 y Windows Server 2016 Technical Preview 5. Compatibilidad con Linux y Windows Server 2012 se agregará pronto.
- **Tamaño de memoria virtual:** Standard_D15_v2 y Standard_DS15_v2 son que solo admite tamaños de instancia de máquina virtual. Para obtener más información, vea el artículo de [tamaños de máquina virtual de Windows](../virtual-machines/virtual-machines-windows-sizes.md) . El conjunto de tamaños de instancia VM admitidos se expandirá en el futuro.

Cambios en estas limitaciones se anunciará a través de la página de [actualizaciones de red Virtual de Azure](https://azure.microsoft.com/updates/accelerated-networking-in-preview) .

## <a name="create-a-windows-vm-with-accelerated-networking"></a>Crear una máquina virtual de Windows con las redes acelerada

1. Abra un símbolo del sistema de PowerShell y complete los pasos restantes de esta sección dentro de una única sesión de PowerShell. Si todavía no tiene instalado y configurado de PowerShell, complete los pasos en el artículo [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) .
2. Para registrar la versión preliminar, enviar un correo electrónico a [Acelerado suscripciones a redes](mailto:axnpreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) con su identificador de suscripción y el destino previsto. No lleve a cabo los pasos restantes hasta después de recibir un correo electrónico para notificarle que ha aceptado en la vista previa.
3. Registrar la capacidad con la suscripción, escriba los siguientes comandos:

        Register-AzureRmProviderFeature -FeatureName AllowAcceleratedNetworkingFeature -ProviderNamespace Microsoft.Network
        Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network

4. Reemplace *westcentralus* con el nombre de otra ubicación compatible con esta capacidad enumerada en la sección de [las limitaciones](#limitations) de este artículo (si lo desea). Escriba el comando siguiente para establecer una variable para la ubicación:

        $locName = "westcentralus"

5. Reemplazar *RG1* con un nombre para el grupo de recursos que contendrá la nueva interfaz de red y escriba los siguientes comandos para crearla:

        $rgName = "RG1"
        New-AzureRmResourceGroup -Name $rgName -Location $locName

6. Cambiar *VM1 NIC1* a las desea asignar un nombre a la interfaz de red y, a continuación, escriba el siguiente comando:

        $NICName = "VM1-NIC1"

7. La interfaz de red debe estar conectada a una subred dentro de una red Virtual Azure existente (VNet) en la misma ubicación y [suscripción](../azure-glossary-cloud-terminology.md#subscription) como la interfaz de red. Más información sobre VNets lea el artículo [Introducción a la red Virtual](virtual-networks-overview.md) si no está familiarizado con ellos. Para crear un VNet, complete los pasos en el artículo [crear una VNet](virtual-networks-create-vnet-arm-ps.md) . Cambiar los valores de"" de la siguiente $Variables el nombre de la VNet y la subred que desea conectarse a la interfaz de red.

        $VNetName   = "VNet1"
        $SubnetName = "Subnet1"

    Si no conoce el nombre de un VNet existente en la ubicación elegida en el paso 3, escriba los siguientes comandos:
        
        $VirtualNetworks = Get-AzureRmVirtualNetwork
        $VirtualNetworks | Where-Object {$_.Location -eq $locName} | Format-Table Name, Location
        
    Si la lista devuelta está vacía, debe crear un VNet en la ubicación. Para crear un VNet, complete los pasos en el artículo [crear una red virtual](virtual-networks-create-vnet-arm-ps.md) .

    Para obtener el nombre de cada subred dentro de la VNet, escriba los comandos siguientes y reemplace *subred1* anteriormente con el nombre de una subred:
        
        $VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $rgName
        $VNet.Subnets | Format-Table Name, AddressPrefix

8. Escriba los comandos siguientes para recuperar la VNet y la subred y asignarlos a las variables.

        $VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $rgName
        $Subnet = $VNet.Subnets | Where-Object { $_.Name -eq $SubnetName }

9. Identificar un recurso público existente de direcciones IP que se puede asociar a la interfaz de red para que pueda conectarse a él a través de Internet. Si no desea tener acceso a la máquina virtual con la interfaz de red a través de Internet, puede omitir este paso. Sin una dirección IP pública, debe conectarse a la máquina virtual desde otra máquina virtual conectado a la misma VNet. 

    Cambiar *PIP1* el nombre de un recurso público existente de direcciones IP que existe en la ubicación que está creando en la interfaz de red y que no está actualmente asociada a otra interfaz de red. Si es necesario, cambie $rgName por el nombre del grupo de recursos, el recurso de dirección IP público existe en y escriba el siguiente comando:

        $PIP1 = Get-AzureRmPublicIPAddress -Name "PIP1" -ResourceGroupName $rgName

    Si no conoce el nombre de un recurso de dirección IP público existente, escriba los siguientes comandos:

        $PublicIPAddresses = Get-AzureRmPublicIPAddress
        $PublicIPAddresses | Where-Object { $_.Location -eq $locName } |Format-Table Name, Location, IPAddress, IpConfiguration

    Si la columna de **IP** no tiene ningún valor en el resultado devuelto, el recurso de dirección IP público no está asociado con una interfaz de red existente y se puede usar. Si la lista está en blanco o sin disponibles recursos de dirección IP pública, puede crearla con el comando AzureRmPublicIPAddress de nuevo.

    >[AZURE.NOTE] Direcciones IP públicas tienen una cuota nominal. Más información sobre precios, lea la página de [precios de la dirección IP](https://azure.microsoft.com/pricing/details/ip-addresses) .
10. Si decide no agregar un recurso de dirección IP público a la interfaz, quite *- PublicIPAddress $PIP1* al final del comando que sigue. Crear la interfaz de red con las redes acelerada escribiendo el siguiente comando:

        $nic = New-AzureRmNetworkInterface -Location $locName -Name $NICName -ResourceGroupName $rgName -Subnet $Subnet -EnableAcceleratedNetworking -PublicIpAddress $PIP1 

11. Asignar la interfaz de red a una máquina virtual al crear la máquina virtual siguiendo las instrucciones en los pasos 3 a 6 del artículo [crear una máquina virtual](../virtual-machines/virtual-machines-windows-ps-create.md) . En el paso 6-2, reemplace *Standard_A1* con uno de los tamaños VM enumerados en la sección [limitaciones](#limitations) de este artículo.

    >[AZURE.NOTE] Si ha cambiado el *nombre* de la $locName, $rgName o $nic variables en este artículo, se producirá un error en el paso 6 en el crear un artículo de la máquina virtual. Sin embargo, puede cambiar los *valores* de las variables.

12. Una vez creada la máquina virtual, descargue el [controlador acelerado redes](https://gallery.technet.microsoft.com/Azure-Accelerated-471b5d84), conectarse a la máquina virtual y ejecute al instalador de controlador dentro de la máquina virtual.

13. Haga clic en el botón de Windows y haga clic en **Administrador de dispositivos**. Compruebe que el **Adaptador de Ethernet de función Virtual 3 Mellanox ConnectX** aparece en la opción de **red** cuando se expande, tal como se muestra en la siguiente imagen:

    ![Administrador de dispositivos](./media/virtual-network-accelerated-networking-powershell/image2.png)