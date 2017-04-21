<properties 
   pageTitle="Opciones de resolución de nombres de DNS para máquinas virtuales de Linux en Azure"
   description="Nombre servicios de resolución de DNS escenarios para máquinas virtuales de Linux en Azure IaaS, incluidas proporcionados, servidor externo de DNS y de poner su propio híbrido."
   services="virtual-machines"
   documentationCenter="na"
   authors="RicksterCDN"
   manager="timlt"
   editor="tysonn" />
<tags 
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/19/2016"
   ms.author="rclaus" />

# <a name="dns-name-resolution-options-for-linux-vms-in-azure"></a>Opciones de resolución de nombre DNS para máquinas virtuales de Linux en Azure

Azure proporciona resolución de nombres DNS predeterminada para todas las máquinas virtuales dentro de una única red Virtual. Es posible implementar su propia solución de resolución de nombre DNS mediante la configuración de sus propios servicios DNS en su Azure VM hospedadas. Los siguientes escenarios ayudarán a elegir cuál funciona mejor para su situación particular.

- [Resolución de nombre proporcionado Azure](#azure-provided-name-resolution)

- [Resolución de nombres con su propio servidor DNS](#name-resolution-using-your-own-dns-server) 

El tipo de resolución de nombres que use depende de qué necesitan su máquinas virtuales y las instancias de función para comunicarse con ellos.

**La siguiente tabla muestra los escenarios y soluciones de resolución de nombre correspondientes:**

| **Escenario** | **Solución** | **Sufijo** |
|--------------|--------------|----------|
| Resolución de nombres entre las instancias de función o máquinas virtuales que se encuentra en la misma red virtual | [Resolución de nombre proporcionado Azure](#azure-provided-name-resolution)| nombre de host o FQDN |
| Resolución de nombres entre las instancias de función o máquinas virtuales ubicadas en diferentes redes virtuales | Administrado por el cliente DNS servers reenvío de las consultas entre vnets para la resolución de Azure (proxy DNS).  consulte [con su propio servidor DNS de resolución de nombres](#name-resolution-using-your-own-dns-server)| FQDN solo |
| Resolución de nombres de servicio y equipo local de instancias de la función o máquinas virtuales en Azure | Administrado por el cliente servidores DNS (por ejemplo, controlador de dominio local, controlador de dominio local de sólo lectura o un DNS secundario sincronizado con las transferencias de zona).  Consulte [con su propio servidor DNS de resolución de nombres](#name-resolution-using-your-own-dns-server)|FQDN solo |
| Resolución de nombres de host de Azure de equipos locales | Reenviar consultas a un servidor proxy DNS de administrado por el cliente en la vnet correspondiente, el servidor proxy reenvía consultas a Azure para la resolución. Consulte [con su propio servidor DNS de resolución de nombres](#name-resolution-using-your-own-dns-server)| FQDN solo |
| DNS inversa para direcciones IP interno | [Resolución de nombres con su propio servidor DNS](#name-resolution-using-your-own-dns-server) | n/a. |

## <a name="azure-provided-name-resolution"></a>Resolución de nombre proporcionado Azure

Junto con la resolución de nombres DNS públicos, Azure proporciona la resolución de nombres interno para máquinas virtuales y las instancias de función que se encuentran dentro de la misma red virtual.  En redes virtuales basado en ARM, el sufijo DNS es coherente en toda la red virtual (por lo que no es necesario el FQDN) y nombres DNS pueden asignarse a NIC y máquinas virtuales. Aunque la resolución de nombres siempre Azure no requiere ninguna configuración, no es la opción adecuada para todos los escenarios de implementación, tal como se muestra en la tabla anterior.

### <a name="features-and-considerations"></a>Características y consideraciones

**Características:**

- Facilidad de uso: se requiere ninguna configuración para usar la resolución de nombre proporcionado Azure.

- El servicio de resolución de nombre proporcionado Azure es altamente disponible, lo que le ahorra la necesidad de crear y administrar grupos de sus propios servidores DNS.

- Puede usarse junto con sus propios servidores DNS para resolver los nombres de host local y Azure.

- Resolución de nombres se proporciona entre máquinas virtuales en redes virtuales sin necesidad de FQDN. 

- Puede usar nombres de host que mejor se describen las implementaciones, en lugar de trabajar con nombres generado automáticamente.

**Consideraciones:**

- No se puede modificar el sufijo DNS creado Azure.

- No se puede registrar manualmente sus propios registros.

- WINS y NetBIOS no son compatibles.

- Nombres de host deben ser compatible con DNS (deben utilizar solo 0-9, a z y '-' y no puede iniciar o finalizar con un '-'. Consulte RFC 3696 sección 2).

- Tráfico de consultas DNS está limitado para cada máquina virtual. Esto no debe afectar la mayoría de las aplicaciones.  Si se observa la limitación de solicitudes, asegúrese de que está habilitada la caché del cliente.  Para obtener más información, vea [obtener el máximo provecho de resolución de nombres de Azure le ha proporcionado](#Getting-the-most-from-Azure-provided-name-resolution).


### <a name="getting-the-most-from-azure-provided-name-resolution"></a>Obtener el máximo partido de resolución de nombre proporcionado Azure
**Almacenamiento en caché del cliente:**

No todas las consultas DNS se envían a través de la red.  Almacenamiento en caché del cliente, ayuda a reducir la latencia y mejorar la resistencia a señales de red mediante la resolución periódicas consultas DNS desde una caché local.  Registros DNS contienen un Time To Live (TTL) que permite la memoria caché almacenar el registro tanto como sea posible sin afectar a la actualización de registro.  Por este motivo, la caché del cliente es adecuado para la mayoría de las situaciones.

Algunos distros Linux no incluya almacenamiento en caché de forma predeterminada.  Se recomienda que agregar uno para cada VM Linux (después de comprobar que no hay una caché local ya).

Hay varias DNS caché paquetes diferentes disponibles, por ejemplo dnsmasq, estos son los pasos para instalar dnsmasq en la distros más comunes:

- **Ubuntu (usa resolvconf)**:
    - Instale el paquete de dnsmasq ("sudo instalar apt get dnsmasq").
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

> [AZURE.NOTE]: El paquete 'dnsmasq' sólo es la caché de DNS muchos disponible para Linux.  Antes de usarla, compruebe su idoneidad para sus necesidades concretas y que no hay otra caché está instalada.

**Reintentos de cliente:**

DNS es principalmente un protocolo UDP.  Según el protocolo UDP no garantiza la entrega de mensajes, lógica de reintento se trata en el propio protocolo DNS.  Cada cliente DNS (sistema operativo) puede provocar lógica de reintento diferentes según la preferencia de creadores:

 - Sistemas operativos Windows Reintentar después de una segunda y, a continuación, vuelva a después de otro dos, cuatro y otro cuatro segundos. 
 - Reintentos de instalación de Linux predeterminado después de cinco segundos.  Debe cambiar esta opción para volver a intentar cinco veces en intervalos de un segundo.  

Para comprobar la configuración actual de una VM Linux, 'gato /etc/resolv.conf' y busque en la línea 'opciones', por ejemplo:

    options timeout:1 attempts:5

El archivo resolv.conf genera automáticamente y no debe editarse.  Los pasos específicos para agregar la línea 'opciones' varían por distro:

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
Hay diversas situaciones donde sus necesidades de resolución de nombre pueden ir más allá de las características proporcionados por Azure, por ejemplo cuando se requieren una resolución DNS entre redes virtuales (vnets).  Para cubrir este escenario, Azure proporciona la capacidad de usar sus propios servidores DNS.  

Los servidores DNS de una red virtual pueden reenviar consultas DNS a resoluciones de recursiva de Azure para resolver los nombres de host dentro de una red virtual.  Por ejemplo, un servidor DNS que se ejecuta en Azure puede responder a las consultas DNS para sus propios archivos de zona DNS y reenviar todas las demás consultas a Azure.  Esto permite máquinas virtuales ver ambas las entradas en sus archivos de zona y los nombres de host de Azure le ha proporcionado (mediante el reenviador).  Se proporciona acceso a las resoluciones recursivas de Azure a través de la dirección IP virtual 168.63.129.16.

Reenvío DNS también vnet entre la resolución DNS y permite a los equipos local resolver los nombres de host de Azure le ha proporcionado.  Para resolver el nombre de host de la VM, el servidor DNS VM debe estar en la misma red virtual y configurarse para reenviar hostname consultas en Azure.  Como el sufijo DNS es diferente en cada vnet, puede usar las reglas de reenvío condicional para enviar consultas DNS a la vnet correcto para la resolución.  La imagen siguiente muestra dos vnets y una red local haciendo vnet entre la resolución DNS con este método:

![Vnet entre DNS](./media/virtual-machines-linux-azure-dns/inter-vnet-dns.png)

Cuando utiliza la resolución de nombre proporcionado Azure, el sufijo DNS interna se proporciona a cada VM mediante DHCP.  Cuando se utiliza su propia solución de resolución de nombre, no se proporciona este sufijo a máquinas virtuales porque afecte con otras arquitecturas DNS.  Para hacer referencia a equipos por FQDN o para configurar el sufijo en sus máquinas virtuales, puede determinar el sufijo con PowerShell o con la API:

-  Para administración de recursos de Azure administrado vnets, el sufijo está disponible a través de los recursos de [la tarjeta de interfaz de red](https://msdn.microsoft.com/library/azure/mt163668.aspx) , o puede ejecutar el comando `azure network public-ip show <resource group> <pip name>` para mostrar los detalles de su dirección IP público, incluidos el FQDN de NIC.    


Si el reenvío de las consultas en Azure no ajusten a sus necesidades, debe proporcionar su propia solución DNS.  Su solución DNS necesita:

-  Proporcionar la resolución de nombre de host correspondiente, por ejemplo a través de [DDNS](../virtual-network/virtual-networks-name-resolution-ddns.md).  Tenga en cuenta que si usa DDNS debe deshabilitar borrado de registros de DNS como concesiones de Azure son muy larga y borrado puede quitar registros DNS antes de tiempo. 
-  Proporcionar resolución recursiva apropiado para permitir la resolución de nombres de dominio externo.
-  Ser accesibles (TCP y UDP en el puerto 53) de los clientes que sirve y tener acceso a internet.
-  Protegerse contra el acceso desde internet, mitigar amenazas agentes externos.

> [AZURE.NOTE] Mejor rendimiento al utilizar máquinas virtuales de Azure como servidores DNS, debe estar deshabilitada IPv6 y un [Nivel de instancia público IP](../virtual-network/virtual-networks-instance-level-public-ip.md) debe asignarse a cada servidor DNS VM.  

