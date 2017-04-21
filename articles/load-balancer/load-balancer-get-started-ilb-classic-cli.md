<properties
   pageTitle="Crear un equilibrador de carga interno mediante la CLI de Azure en el modelo de implementación clásica | Microsoft Azure"
   description="Aprenda a crear un equilibrador de carga interno mediante la CLI de Azure en el modelo de implementación clásica"
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

# <a name="get-started-creating-an-internal-load-balancer-classic-using-the-azure-cli"></a>Empezar a crear un equilibrador de carga interno (clásico) mediante la CLI de Azure

[AZURE.INCLUDE [load-balancer-get-started-ilb-classic-selectors-include.md](../../includes/load-balancer-get-started-ilb-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]Obtenga información sobre cómo llevar a [cabo estos pasos con el modelo de administrador de recursos](load-balancer-get-started-ilb-arm-cli.md).

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]


## <a name="to-create-an-internal-load-balancer-set-for-virtual-machines"></a>Para crear un equilibrador de carga interno para máquinas virtuales

Para crear un conjunto de equilibrador de carga interno y los servidores que va a enviar el tráfico a él, haga lo siguiente:

1. Crear una instancia de interno equilibrio de carga que será el extremo de tráfico entrante carga equilibrada en todos los servidores de un conjunto de equilibrio de carga.

1. Agregar extremos correspondiente a los equipos virtuales que va a recibir el tráfico entrante.

1. Configure los servidores que va a enviar el tráfico de carga equilibrada para enviar el tráfico a la dirección IP (VIP) virtual de la instancia de equilibrio de carga interno.

## <a name="step-by-step-creating-an-internal-load-balancer-using-cli"></a>Paso a paso creando un equilibrador de carga interno mediante CLI

Esta guía muestra cómo crear un equilibrador de carga interno basado en el ejemplo anterior.

1. Si nunca ha usado CLI de Azure, vea [instalar y configurar la CLI de Azure](../../articles/xplat-cli-install.md) y siga las instrucciones hasta el punto donde seleccionar su cuenta de Azure y suscripción.

2. Ejecute el comando de **modo de azure config** para cambiar al modo clásico, como se muestra a continuación.

        azure config mode asm

    Resultados esperados:

        info:    New mode is asm


## <a name="create-endpoint-and-load-balancer-set"></a>Crear extremo y conjunto de equilibrador de carga

El escenario asume la máquinas virtuales "DB1" y "DB2" en un servicio de nube denominado "mytestcloud". Ambos equipos virtuales está utilizando una red virtual llamada mi "testvnet" con subred "subred-1".

Esta guía creará un conjunto de equilibrador de carga interno utilizan el puerto 1433 como puerto privado y 1433 como puerto local.

Este es un escenario común donde tiene máquinas virtuales SQL en el back-end mediante un equilibrador de carga interno para garantizar que los servidores de base de datos no se exponen directamente mediante una dirección IP pública.


### <a name="step-1"></a>Paso 1

Crear conjunto mediante un equilibrador de carga interno `azure network service internal-load-balancer add`.

     azure service internal-load-balancer add -r mytestcloud -n ilbset -t subnet-1 -a 192.168.2.7

Parámetros que se utilizan:

**-r** - nombre del servicio de nube<BR>
**-n** - nombre del equilibrador de carga interno<BR>
**-t** - nombre de la subred (misma subred por las máquinas virtuales que se agregará al equilibrador de carga interno)<BR>
**-un** - (opcional) agregue una dirección IP privada estática<BR>

Consulte `azure service internal-load-balancer --help` para obtener más información.

Puede comprobar las propiedades del equilibrador de carga interno con el comando `azure service internal-load-balancer list` *nombre de servicio de nube*.

Sigue aquí un ejemplo del resultado:

    azure service internal-load-balancer list my-testcloud
    info:    Executing command service internal-load-balancer list
    + Getting cloud service deployment
    data:    Name    Type     SubnetName  StaticVirtualNetworkIPAddress
    data:    ------  -------  ----------  -----------------------------
    data:    ilbset  Private  subnet-1    192.168.2.7
    info:    service internal-load-balancer list command OK


## <a name="step-2"></a>Paso 2

Configurar el conjunto de equilibrador de carga interno al agregar el primer extremo. Asociará el extremo, la máquina virtual y el puerto de sondeo para el conjunto de equilibrador de carga interno en este paso.

    azure vm endpoint create db1 1433 -k 1433 tcp -t 1433 -r tcp -e 300 -f 600 -i ilbset

Parámetros que se utilizan:

**-k** - puerto local máquina virtual<BR>
**-t** - puerto de sondeo<BR>
**-r** - protocolo de sondeo<BR>
**-e** - intervalo de sondeo en segundos<BR>
**-f** - intervalo de tiempo de espera en segundos <BR>
**-i** - nombre del equilibrador de carga interno <BR>


## <a name="step-3"></a>Paso 3

Compruebe la configuración del equilibrador de carga con `azure vm show` *nombre de máquina virtual*

    azure vm show DB1

El resultado será:

        azure vm show DB1
    info:    Executing command vm show
    + Getting virtual machines
    data:    DNSName "mytestcloud.cloudapp.net"
    data:    Location "East US 2"
    data:    VMName "DB1"
    data:    IPAddress "192.168.2.4"
    data:    InstanceStatus "ReadyRole"
    data:    InstanceSize "Standard_D1"
    data:    Image "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-20151022-en.us-127GB.vhd"
    data:    OSDisk hostCaching "ReadWrite"
    data:    OSDisk name "db1-DB1-0-201511120457370846"
    data:    OSDisk mediaLink "https://XXXX.blob.core.windows.net/vhd"
    data:    OSDisk sourceImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-20151022-en.us-127GB.vhd"
    data:    OSDisk operatingSystem "Windows"
    data:    OSDisk iOType "Standard"
    data:    ReservedIPName ""
    data:    VirtualIPAddresses 0 address "137.116.64.107"
    data:    VirtualIPAddresses 0 name "db1ContractContract"
    data:    VirtualIPAddresses 0 isDnsProgrammed true
    data:    VirtualIPAddresses 1 address "192.168.2.7"
    data:    VirtualIPAddresses 1 name "ilbset"
    data:    Network Endpoints 0 localPort 5986
    data:    Network Endpoints 0 name "PowerShell"
    data:    Network Endpoints 0 port 5986
    data:    Network Endpoints 0 protocol "tcp"
    data:    Network Endpoints 0 virtualIPAddress "137.116.64.107"
    data:    Network Endpoints 0 enableDirectServerReturn false
    data:    Network Endpoints 1 localPort 3389
    data:    Network Endpoints 1 name "Remote Desktop"
    data:    Network Endpoints 1 port 60173
    data:    Network Endpoints 1 protocol "tcp"
    data:    Network Endpoints 1 virtualIPAddress "137.116.64.107"
    data:    Network Endpoints 1 enableDirectServerReturn false
    data:    Network Endpoints 2 localPort 1433
    data:    Network Endpoints 2 name "tcp-1433-1433"
    data:    Network Endpoints 2 port 1433
    data:    Network Endpoints 2 loadBalancerProbe port 1433
    data:    Network Endpoints 2 loadBalancerProbe protocol "tcp"
    data:    Network Endpoints 2 loadBalancerProbe intervalInSeconds 300
    data:    Network Endpoints 2 loadBalancerProbe timeoutInSeconds 600
    data:    Network Endpoints 2 protocol "tcp"
    data:    Network Endpoints 2 virtualIPAddress "192.168.2.7"
    data:    Network Endpoints 2 enableDirectServerReturn false
    data:    Network Endpoints 2 loadBalancerName "ilbset"
    info:    vm show command OK


## <a name="create-a-remote-desktop-endpoint-for-a-virtual-machine"></a>Crear un extremo de escritorio remoto para una máquina virtual

Puede crear un extremo de escritorio remoto para reenviar el tráfico de red desde un puerto público a un puerto local para una máquina virtual específica utilizando `azure vm endpoint create`.

    azure vm endpoint create web1 54580 -k 3389


## <a name="remove-virtual-machine-from-load-balancer"></a>Quitar máquina virtual del equilibrador de carga

Puede quitar una máquina virtual de un equilibrador de carga interno establecer eliminando el extremo asociado. Una vez que se elimina el extremo, la máquina virtual no pertenece al equilibrador de carga establecer ya.

 En el ejemplo anterior, puede quitar el extremo creado para máquina virtual "DB1" equilibrador de carga interno "ilbset" mediante el comando `azure vm endpoint delete`.

    azure vm endpoint delete DB1 tcp-1433-1433


Consulte `azure vm endpoint --help` para obtener más información.


## <a name="next-steps"></a>Pasos siguientes

[Configurar un modo de distribución del equilibrador de carga mediante la afinidad IP de origen](load-balancer-distribution-mode.md)

[Configurar los valores de tiempo de espera TCP inactivas para el equilibrador de carga](load-balancer-tcp-idle-timeout.md)