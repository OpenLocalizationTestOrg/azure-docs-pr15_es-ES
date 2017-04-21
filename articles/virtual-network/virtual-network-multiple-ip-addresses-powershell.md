<properties
   pageTitle="Varias direcciones IP para máquinas virtuales - PowerShell | Microsoft Azure"
   description="Obtenga información sobre cómo asignar varias direcciones IP a una máquina virtual con PowerShell de Azure."
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
   ms.date="10/05/2016"
   ms.author="jdial;annahar" />


# <a name="assign-multiple-ip-addresses-to-virtual-machines"></a>Asignar varias direcciones IP para máquinas virtuales

Una máquina Virtual (VM) de Azure puede tener una o más interfaces de red (NIC) adjuntas. Cualquier NIC puede tener uno o más públicas o privadas direcciones IP asignadas a él. Si no está familiarizado con las direcciones IP de Azure, lea el artículo de [direcciones IP en Azure](virtual-network-ip-addresses-overview-arm.md) para obtener más información acerca de ellos. En este artículo se explica cómo usar PowerShell de Azure para asignar varias direcciones IP a una máquina virtual en el modelo de implementación de administrador de recursos de Azure.

Asignar varias direcciones IP para una máquina virtual permite las siguientes capacidades:

- Hospedar varios sitios Web o los servicios con distintas direcciones IP y certificados SSL en un solo servidor.
- Servir como un dispositivo virtual de red, como un firewall o equilibrador de carga.
- La capacidad de agregar a un grupo de back-end del equilibrador de carga de Azure cualquiera de las direcciones IP privadas de cualquiera de las NIC. En el pasado, solo la dirección IP principal para la NIC principal puede agregarse a un grupo de back-end.

[AZURE.INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

Para registrar la versión preliminar, enviar un correo electrónico a [Direcciones IP de varios](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) con su identificador de suscripción y el destino previsto.

## <a name="scenario"></a>Escenario

En este artículo, se asocian tres configuraciones de IP para una interfaz de red.
Se creará y asignadas a una NIC que tendrá tres direcciones IP y una dirección IP pública asignado las configuraciones de ejemplo siguientes:

- IPConfig-1: Una dirección IP privada dinámica (predeterminado) y un público la dirección IP desde el recurso de dirección IP público denominado PIP1.
- IPConfig-2: Una dirección IP privada estática y ninguna dirección IP pública.
- IPConfig-3: Una dirección IP privada dinámica y ninguna dirección IP pública.

    ![Texto alternativo de imagen](./media/virtual-network-multiple-ip-addresses-powershell/OneNIC-3IP.png)

En este escenario, se supone que tiene un grupo de recursos denominado *RG1* dentro de que hay un VNet denominado *VNet1* y una subred denominado *subred1*. Además, se supone que tiene una máquina virtual denominada *VM1*, una interfaz de red denominada *VM1 NIC1* asociada a ella y una dirección IP pública denominado *PIP1*.

[En este artículo](./virtual-machines/virtual-machines-windows-ps-create.md ) se recorren cómo crear los recursos mencionados en caso de que no ha creado antes.

## <a name = "create"></a>Crear una máquina virtual con varias direcciones IP

1. Abra un símbolo del sistema de PowerShell y complete los pasos restantes de esta sección dentro de una única sesión de PowerShell. Si todavía no tiene instalado y configurado de PowerShell, complete los pasos en el artículo [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) .

2. Cambiar los valores de"" de la siguiente $Variables hasta la Azure [ubicación](https://azure.microsoft.com/regions) su red virtual en el nombre del [grupo de recursos](../azure-resource-manager/resource-group-overview.md#resource-groups), el VNet en el grupo de recursos, la subred que desea conectar el NIC para y el nombre de la NIC. Complete los pasos para agregar varias direcciones IP para cualquier NIC conectado a una máquina virtual, según sea necesario.

        $Location = "westcentralus"
        $RgName   = "RG1"
        $VNetName   = "VNet1"
        $SubnetName = "Subnet1"
        $NicName     = "VM1-NIC1"
        $PIP = "PIP"

    Si no conoce el nombre de una ubicación de Azure existente o grupo de recursos, escriba los siguientes comandos:

        Get-AzureRmLocation      | Format-Table Location
        Get-AzureRmResourceGroup | Format-Table ResourceGroupName

    <a name="subnet"></a>La NIC debe estar conectada a una subred dentro de una red Virtual Azure existente (VNet). Los tres componentes: NIC, subred y VNet, debe existir en la misma configuración regional y de [suscripción](../azure-glossary-cloud-terminology.md#subscription).  Si no está familiarizado con VNets, lea el artículo [Introducción a la red Virtual](virtual-networks-overview.md) para obtener más información sobre ellos o lea el artículo [crear una VNet](virtual-networks-create-vnet-arm-ps.md) para obtener información sobre cómo crear una.

    Si no conoce el nombre de un VNet existente, escriba el siguiente comando y reemplace *VNet1* en la variable anterior con el nombre de un VNet:

        Get-AzureRmVirtualNetwork | Format-Table Name

    Si la lista devuelta está vacía, debe crear un VNet. Para obtener información sobre cómo hacerlo, vea el artículo [crear una red virtual](virtual-networks-create-vnet-arm-ps.md) .

    Escriba los comandos siguientes para obtener el nombre de la subred existente dentro de la VNet y reemplazar *subred1* anteriormente con el nombre de una subred:

        $VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RgName
        $VNet.Subnets | Format-Table Name, AddressPrefix

4. Escriba el comando siguiente para recuperar la subred y asignar a una variable.

        $Subnet = $VNet.Subnets | Where-Object { $_.Name -eq $SubnetName }

5. <a name="ipconfigs"></a>Definir las configuraciones de IP que desea asignar al NIC. Cada configuración puede tener una estática o dinámica dirección IP privada y uno asociado público recurso de dirección IP con una dirección estática o dinámica.

    Agregue o quite cualquier número de las configuraciones que siguen dependiendo de cuántos direcciones IP que desee asociar a la NIC y la configuración que desee configurar.

    **IPConfig-1**

    Cambie el valor *PIP1* en el nombre de un recurso público existente de direcciones IP que existe en la ubicación que está creando la NIC en y que no está actualmente asociada a otra NIC. Cambiar *RG1* el nombre del grupo de recursos del recurso de dirección IP público existe en. Cambiar *IPConfig-1* en el nombre que desea dar a la primera configuración de IP. Escriba los siguientes comandos:

        $PIP1 = Get-AzureRmPublicIPAddress -Name "PIP1" -ResourceGroupName $RgName

        $IpConfigName1 = "IPConfig-1"
        $IPConfig1     = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName1 -Subnet $Subnet -PublicIpAddress $PIP1 -Primary

    Nota la *-principal* cambiar. Cuando se asignan varias configuraciones de IP a una NIC, debe tener asignada una configuración como el *principal*. Si no conoce el nombre de un recurso de dirección IP público existente, escriba el siguiente comando: Get-AzureRMPublicIPAddress | Formato de tabla nombre, ubicación, la dirección IP, IP

    Si la columna de **IP** no tiene ningún valor en el resultado devuelto, el recurso de dirección IP público no está asociado a una NIC existente y se puede usar. Si la lista está en blanco o sin disponibles recursos de dirección IP pública, puede crearla con el comando **AzureRmPublicIPAddress de nuevo** .

    >[AZURE.NOTE] Direcciones IP públicas tienen una cuota nominal. Para obtener más información sobre precios de direcciones IP, lea la página de [precios de dirección IP](https://azure.microsoft.com/pricing/details/ip-addresses) .

    **IPConfig 2**

    Cambiar *IPConfig 2* en el nombre que desea dar a la segunda configuración IP y cambie *10.0.0.5* a una dirección IP válida sin usar para la subred que desea asignar la NIC para. Escriba los siguientes comandos:

        $IPConfigName2 = "IPConfig-2"
        $IPAddress = 10.0.0.5

        $IPConfig2 = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName2 -Subnet $Subnet -PrivateIpAddress $IPAddress

    Escriba el comando siguiente, si no conoce la dirección IP asignado a la subred del intervalo de direcciones:

        $VNet.Subnets | Format-Table Name, AddressPrefix

    **IPConfig-3**

    Cambiar *IPConfig 3* en el nombre que desea dar a la configuración de IP de terceros y escriba los siguientes comandos:

        $IPConfigName3 = "IPConfig-3"
        $IPConfig3 = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName3 -Subnet $Subnet

    >[AZURE.NOTE] Puede asignar hasta 250 dirección IP privada a una NIC. Hay un límite de direcciones IP públicas que se pueden usar dentro de una suscripción. Para obtener más información, lea el artículo de [Azure limita](../azure-subscription-service-limits.md#networking-limits---azure-resource-manager) .

6. Crear la NIC con las configuraciones de IP definidas en el paso anterior.

        $nic = New-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $RgName -Location $Location -IpConfiguration $IpConfig1,$IpConfig2,$IpConfig3

7. Adjuntar la NIC al crear una máquina virtual siguiendo los pasos en el artículo [crear una máquina virtual](../virtual-machines/virtual-machines-windows-ps-create.md) . Aunque el artículo, crea una máquina virtual que ejecutan Windows Server, los pasos son los mismos para una VM Linux, aparte de seleccionar un sistema operativo diferente. Siga los pasos 1 a 3 de este artículo. Omitir los pasos 4 y 5 y, a continuación, completar el paso 6 en el crear un artículo de la máquina virtual.

    >[AZURE.WARNING] Paso 6 para crear un artículo de la máquina virtual se producirá un error si cambia la variable denominada $nic a otra cosa en el paso 6 de este artículo, o no se han completado los pasos anteriores de este artículo.

8. Ver la dirección IP privada direcciones que DHCP de Azure asignada a la NIC y el recurso de dirección IP público asignada a la NIC escribiendo el siguiente comando:

        $nic.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary

9. <a name="os"></a>Agregar manualmente todas las direcciones IP privadas (incluidos el principal) a la configuración de TCP/IP en el sistema operativo. 

**Windows**

1. Desde el símbolo del sistema, escriba *ipconfig/all*.  Sólo puede ver la dirección IP privada *principal* (a través de DHCP).
2. A continuación, escriba *ncpa.cpl* en la ventana de símbolo del sistema. Se abrirá una nueva ventana.
3. Abra las propiedades de **Conexión de área Local**.
4. Haga doble clic en la versión del protocolo de Internet 4 (IPv4)
5. Seleccione **usar la siguiente dirección IP** y escriba los valores siguientes:
    - **Dirección IP**: escriba la dirección IP privada *principal*
    - **Máscara de subred**: conjunto basado en la subred. Por ejemplo, si la subred es una /24 la máscara de subred es 255.255.255.0.
    - **Puerta de enlace predeterminada**: la primera dirección IP de la subred. Si la subred es 10.0.0.0/24, la dirección IP de puerta de enlace es 10.0.0.1.
    - Haga clic en **usar las siguientes direcciones de servidor DNS** y escriba los valores siguientes:
        - **Servidor DNS preferido:** Escriba 168.63.129.16 si no está utilizando su propio servidor DNS.  Si se encuentra, escriba la dirección IP del servidor DNS.
    - Haga clic en el botón **Avanzadas** y agregue las direcciones IP adicionales. Agregar cada una de las direcciones IP privadas secundarias enumeradas en el paso 8 a la NIC con la misma subred especificada para la dirección IP principal.
    - Haga clic en **Aceptar** para cerrar la configuración de TCP/IP y, a continuación, en **Aceptar** para cerrar la configuración del adaptador. A continuación, se volverá a establecer la conexión RDP.

6. Desde el símbolo del sistema, escriba *ipconfig/all*. Se muestran todas las direcciones IP que agregó y DHCP está desactivado.


**Linux (Ubuntu)**

1. Abra una ventana de terminal.
2. Asegúrese de que se encuentra el usuario raíz. Si no es así, puede hacerlo mediante el siguiente comando:

            sudo -i
3. Actualizar el archivo de configuración de la interfaz de red (suponiendo que 'eth0').
    - Mantener el elemento de línea existente para dhcp. Esto configurará la dirección IP principal como solía ser anteriores.
    - Agregar una configuración para una dirección IP estática adicional con los siguientes comandos:

                cd /etc/network/interfaces.d/
                ls

        Debería ver un archivo .cfg.
4. Abra el archivo: vi *nombre de archivo*.

    Debe ver las siguientes líneas al final del archivo:

            auto eth0
            iface eth0 inet dhcp
5. Agregue las siguientes líneas después de las líneas que existen en este archivo:

            iface eth0 inet static
            address <your private IP address here>
6. Guarde el archivo con el comando siguiente:

            :wq
7.  Restablecer la interfaz de red con el siguiente comando:

            sudo ifdown eth0 && sudo ifup eth0

    >[AZURE.IMPORTANT] Ejecute ifdown y ifup en la misma línea si usa una conexión remota.
8. Compruebe que la dirección IP se agrega a la interfaz de red con el siguiente comando:

            ip addr list eth0

    Debe ver la dirección IP que se haya agregado como parte de la lista.

**Linux (Redhat, CentOS y otros)**

1. Abra una ventana de terminal.
2. Asegúrese de que se encuentra el usuario raíz. Si no es así, puede hacerlo mediante el siguiente comando:

            sudo -i
3. Escriba su contraseña y siga las instrucciones que se le pida. Una vez que el usuario raíz, vaya a la carpeta de secuencias de comandos de red con el siguiente comando:

            cd /etc/sysconfig/network-scripts
4. Lista de los archivos de ifcfg relacionados con el siguiente comando:

            ls ifcfg-*

    Debería ver *eth0 ifcfg* como uno de los archivos.
5. Copie el archivo *ifcfg eth0* y asígnele el nombre *ifcfg-eth0:0* con el siguiente comando:

            cp ifcfg-eth0 ifcfg-eth0:0
6. Editar la *ifcfg-eth0:0* archivo con el siguiente comando:

            vi ifcfg-eth1
7. Cambiar el dispositivo en el nombre correspondiente en el archivo. *eth0:0* en este caso, con el siguiente comando:

            DEVICE=eth0:0
8. Cambiar el *DIRIP = YourPrivateIPAddress* línea para reflejar la dirección IP.
9. Guarde el archivo con el siguiente comando:

            :wq
10. Reinicie los servicios de red y asegurarse de que los cambios son correctos, ejecute los comandos siguientes:

            /etc/init.d/network restart
            Ipconfig

    Debe ver la dirección IP que ha agregado, *eth0:0*, en la lista devuelta.

## <a name="add"></a>Agregar direcciones IP a una máquina virtual existente

Complete los siguientes pasos para agregar direcciones IP adicionales a una NIC existente:

1. Abra un símbolo del sistema de PowerShell y complete los pasos restantes de esta sección dentro de una única sesión de PowerShell. Si todavía no tiene instalado y configurado de PowerShell, complete los pasos en el artículo [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) .

2. Cambiar los valores de"" de la siguiente $Variables el nombre de la NIC que desea agregar las direcciones IP para el grupo de recursos y ubicación de en que la NIC existe:

        $NicName     = "RG1-VM1-NIC1"
        $RgName   = "RG1"
        $NicLocation = "westcentralus"

    Si no conoce el nombre de la que desea cambiar, escriba los siguientes comandos NIC, cambie los valores de las variables anterior:

        Get-AzureRmNetworkInterface | Format-Table Name, ResourceGroupName, Location

3. Cree una variable y establezca la NIC existente escribiendo el siguiente comando:

        $nic = Get-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $RgName

4. Recuperar el ID de subred la NIC está conectada a Complete el [paso 3](#subnet) del crear una máquina virtual con sección varias de las direcciones IP de este artículo.

5. Crear las configuraciones de IP que desea agregar a la red siguiendo las instrucciones en el [paso 5](#ipconfigs) del crear una máquina virtual con sección varias de las direcciones IP de este artículo.

6. Cambiar *IPConfigName4 $* en el nombre de la configuración de IP que creó en el paso anterior. Para agregar la configuración, escriba el siguiente comando:

        Add-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName4 -NetworkInterface $nic -Subnet $Subnet1

7. Para configurar la NIC con la configuración de IP, escriba el siguiente comando:

        Set-AzureRmNetworkInterface -NetworkInterface $nic

8. Agregue las direcciones IP que haya agregado a la NIC para el sistema operativo VM siguiendo las instrucciones en el [paso 9](#os) del crear una máquina virtual con sección varias de las direcciones IP de este artículo.
