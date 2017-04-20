## <a name="virtual-network-basics"></a>Conceptos básicos de red virtual

### <a name="what-is-an-azure-virtual-network-vnet"></a>¿Qué es una red Virtual de Azure (VNet)?

Puede usar VNets para aprovisionar y administrar redes privadas virtuales (VPN) en Azure y, opcionalmente, vincular la VNets con otros VNets en Azure o con su local infraestructura de TI para crear soluciones entre locales o híbridos. Cada VNet que crear tiene su propio bloque CIDR y se pueden vincular a otras redes VNets y local, como los bloques CIDR no entran en conflicto. También tiene controles de configuración del servidor DNS para VNets y segmentación de la VNet en subredes.

Use VNets para:

- Crear una red virtual dedicada a un solo de nube privada

    A veces no necesitan una configuración local de cruz para la solución. Cuando se crea un VNet, los servicios y máquinas virtuales dentro de su VNet pueden comunicarse directamente y de forma segura entre sí en la nube. Mantiene el tráfico de forma segura en el VNet, pero le permite configurar conexiones de punto final para las máquinas virtuales y servicios que requieren comunicaciones de Internet como parte de su solución.

- Ampliar su centro de datos de forma segura

    Con VNets, se pueden generar tradicionales VPN (S2S) de sitio a otro para ajustar la capacidad de su centro de datos de forma segura. VPN S2S utilizar IPSEC para proporcionar una conexión segura entre la puerta de enlace VPN corporativo y Azure.

- Habilitar escenarios de nube híbrida

    VNets proporcionarle la flexibilidad para admitir una gama de escenarios de nube híbrida. Puede conectarse con seguridad aplicaciones basadas en la nube para cualquier tipo de sistema local como grandes sistemas y sistemas Unix.

### <a name="how-do-i-know-if-i-need-a-virtual-network"></a>¿Cómo puedo saber si tengo una red virtual?

Visite la [Introducción de red Virtual](../articles/virtual-network/virtual-networks-overview.md) para ver una tabla de decisión que le ayudarán a decidir la mejor opción de diseño de red para usted.

### <a name="how-do-i-get-started"></a>¿Cómo empezar?

Visite [la documentación de red Virtual](https://azure.microsoft.com/documentation/services/virtual-network/) para empezar. Esta página contiene vínculos a comunes pasos de configuración, así como información que le ayudará a comprender las cosas que debe tener en cuenta al diseñar la red virtual.

### <a name="what-services-can-i-use-with-vnets"></a>¿A qué servicios puedo usar con VNets?

VNets se puede utilizar con una amplia variedad de servicios de Azure diferentes, como servicios de nube (PaaS), máquinas virtuales y aplicaciones Web. Sin embargo, hay algunos servicios que no son compatibles en una VNet. Compruebe si el servicio específico que desea usar y comprobar que es compatible.

### <a name="can-i-use-vnets-without-cross-premises-connectivity"></a>¿Puedo usar VNets sin conexión entre local?

Sí. Puede usar un VNet sin usar la conectividad de sitio a otro. Esto es especialmente útil si desea ejecutar los controladores de dominio y granjas de servidores de SharePoint en Azure.

## <a name="virtual-network-configuration"></a>Configuración de red virtual

### <a name="what-tools-do-i-use-to-create-a-vnet"></a>¿Qué herramientas puedo usar para crear una VNet?

Puede usar las siguientes herramientas para crear o configurar una red virtual:

- Portal de Azure (para clásica y VNets Administrador de recursos).

- Un archivo de configuración de red (netcfg - para solo VNets clásico). Vea [Configurar una red virtual mediante un archivo de configuración de red](../articles/virtual-network/virtual-networks-using-network-configuration-file.md).

- PowerShell (para clásica y VNets Administrador de recursos).

- Azure CLI (para clásica y VNets Administrador de recursos).

### <a name="what-address-ranges-can-i-use-in-my-vnets"></a>¿Qué intervalos de direcciones puedo usar en mi VNets?

Puede usar los intervalos de direcciones IP públicas y cualquier intervalo de direcciones IP definido en [RFC 1918](http://tools.ietf.org/html/rfc1918).

### <a name="can-i-have-public-ip-addresses-in-my-vnets"></a>¿Puedo tener direcciones IP públicas en mi VNets?

Sí. Para obtener más información sobre los intervalos de direcciones IP públicas, vea [el espacio de direcciones IP pública en una red Virtual (VNet)](../articles/virtual-network/virtual-networks-public-ip-within-vnet.md). Tenga en cuenta que sus direcciones IP pública no será accesible directamente desde Internet.

### <a name="is-there-a-limit-to-the-number-of-subnets-in-my-virtual-network"></a>¿Hay un límite de subredes en mi red virtual?

No hay ningún límite del número de subredes que usar dentro de un VNet. Todas las subredes deben estar totalmente contenidas en el espacio de direcciones de red virtual y no deben superponerse entre sí.

### <a name="are-there-any-restrictions-on-using-ip-addresses-within-these-subnets"></a>¿Hay algunas restricciones sobre el uso de direcciones IP dentro de estas subredes?

Azure reserva algunas direcciones IP dentro de cada subred. Las direcciones IP de primer y últimos subred están reservadas para conformidad protocolo, junto con 3 direcciones más utilizadas para los servicios de Azure.

### <a name="how-small-and-how-large-can-vnets-and-subnets-be"></a>¿Cómo se cómo grandes y pequeños pueden ser VNets y subredes?

La subred menor que admitimos es una /29 y el mayor es una /8 (con definiciones de subred CIDR).

### <a name="can-i-bring-my-vlans-to-azure-using-vnets"></a>¿Traigo mi VLAN a Azure con VNets?

No. VNets son las superposiciones de nivel 3. Azure no admite cualquier semántica de nivel 2.

### <a name="can-i-specify-custom-routing-policies-on-my-vnets-and-subnets"></a>¿Puedo especificar directivas de enrutamiento personalizadas en mi VNets y subredes?

Sí. Puede usar enrutamiento definidas por el usuario (UDR). Para obtener más información sobre UDR, visite [rutas de definidas por el usuario y el reenvío de IP](../articles/virtual-network/virtual-networks-udr-overview.md).

### <a name="do-vnets-support-multicast-or-broadcast"></a>¿VNets admiten difusión o multidifusión?

No. No se admite multidifusión o difusión.

### <a name="what-protocols-can-i-use-within-vnets"></a>¿Qué protocolos puedo usar en VNets?

Puede usar protocolos estándar de IP dentro de VNets. Sin embargo, multidifusión, difusión, IP en IP paquetes encapsulados y encapsulado de enrutamiento genérico (GRE) se bloquean en VNets. Incluyen protocolos estándar que funcionan:

- TCP
- UDP
- ICMP

### <a name="can-i-ping-my-default-routers-within-a-vnet"></a>¿Puedo hacer ping mi encaminadores predeterminados dentro de una VNet?

No.

### <a name="can-i-use-tracert-to-diagnose-connectivity"></a>¿Puedo utilizar tracert para diagnosticar conectividad?

No.

### <a name="can-i-add-subnets-after-the-vnet-is-created"></a>¿Puedo agregar subredes después de crea la VNet?

Sí. Subredes pueden agregarse a VNets en cualquier momento, como la dirección de subred no forma parte de otra subred en la VNet.

### <a name="can-i-modify-the-size-of-my-subnet-after-i-create-it"></a>¿Puedo modificar el tamaño de mi subred después de la cree?

Puede agregar, quitar, expandir o contraer una subred si existen servicios implementados dentro de ella mediante cmdlets de PowerShell o el archivo NETCFG ni máquinas virtuales. También puede agregar, quitar, expandir o reducir prefijos como las subredes que contienen máquinas virtuales o servicios no se ven afectadas por el cambio.

### <a name="can-i-modify-subnets-after-i-created-them"></a>¿Puedo modificar subredes después de que había creado?

Sí. Puede agregar, quitar y modificar los bloques CIDR utilizados por un VNet.

### <a name="can-i-connect-to-the-internet-if-i-am-running-my-services-in-a-vnet"></a>¿Puedo conectarme a internet si ejecuto Mis servicios en un VNet?

Sí. Todos los servicios implementados dentro de un VNet pueden conectarse a internet. Cada servicio de nube implementado en Azure tiene asignada VIP públicamente accesible. Debe definir extremos de entrada para las funciones de PaaS y extremos para máquinas virtuales habilitar estos servicios Aceptar las conexiones de internet.

### <a name="do-vnets-support-ipv6"></a>¿VNets admite IPv6?

No. No puede utilizar IPv6 con VNets en este momento.

### <a name="can-a-vnet-span-regions"></a>¿Puede abarcar un VNet regiones?

No. Una VNet se limita a una sola región.

### <a name="can-i-connect-a-vnet-to-another-vnet-in-azure"></a>¿Puedo conectar un VNet a otro VNet en Azure?

Sí. Puede crear VNet VNet comunicación mediante las API de REST o Windows PowerShell. También puede conectar VNets a través de interconexión VNet. Ver más detalles sobre interconexión [aquí.](../articles/virtual-network/virtual-network-peering-overview.md)

## <a name="name-resolution-dns"></a>Resolución de nombres (de dominio DNS)

### <a name="what-are-my-dns-options-for-vnets"></a>¿Cuáles son Mis opciones de DNS para VNets?

Use la tabla de decisión en la página de [Resolución de nombres para máquinas virtuales y las instancias de la función](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) que le guiará a lo largo de todas las opciones de DNS disponibles.

### <a name="can-i-specify-dns-servers-for-a-vnet"></a>¿Puedo especificar los servidores DNS para una VNet?

Sí. Puede especificar direcciones IP del servidor DNS en la configuración de VNet. Esto se aplicará como los servidores DNS de forma predeterminada para todas las máquinas virtuales en la VNet.

### <a name="how-many-dns-servers-can-i-specify"></a>¿Cuántos servidores DNS se debe especificar?

Puede especificar hasta 12 servidores DNS.

### <a name="can-i-modify-my-dns-servers-after-i-have-created-the-network"></a>¿Puedo modificar los servidores DNS después de crear la red?

Sí. Puede cambiar la lista del servidor DNS para su VNet en cualquier momento. Si cambia su lista del servidor DNS, debe reiniciar cada una de las máquinas virtuales en su VNet máquina para que capte el nuevo servidor DNS.


### <a name="what-is-azure-provided-dns-and-does-it-work-with-vnets"></a>¿Qué es DNS siempre Azure y funciona con VNets?

Siempre Azure DNS es un servicio DNS de múltiples arrendatario ofrecido por Microsoft. Azure registra sus máquinas virtuales y las instancias de función en este servicio. Este servicio proporciona la resolución de nombres por nombre de host para máquinas virtuales y las instancias de función contenidas en el mismo servicio de nube y FQDN para máquinas virtuales y las instancias de función en la misma VNet.

> [AZURE.NOTE] Hay una limitación en este momento a los servicios de 100 nube primeros en la red virtual para la resolución de nombres de inquilino cruzado con DNS Azure le ha proporcionado. Si está utilizando su propio servidor DNS, no se aplica esta limitación.

### <a name="can-i-override-my-dns-settings-on-a-per-vm--service-basis"></a>¿Invalidar la configuración de DNS en por VM / base del servicio?

Sí. Puede configurar los servidores DNS en una base de servicio por nube para invalidar la configuración predeterminada de la red. Sin embargo, se recomienda que use DNS de toda la red tanto como sea posible.

### <a name="can-i-bring-my-own-dns-suffix"></a>¿Traigo mi propia sufijo DNS?

No. No puede especificar un sufijo DNS personalizado para su VNets.

## <a name="vnets-and-vms"></a>VNets y máquinas virtuales

### <a name="can-i-deploy-vms-to-a-vnet"></a>¿Puedo implementar máquinas virtuales en un VNet?

Sí.

### <a name="can-i-deploy-linux-vms-to-a-vnet"></a>¿Puedo implementar máquinas virtuales de Linux en un VNet?

Sí. Puede implementar cualquier distro de Linux compatible con Azure.

### <a name="what-is-the-difference-between-a-public-vip-and-an-internal-ip-address"></a>¿Cuál es la diferencia entre una VIP pública y una dirección IP interna?

- Una dirección IP interna es una dirección IP que está asignada a cada máquina virtual dentro de un VNet DHCP. No es orientación pública. Si ha creado un VNet, se asigna la dirección IP interna del rango especificado en la configuración de subred de su VNet. Si no tiene un VNet, una dirección IP interna se asignará. La dirección IP interna permanecerá con la máquina virtual para su duración, a menos que se cancela la asignación de máquina virtual.

- Una VIP pública es la dirección IP pública que se ha asignado el equilibrador de carga o servicio de nube. No se asigna directamente a su NIC. VM La dirección VIP permanecerá con el servicio de nube se asigna a hasta que todas las máquinas virtuales en que se cancela la asignación de servicio de nube o se eliminan. En ese momento, se libera.

### <a name="what-ip-address-will-my-vm-receive"></a>¿Qué dirección IP recibirá mi VM?

- **Dirección IP interno:** Si implementa una máquina virtual a un VNet, la máquina virtual recibe una dirección IP interna de un grupo de direcciones IP internas que especifique. Máquinas virtuales comunican dentro de la VNets con las direcciones IP internas. Aunque Azure le asigna una dirección IP interna dinámica, puede solicitar una dirección estática para su máquina virtual. Para obtener más información acerca de direcciones IP internas, visite [cómo establecer una dirección IP interna estática](../articles/virtual-network/virtual-networks-reserved-private-ip.md).

- **VIP-** La máquina virtual también está asociada a una dirección VIP, aunque VIP nunca está asignada a la máquina virtual directamente. Una VIP es una dirección IP pública que se puede asignar a su servicio de nube. Si lo desea, puede, reservar a VIP de su servicio de nube.

- **ILPIP-** También puede configurar una dirección IP pública de nivel de instancia (ILPIP). ILPIPs están directamente asociados a la máquina virtual, en lugar de con el servicio de nube. Para obtener más información sobre ILPIPs, visite [Introducción de nivel de instancia público IP](../articles/virtual-network/virtual-networks-instance-level-public-ip.md).

### <a name="can-i-reserve-an-internal-ip-address-for-a-vm-that-i-will-create-at-a-later-time"></a>¿Puedo reservar una dirección IP interna para una máquina virtual que creo en otro momento?

No. No puede reservar una dirección IP interna. Si hay una dirección IP interna se asignará a una instancia VM o función por haberlo. Máquina puede o no ser el elemento que desee que la dirección IP interna que se asignará a. Sin embargo, puede cambiar la dirección IP interna de una máquina virtual ya se ha creado para cualquier dirección IP interno disponible.

### <a name="do-internal-ip-addresses-change-for-vms-in-a-vnet"></a>¿Realice el cambio de direcciones IP interno para máquinas virtuales en un VNet?

Sí. Direcciones IP internas permanecen con la máquina virtual para su duración a menos que se cancela la asignación de la máquina virtual. Cuando una máquina virtual se ha quitado la dirección IP interna se libera a menos que haya definido una dirección IP estática interna para su máquina virtual. Si la máquina virtual es simplemente detenida (y no se coloca en el estado **detenido (Deallocated)**) la dirección IP seguirá estando asignada a la máquina virtual.

### <a name="can-i-manually-assign-ip-addresses-to-nics-in-vms"></a>¿Puedo asignar manualmente direcciones IP a la NIC en máquinas virtuales?

No. No debe cambiar las propiedades de la interfaz de máquinas virtuales. Los cambios pueden producir potencialmente perder la conexión a la máquina virtual.

### <a name="what-happens-to-my-ip-addresses-if-i-shut-down-a-vm"></a>¿Qué sucede con Mis direcciones IP si cierra una máquina virtual?

No hay nada. Las direcciones IP (VIP público y dirección IP interna) permanecerá con el servicio de nube o VM.

> [AZURE.NOTE] Si desea simplemente apagar la máquina virtual, no use el Portal de administración para hacerlo. Actualmente, el botón Apagar anulará la asignación de la máquina virtual.

### <a name="can-i-move-vms-from-one-subnet-to-another-subnet-in-a-vnet-without-re-deploying"></a>¿Puedo mover máquinas virtuales de una subred a otra subred en un VNet sin volver a implementar?

Sí. Puede encontrar más información [aquí](../articles/virtual-network/virtual-networks-move-vm-role-to-subnet.md).

### <a name="can-i-configure-a-static-mac-address-for-my-vm"></a>¿Puedo configurar una dirección MAC estática para mi VM?

No. Una dirección MAC no se puede configurar de forma estática.

### <a name="will-the-mac-address-remain-the-same-for-my-vm-once-it-has-been-created"></a>¿La dirección MAC permanecerán igual para mi máquina virtual una vez que se ha creado?

Sí, la dirección MAC permanecerán igual para una máquina virtual aunque se ha detenido la máquina virtual (manera) y relanzado.

### <a name="can-i-connect-to-the-internet-from-a-vm-in-a-vnet"></a>¿Puedo conectar a internet desde una máquina virtual en un VNet?

Sí. Todos los servicios implementados dentro de un VNet pueden conectarse a Internet. Además, cada servicio de nube implementado en Azure tiene asignada VIP públicamente accesible. Debe definir extremos de entrada para las funciones de PaaS y extremos para máquinas virtuales habilitar estos servicios Aceptar las conexiones de Internet.

## <a name="vnets-and-services"></a>VNets y servicios

### <a name="what-services-can-i-use-with-vnets"></a>¿A qué servicios puedo usar con VNets?

Solo puede usar servicios de cálculo en VNets. Calcular servicios están limitados a servicios en la nube (funciones web y trabajador) y máquinas virtuales.

### <a name="can-i-use-web-apps-with-virtual-network"></a>¿Puedo usar aplicaciones Web con una red Virtual?

Sí. Puede implementar aplicaciones Web dentro de una VNet con ASE (aplicación de servicio de entorno). Agregar a la, aplicaciones Web puede segura conectarse y tener acceso a los recursos en su VNet de Azure si ha configurado para su VNet punto al sitio. Para obtener más información, consulte lo siguiente:


- [Crear aplicaciones Web en un entorno de servicio de aplicaciones](../articles/app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase.md)

- [Integración de red Virtual de aplicaciones Web](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/)

- [Utilizar conexiones de híbrido y VNet integración con aplicaciones Web](https://azure.microsoft.com/blog/2014/10/30/using-vnet-or-hybrid-conn-with-websites/)

- [Integrar una aplicación web con una red Virtual de Azure](../articles/app-service-web/web-sites-integrate-with-vnet.md)

### <a name="can-i-deploy-cloud-services-with-web-and-worker-roles-paas-in-a-vnet"></a>¿Puedo implementar servicios en la nube con roles de web y trabajador (PaaS) en un VNet?

Sí. Puede implementar los servicios de PaaS dentro de VNets.

### <a name="how-do-i-deploy-paas-roles-to-a-vnet"></a>¿Cómo puedo implementar PaaS roles en una VNet?

Para ello, puede especificar el nombre de VNet y las asignaciones de roles /subnet en la sección de configuración de red de la configuración del servicio. No es necesario actualizar cualquiera de los archivos binarios.

### <a name="can-i-move-my-services-in-and-out-of-vnets"></a>¿Puedo mover Mis servicios y VNets?

No. No se puede mover servicios y VNets. Tendrá que eliminar y volver a implementar el servicio moverla a otro VNet.

## <a name="vnets-and-security"></a>VNets y seguridad

### <a name="what-is-the-security-model-for-vnets"></a>¿Qué es el modelo de seguridad para VNets?

VNets están completamente aisladas entre sí y otros servicios alojados en la infraestructura de Azure. Un VNet es un límite de confianza.

### <a name="can-i-define-acls-or-nsgs-on-my-vnets"></a>¿Puedo definir ACL o NSGs en mi VNets?

No. No se puede asociar ACL o NSGs a VNets. Sin embargo, ACL pueden definirse en los extremos de entrada para máquinas virtuales que se han implementado en un VNets y NSGs se pueden asociadas a subredes o NIC.

### <a name="is-there-a-vnet-security-whitepaper"></a>¿Hay algún artículo de seguridad de VNet?

Sí. Puede descargarla [aquí](http://go.microsoft.com/fwlink/?LinkId=386611).

## <a name="apis-schemas-and-tools"></a>API, esquemas y herramientas

### <a name="can-i-manage-vnets-from-code"></a>¿Puedo administrar VNets desde código?

Sí. Puede usar las API de REST para administrar la conectividad VNets y entre local. Puede encontrar más información [aquí](http://go.microsoft.com/fwlink/?LinkId=296833).

### <a name="is-there-tooling-support-for-vnets"></a>¿Hay compatibilidad de herramientas para VNets?

Sí. Puede usar herramientas de PowerShell y línea de comandos para una gran variedad de plataformas. Puede encontrar más información [aquí](http://go.microsoft.com/fwlink/?LinkId=317721).
