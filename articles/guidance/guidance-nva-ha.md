<properties
   pageTitle="Implementación de equipos virtuales en alta disponibilidad | Microsoft Azure"
   description="Cómo implementar los dispositivos de red virtual de alta disponibilidad."
   services=""
   documentationCenter="na"
   authors="telmosampaio"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/21/2016"
   ms.author="telmos"/>

# <a name="deploying-virtual-appliances-in-high-availability"></a>Implementación de equipos virtuales en alta disponibilidad

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

En este artículo se describe un conjunto de prácticas de implementación de dispositivos de red virtual (NVAs) de una manera altamente disponible. Antes de continuar, asegúrese de que sabe cómo [rutas definidas por el usuario (UDR)] [ udr-overview] y [equilibrador de carga] [ lb-overview] profesional en Azure.

Puede usar diferentes NVAs disponibles en Azure marketplace para ampliar la funcionalidad de Azure en la misma manera que usan dispositivos en su centro de datos local. La figura siguiente muestra un ejemplo de implementación de una [sola NVA] [ nva-scenario] como un dispositivo de firewall. 

![[0]][0]

Aunque la implementación anterior funciona, presenta un único punto de error. Si se produce un error en el dispositivo virtual, no el tráfico fluye. Para solucionar el problema, necesita usar varios NVAs. Sin embargo, que requiere otros recursos y la configuración que se utilizará según sus necesidades.

Puede usar una de las siguientes soluciones para implementar un entorno de NVA altamente disponible.

|Solución|Ventajas|Consideraciones|
|---|---|---|
|Entrada con dispositivos virtuales de nivel 7|Todos los nodos están activos|Requiere un NVA que puede utilizar SNAT y terminar conexiones<br/>Requiere un conjunto diferente de NVAs para el tráfico procedente de Internet y de Azure<br/>Solo se puede usar para el tráfico originado fuera de Azure|
|Entrada y salida con dispositivos virtuales de nivel 7|Todos los nodos están activos<br/>Puede controlar el tráfico que se ha originado en Azure |Requiere un NVA que puede utilizar SNAT y terminar conexiones<br/>Requiere un conjunto diferente de NVAs para el tráfico procedente de Internet y de Azure|
|Cambiar puntos UDR|Conjunto único de NVAs para todo el tráfico<br/>Puede controlar todo el tráfico (sin límite en las reglas de puerto)|Activo y pasivo<br/>Requiere un proceso de migración tras error|

## <a name="ingress-with-layer-7-virtual-appliances"></a>Entrada con dispositivos virtuales de nivel 7
Puede usar un conjunto de NVAs detrás de un equilibrador de carga de Azure para proporcionar conectividad a cargas de trabajo de Azure detrás de un conjunto pequeño de puertos del servidor (por ejemplo, HTTP y HTTPS). La siguiente ilustración resalta cómo proporcionar alta disponibilidad en este escenario en el nivel NVA.

![[1]][1]

En este escenario, el dispositivo de red virtual utiliza debe terminar todas las conexiones y pasarlos a la subred de carga de trabajo. Las máquinas virtuales de carga de trabajo (VM) responder a la NVA que reciben una solicitud de y flujos de tráfico sin problemas. 

## <a name="ingress-egress-with-layer-7-virtual-appliances"></a>Entrada y salida con dispositivos virtuales de nivel 7
Puede extender la arquitectura anterior y agregar otro conjunto de NVAs para controlar el tráfico procedente de Azure controle NVAs, tal como se muestra en la siguiente ilustración:

![[2]][2]

En este escenario, todo el tráfico de origen en Azure se dirige a un equilibrador de carga interno que distribuye la carga entre un conjunto diferente de NVAs. Estos NVAs dirigen el tráfico a Internet usando sus direcciones IP públicas individuales. 

## <a name="pip-udr-switch"></a>Cambiar puntos UDR
Para evitar la creación de varias pilas de NVA con dos NVAs en modo activo pasivo. En este escenario, puede cambiar la dirección IP pública (puntos) y rutas definidas por el usuario (UDRs) cuando se detiene el nodo activo.  

![[3]][3]

Este escenario es similar al escenario NVA único. La única diferencia es que los puntos y UDRs debe modificarse para cambiar el tráfico entre el NVAs. Se pueden realizar estos cambios manualmente, o también se pueden automatizar. Para automatizar, puede implementar una aplicación en ambos NVAs que compruebe el estado del nodo activo. Una vez el nodo activo hacia abajo, la aplicación puede cambiar los puntos y UDRs al que vincular el nodo pasivo.

Una posible implementación de esta solución es usar un [ZooKeeper] [ zookeeper] daemon en el NVAs para controlar la elección de relleno (decidir qué nodo es el nodo activo). Una vez que se elige una guía, llama a la API de REST de Azure para quitar los puntos de error y adjuntar en el coordinador. También deben cambiar UDRs para que apunten a la dirección IP privada del coordinador nuevo.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre cómo [implementar una DMZ entre Azure y su centro de datos local] [ dmz-on-prem] con NVAs de nivel 7.
- Obtenga información sobre cómo [implementar una DMZ entre Azure e Internet] [ dmz-internet] con NVAs de nivel 7.

<!-- links -->
[udr-overview]: ../virtual-network/virtual-networks-udr-overview.md
[lb-overview]: ../load-balancer/load-balancer-overview.md
[zookeeper]: https://zookeeper.apache.org/
[nva-scenario]: ../virtual-network/virtual-network-scenario-udr-gw-nva.md
[dmz-on-prem]: guidance-iaas-ra-secure-vnet-hybrid.md
[dmz-internet]: guidance-iaas-ra-secure-vnet-dmz.md

<!-- images -->
[0]: ./media/guidance-nva-ha/single-nva.png "Una sola arquitectura NVA"
[1]: ./media/guidance-nva-ha/l7-ingress.png "Entrada de nivel 7"
[2]: ./media/guidance-nva-ha/l7-ingress-egress.png "7 de entrada y salida de la capa"
[3]: ./media/guidance-nva-ha/active-passive.png "Clúster activo y pasivo"