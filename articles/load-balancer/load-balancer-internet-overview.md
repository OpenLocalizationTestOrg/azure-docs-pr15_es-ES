
<properties
   pageTitle="Internet opuestas información general del equilibrador de carga | Microsoft Azure "
   description="Descripción general de Internet opuestas equilibrador de carga y sus características. Funcionamiento de un equilibrador de carga para Azure con máquinas virtuales y servicios en la nube."
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="sewhee" />


# <a name="internet-facing-load-balancer-overview"></a>Información general del equilibrador de carga hacia de Internet

Equilibrador de carga de Azure asigna el número de puerto y la dirección IP público del tráfico de entrada a la privada IP dirección y número de puerto de la máquina virtual y viceversa para el tráfico de respuesta de la máquina virtual. Reglas de equilibrio de carga permiten distribuir tipos específicos de tráfico entre varias máquinas virtuales o servicios. Por ejemplo, puede distribuir la carga de tráfico de solicitud de web entre varios servidores web o los roles de web.

Para un servicio de nube que contiene instancias de roles de web o trabajo, puede definir un extremo del público en el archivo de definición (.csdef) de servicio.

El archivo _servicedefinition.csdef_ contiene la configuración de extremo y cuando tiene varias instancias de la función de una implementación de rol web o de trabajo, el equilibrador de carga se configuración para el mismo. La manera de agregar instancias a la implementación de nube está cambiando el recuento de instancia en el archivo de configuración de servicio (.csfg).

La figura siguiente muestra un extremo de equilibrio de carga para el tráfico de web cifrada que se comparte entre tres máquinas virtuales de Windows para el puerto TCP público y privado 443. Estos tres equipos virtuales están en un conjunto de equilibrio de carga.

![ejemplo de equilibrador de carga público](./media/load-balancer-internet-overview/IC727496.png))

Figura 1 - extremo de equilibrio de carga para el tráfico web cifrada

Cuando los clientes de Internet envían las solicitudes de la página web en la dirección IP pública del servicio de nube en el puerto TCP 443, el equilibrio de carga de Azure distribuye las solicitudes entre las tres máquinas virtuales en el conjunto de equilibrio de carga. Para obtener más información acerca de los algoritmos de equilibrador de carga, consulte la [página de información general del equilibrador de carga](load-balancer-overview.md#load-balancer-features).

De forma predeterminada, equilibrador de carga de Azure distribuye el tráfico de red uniformemente entre varias instancias de máquina virtual. También puede configurar la afinidad de la sesión, para obtener más información, consulte [el modo de distribución del equilibrador de carga](load-balancer-distribution-mode.md).

## <a name="next-steps"></a>Pasos siguientes

Aprenda a [equilibrador de carga interno](load-balancer-internal-overview.md) comprender mejor qué equilibrador de carga es una mejor opción para la implementación de la nube.

También puede [empezar a crear un opuestas equilibrador de carga de Internet](load-balancer-get-started-internet-arm-ps.md) y configurar el tipo de [modo de distribución](load-balancer-distribution-mode.md) para un comportamiento de tráfico de red de equilibrador de carga específica.

Si la aplicación se necesita mantener las conexiones activas para servidores detrás de un equilibrador de carga, puede saber más sobre [configuración de tiempo de espera TCP inactivas equilibradores de carga](load-balancer-tcp-idle-timeout.md). Le servirá para obtener información sobre el comportamiento de la conexión inactiva cuando se usa equilibrador de carga de Azure.
