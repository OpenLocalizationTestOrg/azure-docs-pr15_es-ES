<properties 
   pageTitle="Cómo establecer una dirección IP privada estática en modo clásico ausing CLI | Microsoft Azure"
   description="Descripción de direcciones IP privado estático (DIP) y cómo se administran en modo clásico mediante CLI"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-service-management"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="jdial" />

# <a name="how-to-set-a-static-private-ip-address-classic-in-azure-cli"></a>Cómo configurar una dirección IP privada estática (clásico) en CLI de Azure

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]En este artículo trata sobre el modelo de implementación clásico. También puede [administrar una dirección IP privada estática en el modelo de implementación de administrador de recursos](virtual-networks-static-private-ip-arm-cli.md).

Los comandos de CLI de Azure de ejemplo siguientes esperan un entorno simple ya ha creado. Si desea ejecutar los comandos que se muestran en este documento, cree primero el entorno de prueba que se describe en [crear un vnet](virtual-networks-create-vnet-classic-cli.md).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Cómo especificar una dirección IP privada estática al crear una máquina virtual
Para crear una nueva máquina virtual denominada *DNS01* en un nuevo servicio de nube denominado *TestService* basado en el ejemplo anterior, siga estos pasos:

1. Si nunca ha usado CLI de Azure, vea [instalar y configurar la CLI de Azure](../xplat-cli-install.md) y siga las instrucciones hasta el punto donde seleccionar su cuenta de Azure y suscripción.
1. Ejecute el comando **servicio azure crear** para crear el servicio de nube.

        azure service create TestService --location uscentral

    Resultados esperados:

        info:    Executing command service create
        info:    Creating cloud service
        data:    Cloud service name TestService
        info:    service create command OK
    
2. Ejecute el comando **crear de azure vm** para crear la máquina virtual. Observe el valor de dirección IP estática privada. La lista que aparece después de la salida explica los parámetros que se utiliza.

        azure vm create -l centralus -n DNS01 -w TestVNet -S "192.168.1.101" TestService bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2 adminuser AdminP@ssw0rd

    Resultados esperados:

        info:    Executing command vm create
        warn:    --vm-size has not been specified. Defaulting to "Small".
        info:    Looking up image bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2
        info:    Looking up virtual network
        info:    Looking up cloud service
        warn:    --location option will be ignored
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Retrieving storage accounts
        info:    Creating VM
        info:    OK
        info:    vm create command OK

    - **-l (o--ubicación)**. Región de Azure donde se creará la máquina virtual. En el escenario, *centralus*.
    - **-n (o nombre de--vm)**. Nombre de la máquina virtual que debe crearse.
    - **-w (o--nombre de red virtual)**. Nombre de la VNet donde se creará la máquina virtual. 
    - **-S (o--ip estática)**. Privado dirección IP estática de la máquina virtual.
    - **TestService**. Nombre del servicio de nube donde se creará la máquina virtual.
    - **bd507d3a70934695bc2128e3e5a255ba__RightImage Windows 2012R2 x64 v14.2**. Imagen que se utiliza para crear la máquina virtual.
    - **adminuser**. Administrador local de la máquina virtual de Windows.
    - **AdminP@ssw0rd**. Contraseña de administrador local de la máquina virtual de Windows.

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Cómo recuperar información de dirección IP privada estática de una máquina virtual
Para ver la información de dirección IP privada estática de la máquina virtual creada con esta secuencia de comandos, ejecute el siguiente comando CLI de Azure y observe el valor de *StaticIP de red*:

    azure vm static-ip show DNS01

Resultados esperados:

    info:    Executing command vm static-ip show
    info:    Getting virtual machines
    data:    Network StaticIP "192.168.1.101"
    info:    vm static-ip show command OK

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Cómo quitar una dirección IP privada estática de una máquina virtual
Para quitar la dirección IP privada se agrega a la máquina virtual en la secuencia de comandos anterior, ejecute el siguiente comando CLI de Azure:
    
    azure vm static-ip remove DNS01

Resultados esperados:

    info:    Executing command vm static-ip remove
    info:    Getting virtual machines
    info:    Reading network configuration
    info:    Updating network configuration
    info:    vm static-ip remove command OK

## <a name="how-to-add-a-static-private-ip-to-an-existing-vm"></a>Cómo agregar una dirección IP privada estática a una máquina virtual existente
Para agregar un estático privado dirección IP de la máquina virtual creada con la secuencia de comandos anterior, la ejecución el siguiente comando:

    azure vm static-ip set DNS01 192.168.1.101

Resultados esperados:

    info:    Executing command vm static-ip set
    info:    Getting virtual machines
    info:    Looking up virtual network
    info:    Reading network configuration
    info:    Updating network configuration
    info:    vm static-ip set command OK

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre las direcciones [IP públicas reservada](virtual-networks-reserved-public-ip.md) .
- Obtenga información sobre las direcciones [IP públicas (ILPIP) de nivel de instancia](virtual-networks-instance-level-public-ip.md) .
- Consulte las [API REST de IP reservada](https://msdn.microsoft.com/library/azure/dn722420.aspx).
