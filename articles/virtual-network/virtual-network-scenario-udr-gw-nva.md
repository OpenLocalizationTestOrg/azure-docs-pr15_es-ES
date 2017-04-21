<properties 
   pageTitle="Conexión de híbrido con aplicaciones de nivel 2 | Microsoft Azure"
   description="Obtenga información sobre cómo implementar equipos virtuales y UDR para crear un entorno de aplicación de varios niveles en Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/05/2016"
   ms.author="jdial" />

# <a name="virtual-appliance-scenario"></a>Escenario de dispositivo virtual

Un escenario común entre el cliente de Azure más grande es la necesidad de proporcionar una aplicación de dos niveles que se exponen en Internet, mientras permite el acceso a la capa hacia atrás desde un centro de datos local. Este documento le guiará en un escenario con rutas definidas por el usuario (UDR), una puerta de enlace VPN y dispositivos de red virtual para implementar un entorno de dos niveles que cumpla los siguientes requisitos:

- Aplicación Web debe ser accesible desde Internet solo.
- Servidor Web que hospeda la aplicación puedan tener acceso a un servidor de aplicaciones de back-end.
- Todo el tráfico de Internet a la aplicación web debe ir a través de un dispositivo virtual de firewall. Este dispositivo virtual se usará para solo el tráfico de Internet.
- Todo el tráfico en el servidor de aplicaciones debe ir a través de un dispositivo virtual de firewall. Este dispositivo virtual se utilizará para el acceso al servidor back-end y access procedentes de la red local a través de una puerta de enlace VPN.
- Los administradores deben poder administrar los dispositivos de firewall virtual desde sus equipos locales, utilizando una tercera firewall virtual dispositivo que usa exclusivamente con fines de administración.

Se trata de un escenario DMZ estándar con una DMZ y protegido de red. Escenario de este tipo se puede construir en Azure mediante NSGs, electrodomésticos virtual de firewall o una combinación de ambos. La siguiente tabla muestra algunas de las ventajas y desventajas entre NSGs y accesorios virtuales de firewall.

||Los profesionales de TI|Desventajas|
|---|---|---|
|GSN|Sin costo. <br/>Integrada en RBAC Azure. <br/>Las reglas pueden crearse en las plantillas ARM.|Complejidad podría variar en entornos más grandes. |
|Firewall|Control total sobre plano de datos. <br/>Administración central a través de la consola de firewall.|Costo de dispositivo de firewall. <br/>No se integra con RBAC de Azure.|

La siguiente solución usa dispositivos virtual de firewall para implementar un escenario de red DMZ o protegido.

## <a name="considerations"></a>Consideraciones

Puede implementar el entorno descrito en Azure con distintas características hoy, como sigue.

- **Red Virtual (VNet)**. Un VNet de Azure actúa de modo similar a una red local y se puede dividir en una o varias subredes para proporcionar aislamiento de tráfico y separación de asuntos.
- **Dispositivo Virtual**. Varios socios proporcionan accesorios virtuales en Azure Marketplace que se pueden usar para los tres firewalls descritos anteriormente. 
- **Rutas (UDR) definidas por el usuario**. Tablas de ruta pueden contener UDRs utilizados redes Azure para controlar el flujo de paquetes dentro de una VNet. Estas tablas de ruta se pueden aplicar a subredes. Una de las características más recientes en Azure es la capacidad de aplicar una tabla de la ruta a la GatewaySubnet, lo que proporciona la capacidad de reenviar todo el tráfico entra VNet de Azure desde una conexión híbrido a un equipo virtual.
- **Reenvío de IP**. De forma predeterminada, el motor de red Azure reenviar paquetes a tarjetas de interfaz de red virtual (NIC) solo si la dirección IP de destino de paquete coincide con la dirección IP de NIC. Por lo tanto, si un UDR define que debe enviarse un paquete a un equipo virtual determinado, el motor de red Azure ¿anular ese paquete. Para asegurarse de que el paquete se envía a una máquina virtual (en este caso, un dispositivo virtual) que no sea el destino real del paquete, debe habilitar el reenvío IP para el dispositivo virtual.
- **Grupos de seguridad de red (NSGs)**. El ejemplo siguiente no hace uso de la NSGs, pero puede usar NSGs aplicados a las subredes o NIC en esta solución para filtrar aún más el tráfico dentro y fuera de esas subredes y NIC.


![Conectividad IPv6](./media/virtual-network-scenario-udr-gw-nva/figure01.png)

En este ejemplo hay una suscripción que contiene lo siguiente:

- grupos de recursos 2, que no se muestra en el diagrama. 
    - **ONPREMRG**. Contiene todos los recursos necesarios para simular una red local.
    - **AZURERG**. Contiene todos los recursos necesarios para el entorno de red virtual Azure. 
- Un VNet denominado **onpremvnet** usado para imitar un centro de datos local segmentado como se indica a continuación.
    - **onpremsn1**. Subred que contiene una máquina virtual (VM) ejecutan Ubuntu para imitar un servidor local.
    - **onpremsn2**. Subred que contiene una VM que ejecuta Ubuntu para imitar un equipo local que utiliza un administrador.
- Hay un dispositivo virtual de firewall denominado **OPFW** en **onpremvnet** que se utiliza para mantener un túnel a **azurevnet**.
- Un VNet denominado **azurevnet** segmenta como se indica a continuación.
    - **azsn1**. Subred de firewall externo que se usa exclusivamente para el firewall externo. Todo el tráfico de Internet vaya a través de esta subred. Esta subred contiene solo una NIC vinculada al firewall externo.
    - **azsn2**. Subred de front-end que aloja una máquina virtual que se ejecuta como un servidor web que serán accesibles desde Internet.
    - **azsn3**. Subred de back-end aloja una VM que ejecuta un servidor de aplicaciones de back-end que serán accesibles mediante el servidor web front-end.
    - **azsn4**. Subred de administración que se usa exclusivamente para proporcionar administración de acceso a todos los equipos virtuales de firewall. Esta subred contiene solo una NIC para cada dispositivo virtual firewall utilizada en la solución.
    - **GatewaySubnet**. Subred de conexión de Azure híbrido necesario para que ExpressRoute y puerta de enlace VPN para proporcionar conectividad entre VNets de Azure y otras redes. 
- Hay 3 equipos virtuales firewall de la red **azurevnet** . 
    - **AZF1**. Firewall externo que se exponen en Internet pública usando un recurso de dirección IP público de Azure. Debe asegurarse de que tiene una plantilla desde el catálogo de soluciones o directamente desde el fabricante del dispositivo, esa disposiciones dispositivo virtual 3 NIC.
    - **AZF2**. Servidor de seguridad interno que se usa para controlar el tráfico entre **azsn2** y **azsn3**. También es un dispositivo virtual de NIC 3.
    - **AZF3**. Firewall de administración accesible para los administradores desde el centro de datos local y, a continuación, conectada a una subred administración usada para administrar todos los dispositivos de firewall. Puede encontrar plantillas de dispositivo virtual de NIC 2 en el catálogo de soluciones o solicitar uno directamente desde el proveedor del dispositivo.

## <a name="user-defined-routing-udr"></a>Enrutamiento (UDR) definidas por el usuario

Cada subred en Azure se pueden vincular a una tabla UDR permite definir cómo se inicia tráfico en que se dirige subred. Si no se definen UDRs, Azure usa rutas predeterminadas para que admita el tráfico fluya desde una subred a otra. Para entender mejor UDRs, visite [¿Cuáles son las rutas de definidas por el usuario y el reenvío de IP](./virtual-networks-udr-overview.md#ip-forwarding).

Para asegurarse de que se realiza la comunicación a través del dispositivo firewall derecha, en función de la último requisito anterior, deberá crear la siguiente tabla de ruta que contiene UDRs en **azurevnet**.

### <a name="azgwudr"></a>azgwudr

En este escenario, el único tráfico que fluye locales con Azure se usan para administrar los firewalls conectándose a **AZF3**y que el tráfico debe ir a través del firewall interno, **AZF2**. Por lo tanto, solo una ruta es necesaria en **GatewaySubnet** tal como se muestra a continuación.

|Destino|Próximo salto|Explicación|
|---|---|---|
|10.0.4.0/24|10.0.3.11|Permite que el tráfico local llegar a firewall de administración **AZF3**|

### <a name="azsn2udr"></a>azsn2udr

|Destino|Próximo salto|Explicación|
|---|---|---|
|10.0.3.0/24|10.0.2.11|Permite el tráfico a la subred de back-end aloja el servidor de aplicación a través de **AZF2**|
|0.0.0.0/0|10.0.2.10|Permite que todos los otro tráfico se enrutó a través de **AZF1**|

### <a name="azsn3udr"></a>azsn3udr

|Destino|Próximo salto|Explicación|
|---|---|---|
|10.0.2.0/24|10.0.3.10|Permite el tráfico **azsn2** fluya desde el servidor de aplicaciones para el servidor Web a través de **AZF2**|

También debe crear tablas de ruta para las subredes en **onpremvnet** para imitar el centro de datos local.

### <a name="onpremsn1udr"></a>onpremsn1udr

|Destino|Próximo salto|Explicación|
|---|---|---|
|192.168.2.0/24|192.168.1.4|Permite el tráfico **onpremsn2** a través de **OPFW**|

### <a name="onpremsn2udr"></a>onpremsn2udr

|Destino|Próximo salto|Explicación|
|---|---|---|
|10.0.3.0/24|192.168.2.4|Permite el tráfico a la subred de copia de seguridad en Azure a través de **OPFW**|
|192.168.1.0/24|192.168.2.4|Permite el tráfico **onpremsn1** a través de **OPFW**|

## <a name="ip-forwarding"></a>Reenvío de IP 

UDR y reenvío IP son características que puede usar en combinación para permitir que los dispositivos virtuales se usa para controlar el flujo del tráfico en un VNet de Azure.  Un dispositivo virtual es nada más que una máquina virtual que se ejecuta una aplicación que se usa para controlar el tráfico de red en algunos casos, como un firewall o un dispositivo NAT.

Este dispositivo virtual VM debe ser capaz de recibir el tráfico entrante que no se dirige a sí mismo. Para permitir que una máquina virtual para recibir tráfico dirigido a otros destinos, debe habilitar el reenvío IP de la máquina virtual. Se trata de una configuración, no un valor en el sistema operativo invitado de Azure. Su dispositivo virtual todavía debe ejecutar algún tipo de aplicación para manejar el tráfico entrante y distribuirlo adecuadamente.

Para obtener más información sobre el reenvío IP, visite [¿Cuáles son las rutas de definidas por el usuario y el reenvío de IP](./virtual-networks-udr-overview.md#ip-forwarding).

Por ejemplo, imagine que tiene la siguiente configuración en un vnet Azure:

- Subred **onpremsn1** contiene una máquina virtual denominada **onpremvm1**.
- Subred **onpremsn2** contiene una máquina virtual denominada **onpremvm2**.
- Dispositivo virtual denominado **OPFW** está conectado a **onpremsn1** y **onpremsn2**.
- Una ruta definida por el usuario vinculada a **onpremsn1** especifica que todo el tráfico a **onpremsn2** debe enviarse a **OPFW**.

En este momento, si **onpremvm1** intenta establecer una conexión con **onpremvm2**, se usará el UDR y tráfico se enviarán a **OPFW** como el próximo salto. Tenga en cuenta que no se cambia el destino del paquete real, sigue indicando **onpremvm2** es el destino. 

Sin reenvío IP habilitado para **OPFW**, la lógica de red virtual Azure quitará los paquetes, ya que sólo permite paquetes que se envían a una máquina virtual si la dirección IP de la máquina virtual es el destino del paquete.

Con el reenvío IP, la lógica de una red virtual Azure reenvía los paquetes a OPFW, sin cambiar su dirección de destino original. **OPFW** debe manejar los paquetes y determinar qué hacer con ellos.

Para el escenario anterior para trabajar, debe habilitar el reenvío IP en la NIC para **OPFW**, **AZF1**, **AZF2**y **AZF3** que se utilizan para enrutar (NIC todos excepto los vinculados a la subred de administración). 

## <a name="firewall-rules"></a>Reglas de Firewall

Como se describió anteriormente, solo el reenvío de IP garantiza que los paquetes se envían a los equipos virtuales. Su dispositivo todavía debe decidir qué hacer con los paquetes. En el ejemplo anterior, debe crear las siguientes reglas en los dispositivos:

### <a name="opfw"></a>OPFW

OPFW representa un dispositivo local que contiene las siguientes reglas:

- **Ruta**: todo el tráfico a 10.0.0.0/16 (**azurevnet**) debe enviarse a través de túnel **ONPREMAZURE**.
- **Directiva**: permitir todo el tráfico bidireccional entre **port2** y **ONPREMAZURE**.
 
### <a name="azf1"></a>AZF1

AZF1 representa un dispositivo virtual Azure que contiene las siguientes reglas:

- **Directiva**: permitir todo el tráfico bidireccional entre **port1** y **port2**.

### <a name="azf2"></a>AZF2

AZF2 representa un dispositivo virtual Azure que contiene las siguientes reglas:

- **Ruta**: todo el tráfico a 10.0.0.0/16 (**onpremvnet**) debe enviarse a la dirección IP de puerta de enlace de Azure (es decir, 10.0.0.1) a través de **port1**.
- **Directiva**: permitir todo el tráfico bidireccional entre **port1** y **port2**.

## <a name="network-security-groups-nsgs"></a>Grupos de seguridad de red (NSGs)

En este escenario, no se utiliza NSGs. Sin embargo, puede aplicar NSGs en cada subred para restringir el tráfico entrante y saliente. Por ejemplo, puede aplicar las siguientes reglas GSN a la subred FW externa.

**Correo entrante**

- Permitir todo el tráfico TCP desde Internet al puerto 80 en cualquier máquina virtual en la subred.
- Denegar todo el tráfico de Internet.

**Salientes**
- Denegar todo el tráfico a Internet.

## <a name="high-level-steps"></a>Pasos de nivel alto

Para implementar este escenario, siga los pasos de alto niveles siguientes.

1.  Inicie sesión en su suscripción de Azure.
2.  Si desea implementar un VNet para imitar la red local, aprovisionar los recursos que forman parte de **ONPREMRG**.
3.  Aprovisionar los recursos que forman parte de **AZURERG**.
4.  Disposición del túnel **onpremvnet** a **azurevnet**.
5.  Una vez aprovisionados todos los recursos, inicie sesión en **onpremvm2** y ping 10.0.3.101 para probar la conectividad entre **onpremsn2** y **azsn3**.
