Cuando se crea una puerta de enlace de red virtual, debe especificar la puerta de enlace SKU que desea usar. Cuando se selecciona una puerta de enlace superior SKU, más CPU y ancho de banda de red están asignados a la puerta de enlace y por tanto, la puerta de enlace compatible con la velocidad de red superior a la red virtual.

Puerta de enlace VPN puede usar las SKU siguientes:

- Básico
- Estándar
- Alto rendimiento

Al seleccionar un SKU, considere lo siguiente:

- Si desea usar un tipo de PolicyBased VPN, debe utilizar el SKU básica. VPN PolicyBased (anteriormente denominado enrutamiento estático) no son compatibles con los demás SKU.
- BGP no es compatible en el SKU básica.
- Coexistencia de puerta de enlace VPN ExpressRoute configuraciones no son compatibles con la SKU básica.
- Conexiones de puerta de enlace de VPN S2S activo activo se pueden configurar en el SKU HighPerformance solo.
