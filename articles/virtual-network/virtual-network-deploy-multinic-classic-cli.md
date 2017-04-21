<properties
   pageTitle="Implementar máquinas virtuales de NIC múltiple mediante la CLI de Azure en el modelo de implementación clásica | Microsoft Azure"
   description="Obtenga información sobre cómo implementar máquinas virtuales de NIC múltiple mediante la CLI de Azure en el modelo de implementación clásica"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="jdial" />

#<a name="deploy-multi-nic-vms-classic-using-the-azure-cli"></a>Implementar máquinas virtuales de NIC de múltiples (clásico) mediante la CLI de Azure

[AZURE.INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

Puede crear máquinas virtuales (VM) en Azure y adjuntar varias interfaces de red (NIC) para cada uno de sus máquinas virtuales. Varias NIC Habilitar separación de tipos de tráfico a través de NIC. Por ejemplo, una NIC puede comunicarse con Internet, mientras que otro sólo se comunica con recursos internos no está conectados a Internet. La capacidad para separar el tráfico de red en varias NIC es necesaria para muchos dispositivos virtual red, como la entrega de aplicaciones y soluciones de optimización de WAN.

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]Obtenga información sobre cómo llevar a [cabo estos pasos con el modelo de administrador de recursos](virtual-network-deploy-multinic-arm-cli.md).

[AZURE.INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Actualmente, no puede tener máquinas virtuales con una sola NIC y máquinas virtuales con varias NIC en el mismo servicio de nube. Por lo tanto, debe implementar los servidores back-end en un servicio de nube diferente que y todos los demás componentes en el escenario. Los pasos siguientes utilizan un servicio de nube denominado *IaaSStory* para los recursos principales y *Back-end de IaaSStory* para los servidores de back-end.

## <a name="prerequisites"></a>Requisitos previos

Antes de implementar los servidores back-end, debe implementar el servicio de nube principal con todos los recursos necesarios para este escenario. Como mínimo, debe crear una red virtual con una subred para el back-end. Visite [crear una red virtual usando CLI Azure](virtual-networks-create-vnet-classic-cli.md) para obtener información sobre cómo implementar una red virtual.

[AZURE.INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="deploy-the-back-end-vms"></a>Implementar la VM back-end

Las máquinas virtuales de back-end dependen de la creación de los recursos que se muestran a continuación.

- **Cuenta de almacenamiento para los discos de datos**. Para mejorar el rendimiento, los discos de datos en los servidores de base de datos utiliza tecnología de unidades (SSD) de estado sólido, que requiere una cuenta de almacenamiento premium. Asegúrese de que la ubicación de Azure implementa para admitir el almacenamiento de premium.
- **NIC**. Cada VM tendrá dos NIC, uno para el acceso de la base de datos y otro para la administración.
- **Establece la disponibilidad**. Todos los servidores de base de datos se agregará a una sola disponibilidad establecida, para asegurarse de que al menos una de las máquinas virtuales es hacia arriba y ejecuta durante el mantenimiento.

### <a name="step-1---start-your-script"></a>Paso 1: comenzar la secuencia de comandos

Puede descargar la secuencia de comandos de fiesta completa utilizada [aquí](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-cli.sh). Siga los pasos siguientes para cambiar la secuencia de comandos para trabajar en su entorno.

1. Cambiar los valores de las variables siguientes en función de su grupo de recursos existente implementado por encima de [los requisitos previos](#Prerequisites).

        location="useast2"
        vnetName="WTestVNet"
        backendSubnetName="BackEnd"

2. Cambiar los valores de las variables siguientes en función de los valores que desea usar para la implementación de back-end.

        backendCSName="IaaSStory-Backend"
        prmStorageAccountName="iaasstoryprmstorage"
        image="0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1"
        avSetName="ASDB"
        vmSize="Standard_DS3"
        diskSize=127
        vmNamePrefix="DB"
        osDiskName="osdiskdb"
        dataDiskPrefix="db"
        dataDiskName="datadisk"
        ipAddressPrefix="192.168.2."
        username='adminuser'
        password='adminP@ssw0rd'
        numberOfVMs=2

### <a name="step-2---create-necessary-resources-for-your-vms"></a>Paso 2: crear recursos necesarios para sus máquinas virtuales

1. Crear un nuevo servicio de nube para todas las máquinas virtuales de back-end. Observe el uso de la `$backendCSName` variable para el nombre del grupo de recursos y `$location` de la región de Azure.

        azure service create --serviceName $backendCSName \
            --location $location

2. Crear una cuenta de almacenamiento premium para los discos de sistema operativo y datos usarlo con los suyos máquinas virtuales.

        azure storage account create $prmStorageAccountName \
            --location $location \
            --type PLRS

### <a name="step-3---create-vms-with-multiple-nics"></a>Paso 3: crear máquinas virtuales con varias NIC

1. Iniciar un bucle para crear varias máquinas virtuales, en función de la `numberOfVMs` variables.

        for ((suffixNumber=1;suffixNumber<=numberOfVMs;suffixNumber++));
        do

2. Para cada VM, especifique el nombre y la dirección IP de cada una de las dos NIC.

            nic1Name=$vmNamePrefix$suffixNumber-DA
            x=$((suffixNumber+3))
            ipAddress1=$ipAddressPrefix$x

            nic2Name=$vmNamePrefix$suffixNumber-RA
            x=$((suffixNumber+53))
            ipAddress2=$ipAddressPrefix$x

4. Crear la máquina virtual. Observe el uso de la `--nic-config` parámetro, que contiene una lista de todas las NIC con nombre, la subred y la dirección IP.

            azure vm create $backendCSName $image $username $password \
                --connect $backendCSName \
                --vm-name $vmNamePrefix$suffixNumber \
                --vm-size $vmSize \
                --availability-set $avSetName \
                --blob-url $prmStorageAccountName.blob.core.windows.net/vhds/$osDiskName$suffixNumber.vhd \
                --virtual-network-name $vnetName \
                --subnet-names $backendSubnetName \
                --nic-config $nic1Name:$backendSubnetName:$ipAddress1::,$nic2Name:$backendSubnetName:$ipAddress2::

5. Para cada VM crear dos discos de datos.

            azure vm disk attach-new $vmNamePrefix$suffixNumber \
                $diskSize \
                vhds/$dataDiskPrefix$suffixNumber$dataDiskName-1.vhd

            azure vm disk attach-new $vmNamePrefix$suffixNumber \
                $diskSize \
                vhds/$dataDiskPrefix$suffixNumber$dataDiskName-2.vhd
        done

### <a name="step-4---run-the-script"></a>Paso 4: ejecute la secuencia de comandos

Ahora que descargó y cambiar la secuencia de comandos en función de sus necesidades, ejecute la secuencia de comandos para crear máquinas virtuales de base de datos de back-end con varias NIC.

1. Guarde la secuencia de comandos y ejecutar desde su terminal **Bash** . Verá el resultado inicial, como se muestra a continuación.

        info:    Executing command service create
        info:    Creating cloud service
        data:    Cloud service name IaaSStory-Backend
        info:    service create command OK
        info:    Executing command storage account create
        info:    Creating storage account
        info:    storage account create command OK
        info:    Executing command vm create
        info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
        info:    Looking up virtual network
        info:    Looking up cloud service
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Creating VM

2. Después de unos minutos, finalizará la ejecución y verá el resto de los resultados como se muestra a continuación.

        info:    OK
        info:    vm create command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm create
        info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
        info:    Looking up virtual network
        info:    Looking up cloud service
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Creating VM
        info:    OK
        info:    vm create command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
