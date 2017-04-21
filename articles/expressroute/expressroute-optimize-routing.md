<properties
   pageTitle="Optimizar el enrutamiento de ExpressRoute | Microsoft Azure"
   description="Esta página proporciona detalles acerca de cómo optimizar el enrutamiento cuando un cliente tiene más de un circuitos ExpressRoute que se conectan entre Microsoft y la red del cliente corp."
   documentationCenter="na"
   services="expressroute"
   authors="charwen"
   manager="carmonm"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="charwen"/>

# <a name="optimize-expressroute-routing"></a>Optimizar el enrutamiento de ExpressRoute
Cuando haya varios circuitos ExpressRoute, tiene más de una ruta de acceso para conectarse a Microsoft. Como resultado, puede ocurrir enrutamiento más apropiados - es decir, el tráfico puede tardar una ruta de acceso más tiempo para llegar a Microsoft y Microsoft a su red. Más tiempo la ruta de acceso de red, cuanto mayor sea la latencia. Latencia tiene impacto directo en la experiencia de usuario y rendimiento de la aplicación. En este artículo se ilustran este problema y se explica cómo optimizar el enrutamiento mediante las tecnologías de enrutamiento estándares.

## <a name="suboptimal-routing-case-1"></a>Caso de enrutamiento más apropiado 1
Echemos un vistazo cerrar en el problema de enrutamiento por un ejemplo. Imagine que tiene dos oficinas en los Estados Unidos, uno de Los Ángeles y uno en Nueva York. Las oficinas están conectadas en una red de área extensa (WAN), que puede ser su propia red troncal o IP VPN su proveedor de servicios. Tiene dos circuitos ExpressRoute, una nos oeste y otra en nosotros Oriente, que también están conectadas en la WAN. Evidentemente, tiene dos rutas de acceso para conectarse a la red de Microsoft. Ahora imagine que tiene la implementación de Azure (por ejemplo, servicio de aplicación de Azure) en nosotros oeste y nos Oriente. Su intención es conectar a los usuarios en Los Ángeles oeste de Azure nos y los usuarios de Nueva York a Azure nos Oriente porque el administrador del servicio anuncia que los usuarios de cada oficina acceso a los servicios de Azure cercanos un rendimiento óptimo. Desgraciadamente, el plan funciona bien para los usuarios de la costa este, pero no para los usuarios de la costa oeste. La causa del problema es la siguiente. En cada circuito ExpressRoute, nos anunciar que el prefijo en Azure nos oriental (23.100.0.0/16) y el prefijo en Azure nos Oeste (13.100.0.0/16). Si no sabe qué prefijo es desde qué región, no es posible tratar de manera diferente. Ambos de los prefijos son más cerca de nosotros oriental que nos oeste y, por tanto, enrutar ambos usuarios de office al circuito ExpressRoute en nosotros Oriente, puede considerar la red WAN. Al final, tendrá muchos usuarios triste en la oficina de Los Ángeles.

![](./media/expressroute-optimize-routing/expressroute-case1-problem.png)

### <a name="solution-use-bgp-communities"></a>Solución: use BGP Comunidades
Para optimizar el enrutamiento de los usuarios de office, debe saber qué prefijo es de Azure nos oeste y que de Azure nos Oriente. Esta información se codifica con [valores de la Comunidad de BGP](expressroute-routing.md). Nos hemos asignado un valor de comunidad BGP único para cada región Azure, por ejemplo, "12076:51004" para nosotros Oriente, "12076:51006" para nosotros oeste. Ahora que sabe qué prefijo es desde la que Azure, puede configurar qué circuito ExpressRoute debería preferido. Porque usamos la BGP para intercambiar información de enrutamiento, puede usar Local preferencia de BGP para influir en el enrutamiento. En nuestro ejemplo, puede asignar un valor más alto de preferencia local a 13.100.0.0/16 nos oeste que nos este y del mismo modo, un valor más alto de preferencia local a 23.100.0.0/16 en nosotros oriental que nos oeste. Esta configuración se asegurará de que, si ambas rutas de Microsoft están disponibles, los usuarios en Los Ángeles tendrá que el circuito ExpressRoute nos Oeste para conectarse a Azure nos oeste, mientras que los usuarios de Nueva York tomar la ExpressRoute en nosotros Oriente a Azure nos Oriente. Enrutamiento está optimizado en ambos lados. 

![](./media/expressroute-optimize-routing/expressroute-case1-solution.png)

## <a name="suboptimal-routing-case-2"></a>Caso de enrutamiento más apropiado 2
A continuación se ofrece otro ejemplo donde las conexiones de Microsoft tomen una ruta de acceso más tiempo para llegar a su red. En este caso, use los servidores locales de Exchange y Exchange Online en un [entorno híbrido](https://technet.microsoft.com/library/jj200581%28v=exchg.150%29.aspx). Sedes están conectados a una WAN. Anunciar los prefijos de los servidores locales en dos sedes a Microsoft a través de los dos circuitos ExpressRoute. Exchange Online se iniciará conexiones a los servidores locales en casos como migración del buzón. Desgraciadamente, la conexión a su oficina de Los Ángeles se dirige al circuito ExpressRoute en nosotros Oriente antes de recorrer el reverso de Europa todo la costa oeste. La causa del problema es similar a la primera. Sin ninguna sugerencia Microsoft network no indica qué prefijo del cliente está cerca nos Oriente y cuál es cerca nos oeste. Sucede elegir la ruta de acceso incorrecta a su oficina en Los Ángeles.

![](./media/expressroute-optimize-routing/expressroute-case2-problem.png)

### <a name="solution-use-as-path-prepending"></a>Solución: use anteponiendo la ruta de acceso de AS
Existen dos soluciones al problema. La primera es que simplemente anunciar el prefijo local para su oficina Los Ángeles, 177.2.0.0/31, en el circuito ExpressRoute nos oeste y prefijo en sus instalaciones de la oficina de Nueva York, 177.2.0.2/31, en el circuito ExpressRoute en nosotros Oriente. Como resultado, hay solo una ruta de acceso de Microsoft para conectarse a cada una de las oficinas. No hay ninguna ambigüedad y enrutamiento está optimizado. Con este diseño, debe pensar en la estrategia de migración tras error. En caso de que la ruta de acceso a Microsoft a través de ExpressRoute se interrumpe, debe asegurarse de que Exchange Online todavía puede conectarse con los servidores locales. 

La segunda solución es que continuar anunciar ambos de los prefijos en ambos circuitos ExpressRoute y, además, que enviar una sugerencia de qué prefijo está cerca que uno de sus oficinas. Puesto que admitimos la ruta de acceso de AS BGP anteponiendo, puede configurar la ruta de acceso para el prefijo AS para influir en el enrutamiento. En este ejemplo, puede alargar la ruta de acceso como 172.2.0.0/31 en nosotros oriental para que se se prefiere el circuito ExpressRoute nos oeste tráfico destinado para este prefijo (como nuestra red se cree que la ruta de acceso a este prefijo es más corta en el oeste). Del mismo modo puede alargar la ruta de acceso como 172.2.0.2/31 nos Oeste para que se le prefiere el circuito ExpressRoute en nosotros Oriente. Enrutamiento está optimizado para ambas oficinas. Con este diseño, si un circuito de ExpressRoute se interrumpe, Exchange Online puede seguir conectando a través de otro circuito ExpressRoute y su WAN. 

>[AZURE.IMPORTANT] Eliminamos privada como números en la ruta de acceso de AS para los prefijos ha recibido en Microsoft Peering. Se necesita anexar públicas como números en la ruta de acceso de AS para influir en el enrutamiento de Microsoft Peering.

![](./media/expressroute-optimize-routing/expressroute-case2-solution.png)

>[AZURE.IMPORTANT] Aunque los ejemplos aquí son para Microsoft y peerings público, se admiten las mismas capacidades para la interconexión privada. Además, la ruta de acceso de AS anteponiendo funciona dentro de un solo circuito ExpressRoute, influir en la selección de las rutas de primaria y secundarias.
