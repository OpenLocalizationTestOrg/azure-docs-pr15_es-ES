##<a name="tcp-settings-for-azure-vms"></a>Configuración de TCP para máquinas virtuales de Azure

Máquinas virtuales de Azure comunican con Internet pública mediante [NAT] [ nat] (traducción de direcciones de red). Dispositivos NAT asignar una dirección IP pública y puerto a una máquina virtual de Azure, permitiendo que VM para establecer un socket para la comunicación con otros dispositivos. Si paquetes dejar que pasan a través de ese socket después de una hora determinada, el dispositivo NAT elimina la asignación y el socket está libre para usarlo con otras máquinas virtuales.

Se trata de un comportamiento NAT comunes, que puede provocar problemas de comunicación en aplicaciones basadas en TCP espera un socket mantenerse más allá de un período de tiempo de espera. Existen dos opciones de tiempo de espera inactivo para tener en cuenta para las sesiones en un estado *establecido conexión* :

- **entrantes** a través del [equilibrador de carga de Azure][azure-lb-timeout]. Tiempo de espera predeterminado es 4 minutos y puede ajustarse a 30 minutos.
- **salida** con [SNAT] [ snat] (NAT de origen). Tiempo de espera se establece en 4 minutos y no se puede ajustar.

Para asegurarse de que no se pierden las conexiones más allá del límite de tiempo de espera, debe asegurarse de la aplicación mantiene la sesión activo, o puede configurar el sistema operativo subyacente para hacerlo. La configuración que se utilizarán es diferente para sistemas Linux y Windows, tal como se muestra a continuación.

Para [Linux][linux], debe cambiar las variables de kernel siguientes.
NET.IPv4.tcp_keepalive_time = 120 net.ipv4.tcp_keepalive_intvl = 30 net.ipv4.tcp_keepalive_probes = 8
 
Para [Windows][windows], debe cambiar los valores del registro siguiente.
KeepAliveInterval = 30 KeepAliveTime = 120 TcpMaxDataRetransmissions = 8


La configuración anterior garantizar un paquete activo se envía después de 2 minutos (120 segundos) tiempo de inactividad y, a continuación, envía cada 30 segundos. Y si no 8 de esos paquetes, se interrumpe la sesión.

<!-- links -->
[nat]: http://computer.howstuffworks.com/nat.htm
[snat]: ../load-balancer/load-balancer-overview.md/#source-nat
[linux]: http://tldp.org/HOWTO/TCP-Keepalive-HOWTO/usingkeepalive.html
[windows]: http://blogs.technet.com/b/nettracer/archive/2010/06/03/things-that-you-may-want-to-know-about-tcp-keepalives.aspx
[azure-lb-timeout]: ../load-balancer/load-balancer-tcp-idle-timeout.md