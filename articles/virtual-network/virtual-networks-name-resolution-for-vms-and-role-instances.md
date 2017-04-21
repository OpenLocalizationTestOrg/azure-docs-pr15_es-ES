<properties 
   pageTitle="Resolución de máquinas virtuales y las instancias de función"
   description="Escenarios de resolución de nombres para Azure IaaS, soluciones híbridas, entre servicios de nube diferente, Active Directory y usar su propio servidor DNS "
   services="virtual-network"
   documentationCenter="na"
   authors="GarethBradshawMSFT"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/31/2016"
   ms.author="telmos" />

# <a name="name-resolution-for-vms-and-role-instances"></a>Resolución de nombres para máquinas virtuales y las instancias de función

Dependiendo de cómo usar Azure para hospedar IaaS, PaaS y soluciones híbridas, debe permitir que las máquinas virtuales y las instancias de función que se crean para comunicarse entre sí. Aunque esta comunicación puede hacerse mediante direcciones IP, es más sencillo usar nombres que pueden ser fáciles de recordar y no cambian. 

Cuando las instancias de función y máquinas virtuales hospedadas en Azure necesitan resolver los nombres de dominio a direcciones IP internas, puede usar uno de estos dos métodos:

- [Resolución de nombre proporcionado Azure](#azure-provided-name-resolution)

- [Resolución de nombres con su propio servidor DNS](#name-resolution-using-your-own-dns-server) (lo que puede reenviar consultas a los servidores DNS siempre Azure) 

El tipo de resolución de nombres que use depende de qué necesitan su máquinas virtuales y las instancias de función para comunicarse con ellos.

**La siguiente tabla muestra los escenarios y soluciones de resolución de nombre correspondientes:**

| **Escenario** | **Solución** | **Sufijo** |
|--------------|--------------|----------|
| Resolución de nombres entre las instancias de función o máquinas virtuales que se encuentran en el mismo servicio de nube o una red virtual | [Resolución de nombre proporcionado Azure](#azure-provided-name-resolution)| nombre de host o FQDN |
| Resolución de nombres entre las instancias de función o máquinas virtuales ubicadas en diferentes redes virtuales | Administrado por el cliente DNS servers reenvío de las consultas entre vnets para la resolución de Azure (proxy DNS).  consulte [con su propio servidor DNS de resolución de nombres](#name-resolution-using-your-own-dns-server)| FQDN solo |
| Resolución de nombres de servicio y equipo local de instancias de la función o máquinas virtuales en Azure | Administrado por el cliente servidores DNS (controlador de dominio, por ejemplo, local, controlador de dominio local de solo lectura o un DNS secundario sincronizado con las transferencias de zona).  Consulte [con su propio servidor DNS de resolución de nombres](#name-resolution-using-your-own-dns-server)|FQDN solo |
| Resolución de nombres de host de Azure de equipos locales | Reenviar consultas a un servidor proxy DNS de administrado por el cliente en la vnet correspondiente, el servidor proxy reenvía consultas a Azure para la resolución. Consulte [con su propio servidor DNS de resolución de nombres](#name-resolution-using-your-own-dns-server)| FQDN solo |
| DNS inversa para direcciones IP interno | [Resolución de nombres con su propio servidor DNS](#name-resolution-using-your-own-dns-server) | n/a. |
| Resolución de nombres entre máquinas virtuales o las instancias de función ubicadas en servicios de nube diferente, no en una red virtual| No aplicable. No se admite la conectividad entre máquinas virtuales y servicios de nube diferente de las instancias de función fuera de una red virtual.| n/a. |



## <a name="azure-provided-name-resolution"></a>Resolución de nombre proporcionado Azure

Junto con la resolución de nombres DNS públicos, Azure proporciona la resolución de nombres interno para máquinas virtuales y las instancias de función que se encuentran dentro de la misma red virtual o servicio de nube.  Máquinas virtuales o instancias en un servicio de nube compartir el mismo sufijo DNS (para que el nombre de host solo es suficiente), pero en nube diferente de redes virtuales clásica servicios tener distintos sufijos DNS para que el FQDN es necesaria para resolver nombres entre servicios de nube diferente.  En redes virtuales en el modelo de implementación de administrador de recursos, el sufijo DNS es coherente en toda la red virtual (por lo que no es necesario el FQDN) y nombres DNS pueden asignarse a NIC y máquinas virtuales. Aunque la resolución de nombres siempre Azure no requiere ninguna configuración, no es la opción adecuada para todos los escenarios de implementación, tal como se muestra en la tabla anterior.

> [AZURE.NOTE] En el caso de los roles de web y trabajador, también puede acceder a las direcciones IP internas de instancias de función en función del número de nombre y la instancia de función con la API de REST de administración de servicio de Azure. Para obtener más información, vea [Referencia de la API de REST de servicio de administración](https://msdn.microsoft.com/library/azure/ee460799.aspx).

### <a name="features-and-considerations"></a>Características y consideraciones

**Características:**

- Facilidad de uso: se requiere ninguna configuración para usar la resolución de nombres de Azure le ha proporcionado.

- El servicio de resolución de nombre proporcionado Azure es altamente disponible, lo que le ahorra la necesidad de crear y administrar grupos de sus propios servidores DNS.

- Puede usarse junto con sus propios servidores DNS para resolver los nombres de host local y Azure.

- Resolución de nombres se proporciona entre instancias de función o máquinas virtuales dentro del mismo servicio de nube sin necesidad de un nombre de dominio completo.

- Resolución de nombres se proporciona entre máquinas virtuales en redes virtuales que utilizan el modelo de implementación de administrador de recursos, sin necesidad de FQDN. Redes virtuales en el modelo de implementación clásica requieren FQDN al resolver los nombres de los servicios de nube diferente. 

- Puede usar nombres de host que mejor se describen las implementaciones, en lugar de trabajar con nombres generado automáticamente.

**Consideraciones:**

- No se puede modificar el sufijo DNS creado Azure.

- No se puede registrar manualmente sus propios registros.

- WINS y NetBIOS no son compatibles. (No puede ver sus máquinas virtuales en el Explorador de Windows).

- Nombres de host deben ser compatible con DNS (deben utilizar solo 0-9, a z y '-' y no puede iniciar o finalizar con un '-'. Consulte RFC 3696 sección 2).

- Tráfico de consultas DNS está limitado para cada máquina virtual. Esto no debe afectar la mayoría de las aplicaciones.  Si se observa la limitación de solicitudes, asegúrese de que está habilitada la caché del cliente.  Para obtener más detalles, vea [obtener el máximo provecho de resolución de nombres de Azure le ha proporcionado](#Getting-the-most-from-Azure-provided-name-resolution).

- Solo máquinas virtuales en los servicios de 180 nube primera registradas para cada red virtual en un modelo de implementación clásico. No se aplica a redes virtuales en los modelos de implementación de administrador de recursos.


### <a name="getting-the-most-from-azure-provided-name-resolution"></a>Obtener el máximo partido de resolución de nombre proporcionado Azure
**Almacenamiento en caché del cliente:**

No todas las consultas DNS deben enviarse a través de la red.  Almacenamiento en caché del cliente, ayuda a reducir la latencia y mejorar la resistencia a señales de red mediante la resolución periódicas consultas DNS desde una caché local.  Registros DNS contienen un Time To Live (TTL) que permite la memoria caché almacenar el registro tanto como sea posible sin afectar a la actualización de registro, por lo que la caché del cliente es adecuado para la mayoría de las situaciones.

El valor predeterminado de cliente de DNS de Windows tiene una caché del DNS integrada.  Algunos Linux distros no incluye almacenamiento en caché de forma predeterminada, se recomienda que se agrega uno a cada VM Linux (después de comprobar que no hay una caché local ya).

Hay una serie de diferentes DNS caché paquetes disponibles, por ejemplo, dnsmasq, estos son los pasos para instalar dnsmasq en la distros más comunes:

- **Ubuntu (usa resolvconf)**:
    - sólo tiene que instalar el paquete de dnsmasq ("sudo instalar apt get dnsmasq").
- **SUSE (usa netconf)**:
    - instalar el paquete de dnsmasq ("sudo zypper instalar dnsmasq") 
    - habilitar el servicio de dnsmasq ("systemctl habilitar dnsmasq.service") 
    - iniciar el servicio de dnsmasq ("systemctl inicio dnsmasq.service") 
    - Editar "/ etcetera/sysconfig/red/config" y cambie NETCONFIG_DNS_FORWARDER = "" a "dnsmasq"
    - actualizar resolv.conf ("netconfig actualización") para establecer la memoria caché como el solucionador DNS local
- **OpenLogic (usa NetworkManager)**:
    - instalar el paquete de dnsmasq ("sudo yum instalar dnsmasq")
    - habilitar el servicio de dnsmasq ("systemctl habilitar dnsmasq.service")
    - iniciar el servicio de dnsmasq ("systemctl inicio dnsmasq.service")
    - Agregar "detrás de servidores de nombres de dominio 127.0.0.1;" a "/etc/dhclient-eth0.conf"
    - Reinicie el servicio de red ("reinicio de red de servicio") para establecer la memoria caché como el solucionador DNS local

> [AZURE.NOTE] El paquete 'dnsmasq' sólo es la caché de DNS muchos disponible para Linux.  Antes de usarla, compruebe su idoneidad para sus necesidades concretas y que no hay otra caché está instalada.

**Reintentos de cliente:**

DNS es principalmente un protocolo UDP.  Según el protocolo UDP no garantiza la entrega de mensajes, lógica de reintento se trata en el propio protocolo DNS.  Cada cliente DNS (sistema operativo) puede provocar lógica de reintento diferentes según la preferencia de creadores:

 - Sistemas operativos Windows Reintentar después de 1 segundo y, a continuación, vuelva a después de otro 2, 4 y otro 4 segundos. 
 - Reintentos de instalación de Linux predeterminado después de 5 segundos.  Se recomienda cambiar esta opción para volver a intentar 5 por intervalos de 1 segundo.  

Para comprobar la configuración actual de una VM Linux, 'gato /etc/resolv.conf' y busque en la línea 'opciones', por ejemplo:

    options timeout:1 attempts:5

El archivo resolv.conf automática normalmente se genera y no debe editarse.  Los pasos específicos para agregar la línea 'opciones' varían por distro:

- **Ubuntu** (usa resolvconf):
    - Agregue la línea de opciones a ' / etc/resolveconf/resolv.conf.d/head' 
    - Ejecute 'resolvconf -u' actualizar
- **SUSE** (usa netconf):
    - Agregar 'timeout:1 intentos: 5' a la NETCONFIG_DNS_RESOLVER_OPTIONS = "" parámetro en '/ etcetera/sysconfig/red/config' 
    - Ejecute 'netconfig update' actualizar
- **OpenLogic** (usa NetworkManager):
    - Agregar 'eco "opciones timeout:1 intentos: 5" ' a ' / etc/NetworkManager/dispatcher.d/11-dhclient' 
    - ejecutar 'servicio reinicio de red' actualizar

## <a name="name-resolution-using-your-own-dns-server"></a>Resolución de nombres con su propio servidor DNS
Hay una serie de situaciones en las que sus necesidades de resolución de nombre pueden ir más allá de las características proporcionadas por Azure, por ejemplo cuando usando los dominios de Active Directory o cuando se requiere la resolución DNS entre redes virtuales (vnets).  Para cubrir estos escenarios, Azure proporciona la capacidad de usar sus propios servidores DNS.  

Los servidores DNS de una red virtual pueden reenviar consultas DNS a resoluciones de recursiva de Azure para resolver los nombres de host dentro de una red virtual.  Por ejemplo, un controlador de dominio (DC) ejecuta en Azure puede responder a las consultas DNS para sus dominios y reenviar todas las demás consultas a Azure.  Esto permite máquinas virtuales ver el recursos locales (mediante el controlador de dominio) y los nombres de host de Azure le ha proporcionado (mediante el reenviador).  Se proporciona acceso a las resoluciones recursivas de Azure a través de la dirección IP virtual 168.63.129.16.

Reenvío DNS también vnet entre la resolución DNS y permite a los equipos local resolver los nombres de host de Azure le ha proporcionado.  Para resolver el nombre de host de la VM, el servidor DNS VM debe estar en la misma red virtual y configurar las consultas de reenvío de nombre de host para Azure.  Como el sufijo DNS es diferente en cada vnet, puede usar las reglas de reenvío condicional para enviar consultas DNS a la vnet correcto para la resolución.  La imagen siguiente muestra dos vnets y una red local haciendo vnet entre la resolución DNS con este método.  Un reenviador DNS de ejemplo está disponible en la [Galería de plantillas de Azure tutorial rápido](https://azure.microsoft.com/documentation/templates/301-dns-forwarder/) y [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder).

![Vnet entre DNS](./media/virtual-networks-name-resolution-for-vms-and-role-instances/inter-vnet-dns.png)

Cuando se usa la resolución de nombres de Azure le ha proporcionado un sufijo DNS interno (*. internal.cloudapp.net) se proporciona para cada VM mediante DHCP.  Esto permite resolución de nombre de host, como los registros de nombre de host están en la zona internal.cloudapp.net.  Cuando se utiliza su propia solución de resolución de nombre, no se proporciona el sufijo IDN en máquinas virtuales porque afecte con otras arquitecturas DNS (como escenarios de dominio).  En su lugar, le proporcionamos un marcador de posición no funciona (reddog.microsoft.com).  

Si es necesario, puede determinar el sufijo DNS interno con PowerShell o con la API:

-  Para redes virtuales en los modelos de implementación de administrador de recursos, el sufijo está disponible a través de los recursos de [la tarjeta de interfaz de red](https://msdn.microsoft.com/library/azure/mt163668.aspx) o mediante el cmdlet [Get-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619434.aspx) .    
-  En los modelos de implementación clásica, el sufijo está disponible a través de la llamada de la [API de implementación de obtener](https://msdn.microsoft.com/library/azure/ee460804.aspx) o a través de la [AzureVM Get-depurar](https://msdn.microsoft.com/library/azure/dn495236.aspx) cmdlet.


Si el reenvío de las consultas en Azure no ajusten a sus necesidades, debe proporcionar su propia solución DNS.  Necesitará la solución DNS:

-  Proporcionar la resolución de nombre de host correspondiente, por ejemplo, mediante [DDNS](virtual-networks-name-resolution-ddns.md).  Tenga en cuenta que si usa DDNS debe deshabilitar borrado de registros de DNS como concesiones de Azure son muy larga y borrado puede quitar registros DNS antes de tiempo. 
-  Proporcionar resolución recursiva apropiado para permitir la resolución de nombres de dominio externo.
-  Ser accesibles (TCP y UDP en el puerto 53) de los clientes que sirve y tener acceso a internet.
-  Protegerse contra el acceso desde internet, mitigar amenazas agentes externos.

> [AZURE.NOTE] Mejor rendimiento al utilizar máquinas virtuales de Azure como servidores DNS, debe estar deshabilitada IPv6 y un [Nivel de instancia público IP](virtual-networks-instance-level-public-ip.md) debe asignarse a cada servidor DNS VM.  Si decide usar Windows Server como su servidor DNS, [en este artículo](http://blogs.technet.com/b/networking/archive/2015/08/19/name-resolution-performance-of-a-recursive-windows-dns-server-2012-r2.aspx) proporciona las optimizaciones y análisis de rendimiento adicionales.


### <a name="specifying-dns-servers"></a>Especificación de servidores DNS

Cuando se utiliza sus propios servidores DNS, Azure proporciona la capacidad de especificar varios servidores DNS por una red virtual o por la interfaz de red (jefe de recursos) / (clásico) de servicio de nube.  Los servidores DNS especificados para una interfaz de red o servicio de nube Obtén prioridad sobre las especificadas en una red virtual.

> [AZURE.NOTE] Propiedades de conexión de red, como el servidor DNS de direcciones IP, no debe editarse directamente en máquinas virtuales de Windows tal como lo pueden obtener borrarán durante el servicio corregir cuando obtiene reemplaza el adaptador de red virtual. 


Cuando se utiliza el modelo de implementación de administrador de recursos, pueden especificarse los servidores DNS en el Portal de la API/plantillas ([vnet](https://msdn.microsoft.com/library/azure/mt163661.aspx), [nic](https://msdn.microsoft.com/library/azure/mt163668.aspx)) o PowerShell ([vnet](https://msdn.microsoft.com/library/mt603657.aspx), [nic](https://msdn.microsoft.com/library/mt619370.aspx)).

Cuando se utiliza el modelo de implementación clásica, los servidores DNS de la red virtual se pueden especificar en el Portal o [el archivo de *Configuración de red* ](https://msdn.microsoft.com/library/azure/jj157100).  Para los servicios de nube, los servidores DNS se especifican mediante [un archivo de *Configuración del servicio* ](https://msdn.microsoft.com/library/azure/ee758710) o en PowerShell ([AzureVM nuevo](https://msdn.microsoft.com/library/azure/dn495254.aspx)).

> [AZURE.NOTE] Si cambia la configuración de DNS para un equipo de red o virtual que ya se ha implementado, debe reiniciar cada VM afectado para que los cambios surtan efecto.


## <a name="next-steps"></a>Pasos siguientes

Modelo de implementación de administrador de recursos:

- [Crear o actualizar una red virtual](https://msdn.microsoft.com/library/azure/mt163661.aspx)
- [Crear o actualizar una tarjeta de interfaz de red](https://msdn.microsoft.com/library/azure/mt163668.aspx)
- [Nueva AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603657.aspx)
- [Nueva AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619370.aspx)

 
Modelo de implementación clásica:

- [Esquema de configuración de servicio de Azure](https://msdn.microsoft.com/library/azure/ee758710)
- [Esquema de configuración de red virtual](https://msdn.microsoft.com/library/azure/jj157100)
- [Configurar una red Virtual mediante un archivo de configuración de red](virtual-networks-using-network-configuration-file.md) 

