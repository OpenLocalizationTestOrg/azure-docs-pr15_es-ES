
<properties
   pageTitle="Información general del equilibrador de carga interno | Microsoft Azure"
   description="Descripción general de equilibrador de carga interno y sus características. Cómo funciona un equilibrador de carga para que escenarios de Azure y posibles configurar extremos internos"
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


# <a name="internal-load-balancer-overview"></a>Información general del equilibrador de carga interno

A diferencia de luego Internet hacia equilibrador de carga, el equilibrador de carga interno (ILB) dirige el tráfico solo a los recursos dentro del servicio de nube o mediante VPN para obtener acceso a la infraestructura de Azure. La infraestructura restringe el acceso a la carga equilibrada direcciones IP virtuales (VIP) de un servicio de nube o una red Virtual para que no se le nunca directamente exponer a un extremo de Internet. Esto permite interna aplicaciones de línea de negocio (LOB) para ejecutar en Azure y tener acceso desde dentro de la nube o recursos locales.

## <a name="why-you-may-need-an-internal-load-balancer"></a>¿Por qué es posible que tenga un equilibrador de carga interno

Azure interno cargar equilibrio (ILB) proporciona equilibrio de carga entre máquinas virtuales que se encuentran dentro de un servicio de nube o de una red virtual con un ámbito regional. Para obtener información sobre el uso y la configuración de redes virtuales con un ámbito regional, vea [Redes virtuales Regional](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/) en el blog de Azure. Redes virtuales existentes que se han configurado para un grupo de afinidad no pueden usar ILB.

ILB permite a los siguientes tipos de equilibrio de carga:

- Dentro de un servicio de nube desde máquinas virtuales a un conjunto de máquinas virtuales que residen en el mismo servicio de nube (consulte la figura 1).
- Dentro de una red virtual, desde máquinas virtuales de una red virtual a un conjunto de máquinas virtuales que residen en el mismo servicio de nube del directorio de red (consulte la figura 2).
- Una red virtual entre local, de equipos locales a un conjunto de máquinas virtuales que residen en el mismo servicio de nube de virtual de la red (consulte la figura 3).
- Aplicaciones a través de Internet, varios niveles en la que los niveles de back-end no están a través de Internet, pero requieren equilibrio de carga para el tráfico de la capa a través de Internet.
- Equilibrio de carga para las aplicaciones LOB hospedadas en Azure sin necesidad de software o hardware de equilibrador de carga adicional. Incluidos los servidores locales en el conjunto de equipos cuyo tráfico es carga equilibrada.

## <a name="internet-facing-multi-tier-applications"></a>Internet opuestas en aplicaciones de varios niveles


El nivel de web tiene extremos opuestas de Internet para clientes de Internet y forma parte de un conjunto de equilibrio de carga. El equilibrador de carga distribuye el tráfico entrante de los clientes web para el puerto TCP 443 (HTTPS) a los servidores web.

Los servidores de base de datos están detrás de un extremo ILB que los servidores web para el almacenamiento. Extremo, qué tráfico se cargan equilibradas en los servidores de base de datos en el conjunto ILB de equilibrio de esta carga de servicio de base de datos.

La imagen siguiente muestra el orientados a la aplicación de varios niveles dentro del mismo servicio de nube de Internet.

Figura 1 - Internet opuestas aplicación de varios niveles

![Servicio de nube solo de equilibrio de carga interno](./media/load-balancer-internal-overview/IC736321.png)

Otro uso posible para una aplicación de varios niveles es el ILB implementado en un servicio de nube diferente que el consumo del servicio para el ILB.

Servicios de nube con la misma red virtual tendrá acceso al extremo ILB.

Figura 2 muestra los servidores que están en un servicio de nube diferente desde la base de datos back-end y utilizando el extremo ILB dentro de la misma red virtual de web front-end.

![Interno equilibrio de carga entre servicios de nube](./media/load-balancer-internal-overview/IC744147.png)

Ilustración 2: servidores front-end de un servicio de nube diferente

## <a name="intranet-line-of-business-applications"></a>Línea de la intranet de aplicaciones empresariales

Tráfico de clientes en la red local obtener el equilibrio de carga en el conjunto de servidores LOB mediante la conexión VPN a la red de Azure.

El equipo cliente tendrá acceso a una dirección IP de servicio de Azure VPN con punto al sitio VPN. Permitir el uso de la aplicación de LOB hospedada detrás del extremo de ILB.

![Equilibrio de carga interno con punto al sitio VPN](./media/load-balancer-internal-overview/IC744148.png)

Figura 3 - aplicaciones LOB hospedadas detrás del extremo de Libras

Otro escenario para LOB es tener una VPN de sitio a sitio a la red virtual que se ha configurado el extremo ILB. Esto permite enrutar al extremo ILB el tráfico de red local.

![Equilibrio de carga interno mediante VPN de sitio a sitio](./media/load-balancer-internal-overview/IC744150.png)

Figura 4 - dirige al extremo ILB el tráfico de red local


## <a name="next-steps"></a>Pasos siguientes

[Compatibilidad de Azure del Administrador de recursos con equilibrador de carga de Azure](load-balancer-arm.md)

[Empezar a configurar una Internet opuestas equilibrador de carga](load-balancer-get-started-internet-arm-ps.md)

[Empezar a configurar un equilibrador de carga interno](load-balancer-get-started-ilb-arm-ps.md)

[Configurar un modo de distribución del equilibrador de carga](load-balancer-distribution-mode.md)

[Configurar los valores de tiempo de espera TCP inactivas para el equilibrador de carga](load-balancer-tcp-idle-timeout.md)

