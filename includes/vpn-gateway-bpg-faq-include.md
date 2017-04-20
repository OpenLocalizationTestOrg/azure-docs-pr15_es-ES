### <a name="is-bgp-supported-on-all-azure-vpn-gateway-skus"></a>¿Es compatible BGP en todos los SKU de puerta de enlace VPN de Azure?

No, BGP es compatible con puertas de enlace VPN **estándar** y de **alto rendimiento** Azure. **Básico** SKU no es compatible.

### <a name="can-i-use-bgp-with-azure-policy-based-vpn-gateways"></a>¿Puedo usar BGP con puertas de enlace VPN Azure Policy-Based?

No, BGP es compatible con solo puertas de enlace VPN basada en la ruta.

### <a name="can-i-use-private-asns-autonomous-system-numbers"></a>¿Puedo usar ASNs privadas (números de sistema autónomo)?

Sí, puede usar su propio ASNs públicos o privados ASNs para su redes local y redes virtuales Azure.

#### <a name="are-there-asns-reserved-by-azure"></a>¿Hay ASNs reservados por Azure?

Sí, el siguientes ASNs están reservados por Azure para peerings internos y externos:

- ASNs públicos: 8075, 8076, 12076
- ASNs privado: 65515, 65517, 65518, 65519, 65520

No puede especificar estos ASNs para su local en dispositivos VPN al conectarse a puertas de enlace VPN de Azure.

### <a name="can-i-use-the-same-asn-for-both-on-premises-vpn-networks-and-azure-vnets"></a>¿Puedo usar la misma ASN para redes VPN local y VNets de Azure?

No, es necesario asignar ASNs diferentes entre sus redes local y su VNets de Azure si se está conectando junto con BGP. Las puertas de enlace VPN Azure tiene predeterminado ASN de 65515 asignado, si BGP está habilitada para no para la conectividad entre local. Puede reemplazar este valor predeterminado asignando un ASN diferente al crear la puerta de enlace VPN, o cambiar la ASN después de crea la puerta de enlace. Debe asignar su ASNs local a puertas de red Local de Azure correspondiente.

### <a name="what-address-prefixes-will-azure-vpn-gateways-advertise-to-me"></a>¿Qué prefijos de direcciones anunciará puertas de enlace VPN de Azure para mí?

La puerta de enlace VPN Azure anunciará las rutas siguientes a los dispositivos BGP local:

- Los prefijos de dirección VNet
- Prefijos de direcciones para cada puertas de enlace de red Local conectado a la puerta de enlace VPN de Azure
- Rutas de otras sesiones interconexión BGP conectados a la puerta de enlace VPN de Azure, **excepto predeterminado o varias rutas superpuestas con cualquier prefijo VNet**que ha aprendido.

#### <a name="can-i-advertise-default-route-00000-to-azure-vpn-gateways"></a>¿Anunciar ruta predeterminada (0.0.0.0/0) a puertas de enlace VPN de Azure?

No en este momento.

#### <a name="can-i-advertise-the-exact-prefixes-as-my-virtual-network-prefixes"></a>¿Anunciar los prefijos exactos como mi prefijos de red Virtual?

No, anunciar los mismos prefijos como uno de su red Virtual prefijos de direcciones se bloquea o filtrados por la plataforma Windows Azure. Sin embargo puede anunciar un prefijo que es un superconjunto de tiene dentro de su red Virtual. Por ejemplo, su red Virtual podría usar la 10.10.0.0/16 espacio dirección y podría anunciar 10.0.0.0/8.

### <a name="can-i-use-bgp-with-my-vnet-to-vnet-connections"></a>¿Puedo usar BGP con Mis conexiones VNet a VNet?

Sí, puede usar BGP para conexiones entre local y conexiones de VNet a VNet.

### <a name="can-i-mix-bgp-with-non-bgp-connections-for-my-azure-vpn-gateways"></a>¿Puedo combinar BGP con conexiones no BGP para mi puertas de enlace VPN de Azure?

Sí, puede mezclar BGP y no BGP conexiones para la misma puerta de enlace VPN de Azure.

### <a name="does-azure-vpn-gateway-support-bgp-transit-routing"></a>¿Significa Azure VPN gateway soporte BGP tránsito enrutamiento?

Sí, el enrutamiento de tránsito BGP es compatible, con la excepción de que **no** será de puertas de enlace de Azure VPN anuncia rutas predeterminadas para otros iguales BGP. Para habilitar el enrutamiento de tránsito a través de varias puertas de enlace VPN de Azure, debe habilitar BGP en todas las conexiones de VNet a VNet intermedias.

### <a name="can-i-have-more-than-one-tunnels-between-azure-vpn-gateway-and-my-on-premises-network"></a>¿Puedo tener más de un túneles entre la puerta de enlace VPN de Azure y mi red local?

Sí, puede establecer varios túneles VPN S2S entre una puerta de enlace VPN de Azure y su red local. Tenga en cuenta que se van a contar todos estos túneles contra el número total de túneles para las puertas de enlace VPN de Azure. Por ejemplo, si tiene dos túneles redundantes entre la puerta de enlace VPN de Azure y uno de su red local, se consumen 2 túneles fuera de la cuota de la puerta de enlace VPN de Azure (10 estándar) y el 30 de alto rendimiento.

### <a name="can-i-have-multiple-tunnels-between-two-azure-vnets-with-bgp"></a>¿Puedo tener varios túneles entre dos VNets de Azure con BGP?

No, no se admiten túneles redundantes entre un par de redes virtuales.

### <a name="can-i-use-bgp-for-s2s-vpn-in-an-expressroutes2s-vpn-co-existence-configuration"></a>¿Puedo usar BGP para VPN S2S en una configuración de coexistencia VPN ExpressRoute/S2S?

No en este momento.

### <a name="what-address-does-azure-vpn-gateway-use-for-bgp-peer-ip"></a>¿Qué dirección usar puerta de enlace VPN de Azure para BGP punto IP?

La puerta de enlace VPN de Azure asignará una dirección IP del intervalo de GatewaySubnet definido para la red virtual. De forma predeterminada, es la segunda última dirección del intervalo. Por ejemplo, si la GatewaySubnet es 10.12.255.0/27, comprendido entre 10.12.255.0 y 10.12.255.31, a continuación, la dirección IP de punto de BGP en la puerta de enlace VPN de Azure será 10.12.255.30. Puede encontrar esta información cuando lista la información de la puerta de enlace VPN de Azure.

### <a name="what-are-the-requirements-for-the-bgp-peer-ip-addresses-on-my-vpn-device"></a>¿Cuáles son los requisitos para las direcciones IP de punto de BGP en mi dispositivo VPN?

Su BGP local del mismo nivel dirección **No debe** ser igual que la dirección IP pública del dispositivo VPN. Usar una dirección IP diferente en el dispositivo VPN para su dirección IP del mismo nivel de BGP. Puede ser una dirección asignada a la interfaz de bucle en el dispositivo. Especificar esta dirección en el enlace de red Local correspondiente que representa la ubicación.

### <a name="what-should-i-specify-as-my-address-prefixes-for-the-local-network-gateway-when-i-use-bgp"></a>¿Qué debo especificar como mi prefijos de direcciones de la puerta de enlace de red Local al utilizar BGP?

Puerta de enlace de red Local Azure especifica los prefijos de dirección inicial para la red local. Con BGP, debe asignar el prefijo de host (/ 32 del prefijo) de la dirección IP de punto de BGP como el espacio de direcciones para esa red local. Si su dirección IP del mismo nivel de BGP es 10.52.255.254, debe especificar "10.52.255.254/32" como la localNetworkAddressSpace de la puerta de enlace de red Local que representa esta red local. Esto es para asegurarse de que la puerta de enlace VPN de Azure establece una sesión de BGP a través del túnel VPN S2S.

### <a name="what-should-i-add-to-my-on-premises-vpn-device-for-the-bgp-peering-session"></a>¿Qué debo agregar a mi dispositivo VPN local para la sesión de interconexión BGP?

Deberá agregar una ruta de host de la dirección IP del mismo nivel de Azure BGP en el dispositivo VPN señalando el túnel IPsec S2S VPN. Por ejemplo, si la dirección IP del mismo nivel de Azure VPN es "10.12.255.30", deberá agregar una ruta de host para "10.12.255.30" con una interfaz de siguiente salto de la interfaz de túnel IPsec coincidente en el dispositivo VPN.
