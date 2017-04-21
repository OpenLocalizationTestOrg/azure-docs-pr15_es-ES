<properties
   pageTitle="Empezar a crear una Internet opuestas equilibrador de carga en el modelo de implementación clásica con CLI Azure | Microsoft Azure"
   description="Aprenda a crear un opuestas equilibrador de carga en el modelo de implementación clásica con Azure CLI de Internet"
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

# <a name="get-started-creating-an-internet-facing-load-balancer-classic-in-the-azure-cli"></a>Empezar a crear un opuestas equilibrador de carga (clásico) en Azure CLI de Internet

[AZURE.INCLUDE [load-balancer-get-started-internet-classic-selectors-include.md](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]En este artículo trata sobre el modelo de implementación clásico. También puede [obtener información sobre cómo crear un Internet opuestas equilibrador de carga con el Administrador de recursos de Azure](load-balancer-get-started-internet-arm-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]


## <a name="step-by-step-creating-an-internet-facing-load-balancer-using-cli"></a>Paso a paso creando un opuestas equilibrador de carga con CLI de Internet

Esta guía muestra cómo crear un equilibrador de carga de Internet basado en el ejemplo anterior.

1. Si nunca ha usado CLI de Azure, vea [instalar y configurar la CLI de Azure](../../articles/xplat-cli-install.md) y siga las instrucciones hasta el punto donde seleccionar su cuenta de Azure y suscripción.

2. Ejecute el comando de **modo de azure config** para cambiar al modo clásico, como se muestra a continuación.

        azure config mode asm

    Resultados esperados:

        info:    New mode is asm


## <a name="create-endpoint-and-load-balancer-set"></a>Crear extremo y conjunto de equilibrador de carga

El escenario supone que los equipos virtuales "web1" y "web2" se crearon.
Esta guía creará un conjunto de equilibrador de carga con puertos 80 como puerto público y 80 como puerto local. Un puerto de sondeo también está había configurado en el puerto 80 y con el nombre del conjunto de equilibrador de carga "lbset".


### <a name="step-1"></a>Paso 1

Crear el primer extremo y establecido con equilibrador de carga `azure network vm endpoint create` para máquina virtual "web1".

    azure vm endpoint create web1 80 -k 80 -o tcp -t 80 -b lbset

Parámetros que se utilizan:

**-k** - puerto local máquina virtual<br>
**-o** - protocolo<BR>
**-t** - puerto de sondeo<BR>
**-b** - nombre del equilibrador de carga<BR>

## <a name="step-2"></a>Paso 2

Agregar un segundo equipo virtual "web2" para el conjunto de equilibrador de carga.

    azure vm endpoint create web2 80 -k 80 -o tcp -t 80 -b lbset

## <a name="step-3"></a>Paso 3

Compruebe la configuración del equilibrador de carga con `azure vm show` .

    azure vm show web1

El resultado será:

    data:    DNSName "contoso.cloudapp.net"
    data:    Location "East US"
    data:    VMName "web1"
    data:    IPAddress "10.0.0.5"
    data:    InstanceStatus "ReadyRole"
    data:    InstanceSize "Standard_D1"
    data:    Image "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-2015
    6-en.us-127GB.vhd"
    data:    OSDisk hostCaching "ReadWrite"
    data:    OSDisk name "joaoma-1-web1-0-201509251804250879"
    data:    OSDisk mediaLink "https://XXXXXXXXXXXXXXX.blob.core.windows.
    /vhds/joaomatest-web1-2015-09-25.vhd"
    data:    OSDisk sourceImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Se
    r-2012-R2-20150916-en.us-127GB.vhd"
    data:    OSDisk operatingSystem "Windows"
    data:    OSDisk iOType "Standard"
    data:    ReservedIPName ""
    data:    VirtualIPAddresses 0 address "XXXXXXXXXXXXXXXX"
    data:    VirtualIPAddresses 0 name "XXXXXXXXXXXXXXXXXXXX"
    data:    VirtualIPAddresses 0 isDnsProgrammed true
    data:    Network Endpoints 0 loadBalancedEndpointSetName "lbset"
    data:    Network Endpoints 0 localPort 80
    data:    Network Endpoints 0 name "tcp-80-80"
    data:    Network Endpoints 0 port 80
    data:    Network Endpoints 0 loadBalancerProbe port 80
    data:    Network Endpoints 0 loadBalancerProbe protocol "tcp"
    data:    Network Endpoints 0 loadBalancerProbe intervalInSeconds 15
    data:    Network Endpoints 0 loadBalancerProbe timeoutInSeconds 31
    data:    Network Endpoints 0 protocol "tcp"
    data:    Network Endpoints 0 virtualIPAddress "XXXXXXXXXXXX"
    data:    Network Endpoints 0 enableDirectServerReturn false
    data:    Network Endpoints 1 localPort 5986
    data:    Network Endpoints 1 name "PowerShell"
    data:    Network Endpoints 1 port 5986
    data:    Network Endpoints 1 protocol "tcp"
    data:    Network Endpoints 1 virtualIPAddress "XXXXXXXXXXXX"
    data:    Network Endpoints 1 enableDirectServerReturn false
    data:    Network Endpoints 2 localPort 3389
    data:    Network Endpoints 2 name "Remote Desktop"
    data:    Network Endpoints 2 port 58081
    info:    vm show command OK

## <a name="create-a-remote-desktop-endpoint-for-a-virtual-machine"></a>Crear un extremo de escritorio remoto para una máquina virtual

Puede crear un extremo de escritorio remoto para reenviar el tráfico de red desde un puerto público a un puerto local para una máquina virtual específica utilizando `azure vm endpoint create`.

    azure vm endpoint create web1 54580 -k 3389


## <a name="remove-virtual-machine-from-load-balancer"></a>Quitar máquina virtual del equilibrador de carga

Tendrá que eliminar el extremo asociado al equilibrador de carga de la máquina virtual. Cuando se quita el extremo, la máquina virtual no pertenece al establecer ya del equilibrador de carga.

 En el ejemplo anterior, puede quitar el extremo creado para máquina virtual "web1" de equilibrador de carga con el comando "lbset" `azure vm endpoint delete`.

    azure vm endpoint delete web1 tcp-80-80


>[AZURE.NOTE] Puede explorar más opciones para administrar los extremos con el comando`azure vm endpoint --help`


## <a name="next-steps"></a>Pasos siguientes

[Empezar a configurar un equilibrador de carga interno](load-balancer-get-started-ilb-arm-ps.md)

[Configurar un modo de distribución del equilibrador de carga](load-balancer-distribution-mode.md)

[Configurar los valores de tiempo de espera TCP inactivas para el equilibrador de carga](load-balancer-tcp-idle-timeout.md)

