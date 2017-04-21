
<properties
   pageTitle="Empezar a crear una Internet opuestas equilibrador de carga en el modelo de implementación clásica con el portal clásico Azure | Microsoft Azure"
   description="Aprenda a crear un opuestas equilibrador de carga en el modelo de implementación clásica con el portal Azure clásico de Internet"
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
   ms.date="08/31/2016"
   ms.author="sewhee" />

# <a name="get-started-creating-an-internet-facing-load-balancer-classic-in-the-azure-classic-portal"></a>Empezar a crear un opuestas equilibrador de carga (clásico) en el portal Azure clásico de Internet

[AZURE.INCLUDE [load-balancer-get-started-internet-classic-selectors-include.md](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]En este artículo trata sobre el modelo de implementación clásico. También puede [obtener información sobre cómo crear un Internet opuestas equilibrador de carga con el Administrador de recursos de Azure](load-balancer-get-started-internet-arm-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]


## <a name="set-up-an-internet-facing-load-balancer-for-virtual-machines"></a>Configurar un equilibrador de carga a través de Internet para máquinas virtuales

Para cargar el tráfico de red saldo desde Internet a través de las máquinas virtuales de un servicio de nube, debe crear un conjunto de equilibrio de carga. Este procedimiento supone que ya ha creado las máquinas virtuales y que están en el mismo servicio de nube.

**Para configurar un conjunto de equilibrio de carga para máquinas virtuales**

1. En el portal de Azure clásico, haga clic en **máquinas virtuales de Windows**y, a continuación, haga clic en el nombre de una máquina virtual en el conjunto de equilibrio de carga.

2. Haga clic en los **extremos**y, a continuación, haga clic en **Agregar**.

3. En la página **Agregar un extremo para una máquina virtual** , haga clic en la flecha derecha.

4. En la página **especificar los detalles del extremo** :

    * En **nombre**, escriba un nombre para el punto final o seleccione el nombre de la lista de extremos predefinidos para los protocolos comunes.
    * En **protocolo**, seleccione el protocolo necesario para el tipo de extremo, TCP o UDP, según sea necesario.
    * En **los puertos públicos y privados**, escriba los números de puerto que desea que la máquina virtual para usar, según sea necesario. Puede usar las reglas de firewall y puerto privado en la máquina virtual para redirigir el tráfico de manera apropiada para su aplicación. El puerto privado puede ser el mismo que el puerto público. Por ejemplo, para un extremo para el tráfico web (HTTP), puede asignar el puerto 80 para el puerto público y privado.

5. Seleccione **crear un conjunto de equilibrio de carga**y, a continuación, haga clic en la flecha derecha.

6. En la página **Configurar el conjunto de equilibrio de carga** , escriba un nombre para el conjunto de equilibrio de carga y, a continuación, asignar los valores de comportamiento de sondeo de equilibrio de carga de Azure. El equilibrador de carga se usa sondeos para determinar si los equipos virtuales en el conjunto de equilibrio de carga está disponibles para recibir el tráfico entrante.

7. Haga clic en la marca de verificación para crear el extremo de equilibrio de carga. Se muestra **Sí** en la columna **nombre del conjunto de equilibrio de carga** de la página **puntos finales** de la máquina virtual.

8. En el portal, haga clic en **máquinas virtuales de Windows**, haga clic en el nombre de una máquina virtual adicional en el conjunto de equilibrio de carga, haga clic en los **extremos**y, a continuación, haga clic en **Agregar**.

9. En la página **Agregar un extremo para una máquina virtual** , haga clic en el **extremo de agregar a un conjunto existente de equilibrio de carga**, seleccione el nombre del conjunto de equilibrio de carga y, a continuación, haga clic en la flecha derecha.

10. En la página **especificar los detalles del extremo** , escriba un nombre para el punto final y, a continuación, haga clic en la marca de verificación.

Para los equipos virtuales adicionales en el conjunto de equilibrio de carga, repita los pasos 8 a 10.



## <a name="next-steps"></a>Pasos siguientes

[Empezar a configurar un equilibrador de carga interno](load-balancer-get-started-ilb-arm-ps.md)

[Configurar un modo de distribución del equilibrador de carga](load-balancer-distribution-mode.md)

[Configurar los valores de tiempo de espera TCP inactivas para el equilibrador de carga](load-balancer-tcp-idle-timeout.md)

