<properties
   pageTitle="Equilibrador de carga de varios VIP para Azure | Microsoft Azure"
   description="Información general de varias direcciones VIP en equilibrador de carga de Azure"
   services="load-balancer"
   documentationCenter="na"
   authors="chkuhtz"
   manager="narayan"
   editor=""
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/11/2016"
   ms.author="chkuhtz"
/>

# <a name="multiple-vips-for-azure-load-balancer"></a>Equilibrador de carga de varios VIP de Azure

Equilibrador de carga de Azure le permite cargar servicios del saldo en varios puertos, varias direcciones IP o ambos. Puede usar las definiciones de equilibrador de carga públicos e internos cargar flujos de saldo a través de un conjunto de máquinas virtuales.

Este artículo describen los conceptos básicos de esta capacidad, conceptos importantes y restricciones. Si solo desea exponer servicios en una dirección IP, puede encontrar las configuraciones de equilibrador de carga de instrucciones simplificadas para [público](load-balancer-get-started-internet-portal.md) o [interno](load-balancer-get-started-ilb-arm-portal.md) . Agregar varias VIP es incremental a una sola configuración VIP. Con los conceptos de este artículo, se puede expandir una configuración simplificada en cualquier momento.

Cuando define un equilibrador de carga de Azure, un front-end y una configuración de back-end están conectados con las reglas. El sondeo de estado que hace referencia a la regla se utiliza para determinar cómo nuevos flujos se envían a un nodo en el grupo de back-end. El cliente está definida por una IP Virtual (VIP), que es una tupla de 3 consta de una dirección IP (pública o interna), un protocolo de transporte (UDP o TCP) y un número de puerto. Un DIP es una dirección IP de una NIC virtual Azure conectado a una máquina virtual en el grupo de back-end.

La tabla siguiente contiene algunas configuraciones de front-end de ejemplo:

| VIP | Dirección IP | Protocolo | puerto |
|-----|------------|----------|------|
|1|65.52.0.1|TCP|80|
|2|65.52.0.1|TCP|_8080_|
|3|65.52.0.1|_UDP_|80|
|4|_65.52.0.2_|TCP|80|

La tabla muestra cuatro frontends diferentes. Frontends #1, 2 # y #3 son solo VIP con varias reglas. Se usa la misma dirección IP, pero el protocolo o puerto es diferente para cada cliente. 1 de Frontends # y #4 son un ejemplo de varios VIP, donde el mismo front-end protocolo y puerto vuelven a través de varias VIP.

Azure equilibrador de carga proporciona flexibilidad para definir las reglas de equilibrio de carga. Una regla declara cómo se asigna una dirección y el puerto en el front-end para la dirección de destino y el puerto en el servidor. O no se vuelven a utilizar los puertos back-end entre reglas dependiendo del tipo de la regla. Cada tipo de regla tiene requisitos específicos que pueden afectar al diseño de configuración y el sondeo de host. Existen dos tipos de reglas:

1. La regla predeterminada con ningún reutilización de puerto de back-end
2. La regla flotante IP donde se vuelven a utilizar los puertos back-end

Equilibrador de carga de Azure le permite mezclar ambos tipos de regla en la misma configuración del equilibrador de carga. El equilibrador de carga puede usarlos simultáneamente para una máquina virtual determinada, o cualquier combinación, como respetar por las restricciones de la regla. Elegir qué tipo de regla depende de los requisitos de la aplicación y la complejidad de admitir dicha configuración. Evalúe qué tipos de reglas están la mejores para su situación.

Nos explorar aún más estos escenarios según el comportamiento predeterminado.

## <a name="rule-type-1-no-backend-port-reuse"></a>#1 del tipo de regla: sin reutilización de puerto de back-end

![Ilustración de MultiVIP](./media/load-balancer-multivip-overview/load-balancer-multivip.png)

En este escenario, VIP de front-end se configuran de la siguiente manera:

| VIP | Dirección IP | Protocolo | puerto |
|-----|------------|----------|------|
|![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1|65.52.0.1|TCP|80|
|![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2|*65.52.0.2*|TCP|80|

El DIP es el destino del flujo de entrada. En el grupo de back-end, cada VM expone el servicio deseado en un puerto único en un DIP. Este servicio está asociado con el cliente a través de una definición de la regla.

Se definen dos reglas:

| Regla | Asignar front-end | Grupo de back-end |
|------|--------------|-----------------|
| 1 | ![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) VIP1:80 | ![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP1:80, ![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP2:80 |
| 2 | ![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) VIP2:80 | ![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP1:81, ![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP2:81 |

La asignación completa en equilibrador de carga de Azure ahora es la siguiente:

| Regla | Dirección IP de la dirección VIP | Protocolo | puerto | Destino | puerto |
|------|----------------|----------|------|-----|------|
|![regla](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1|65.52.0.1|TCP|80|PASAR a la dirección IP|80|
|![regla](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2|65.52.0.2|TCP|80|PASAR a la dirección IP|81|

Cada regla debe generar un flujo con una combinación única de dirección IP de destino y puerto de destino. Modificando el puerto de destino del flujo de varias reglas pueden ofrecer flujos a la misma DIP en puertos diferentes.

Estado sondeos siempre se dirigen a la DIP de una máquina virtual. Debe asegurarse de que el sondeo refleja el estado de la máquina virtual.

## <a name="rule-type-2-backend-port-reuse-by-using-floating-ip"></a>#2 de tipo de regla: reutilización de puerto de back-end mediante IP flotantes

Equilibrador de carga de Azure proporciona la flexibilidad para volver a utilizar el puerto front-end a través de varias VIP independientemente del tipo de regla utilizado. Además, algunos escenarios de aplicación prefieren o requieren el mismo puerto para usarlo con varias instancias de aplicación en una única VM en el grupo de back-end. Ejemplos comunes de reutilización de puerto incluir clústeres para alta disponibilidad, la red de equipos virtuales y exponer múltiples extremos TLS sin cifrado.

Si desea volver a utilizar el puerto de back-end a través de varias reglas, debe habilitar IP flotante en la definición de la regla.

Flotante IP es una parte de lo que se conoce como directa servidor devuelto (DLV). DLV consta de dos partes: una topología de flujo y una dirección IP combinación de asignación. En un nivel de plataforma equilibrador de carga de Azure siempre funciona en una topología de flujo DLV independientemente flotante IP esté habilitado o no. Esto significa que la parte de un flujo de salida siempre correctamente es volver a escribir para flujo directamente al origen.

Con el tipo de regla de forma predeterminada, Azure expone una esquema de asignación de direcciones IP para facilitar el uso de equilibrio de carga tradicional. Habilitar IP flotante cambia la combinación de asignación de direcciones IP para permitir la flexibilidad adicional como se explica a continuación.

El diagrama siguiente muestra esta configuración:

![Ilustración de MultiVIP](./media/load-balancer-multivip-overview/load-balancer-multivip-dsr.png)

En este escenario, cada VM en el grupo de back-end tiene tres interfaces de red:

* DIP: una NIC Virtual asociada con la máquina virtual (recurso NIC de Azure)
* VIP1: una interfaz de bucle dentro de sistema operativo que está configurado con la dirección IP de VIP1 invitado
* VIP2: una interfaz de bucle dentro de sistema operativo que está configurado con la dirección IP de VIP2 invitado

>[AZURE.IMPORTANT] La configuración de las interfaces lógicas se realiza en el sistema operativo de invitado. Esta configuración no se realiza o administrada por Azure. Sin esta configuración, las reglas no funcionarán. Definiciones de sondeo de mantenimiento usan la DIP de la máquina virtual en lugar de la dirección VIP lógica. Por lo tanto, el servicio debe proporcionar las respuestas de sondeo en un puerto DIP que refleja el estado del servicio ofrecido en la dirección VIP lógica.

Supongamos que la misma configuración de front-end como en la situación anterior:

| VIP | Dirección IP | Protocolo | puerto |
|-----|------------|----------|------|
|![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1|65.52.0.1|TCP|80|
|![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2|*65.52.0.2*|TCP|80|

Se definen dos reglas:

| Regla | Asignar front-end | Grupo de back-end |
|------|--------------|-----------------|
| 1 | ![regla](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) VIP1:80 | ![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) VIP1:80 (en VM1 y VM2) |
| 2 | ![regla](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) VIP2:80 | ![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) VIP2:80 (en VM1 y VM2) |

La siguiente tabla muestra la asignación completa en el equilibrador de carga:

| Regla | Dirección IP de la dirección VIP | Protocolo | puerto | Destino | puerto |
|------|----------------|----------|------|-------------|------|
|![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1|65.52.0.1|TCP|80|idéntica VIP (65.52.0.1)|idéntica VIP (80)|
|![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2|65.52.0.2|TCP|80|idéntica VIP (65.52.0.2)|idéntica VIP (80)|

El destino del flujo de entrada es la dirección VIP en la interfaz de bucle de la máquina virtual. Cada regla debe generar un flujo con una combinación única de dirección IP de destino y puerto de destino. Modificando la dirección IP de destino del flujo, es posible en la misma máquina virtual de reutilización de puerto. Enlace a dirección IP y el puerto de la interfaz de bucle respectivos la VIP expone su servicio al equilibrador de carga.

Observe que este ejemplo no cambia el puerto de destino. Aunque se trata de un escenario de IP flotante, equilibrador de carga de Azure también admite la definición de una regla para volver a escribir el puerto de destino de back-end y para que sea el puerto de destino de front-end.

El tipo de regla flotante IP es la base de varios patrones de configuración del equilibrador de carga. Un ejemplo que está disponible actualmente es la configuración de [SQL AlwaysOn con varios procesos de escucha](../virtual-machines/virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md) . Con el tiempo, se documente varios de estos escenarios.

## <a name="limitations"></a>Limitaciones

* Varias configuraciones VIP solo son compatibles con máquinas virtuales de IaaS.
* Con la regla flotante IP, la aplicación debe utilizar el DIP para los flujos de salida. Si su aplicación enlaza a la dirección VIP configurada en la interfaz de bucle en el sistema operativo de invitado, a continuación, SNAT no está disponible para volver a escribir el flujo de salida y se produce un error en el flujo.
* Direcciones IP públicas tienen un efecto en facturación. Para obtener más información, vea [dirección IP precios](https://azure.microsoft.com/pricing/details/ip-addresses/)
* Aplicarán límites de suscripción. Para obtener más información, consulte [límites de servicio](../azure-subscription-service-limits.md#networking-limits) para obtener información detallada.
