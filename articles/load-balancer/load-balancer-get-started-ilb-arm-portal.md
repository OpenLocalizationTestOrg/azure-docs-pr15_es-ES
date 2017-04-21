<properties
   pageTitle="Empezar a crear un equilibrador de carga interno en el Administrador de recursos con el portal de Azure | Microsoft Azure"
   description="Obtenga información sobre cómo crear un equilibrador de carga interno en el Administrador de recursos con el portal de Azure"
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
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="sewhee" />

# <a name="create-an-internal-load-balancer-in-the-azure-portal"></a>Crear un equilibrador de carga interno en el portal de Azure

[AZURE.INCLUDE [load-balancer-get-started-ilb-arm-selectors-include.md](../../includes/load-balancer-get-started-ilb-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][modelo de implementación clásico](load-balancer-get-started-ilb-classic-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="get-started-creating-an-internal-load-balancer-using-azure-portal"></a>Empezar a crear un equilibrador de carga interno con Azure portal

Use los siguientes pasos para crear un equilibrador de carga interno desde el Portal de Azure.

1. Abra un explorador, vaya al [portal de Azure](http://portal.azure.com)e inicie sesión con su cuenta de Azure.
2. En la esquina superior izquierda de la pantalla, haga clic en **nuevo** > **redes** > **equilibrador de carga**.
3. En el módulo **equilibrador de carga de crear** , escriba un **nombre** para el equilibrador de carga.
4. En la **combinación**, haga clic en **interna**.
5. Haga clic en **red Virtual**y, a continuación, seleccione la red virtual donde desee crear el equilibrador de carga.

    >[AZURE.NOTE] Si no ve la red virtual que desee usar, compruebe la **ubicación** que está usando para el equilibrador de carga y cambiarla en consecuencia.

6. Haga clic en **subred**y, a continuación, seleccione la subred donde desea crear el equilibrador de carga.
7. En la **asignación de direcciones IP**, haga clic en **dinámico** o **estático**, dependiendo de si desea que la dirección IP del equilibrador de carga solucionarse (estática) o no.

    >[AZURE.NOTE] Si selecciona usar una dirección IP estática, tendrá que proporcionar una dirección para el equilibrador de carga.

8. En el **grupo de recursos de** especificar el nombre de un nuevo grupo de recursos para el equilibrador de carga, o haga clic en **Seleccionar existente** y seleccione un grupo de recursos existente.
9. Haga clic en **crear**.

## <a name="configure-load-balancing-rules"></a>Configurar reglas de equilibrio de carga

Después de la creación del equilibrador de carga, desplácese hasta el recurso de equilibrador de carga para configurarlo.
Debe configurar primero un grupo de direcciones de back-end y un sondeo antes de configurar una regla de equilibrio de carga.

### <a name="step-1-configure-a-back-end-pool"></a>Paso 1: Configurar un grupo de servidores

1. En el portal de Azure, haga clic en **Examinar** > **equilibradores de carga**y, a continuación, haga clic en el equilibrador de carga que creó anteriormente.
2. En el módulo de **configuración** , haga clic en **grupos de back-end**.
3. En el módulo de **back-end agrupaciones de direcciones** , haga clic en **Agregar**.
4. En el módulo de **grupo de back-end de agregar** , escriba un **nombre** para el grupo de back-end y, a continuación, haga clic en **Aceptar**.

### <a name="step-2-configure-a-probe"></a>Paso 2: Configurar un sondeo

1. En el portal de Azure, haga clic en **Examinar** > **equilibradores de carga**y, a continuación, haga clic en el equilibrador de carga que creó anteriormente.
2. En el módulo de **configuración** , haga clic en **sondeos**.
3. En el módulo **sondeos** , haga clic en **Agregar**.
4. En el módulo de **sondeo de agregar** , escriba un **nombre** para el sondeo.
5. En **protocolo**, seleccione **HTTP** (para sitios web) o **TCP** (para otras aplicaciones basadas en TCP).
6. En **puerto**, especifique el puerto que se utilizará al acceder al sondeo.
7. En la **ruta de acceso** (para comprobaciones de HTTP), especifique la ruta de acceso para usarlo como un sondeo.
8. En **intervalo** , especifique la frecuencia de sondeo de la aplicación.
9. En **mal estado umbral**, especifique cuántas debería consigue antes de la máquina virtual de back-end está marcada como mal estada.
10. Haga clic en **Aceptar** para crear sondeo.

### <a name="step-3-configure-load-balancing-rules"></a>Paso 3: Configurar reglas de equilibrio de carga

1. En el portal de Azure, haga clic en **Examinar** > **equilibradores de carga**y, a continuación, haga clic en el equilibrador de carga que creó anteriormente.
2. En el módulo de **configuración** , haga clic en **reglas de equilibrio de carga**.
3. En el módulo de **Equilibrio de carga de reglas** , haga clic en **Agregar**.
4. En el módulo **Agregar regla de equilibrio de carga** , escriba un **nombre** para la regla.
5. En **protocolo**, seleccione **HTTP** (para sitios web) o **TCP** (para otras aplicaciones basadas en TCP).
6. En **puerto**, especifique los clientes de puerto conectan en el equilibrador de carga.
7. En **puerto de back-end**, especifique el puerto que se utilizarán en el grupo de back-end (por lo general, el puerto de equilibrador de carga y back-end son iguales).
8. En el **grupo de back-end**, seleccione el grupo de back-end que acaba de crear.
9. En **persistencia de sesión**, seleccione cómo desea sesiones conservar.
10. En **tiempo de espera de inactividad (minutos)**, especifique el tiempo de espera de inactividad.
11. **IP flotante (servidor directo devuelto)**, haga clic en **deshabilitado** o **habilitado**.
12. Haga clic en **Aceptar**.

## <a name="next-steps"></a>Pasos siguientes

[Configurar un modo de distribución del equilibrador de carga](load-balancer-distribution-mode.md)

[Configurar los valores de tiempo de espera TCP inactivas para el equilibrador de carga](load-balancer-tcp-idle-timeout.md)