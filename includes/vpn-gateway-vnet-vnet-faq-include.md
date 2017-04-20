- Las redes virtuales pueden estar en la mismas u otra Azure las regiones (ubicaciones).

- Un servicio de nube o un extremo de equilibrio de carga no puede abarcar redes virtuales, incluso si están conectados juntos.

- Conectar varias redes virtuales Azure no requiere las puertas de enlace VPN local a menos que se requiere conectividad entre local.

- VNet a VNet es compatible con la conexión de redes virtual. No admite la conexión máquinas virtuales o servicios no está en una red virtual de nube.

- VNet a VNet requiere puertas de enlace VPN de Azure con RouteBased (anteriormente denominado enrutamiento dinámico) tipos VPN. 

- Conectividad de red virtual puede usarse simultáneamente con múltiples sitios VPN, con un máximo de 10 (puertas de enlace predeterminado o estándar) o 30 (alto rendimiento puertas de enlace) VPN establece un túnel de una puerta de enlace de red virtual VPN conectarse a otras redes de virtuales o sitios local.

- Los espacios de direcciones de los sitios de red local de redes y local virtuales no deben superponerse. Superposición de espacios de direcciones hará que la creación de conexiones de VNet-VNet no se realice correctamente.

- No se admiten túneles redundantes entre un par de redes virtuales.

- Todos los túneles VPN de la red virtual comparten el ancho de banda de la puerta de enlace VPN de Azure y la misma actividad de puerta de enlace VPN SLA en Azure.

- Tráfico de VNet a VNet viaja a través de Microsoft Network, no con Internet.

- Tráfico de VNet a VNet dentro de la misma región es gratuito para ambas direcciones; entre región VNet a VNet salida tráfico se cargará con las tasas de transferencia de datos entre VNet salientes en función de las regiones de origen. Consulte la [página de precios](https://azure.microsoft.com/pricing/details/vpn-gateway/) para obtener más detalles.