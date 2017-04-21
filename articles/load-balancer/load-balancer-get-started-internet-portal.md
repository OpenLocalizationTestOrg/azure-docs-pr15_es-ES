<properties
   pageTitle="Crear un equilibrador de carga a través de Internet en el Administrador de recursos con el portal de Azure | Microsoft Azure"
   description="Obtenga información sobre cómo crear un equilibrador de carga a través de Internet en el Administrador de recursos con el portal de Azure"
   services="load-balancer"
   documentationCenter="na"
   authors="anavinahar"
   manager="narayan"
   editor=""
   tags="azure-resource-manager"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/14/2016"
   ms.author="annahar" />

# <a name="creating-an-internet-facing-load-balancer-using-the-azure-portal"></a>Crear un equilibrador de carga a través de Internet con el portal de Azure

[AZURE.INCLUDE [load-balancer-get-started-internet-arm-selectors-include.md](../../includes/load-balancer-get-started-internet-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]En este artículo trata sobre el modelo de implementación de administrador de recursos. También puede [aprender a crear un equilibrador de carga a través de Internet mediante la implementación clásica](load-balancer-get-started-internet-classic-portal.md)

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

Esto abarca la secuencia de tareas individuales que tiene que hacer para crear un equilibrador de carga y se explica detalladamente lo que se hace para conseguir el objetivo.

## <a name="what-is-required-to-create-an-internet-facing-load-balancer"></a>¿Qué se necesita para crear un equilibrador de carga a través de Internet?

Debe crear y configurar los objetos siguientes para implementar un equilibrador de carga.

- Configuración de IP front-end - contiene las direcciones IP públicas para el tráfico de red entrante.

- Grupo de direcciones de back-end - contiene interfaces de red (NIC) para que los equipos virtuales recibir el tráfico de red de equilibrador de carga.

- Reglas de equilibrio de carga - contiene reglas de asignar un puerto público en el equilibrador de carga al puerto en el grupo de direcciones de back-end.

- NAT reglas de entrada: contiene reglas de la asignación de un puerto público en el equilibrador de carga a un puerto para una máquina virtual específica en el grupo de direcciones de back-end.

- Sondeos: contiene sondeos de estado que usa para comprobar la disponibilidad de instancias de máquinas virtuales en el grupo de direcciones de back-end.

Puede obtener más información acerca de carga de componentes de equilibrador con el Administrador de recursos de Azure en [Azure Administrador de recursos de soporte técnico para equilibrador de carga](load-balancer-arm.md).


## <a name="set-up-a-load-balancer-in-azure-portal"></a>Configurar un equilibrador de carga en el portal de Azure

> [AZURE.IMPORTANT] En este ejemplo, se supone que tiene una red virtual denominada **myVNet**. Consulte [crear una red virtual](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) para realizar esta acción. También se supone hay una subred dentro de **myVNet** denominado **Libras subred ser** y dos máquinas virtuales denominado **web1** y **web2** respectivamente en el mismo conjunto de disponibilidad denominado **myAvailSet** en **myVNet**. Consulte [este vínculo](../virtual-machines/virtual-machines-windows-hero-tutorial.md) para crear máquinas virtuales.


1. Desde un explorador, vaya al portal de Azure: [http://portal.azure.com](http://portal.azure.com) e inicie sesión con su cuenta de Azure.

2. En la parte superior izquierda de la pantalla, seleccione **nuevo** > **redes** > **equilibrador de carga.**

3. En el módulo **equilibrador de carga de crear** , escriba un nombre para el equilibrador de carga. Aquí se llama **myLoadBalancer**.

4. En **tipo**, seleccione **público**.

5. En **dirección IP pública**, cree un nuevo IP pública denominado **myPublicIP**.

6. En el grupo de recursos, seleccione **myRG**. A continuación, seleccione una **ubicación**de adecuada y, a continuación, haga clic en **Aceptar**. El equilibrador de carga se iniciará a implementar y tardará unos minutos en completar correctamente la implementación.

![Actualizar grupo de recursos del equilibrador de carga](./media/load-balancer-get-started-internet-portal/1-load-balancer.png)


## <a name="create-a-back-end-address-pool"></a>Crear un grupo de direcciones de back-end

1. Una vez que se ha implementado correctamente el equilibrador de carga, seleccione desde dentro de los recursos. En configuración, seleccione grupos de back-end. Escriba un nombre para la agrupación de back-end. A continuación, haga clic en el botón **Agregar** hacia la parte superior del módulo que se muestra.

2. Haga clic en **Agregar una máquina virtual** en el módulo de **grupo de back-end de agregar** .  Seleccione **Elegir un conjunto de disponibilidad** en **conjunto de disponibilidad** y **myAvailSet**. A continuación, seleccione **Elegir las máquinas virtuales** en la sección de máquinas virtuales de la hoja y haga clic en **web1** y **web2**, las dos VM creadas equilibrio de carga. Garantizar que ambos tengan azules marcas de verificación a la izquierda, como se muestra en la imagen siguiente. A continuación, haga clic en **Seleccionar** en esa hoja seguido Aceptar en el módulo de **máquinas virtuales elija** y, a continuación, en **Aceptar** en el módulo **Agregar conjunto de back-end** .

    ![Agregar al grupo de direcciones de back-end- ](./media/load-balancer-get-started-internet-portal/3-load-balancer-backend-02.png)

3. Asegúrese de que sus notificaciones de la lista desplegable tiene una actualización referente a guardar el fondo de back-end del equilibrador de carga además de actualizar la interfaz de red para las VM **web1** y **web2**.


## <a name="create-a-probe-lb-rule-and-nat-rules"></a>Crear un sondeo, Libras regla y reglas NAT

1. Crear un sondeo de estado.

    En configuración del equilibrador de carga de, seleccione sondeos. A continuación, haga clic en **Agregar** situado en la parte superior de la hoja.

    Hay dos formas de configurar un sondeo: HTTP o TCP. Este ejemplo muestra HTTP, pero TCP se pueden configurar de forma similar.
    Actualizar la información necesaria. Como se mencionó, **myLoadBalancer** cargará tráfico de saldo en el puerto 80. La ruta seleccionado es HealthProbe.aspx, intervalo es 15 segundos y umbral negativa es 2. Una vez que haya terminado, haga clic en **Aceptar** para crear el sondeo.

    Sitúe el puntero sobre 'i' icono para obtener más información sobre estas configuraciones individuales y cómo puede cambiarse para satisfacer sus necesidades.

    ![Adición de un sondeo](./media/load-balancer-get-started-internet-portal/4-load-balancer-probes.png)

2. Crear una regla de equilibrador de carga.

    Haga clic en reglas de la sección Configuración de un equilibrador de carga de equilibrio de carga. En el módulo nuevo, haga clic en **Agregar**. Nombre de regla. Aquí, es HTTP. Elija los puertos front-end y back-end. A continuación, se elige 80 ambas. Elija **back-end de Libras** como su grupo de back-end y la creado previamente **HealthProbe** como el sondeo. Pueden establecer otras configuraciones según sus necesidades. A continuación, haga clic en Aceptar para guardar la regla de equilibrio de carga.

    ![Agregar una regla de equilibrio de carga](./media/load-balancer-get-started-internet-portal/5-load-balancing-rules.png)

3. Crear reglas NAT entrante

    Haga clic en reglas de entrada NAT en la sección Configuración de un equilibrador de carga. En el módulo de nuevo, haga clic en **Agregar**. A continuación, nombre de regla NAT entrante. Aquí se llama **inboundNATrule1**. El destino debe ser la dirección IP pública creado previamente. Seleccione personalizado en el servicio y seleccione el protocolo que desea usar. Aquí se selecciona TCP. Especifique el puerto, 3441 y el puerto de destino, en este caso, 3389. a continuación, haga clic en Aceptar para guardar esta regla.

    Una vez creada la primera regla, repita este paso para la segunda regla NAT entrante llamada inboundNATrule2 de puerto 3442 al puerto de destino 3389.

    ![Agregar una regla NAT entrante](./media/load-balancer-get-started-internet-portal/6-load-balancer-inbound-nat-rules.png)

## <a name="remove-a-load-balancer"></a>Quitar un equilibrador de carga

Para eliminar un equilibrador de carga, seleccione el equilibrador de carga que desea quitar. En el módulo de *Equilibrador de carga* , haga clic en **Eliminar** situado en la parte superior de la hoja. A continuación, seleccione **Sí** cuando se le solicite.

## <a name="next-steps"></a>Pasos siguientes

[Empezar a configurar un equilibrador de carga interno](load-balancer-get-started-ilb-arm-cli.md)

[Configurar un modo de distribución del equilibrador de carga](load-balancer-distribution-mode.md)

[Configurar los valores de tiempo de espera TCP inactivas para el equilibrador de carga](load-balancer-tcp-idle-timeout.md)
