<properties
    pageTitle="Qué hacer en caso de una interrupción de servicio Azure que afectan a las redes virtuales Azure | Microsoft Azure"
    description="Obtenga información sobre qué hacer en caso de una interrupción de servicio Azure que afectan a redes Virtual de Azure."
    services="virtual-network"
    documentationCenter=""
    authors="NarayanAnnamalai"
    manager="jefco"
    editor=""/>

<tags
    ms.service="virtual-network"
    ms.workload="virtual-network"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/16/2016"
    ms.author="narayan;aglick"/>

#<a name="virtual-network--business-continuity"></a>Red virtual: continuidad empresarial

##<a name="overview"></a>Información general

Una red Virtual (VNet) es una representación lógica de su red en la nube. Permite definir su propio espacio de direcciones IP privado y segmentar la red en subredes. VNets sirve como un límite de confianza para hospedar los recursos de cálculo como máquinas virtuales de Azure y servicios de nube (funciones web o trabajador). Un VNet permite la comunicación IP directa privada entre los recursos alojados en él. Una red Virtual también se pueden vincular a una red local a través de una de las opciones de implementación híbrida, como una puerta de enlace VPN o ExpressRoute.
 
Se crea un VNet en el ámbito de una región. Puede crear VNets con el mismo espacio de direcciones en dos regiones diferentes (es decir, nos Oriente y Oeste nos pero no se conecte a otros directamente). 

##<a name="business-continuity"></a>Continuidad empresarial

Puede haber varias formas diferentes que se puede interrumpir la aplicación. Una región determinada podría estar cortada completamente debido a un desastre natural o un desastre parcial debido a un error de varios dispositivos o servicios. El impacto en el servicio de VNet es diferente en cada una de estas situaciones.

**P: ¿qué hacer en caso de una interrupción a toda una región? ¿es decir, si una región es completamente corte debido a un desastre natural? ¿Qué pasa con las redes virtuales alojado en la región?**

R: la red Virtual y los recursos en la región afectada permanece inaccesible durante el tiempo de la interrupción del servicio.

![Diagrama de red Virtual de simple](./media/virtual-network-disaster-recovery-guidance/vnet.png)

**P: ¿Qué puedo volver a crear la misma red Virtual en una región diferente?**

R: red virtual (VNet) es bastante ligera recursos. Puede invocar las API de Azure para crear un VNet con el mismo espacio de direcciones en una región diferente. Para volver a crear el mismo entorno que estaba presente en la región afectada, tendrá que realizar llamadas de API para volver a implementar los servicios de nube (funciones web o trabajador) y a máquinas virtuales que había. También tendrá control de una puerta de enlace VPN y conectarse a su red local, si tiene conectividad local (por ejemplo, en una implementación híbrida).

Las instrucciones para crear un VNet se encuentran [aquí](./virtual-networks-create-vnet-arm-pportal.md). 

**P: ¿una réplica de un VNet en una región determinada puede volver a crear en otra región antelación?**

R: Sí, puede crear dos VNets con el mismo espacio de direcciones IP privada y recursos en dos regiones diferentes antes de tiempo. Si un cliente hospeda orientados a servicios en la VNet internet, podría ha establecido el tráfico administrador para geo-enrutar el tráfico a la región que está activa. Sin embargo, un cliente no puede conectar dos VNets con el mismo espacio de direcciones para su red local como que podría provocar problemas de enrutamiento. En el momento de desastre y pérdida de un VNet en una región, un cliente puede conectar el otro VNet en el área disponibles con coincidentes espacio de direcciones para su red local.

Las instrucciones para crear un VNet se encuentran [aquí](./virtual-networks-create-vnet-arm-pportal.md).
