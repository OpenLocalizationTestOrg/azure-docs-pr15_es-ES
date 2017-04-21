<properties
   pageTitle="Crear VNet interconexión con el portal de Azure | Microsoft Azure"
   description="Aprenda a crear una red virtual con el portal de Azure en el Administrador de recursos."
   services="virtual-network"
   documentationCenter=""
   authors="NarayanAnnamalai"
   manager="jefco"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/14/2016"
   ms.author="narayanannamalai;annahar"/>

# <a name="create-a-virtual-network-peering-using-the-azure-portal"></a>Crear una red virtual interconexión con el portal de Azure

[AZURE.INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

Para crear un VNet interconexión basada en el escenario anterior a través del portal de Azure, siga los pasos siguientes.

1. Desde un explorador, vaya a http://portal.azure.com y, si es necesario, inicie sesión con su cuenta de Azure.
2. Para establecer VNET interconexión, debe crear dos vínculos, uno para cada dirección, entre dos VNets. Puede crear vínculo de interconexión VNET para VNET1 a VNET2 en primer lugar. En el portal, haga clic en **Examinar** > **Elija redes virtuales**

    ![Crear VNet interconexión en el portal de Azure](./media/virtual-networks-create-vnetpeering-arm-portal/figure01.png)

3. En el módulo de redes virtuales, elija VNET1, Peerings, haga clic en haga clic en Agregar

    ![Elija interconexión](./media/virtual-networks-create-vnetpeering-arm-portal/figure02.png)

4. En el módulo agregar interconexión, asigne un nombre de vínculo interconexión LinkToVnet2, elija la suscripción y el punto Virtual VNET2 de red, haga clic en Aceptar.

    ![Vínculo a VNet](./media/virtual-networks-create-vnetpeering-arm-portal/figure03.png)

5. Una vez creado este vínculo de interconexión VNET. Puede ver el estado del vínculo como la siguiente:

    ![Estado de los vínculos](./media/virtual-networks-create-vnetpeering-arm-portal/figure04.png)

6. A continuación, cree el vínculo de interconexión VNET para VNET2 a VNET1. En el módulo de redes virtuales, elija VNET2, Peerings, haga clic en haga clic en Agregar

    ![Punto de otro VNet](./media/virtual-networks-create-vnetpeering-arm-portal/figure05.png)

7. En el módulo agregar interconexión, asigne un nombre de vínculo interconexión LinkToVnet1, elija la suscripción y el punto de una red Virtual, haga clic en Aceptar.

    ![Crear el mosaico de una red virtual](./media/virtual-networks-create-vnetpeering-arm-portal/figure06.png)

8. Una vez creado este vínculo de interconexión VNET. Puede ver el estado del vínculo como la siguiente:

    ![Estado de los vínculos final](./media/virtual-networks-create-vnetpeering-arm-portal/figure07.png)

9. Comprobar el estado de LinkToVnet2 y ahora cambia a conectado también.  

    ![Estado de vínculo final 2](./media/virtual-networks-create-vnetpeering-arm-portal/figure08.png)

    > [AZURE.NOTE] Solo se establece interconexión VNET si los vínculos están conectados.

Hay algunas propiedades configurables para cada vínculo:

|Opción|Descripción|Predeterminado|
|:-----|:----------|:------|
|AllowVirtualNetworkAccess|Si el espacio del punto VNet se incluye como parte de la etiqueta Virtual_network de direcciones|Sí|
|AllowForwardedTraffic|Si se aceptan o se descarta el tráfico no origina una VNet peered|No|
|AllowGatewayTransit|Permite que el interlocutor VNet utilizar la puerta de enlace VNet|No|
|UseRemoteGateways|Use la puerta de enlace de su punto VNet. El punto VNet debe tener una puerta de enlace configurado y AllowGatewayTransit está seleccionada. No puede usar esta opción si ha configurado una puerta de enlace|No|

Cada vínculo de interconexión VNet tiene un conjunto de encima de propiedades. Desde el portal, haga clic en el vínculo de interconexión VNet y cambie las opciones disponibles, haga clic en Guardar para que el efecto de cambio.

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

1. Desde un explorador, vaya a http://portal.azure.com y, si es necesario, inicie sesión con su cuenta de Azure.
2. En este ejemplo se utilizará dos suscripciones A y B y dos usuarios usuario y usuario b con privilegios en las suscripciones respectivamente
3. En el portal, haga clic en Examinar, elija redes virtuales. Haga clic en el VNET y haga clic en Agregar.

    ![Escenario 2 examinar](./media/virtual-networks-create-vnetpeering-arm-portal/figure09.png)

4. En el módulo de access agregar, haga clic en seleccionar una función y elija red colaborador, haga clic en Agregar usuarios, escriba el inicio de sesión de usuario b en nombre y haga clic en Aceptar.

    ![RBAC](./media/virtual-networks-create-vnetpeering-arm-portal/figure10.png)

    Esto no es necesario, se puede establecer interconexión incluso si los usuarios individualmente elevar interconexión solicitudes para sus respectivos Vnets como que coincida con las solicitudes. Agregar usuarios con privilegios de lo otro VNet como usuarios en el VNet local facilita la configuración en el portal.

5. A continuación, inicio de sesión al portal de Azure con usuario b Quién es el usuario de privilegio para SubscriptionB. Siga por encima de los pasos para agregar el usuario como colaborador de red.

    ![RBAC2](./media/virtual-networks-create-vnetpeering-arm-portal/figure11.png)

    > [AZURE.NOTE] Puede cerrar sesión e inicie sesión en ambos sesiones de usuario en el explorador para asegurarse de que la autorización está habilitada correctamente.

6. Inicio de sesión en el portal del usuario, desplácese hasta el módulo VNET3, haga clic en Peering, active ' sé mi identificador de recurso "identificación casilla y escriba el recurso para VNET5 en situada debajo de formato.

    / subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.Network/VirtualNetwork/{VNETname}

    ![Id. de recurso](./media/virtual-networks-create-vnetpeering-arm-portal/figure12.png)

7. Inicie sesión en el portal como usuario b y seguir encima de paso para crear el vínculo de interconexión de VNET5 a VNet3.

    ![Id. de recurso 2](./media/virtual-networks-create-vnetpeering-arm-portal/figure13.png)

8. Interconexión se establecerá y cualquier máquina Virtual en VNet3 debería poder comunicarse con cualquier máquina virtual VNet5

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. Como primer paso, vínculos de interconexión VNET desde HubVnet a VNET1. Tenga en cuenta que la opción de permitir el tráfico reenviado no está seleccionada para el vínculo.

    ![Interconexión básico](./media/virtual-networks-create-vnetpeering-arm-portal/figure14.png)

2. Como paso siguiente, se pueden crear vínculos de interconexión de VNET1 a HubVnet. Tenga en cuenta que está seleccionada la opción de permitir reenviado tráfico.

    ![Interconexión básico](./media/virtual-networks-create-vnetpeering-arm-portal/figure15a.png)

3. Una vez establecida la interconexión, puede hacer referencia a este [artículo](virtual-network-create-udr-arm-ps.md) y definir Route(UDR) de definidas por el usuario para redirigir el tráfico de VNet1 a través de un equipo virtual para utilizar su capacidad. Al especificar la dirección de siguiente salto en la ruta, puede configurarlo para la dirección IP del dispositivo virtual en punto VNet HubVNet


[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-asmtoarm-include](../../includes/virtual-networks-create-vnetpeering-scenario-asmtoarm-include.md)]



1. Desde un explorador, vaya a http://portal.azure.com y, si es necesario, inicie sesión con su cuenta de Azure.

2. Para establecer VNET interconexión en este escenario, debe crear solo un vínculo, desde la red virtual en Administrador de recursos de Azure en clásico. Es decir, de **VNET1** a **VNET2**. En el portal, haga clic en **Examinar** > elija **Redes virtuales**

3. En el módulo de redes virtuales, elija **VNET1**. Haga clic en **Peerings**y luego haga clic en **Agregar**.

4. En el módulo agregar interconexión, el nombre del vínculo. Aquí se llama **LinkToVNet2**. En detalles del mismo nivel, seleccione **clásico**.

5. A continuación, elija la suscripción y el punto de una red Virtual **VNET2**. A continuación, haga clic en Aceptar.

    ![Vincular Vnet1 Vnet 2](./media/virtual-networks-create-vnetpeering-arm-portal/figure18.png)

6. Una vez creado este vínculo de interconexión VNet, se peered las dos redes virtuales y podrá ver lo siguiente:

    ![Comprobar la conexión interconexión](./media/virtual-networks-create-vnetpeering-arm-portal/figure19.png)


## <a name="remove-vnet-peering"></a>Quitar VNet interconexión

1.  Desde un explorador, vaya a http://portal.azure.com y, si es necesario, inicie sesión con su cuenta de Azure.
2.  Vaya a módulo de red virtual, haga clic en Peerings, haga clic en el vínculo que desea quitar, haga clic en el botón Eliminar.

    ![Delete1](./media/virtual-networks-create-vnetpeering-arm-portal/figure15.png)

3. Una vez quitar un vínculo de interconexión VNET, el estado de los vínculos del mismo nivel se vaya a desconectado.

    ![Delete2](./media/virtual-networks-create-vnetpeering-arm-portal/figure16.png)

4. En este estado, volver a no puede crear el vínculo hasta que el estado del vínculo punto cambia a iniciado. Se recomienda que quite los vínculos de ambos antes de volver a crear la VNET interconexión.
