<properties
   pageTitle="Cómo crear NSGs en modo clásico mediante la CLI de Azure | Microsoft Azure"
   description="Obtenga información sobre cómo crear e implementar NSGs en modo clásico mediante la CLI de Azure"
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
   ms.date="02/02/2016"
   ms.author="jdial" />

# <a name="how-to-create-nsgs-classic-in-the-azure-cli"></a>Cómo crear NSGs (clásico) en el CLI de Azure

[AZURE.INCLUDE [virtual-networks-create-nsg-selectors-classic-include](../../includes/virtual-networks-create-nsg-selectors-classic-include.md)]

[AZURE.INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]En este artículo trata sobre el modelo de implementación clásico. También puede [crear NSGs en el modelo de implementación de administrador de recursos](virtual-networks-create-nsg-arm-cli.md).

[AZURE.INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

Los comandos de CLI de Azure de ejemplo siguientes esperan un entorno simple ya ha creado basándose en el ejemplo anterior. Si desea ejecutar los comandos que se muestran en este documento, cree primero el entorno de prueba mediante la [creación de un VNet](virtual-networks-create-vnet-classic-cli.md).

## <a name="how-to-create-the-nsg-for-the-front-end-subnet"></a>Cómo crear la GSN para la subred front-end
Para crear un GSN denominado con nombre **Front-end de GSN** según el escenario anterior, siga los pasos siguientes.

1. Si nunca ha usado CLI de Azure, vea [instalar y configurar la CLI de Azure](../xplat-cli-install.md) y siga las instrucciones hasta el punto donde seleccionar su cuenta de Azure y suscripción.

2. Ejecutar la **`azure config mode`** comando para cambiar al modo clásico, como se muestra a continuación.

        azure config mode asm

    Resultados esperados:

        info:    New mode is asm

3. Ejecutar la **`azure network nsg create`** comando para crear un GSN.

        azure network nsg create -l uswest -n NSG-FrontEnd

    Resultados esperados:

        info:    Executing command network nsg create
        info:    Creating a network security group "NSG-FrontEnd"
        info:    Looking up the network security group "NSG-FrontEnd"
        data:    Name                            : NSG-FrontEnd
        data:    Location                        : West US
        data:    Security group rules:
        data:    Name                               Source IP           Source Port  Destination IP   Destination Port  Protocol  Type      Action  Prior
        ity  Default
        data:    ---------------------------------  ------------------  -----------  ---------------  ----------------  --------  --------  ------  -----
        ---  -------
        data:    ALLOW VNET OUTBOUND                VIRTUAL_NETWORK     *            VIRTUAL_NETWORK  *                 *         Outbound  Allow   65000
             true   
        data:    ALLOW VNET INBOUND                 VIRTUAL_NETWORK     *            VIRTUAL_NETWORK  *                 *         Inbound   Allow   65000
             true   
        data:    ALLOW AZURE LOAD BALANCER INBOUND  AZURE_LOADBALANCER  *            *                *                 *         Inbound   Allow   65001
             true   
        data:    ALLOW INTERNET OUTBOUND            *                   *            INTERNET         *                 *         Outbound  Allow   65001
             true   
        data:    DENY ALL OUTBOUND                  *                   *            *                *                 *         Outbound  Deny    65500
             true   
        data:    DENY ALL INBOUND                   *                   *            *                *                 *         Inbound   Deny    65500
             true   
        info:    network nsg create command OK

    Parámetros:

    - **-l (o--ubicación)**. Región de Azure donde se creará el nuevo GSN. En el escenario, *westus*.
    - **-n (o--nombre)**. Nombre para el nuevo GSN. En el escenario, *GSN-front-end*.

4. Ejecutar la **`azure network nsg rule create`** comando para crear una regla que permita el acceso al puerto 3389 (RDP) de Internet.

        azure network nsg rule create -a NSG-FrontEnd -n rdp-rule -c Allow -p Tcp -r Inbound -y 100 -f Internet -o * -e * -u 3389

    Resultados esperados:

        info:    Executing command network nsg rule create
        info:    Looking up the network security group "NSG-FrontEnd"
        info:    Creating a network security rule "rdp-rule"
        info:    Looking up the network security group "NSG-FrontEnd"
        data:    Name                            : rdp-rule
        data:    Source address prefix           : INTERNET
        data:    Source Port                     : *
        data:    Destination address prefix      : *
        data:    Destination Port                : 3389
        data:    Protocol                        : TCP
        data:    Type                            : Inbound
        data:    Action                          : Allow
        data:    Priority                        : 100
        info:    network nsg rule create command OK

    Parámetros:

    - **-a (o--GSN nombre)**. Nombre de la NSG en el que se creará la regla. En el escenario, *GSN-front-end*.
    - **-n (o--nombre)**. Nombre para la nueva regla. En el escenario, *rdp regla*.
    - **-c (o--acción)**. Nivel de acceso para la regla (denegar o permitir).
    - **-p (o--protocolo)**. Protocolo (Tcp, Udp, o *) para la regla.
    - **-r (o--tipo)**. Dirección de conexión (entrante o saliente).
    - **-y (o--prioridad)**. Prioridad de la regla.
    - **-f (o--prefijo de dirección de origen)**. Prefijo de dirección de origen en CIDR o con las etiquetas predeterminadas.
    - **-o (o: rango de puerto de origen)**. Puerto de origen, o rango.
    - **-e (o--prefijo de dirección de destino)**. Prefijo de dirección de destino en CIDR o con las etiquetas predeterminadas.
    - **-u (o: rango de puerto de destino)**. Puerto de destino, o rango.

5. Ejecutar la **`azure network nsg rule create`** comando para crear una regla que permita el acceso al puerto 80 (HTTP) de Internet.

        azure network nsg rule create -a NSG-FrontEnd -n web-rule -c Allow -p Tcp -r Inbound -y 200 -f Internet -o * -e * -u 80

    Putput esperado:

        info:    Executing command network nsg rule create
        info:    Looking up the network security group "NSG-FrontEnd"
        info:    Creating a network security rule "web-rule"
        info:    Looking up the network security group "NSG-FrontEnd"
        data:    Name                            : web-rule
        data:    Source address prefix           : INTERNET
        data:    Source Port                     : *
        data:    Destination address prefix      : *
        data:    Destination Port                : 80
        data:    Protocol                        : TCP
        data:    Type                            : Inbound
        data:    Action                          : Allow
        data:    Priority                        : 200
        info:    network nsg rule create command OK

6. Ejecutar la **`azure network nsg subnet add`** comando vincular la GSN a la subred front-end.

        azure network nsg subnet add -a NSG-FrontEnd --vnet-name TestVNet --subnet-name FrontEnd

    Resultados esperados:

        info:    Executing command network nsg subnet add
        info:    Looking up the network security group "NSG-FrontEnd"
        info:    Looking up the subnet "FrontEnd"
        info:    Looking up network configuration
        info:    Creating a network security group "NSG-FrontEnd"
        info:    network nsg subnet add command OK

## <a name="how-to-create-the-nsg-for-the-back-end-subnet"></a>Cómo crear la GSN para la subred back-end
Para crear un GSN denominado con nombre *GSN-back-end* basado en el ejemplo anterior, siga los pasos siguientes.

3. Ejecutar la **`azure network nsg create`** comando para crear un GSN.

        azure network nsg create -l uswest -n NSG-BackEnd

    Resultados esperados:

        info:    Executing command network nsg create
        info:    Creating a network security group "NSG-BackEnd"
        info:    Looking up the network security group "NSG-BackEnd"
        data:    Name                            : NSG-BackEnd
        data:    Location                        : West US
        data:    Security group rules:
        data:    Name                               Source IP           Source Port  Destination IP   Destination Port  Protocol  Type      Action  Prior
        ity  Default
        data:    ---------------------------------  ------------------  -----------  ---------------  ----------------  --------  --------  ------  -----
        ---  -------
        data:    ALLOW VNET OUTBOUND                VIRTUAL_NETWORK     *            VIRTUAL_NETWORK  *                 *         Outbound  Allow   65000
             true   
        data:    ALLOW VNET INBOUND                 VIRTUAL_NETWORK     *            VIRTUAL_NETWORK  *                 *         Inbound   Allow   65000
             true   
        data:    ALLOW AZURE LOAD BALANCER INBOUND  AZURE_LOADBALANCER  *            *                *                 *         Inbound   Allow   65001
             true   
        data:    ALLOW INTERNET OUTBOUND            *                   *            INTERNET         *                 *         Outbound  Allow   65001
             true   
        data:    DENY ALL OUTBOUND                  *                   *            *                *                 *         Outbound  Deny    65500
             true   
        data:    DENY ALL INBOUND                   *                   *            *                *                 *         Inbound   Deny    65500
             true   
        info:    network nsg create command OK

    Parámetros:

    - **-l (o--ubicación)**. Región de Azure donde se creará el nuevo GSN. En el escenario, *westus*.
    - **-n (o--nombre)**. Nombre para el nuevo GSN. En el escenario, *GSN-front-end*.

4. Ejecutar la **`azure network nsg rule create`** comando para crear una regla que permite el acceso al puerto 1433 (SQL) desde la subred front-end.

        azure network nsg rule create -a NSG-BackEnd -n sql-rule -c Allow -p Tcp -r Inbound -y 100 -f 192.168.1.0/24 -o * -e * -u 1433

    Resultados esperados:

        info:    Executing command network nsg rule create
        info:    Looking up the network security group "NSG-BackEnd"
        info:    Creating a network security rule "sql-rule"
        info:    Looking up the network security group "NSG-BackEnd"
        data:    Name                            : sql-rule
        data:    Source address prefix           : 192.168.1.0/24
        data:    Source Port                     : *
        data:    Destination address prefix      : *
        data:    Destination Port                : 1433
        data:    Protocol                        : TCP
        data:    Type                            : Inbound
        data:    Action                          : Allow
        data:    Priority                        : 100
        info:    network nsg rule create command OK


5. Ejecutar la **`azure network nsg rule create`** comando para crear una regla que deniegue el acceso a Internet.

        azure network nsg rule create -a NSG-BackEnd -n web-rule -c Deny -p Tcp -r Outbound -y 200 -f * -o * -e Internet -u 80

    Putput esperado:

        info:    Executing command network nsg rule create
        info:    Looking up the network security group "NSG-BackEnd"
        info:    Creating a network security rule "web-rule"
        info:    Looking up the network security group "NSG-BackEnd"
        data:    Name                            : web-rule
        data:    Source address prefix           : *
        data:    Source Port                     : *
        data:    Destination address prefix      : INTERNET
        data:    Destination Port                : 80
        data:    Protocol                        : TCP
        data:    Type                            : Outbound
        data:    Action                          : Deny
        data:    Priority                        : 200
        info:    network nsg rule create command OK

6. Ejecutar la **`azure network nsg subnet add`** comando vincular la GSN a la subred back-end.

        azure network nsg subnet add -a NSG-BackEnd --vnet-name TestVNet --subnet-name BackEnd

    Resultados esperados:

        info:    Executing command network nsg subnet add
        info:    Looking up the network security group "NSG-BackEndX"
        info:    Looking up the subnet "BackEnd"
        info:    Looking up network configuration
        info:    Creating a network security group "NSG-BackEndX"
        info:    network nsg subnet add command OK
