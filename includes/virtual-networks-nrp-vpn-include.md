## <a name="vpn-gateway"></a>Puerta de enlace VPN 
Un recurso de puerta de enlace VPN le permite crear una conexión segura entre su centro de datos local y Azure. Un recurso de puerta de enlace VPN se puede configurar en tres maneras distintas:
 
- **Seleccione sitios** : puede tener acceso a los recursos de Azure alojados en un VNET mediante un cliente VPN desde cualquier equipo de forma segura. 
- **Conexión de varios sitios** , puede conectar segura de los centros de datos locales a los recursos que se ejecuta en una VNET. 
- **VNET a VNET** : puede conectar de forma segura en Azure VNETS dentro de la misma región o en regiones para generar cargas de trabajo con geo redundancia.

Propiedades de clave de puerta de enlace VPN incluyen:
 
- **Tipo de puerta de enlace** - dinámicamente enrutadas o una puerta de enlace enrutado estático. 
- **Grupo de dirección de cliente de VPN prefijo** : direcciones IP que se asignará a los clientes que se conecten en un punto de la configuración del sitio.