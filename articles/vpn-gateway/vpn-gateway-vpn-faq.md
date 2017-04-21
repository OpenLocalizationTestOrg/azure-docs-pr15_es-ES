<properties 
   pageTitle="Preguntas más frecuentes de puerta de enlace VPN de red virtual | Microsoft Azure"
   description="La puerta de enlace VPN preguntas más frecuentes. Preguntas más frecuentes sobre las conexiones de red Virtual de Microsoft Azure entre local, conexiones de configuración híbrida y puertas de enlace VPN"
   services="vpn-gateway"
   documentationCenter="na"
   authors="yushwang"
   manager="rossort"
   editor="" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/10/2016"
   ms.author="yushwang" />

# <a name="vpn-gateway-faq"></a>Preguntas más frecuentes de puerta de enlace VPN

## <a name="connecting-to-virtual-networks"></a>Conectarse a redes virtuales

### <a name="can-i-connect-virtual-networks-in-different-azure-regions"></a>¿Puedo conectar redes virtuales en diferentes regiones de Azure?
Sí. De hecho, no hay ninguna restricción de región. Una red virtual puede conectarse a otra red virtual en la misma región o en una región distinta de Azure.

### <a name="can-i-connect-virtual-networks-in-different-subscriptions"></a>¿Puedo conectar redes virtuales en diferentes suscripciones?
Sí.

### <a name="can-i-connect-to-multiple-sites-from-a-single-virtual-network"></a>¿Se puede conectar con varios sitios desde una única red virtual?

Puede conectarse a varios sitios con Windows PowerShell y las API de REST de Azure. Vea la sección de [varios sitios y conectividad de VNet a VNet](#multi-site-and-vnet-to-vnet-connectivity) preguntas más frecuentes.
## <a name="what-are-my-cross-premises-connection-options"></a>¿Cuáles son Mis opciones de conexión entre local?

Se admiten las siguientes conexiones entre local:

- [Sitio de sitio](vpn-gateway-site-to-site-create.md) : conexión VPN sobre IPsec (IKE v1 y v2 IKE). Este tipo de conexión requiere un dispositivo VPN o RRAS.

- Punto al sitio de [](vpn-gateway-point-to-site-create.md) – conexión VPN a través de SSTP (Secure Socket túnel protocolo). Esta conexión no requiere un dispositivo VPN.

- [VNet a VNet](virtual-networks-configure-vnet-to-vnet-connection.md) : este tipo de conexión es idéntica a la configuración de un sitio a sitio. VNet a VNet es una conexión VPN sobre IPsec (IKE v1 y v2 IKE). No se requiere un dispositivo VPN.

- [Múltiples sitios](vpn-gateway-multi-site.md) : esta es una variación de una configuración de sitio a sitio que le permite conectarse a varios sitios local a una red virtual.

- [ExpressRoute](../expressroute/expressroute-introduction.md) : ExpressRoute es una conexión directa a Azure desde su WAN, no a través de Internet pública. Vea la [Información general técnica de ExpressRoute](../expressroute/expressroute-introduction.md) y las [Preguntas más frecuentes de ExpressRoute](../expressroute/expressroute-faqs.md) para obtener más información.

Para obtener más información acerca de las conexiones, consulte [Acerca de la puerta de enlace VPN](vpn-gateway-about-vpngateways.md).

### <a name="what-is-the-difference-between-a-site-to-site-connection-and-point-to-site"></a>¿Cuál es la diferencia entre una conexión de sitio a sitio y el punto al sitio?

Conexiones de **Sitio a sitio** le permiten conectar entre cualquiera de los equipos que se encuentran en sus dispositivos locales a cualquier máquina virtual o instancia de funciones dentro de la red virtual, dependiendo de cómo elija Configurar el enrutamiento. Es una buena opción para una conexión entre local siempre está disponible y es ideal para configuraciones híbridas. Este tipo de conexión se basa en un dispositivo VPN IPsec (hardware o dispositivo suave), que se debe implementar en el borde de la red. Para crear este tipo de conexión, debe tener el hardware necesario de la VPN y una dirección de IPv4 orientada externamente.

Punto al sitio de **** conexiones le permiten conectar desde un único equipo desde cualquier lugar a algún elemento ubicado en la red virtual. Utiliza al cliente VPN de Windows en el cuadro. Como parte de la configuración del sitio de punto, instale un certificado y un paquete de configuración de cliente VPN, que contiene la configuración que su equipo se conecte a cualquier máquina virtual o instancia de funciones dentro de la red virtual. Es ideal cuando desea conectarse a una red virtual, pero no están ubicados en local. También es una buena opción cuando no tiene acceso a hardware VPN o una dirección de IPv4 orientada externamente, que son necesarios para una conexión de sitio a otro. 

Puede configurar su red virtual para usar a sitios y punto al sitio de forma simultánea, siempre que desea crear la conexión de sitio a sitio con un tipo VPN basado en la ruta de la puerta de enlace. Tipos VPN basados en ruta se denominan puertas de enlace dinámicos en el modelo de implementación clásico.

### <a name="what-is-expressroute"></a>¿Qué es ExpressRoute?

ExpressRoute le permite crear conexiones privadas entre los centros de datos de Microsoft y la infraestructura en sus instalaciones o en un entorno de la ubicación. Con ExpressRoute, puede establecer conexiones a servicios de nube de Microsoft, como Microsoft Azure y Office 365 en una instalación de la ubicación del partner ExpressRoute o conectarse directamente desde la red WAN existente (por ejemplo, una VPN MPLS proporcionados por un proveedor de servicios de red).

Conexiones de ExpressRoute ofrecen una mejor seguridad, más confiabilidad, mayor ancho de banda y latencias menores que típico conexiones a través de Internet. En algunos casos, el uso de conexiones de ExpressRoute para transferir datos entre su red local y Azure también puede generar beneficios de costo significativo. Si ya ha creado una conexión entre local de su red local a Azure, puede migrar a una conexión de ExpressRoute manteniendo intacta su red virtual.

Consulte las [Preguntas más frecuentes de ExpressRoute](../expressroute/expressroute-faqs.md) para obtener más detalles.

## <a name="site-to-site-connections-and-vpn-devices"></a>Conexiones de sitio a sitio y dispositivos VPN

### <a name="what-should-i-consider-when-selecting-a-vpn-device"></a>¿Qué debo tener en cuenta al seleccionar un dispositivo VPN?

Validar un conjunto de dispositivos VPN de sitio a sitio estándar en colaboración con proveedores de dispositivos. Puede encontrar una lista de dispositivos VPN compatibles conocidos, sus correspondientes instrucciones de configuración o ejemplos y especificaciones de dispositivo [aquí](vpn-gateway-about-vpn-devices.md). Todos los dispositivos de las familias de dispositivo aparece como compatible con conocidos deben trabajar con una red Virtual. Para ayudarle a configurar el dispositivo VPN, consulte el ejemplo de configuración de dispositivo o el vínculo que corresponde a la familia de dispositivo adecuado.

### <a name="what-do-i-do-if-i-have-a-vpn-device-that-isnt-in-the-known-compatible-device-list"></a>¿Qué puedo hacer si tengo un dispositivo VPN que no está en la lista de dispositivos compatibles conocidos?

Si no ve el dispositivo aparece como dispositivo VPN compatible conocido y que desea usar para la conexión VPN, deberá comprobar que cumple los compatibles IPsec/IKE configuración opciones y parámetros enumerados [aquí](vpn-gateway-about-vpn-devices.md#devices-not-on-the-compatible-list). Dispositivos que cumplen los requisitos mínimos deberían funcionar bien con puertas de enlace VPN. Póngase en contacto con el fabricante del dispositivo para obtener instrucciones de configuración y soporte técnico adicionales.

### <a name="why-does-my-policy-based-vpn-tunnel-go-down-when-traffic-is-idle"></a>¿Por qué mi túnel VPN basada en directivas avanzar cuando el tráfico es inactivo?

Este es el comportamiento esperado para basada en directivas (también conocido como enrutamiento estático) puertas de enlace VPN. Cuando el tráfico a través del túnel está inactivo durante más de cinco minutos, el túnel se deshace. Cuando inicia el tráfico que fluye en cualquier dirección, el túnel se pueden restablecer inmediatamente.

### <a name="can-i-use-software-vpns-to-connect-to-azure"></a>¿Puedo usar software VPN para conectarse a Azure?

Servidores de acceso remoto (RRAS) y enrutamiento de Windows Server 2012 se admiten para la configuración de sitio a sitio entre local.

Otras soluciones de software de VPN deben trabajar con nuestra puerta de enlace como se ajustan a implementaciones IPsec estándar de la industria. Para obtener instrucciones de configuración y soporte técnico, póngase en contacto con el proveedor del software.

## <a name="point-to-site-connections"></a>Conexiones de sitio a punto

### <a name="what-operating-systems-can-i-use-with-point-to-site"></a>¿Qué sistemas operativos puedo usar con punto al sitio?

Los siguientes sistemas operativos son compatibles:

- Windows 7 (32 bits y 64 bits)

- Windows Server 2008 R2 (solo 64 bits)

- Windows 8 (32 bits y 64 bits)

- Windows 8.1 (32 bits y 64 bits)

- Windows Server 2012 (solo 64 bits)

- Windows Server 2012 R2 (solo 64 bits)

- Windows 10

### <a name="can-i-use-any-software-vpn-client-for-point-to-site-that-supports-sstp"></a>¿Puedo usar a cualquier cliente VPN de software para punto al sitio que admita SSTP?

No. Soporte técnico están limitado solo a las versiones de sistema operativo Windows enumeradas anteriormente.

### <a name="how-many-vpn-client-endpoints-can-i-have-in-my-point-to-site-configuration"></a>¿Cuántos extremos de cliente VPN puedo tener en la configuración de Mi sitio de punto?

Se admiten hasta 128 clientes VPN para que pueda conectarse a una red virtual al mismo tiempo.

### <a name="can-i-use-my-own-internal-pki-root-ca-for-point-to-site-connectivity"></a>¿Puedo usar mi propia CA de raíz PKI interna para conectividad punto al sitio?

Sí. Anteriormente, se podrían usar solo los certificados raíz firmados. Aún puede cargar certificados raíz de 20.

### <a name="can-i-traverse-proxies-and-firewalls-using-point-to-site-capability"></a>¿Recorrer los servidores proxy y servidores de seguridad con la capacidad de punto al sitio?

Sí. Usamos SSTP (Secure Socket túnel protocolo) para túnel a través del firewall. Este túnel aparecerá como una conexión HTTPs.

### <a name="if-i-restart-a-client-computer-configured-for-point-to-site-will-the-vpn-automatically-reconnect"></a>¿Si reiniciar el equipo cliente configurado para el sitio de punto, se la VPN volver a conectar automáticamente?

De forma predeterminada, el equipo cliente no restablezca la conexión VPN automáticamente.

### <a name="does-point-to-site-support-auto-reconnect-and-ddns-on-the-vpn-clients"></a>¿Soporte de sitio a punto automática-volver a conectar y DDNS en los clientes VPN?

Conectar automática y DDNS actualmente no se admiten en VPN de sitio a punto.

### <a name="can-i-have-site-to-site-and-point-to-site-configurations-coexist-for-the-same-virtual-network"></a>¿Puedo tener a sitios y coexisten configuraciones de punto a sitios de la misma red virtual?

Sí. Ambas soluciones funcionará si tiene un tipo de VPN RouteBased de la puerta de enlace. Para el modelo de implementación clásica, necesita una puerta de enlace dinámico. Para ello no soporte técnico punto a sitio para puertas de enlace VPN enrutamiento estáticos o puertas de enlace con - VpnType PolicyBased.

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-networks-at-the-same-time"></a>¿Configurar un cliente de punto al sitio para conectarse a varias redes virtuales al mismo tiempo?

Sí, es posible. Pero no pueden tener las redes virtuales superpuestos IP prefijos y los espacios de dirección de sitio de punto no deben superponerse entre las redes virtuales.

### <a name="how-much-throughput-can-i-expect-through-site-to-site-or-point-to-site-connections"></a>¿Cuánto rendimiento puedo esperar a través de conexiones de sitio a sitio o punto al sitio?

Es difícil de mantener el rendimiento exacto de los túneles VPN. IPsec y SSTP son protocolos VPN crypto visible. Rendimiento también está limitado por la latencia y el ancho de banda entre su local e Internet.

## <a name="gateways"></a>Puertas de enlace

### <a name="what-is-a-policy-based-static-routing-gateway"></a>¿Qué es una directiva (enrutamiento estático) puerta de enlace?

Puertas de enlace de directiva implementan redes privadas virtuales basadas en la directiva. Redes privadas virtuales basadas en directiva cifrar y dirigen paquetes a través de túneles IPsec basados en las combinaciones de prefijos de dirección entre su red local y VNet de Azure. La directiva (o el Selector de tráfico) normalmente se define como una lista de acceso en la configuración de la VPN.

### <a name="what-is-a-route-based-dynamic-routing-gateway"></a>¿Qué es una ruta (enrutamiento de dinámicos) puerta de enlace?

Puertas de enlace de ruta implementan la VPN basado en la ruta. Redes privadas virtuales basadas en ruta use "rutas" en la dirección IP de reenvío o tabla de enrutamiento directo paquetes en sus interfaces de túnel correspondiente. A continuación, las interfaces de túnel cifrar o descifrar los paquetes dentro y fuera de los túneles. El selector de directiva o tráfico de ruta según VPN se han configurado como y a cualquier (o caracteres comodín).

### <a name="can-i-get-my-vpn-gateway-ip-address-before-i-create-it"></a>¿Puedo obtener dirección IP de mi puerta de enlace VPN antes de la cree?

No. Deberá crear la puerta de enlace para obtener la dirección IP. Si eliminar y volver a crear la puerta de enlace VPN, cambie la dirección IP.

### <a name="how-does-my-vpn-tunnel-get-authenticated"></a>¿Cómo autenticado obtener mi túnel VPN?

Azure VPN usa autenticación PSK (clave previamente compartida). Se genera una clave previamente compartida (PSK) cuando se crea el túnel VPN. Puede cambiar la PSK generado automáticamente a su propio con el cmdlet de PowerShell de clave previamente compartida establecer o la API de REST.

### <a name="can-i-use-the-set-pre-shared-key-api-to-configure-my-policy-based-static-routing-gateway-vpn"></a>¿Puedo usar la API de clave previamente compartida establecer configurar basado en mi directiva de puerta de enlace (enrutamiento estático) VPN?

Sí, el cmdlet establecer previamente clave API y PowerShell puede usarse para configurar Azure VPN (estáticas) basada en directivas y basado en la ruta (dinámicas) VPN enrutamiento.

### <a name="can-i-use-other-authentication-options"></a>¿Puedo usar otras opciones de autenticación?

Se está limitados a utilizar claves previamente compartidas (PSK) para la autenticación.

### <a name="what-is-the-gateway-subnet-and-why-is-it-needed"></a>¿Qué es la "subred de puerta de enlace" y por qué se necesita?

Tenemos un servicio de puerta de enlace que se ejecuta para habilitar la conectividad entre local. 

Deberá crear una subred de puerta de enlace para su VNet configurar una puerta de enlace VPN. Todas las subredes de puerta de enlace deben denominarse GatewaySubnet funcione correctamente. No asigne un nombre a la subred de puerta de enlace otra cosa. Y no implementar máquinas virtuales o nada a la subred de puerta de enlace.

El tamaño mínimo de subred de puerta de enlace depende por completo de la configuración que desea crear. Aunque es posible crear una subred de puerta de enlace tan pequeños como /29 para algunas configuraciones, le recomendamos que cree una subred de puerta de enlace de /28 o mayor (/ 28, /27, /26 etcetera.). 

### <a name="can-i-deploy-virtual-machines-or-role-instances-to-my-gateway-subnet"></a>¿Puedo implementar máquinas virtuales o las instancias de función a mi subred de puerta de enlace?

No.

### <a name="how-do-i-specify-which-traffic-goes-through-the-vpn-gateway"></a>¿Cómo especificar qué tráfico pasa a través de la puerta de enlace VPN?

Si está utilizando el Portal de Azure clásica, agregue cada rango que desee enviado a través de la puerta de enlace para su red virtual en la página de redes en redes locales.

### <a name="can-i-configure-forced-tunneling"></a>¿Puedo configurar la forzado túnel?

Sí. Vea [Configurar había forzado túnel](vpn-gateway-about-forced-tunneling.md).

### <a name="can-i-set-up-my-own-vpn-server-in-azure-and-use-it-to-connect-to-my-on-premises-network"></a>¿Puedo configurar mi propio servidor VPN en Azure y utilizarlo para conectarse a mi red local?

Sí, puede implementar su propia puertas de enlace VPN o servidores en Azure de Azure Marketplace o crear sus propios enrutadores VPN. Debe configurar rutas definidas por el usuario en su red virtual para asegurarse de que el tráfico se enruta correctamente entre las redes local y las subredes virtual.

### <a name="why-are-certain-ports-opened-on-my-vpn-gateway"></a>¿Por qué algunos puertos abiertos en mi puerta de enlace VPN?

Son necesarios para la comunicación de infraestructura de Azure. Están protegidos (bloqueado) por los certificados de Azure. Sin certificados apropiados, entidades externas, incluidos a los clientes de las puertas de enlace, no podrá provocar ningún efecto en los extremos.

Una puerta de enlace VPN básicamente es un dispositivo host múltiple con un aprovechamiento NIC de la red privada de cliente y una NIC opuestas en la red pública. Entidades de infraestructura de Azure no conozca redes privadas del cliente por motivos de cumplimiento, por lo que necesitan para utilizar extremos públicas para la comunicación de infraestructura. Auditoría de seguridad de Azure examina periódicamente los extremos públicos.


### <a name="more-information-about-gateway-types-requirements-and-throughput"></a>Para obtener más información sobre los tipos de puerta de enlace, requisitos y rendimiento

Para obtener más información, consulte [Acerca de la configuración de puerta de enlace VPN](vpn-gateway-about-vpn-gateway-settings.md).

## <a name="multi-site-and-vnet-to-vnet-connectivity"></a>Múltiples sitios y conectividad de VNet a VNet

### <a name="which-type-of-gateways-can-support-multi-site-and-vnet-to-vnet-connectivity"></a>¿Qué tipo de puertas de enlace puede admitir varios sitios y conectividad de VNet a VNet?

Sólo basado en ruta VPN (enrutamiento dinámico).

### <a name="can-i-connect-a-vnet-with-a-routebased-vpn-type-to-another-vnet-with-a-policybased-vpn-type"></a>¿Puedo conectar un VNet con un tipo de VPN RouteBased a otro VNet con un tipo de VPN PolicyBased?

No, ambas redes virtuales deben estar usando basado en la ruta (enrutamiento dinámico) VPN.

### <a name="is-the-vnet-to-vnet-traffic-secure"></a>¿Es seguro el tráfico de VNet a VNet?

Sí, está protegido por cifrado IPsec/IKE.

### <a name="does-vnet-to-vnet-traffic-travel-over-the-azure-backbone"></a>¿VNet a VNet tráfico recorrer la espina Azure?

Sí.

### <a name="how-many-on-premises-sites-and-virtual-networks-can-one-virtual-network-connect-to"></a>¿Cuántos sitios local y redes virtuales puede conectar una red virtual a?

Max. 10 combinado para las puertas de enlace Basic y enrutamiento dinámico estándar; 30 para las puertas de enlace VPN de alto rendimiento.

### <a name="can-i-use-point-to-site-vpns-with-my-virtual-network-with-multiple-vpn-tunnels"></a>¿Se puede utilizar punto sitio VPN con mi red virtual con varios túneles VPN?

Sí, VPN punto a sitio (P2S) pueden usarse con las puertas de enlace VPN conectarse a varios sitios local y otras redes virtuales.

### <a name="can-i-configure-multiple-tunnels-between-my-virtual-network-and-my-on-premises-site-using-multi-site-vpn"></a>¿Puedo configurar varios túneles entre mi red virtual y Mi sitio local mediante VPN múltiples sitios?

No, no se admiten túneles redundantes entre una red virtual Azure y un sitio local.

### <a name="can-there-be-overlapping-address-spaces-among-the-connected-virtual-networks-and-on-premises-local-sites"></a>¿Pueden allí superponerse espacios de dirección entre las redes virtuales conectadas y sitios locales de local?

No. Superposición de espacios de direcciones hará que la carga de archivos de configuración de red o "Creación de una red Virtual" no se realice correctamente.

### <a name="do-i-get-more-bandwidth-with-more-site-to-site-vpns-than-for-a-single-virtual-network"></a>¿Obtener más ancho de banda con más VPN de sitio a sitio que para una única red virtual?

No, todos los túneles VPN, incluidos VPN de sitio a punto, compartan la misma puerta de enlace VPN de Azure y el ancho de banda disponible.

### <a name="can-i-use-azure-vpn-gateway-to-transit-traffic-between-my-on-premises-sites-or-to-another-virtual-network"></a>¿Puedo usar puerta de enlace VPN de Azure para el tráfico de tránsito entre Mis sitios local o a otra red virtual?

**Modelo de implementación clásica**<br>
Tránsito a través de la puerta de enlace VPN de Azure es posible usar el modelo de implementación clásica, pero se basa en espacios de dirección estática definido en el archivo de configuración de red. BGP no es compatible todavía con Azure Virtual redes y puertas de enlace VPN utiliza el modelo de implementación clásico. Sin BGP, definir manualmente los espacios de direcciones de tránsito es muy error susceptible y no se recomienda.<br>
**Modelo de implementación de administrador de recursos**<br>
Si está utilizando el modelo de implementación de administrador de recursos, vea la sección [BGP](#bgp) para obtener más información.

### <a name="does-azure-generate-the-same-ipsecike-pre-shared-key-for-all-my-vpn-connections-for-the-same-virtual-network"></a>¿Azure genera la misma clave previamente compartida IPsec/IKE para todas las conexiones de mi VPN para la misma red virtual?

No, Azure predeterminada genera claves previamente compartidas diferentes para diferentes conexiones VPN. Sin embargo, puede usar el cmdlet de PowerShell de establecer los API de REST de clave de puerta de enlace de VPN para establecer el valor de clave que prefiera. La clave debe ser una cadena alfanumérica de longitud entre 1 y 128 caracteres.

### <a name="does-azure-charge-for-traffic-between-virtual-networks"></a>¿Cobra Azure para el tráfico entre redes virtuales?

Para el tráfico entre las distintas redes virtuales Azure, Azure cargos solo para el tráfico atraviesa desde una región Azure a otro. El tipo de cargo aparece en la página de Azure [Precios de puerta de enlace VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/) .


### <a name="can-i-connect-a-virtual-network-with-ipsec-vpns-to-my-expressroute-circuit"></a>¿Puedo conectar una red virtual con IPsec VPN a mi circuito ExpressRoute?

Sí, esto es compatible. Para obtener más información, vea [Configurar ExpressRoute y conexiones de sitio a sitio VPN que coexisten](../expressroute/expressroute-howto-coexist-classic.md).

## <a name="bgp"></a>BGP

[AZURE.INCLUDE [vpn-gateway-bgp-faq-include](../../includes/vpn-gateway-bpg-faq-include.md)] 



## <a name="cross-premises-connectivity-and-vms"></a>Conectividad entre local y máquinas virtuales

### <a name="if-my-virtual-machine-is-in-a-virtual-network-and-i-have-a-cross-premises-connection-how-should-i-connect-to-the-vm"></a>Si mi máquina virtual está en una red virtual y tengo una conexión entre local, ¿cómo debo conectar a la máquina virtual?

Tiene varias opciones. Si tiene RDP habilitado y ha creado un punto final, puede conectarse a su equipo virtual usando la dirección VIP. En ese caso, especificar la dirección VIP y el puerto que desea conectarse. Deberá configurar el puerto en la máquina virtual para el tráfico. Normalmente, se vaya al Portal clásico de Azure y guardar la configuración de la conexión RDP a su equipo. La configuración contiene la información de conexión necesaria.

Si tiene una red virtual con conectividad entre local configurado, puede conectarse a su equipo virtual mediante el DIP interno o la dirección IP privada. También puede conectarse a su equipo virtual por DIP interno desde otra máquina virtual que se encuentra en la misma red virtual. No RDP a su equipo virtual usando el DIP si se está conectando desde una ubicación fuera de la red virtual. Por ejemplo, si tiene una red virtual de sitio a punto configurada y no establecer una conexión desde el equipo, no puede conectarse a la máquina virtual por DIP.

### <a name="if-my-virtual-machine-is-in-a-virtual-network-with-cross-premises-connectivity-does-all-the-traffic-from-my-vm-go-through-that-connection"></a>¿Si mi máquina virtual está en una red virtual con conectividad entre local, todo el tráfico de mi VM ir a través de esta conexión?

No. Solo el tráfico que tiene un destino IP contenida en los intervalos de direcciones IP de red Local de red virtual especificada pasará a través de la puerta de enlace de red virtual. Tráfico tiene un destino IP que se encuentra dentro de la red virtual permanezca dentro de la red virtual. Otro tráfico se envía a través del equilibrador de carga de las redes públicas, o si se utiliza el túnel forzado, envía a través de la puerta de enlace VPN de Azure. Si está solucionando problemas, es importante para asegurarse de que tiene todos los rangos que aparece en su red Local que desea enviar a través de la puerta de enlace. Compruebe que los intervalos de direcciones de la red Local no se superpone con cualquiera de los intervalos de direcciones en la red virtual. Además, desea comprobar que el servidor DNS que está usando resuelve el nombre a la dirección IP correcta.


## <a name="virtual-network-faq"></a>Preguntas más frecuentes sobre una red virtual

Ver información de una red virtual adicional en las [Preguntas más frecuentes de red Virtual](../virtual-network/virtual-networks-faq.md).
 
