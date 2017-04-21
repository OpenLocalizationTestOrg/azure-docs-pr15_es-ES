<properties 
   pageTitle="Controlar el enrutamiento y usar accesorios virtuales con la CLI de Azure en el modelo de implementación clásica | Microsoft Azure"
   description="Obtenga información sobre cómo controlar el enrutamiento de VNets mediante la CLI de Azure en el modelo de implementación clásica"
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
   ms.date="03/15/2016"
   ms.author="jdial" />

#<a name="control-routing-and-use-virtual-appliances-classic-using-the-azure-cli"></a>Controlar el enrutamiento y usar dispositivos virtuales (clásico) mediante la CLI de Azure

[AZURE.INCLUDE [virtual-network-create-udr-classic-selectors-include.md](../../includes/virtual-network-create-udr-classic-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]En este artículo trata sobre el modelo de implementación clásico. También puede [control enrutamiento y utilizar equipos virtuales en el modelo de implementación de administrador de recursos](virtual-network-create-udr-arm-cli.md).

[AZURE.INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

Los comandos de CLI de Azure de ejemplo siguientes esperan un entorno simple ya ha creado basándose en el ejemplo anterior. Si desea ejecutar los comandos que se muestran en este documento, cree el entorno que se muestra en [crear un VNet (clásico) mediante la CLI de Azure](virtual-networks-create-vnet-classic-cli.md).

[AZURE.INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>Crear la UDR para la subred front-end
Para crear la tabla de rutas y la ruta es necesario para la subred front-end basada en el ejemplo anterior, siga los pasos siguientes.

1. Ejecutar el **`azure config mode`** para cambiar al modo clásico.

        azure config mode asm

    Resultado:

        info:    New mode is asm

3. Ejecutar la **`azure network route-table create`** comando para crear una tabla de ruta para la subred front-end.

        azure network route-table create -n UDR-FrontEnd -l uswest

    Resultado:

        info:    Executing command network route-table create
        info:    Creating route table "UDR-FrontEnd"
        info:    Getting route table "UDR-FrontEnd"
        data:    Name                            : UDR-FrontEnd
        data:    Location                        : West US
        info:    network route-table create command OK

    Parámetros:
    - **-l (o--ubicación)**. Región de Azure donde se creará el nuevo GSN. En el escenario, *westus*.
    - **-n (o--nombre)**. Nombre para el nuevo GSN. En el escenario, *GSN-front-end*.

4. Ejecutar la **`azure network route-table route set`** comando para crear una ruta en la tabla de ruta creada anteriormente para enviar todo el tráfico destinado a la subred de back-end (192.168.2.0/24) para **FW1** VM (192.168.0.4).

        azure network route-table route set -r UDR-FrontEnd -n RouteToBackEnd -a 192.168.2.0/24 -t VirtualAppliance -p 192.168.0.4

    Resultado:

        info:    Executing command network route-table route set
        info:    Getting route table "UDR-FrontEnd"
        info:    Setting route "RouteToBackEnd" in a route table "UDR-FrontEnd"
        info:    network route-table route set command OK

    Parámetros:
    - **-r (o--nombre de la tabla de ruta)**. Nombre de la tabla de ruta donde se agregará la ruta. En el escenario, *UDR-front-end*.
    - **-a (o--prefijo de dirección)**. Prefijo de dirección de la subred donde paquetes están destinados a. En el escenario, *192.168.2.0/24*.
    - **-t (o--tipo de salto siguiente)**. Tipo de objeto tráfico se enviarán a. Valores posibles son *VirtualAppliance*, *VirtualNetworkGateway*, *VNETLocal*, *Internet*o *Ninguno*.
    - **-p (o--siguiente salto-dirección ip de**). Dirección IP del siguiente salto. En el escenario, *192.168.0.4*.

5. Ejecutar la **`azure network vnet subnet route-table add`** comando para asociar la tabla de ruta creada anteriormente con la subred **front-end** .

        azure network vnet subnet route-table add -t TestVNet -n FrontEnd -r UDR-FrontEnd

    Resultado:

        info:    Executing command network vnet subnet route-table add
        info:    Looking up the subnet "FrontEnd"
        info:    Looking up network configuration
        info:    Looking up network gateway route tables in virtual network "TestVNet" subnet "FrontEnd"
        info:    Associating route table "UDR-FrontEnd" and subnet "FrontEnd"
        info:    Looking up network gateway route tables in virtual network "TestVNet" subnet "FrontEnd"
        data:    Route table name                : UDR-FrontEnd
        data:      Location                      : West US
        data:      Routes:
        info:    network vnet subnet route-table add command OK 

    Parámetros:
    - **-t (o nombre--vnet)**. Nombre de la VNet donde se encuentra la subred. En el escenario, *TestVNet*.
    - **- n (o--nombre de la subred**. Nombre de la subred que se agrega a la tabla de rutas. En el escenario, *front-end*.
 
## <a name="create-the-udr-for-the-back-end-subnet"></a>Crear la UDR para la subred back-end
Para crear la tabla de rutas y la ruta es necesario para la subred de back-end basada en el ejemplo anterior, siga los pasos siguientes.

3. Ejecutar la **`azure network route-table create`** comando para crear una tabla de ruta para la subred back-end.

        azure network route-table create -n UDR-BackEnd -l uswest

4. Ejecutar la **`azure network route-table route set`** comando para crear una ruta en la tabla de ruta creada anteriormente para enviar todo el tráfico destinado a la subred de front-end (192.168.1.0/24) para **FW1** VM (192.168.0.4).

        azure network route-table route set -r UDR-BackEnd -n RouteToFrontEnd -a 192.168.1.0/24 -t VirtualAppliance -p 192.168.0.4

5. Ejecutar la **`azure network vnet subnet route-table add`** comando para asociar la tabla de ruta creada anteriormente con la subred de **back-end** .

        azure network vnet subnet route-table add -t TestVNet -n BackEnd -r UDR-BackEnd

