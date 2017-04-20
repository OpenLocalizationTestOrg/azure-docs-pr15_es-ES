<properties
    pageTitle="Instalar un controlador de dominio de Active Directory de réplica de Azure | Microsoft Azure"
    description="Tutorial que explica cómo instalar un controlador de dominio desde un bosque de Active Directory local en una máquina virtual Azure."
    services="virtual-network"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="virtual-network"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/30/2016"
    ms.author="curtand"/>


# <a name="install-a-replica-active-directory-domain-controller-in-an-azure-virtual-network"></a>Instalar una réplica de controlador de dominio de Active Directory en una red virtual de Azure

Este tema muestra cómo instalar los controladores de dominio adicionales (también conocido como réplica DC) para un dominio de Active Directory local en Azure máquinas virtuales (VM) en una red virtual Azure.

Es posible que también que le interese en los siguientes temas relacionados:

-  Opcionalmente, puede instalar un nuevo bosque de Active Directory en una red virtual Azure. Para estos pasos, consulte [instalar un nuevo bosque de Active Directory en una red virtual Azure](../active-directory/active-directory-new-forest-virtual-machine.md).
-  Para obtener información conceptual sobre la instalación de los servicios de dominio de Active Directory (AD DS) en una red virtual Azure, vea [las instrucciones para la implementación de Active Directory de Windows Server en Azure máquinas virtuales de Windows](https://msdn.microsoft.com/library/azure/jj156090.aspx).


## <a name="scenario-diagram"></a>Diagrama de escenario

En este escenario, los usuarios externos necesitan tener acceso a aplicaciones que se ejecutan en servidores de dominio. Las máquinas virtuales que ejecutan los servidores de aplicaciones y los controladores de dominio de réplica están instalados en una red virtual Azure. La red virtual se pueden conectar a la red local mediante una conexión [VPN de sitio a sitio](../vpn-gateway/vpn-gateway-site-to-site-create.md) , como se muestra en el diagrama siguiente, o puede usar [ExpressRoute](../expressroute/expressroute-locations-providers.md) para una conexión más rápida.

Los servidores de aplicaciones y los controladores de dominio se implementan dentro de los servicios de nube independiente para distribuir el procesamiento de cálculo y dentro de [los conjuntos de disponibilidad](../virtual-machines/virtual-machines-windows-manage-availability.md) tolerancia a errores mejorada.
Los controladores de dominio replican entre sí y con controladores de dominio local mediante la replicación de Active Directory. Herramientas de sincronización no son necesarias.

![Controlador de dominio de Active Directory de pf réplica de diagrama de una vnet de Azure][1]

## <a name="create-an-active-directory-site-for-the-azure-virtual-network"></a>Crear un sitio de Active Directory de la red virtual de Azure

Es una buena idea crear un sitio de Active Directory que representa la región de red correspondiente a la red virtual. Que le ayuda a optimizar la autenticación, replicación y otras operaciones de ubicación del controlador de dominio. Los siguientes pasos explican cómo crear un sitio y a continuación, para obtener más información, vea [Agregar un nuevo sitio](https://technet.microsoft.com/library/cc781496.aspx).

1. Abra sitios y servicios Active Directory: **Administrador del servidor** > **Herramientas** > **servicios y sitios de Active Directory**.
2. Crear un sitio para representar la región donde lo ha creado una red virtual Azure: haga clic en **sitios** > **acción** > **nuevo sitio** > escriba el nombre del nuevo sitio, como oeste de Azure nos > Seleccionar un vínculo de sitio > **Aceptar**.
3. Crear una subred y asociar con el nuevo sitio: haga doble clic en **sitios** > contextual **subredes** > **nueva subred** > escriba el intervalo de direcciones IP de la red virtual (como 10.1.0.0/16 en el diagrama de escenario) > Seleccionar el nuevo sitio de Azure > **Aceptar**.

## <a name="create-an-azure-virtual-network"></a>Crear una red virtual de Azure

1. En el [portal de clásica Azure](https://manage.windowsazure.com), haga clic en **nuevo** > **Servicios de red** > **Red Virtual** > **Crear personalizado** y use los siguientes valores para completar el asistente.

    En esta página del asistente...  | Especificar estos valores
    ------------- | -------------
    **Detalles de la red virtual**  | <p>Nombre: Escriba un nombre para la red virtual, como WestUSVNet.</p><p>Región: Seleccione la región más cercana.</p>
    **Conectividad DNS y VPN**  | <p>Los servidores DNS: Especifique el nombre y la dirección IP de uno o más servidores DNS local.</p><p>Conectividad: Seleccione **Configurar una VPN de sitio a otro**.</p><p>Red local: especificar una nueva red local.</p><p>Si está utilizando ExpressRoute en lugar de una VPN, vea [Configurar una conexión de ExpressRoute a través de un proveedor de servicios de Exchange](../expressroute/expressroute-locations-providers.md).</p>
    **Conectividad de sitio a otro**  | <p>Nombre: Escriba un nombre para la red local.</p><p>Dirección IP del dispositivo VPN: especifique la dirección IP pública del dispositivo que se conecte a la red virtual. No se encuentra el dispositivo VPN detrás de un NAT.</p><p>Dirección: Especificar los intervalos de direcciones para su red local (por ejemplo, 192.168.0.0/16 en el diagrama del escenario).</p>
    **Espacios de direcciones de red virtual**  | <p>Espacio de direcciones: Especifique el intervalo de direcciones IP para máquinas virtuales que desea ejecutar en la red virtual Azure (como 10.1.0.0/16 en el diagrama del escenario). Este intervalo de direcciones no puede superponerse con los intervalos de direcciones de la red local.</p><p>Subredes: Especifique un nombre y una dirección para una subred para los servidores de aplicaciones (como front-end, 10.1.1.0/24) y para los controladores de dominio (por ejemplo, back-end, 10.1.2.0/24).</p><p>Haga clic en **Agregar subred de puerta de enlace**.</p>

2. A continuación, deberá configurar la puerta de enlace de red virtual para crear una conexión VPN de sitio a sitio segura. Vea [Configurar una puerta de enlace de red Virtual](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md) para ver las instrucciones.
3. Crear la conexión VPN de sitio a sitio entre la nueva red virtual y un dispositivo VPN local. Vea [Configurar una puerta de enlace de red Virtual](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md) para ver las instrucciones.


## <a name="create-azure-vms-for-the-dc-roles"></a>Crear máquinas virtuales de Azure para las funciones de DC

Repita los pasos siguientes para crear máquinas virtuales para hospedar el rol de DC según sea necesario. Debe implementar al menos dos DC virtual para proporcionar redundancia y tolerancia a errores. Si la red virtual Azure incluye al menos dos controladores de dominio que estén configurados de forma similar (es decir, son ambos catálogos globales, ejecutar DNS server, y no contiene cualquier función FSMO etc.), a continuación, coloque las máquinas virtuales que se ejecutan los controladores de dominio en una disponibilidad para tolerancia a errores mejorada.
Para crear las máquinas virtuales con Windows PowerShell en lugar de la interfaz de usuario, vea [Usar PowerShell de Azure para crear y configurar previamente máquinas virtuales basadas en Windows](../virtual-machines/virtual-machines-windows-classic-create-powershell.md).

1. En el [portal de clásica Azure](https://manage.windowsazure.com), haga clic en **nuevo** > **calcular** > **Máquina Virtual** > **Desde la Galería**. Use los valores siguientes para completar al asistente. Acepte el valor predeterminado para una configuración a menos que otro valor se sugiere o requerido.

    En esta página del asistente...  | Especificar estos valores
    ------------- | -------------
    **Elegir una imagen**  | Centro de datos de Windows Server 2012 R2
    **Configuración de máquina virtual**  | <p>Nombre de la máquina virtual: Escriba un nombre de etiqueta única (por ejemplo, AzureDC1).</p><p>Nuevo nombre de usuario: Escriba el nombre de un usuario. Este usuario será un miembro del grupo de administradores locales en la máquina virtual. Necesitará este nombre para iniciar sesión en la máquina virtual por primera vez. La cuenta denominada administrador no funcionará.</p><p>Contraseña nueva y confirmar: Escriba una contraseña</p>
    **Configuración de máquina virtual**  | <p>Servicio de nube: Elija <b>crear un nuevo servicio de nube</b> para la primera VM y seleccione ese mismo nombre de servicio de nube al crear más máquinas virtuales que va a hospedar el rol de controlador de dominio.</p><p>Nombre DNS del servicio de nube: Especifique un nombre único global</p><p>Región/grupo afinidad/red Virtual: especifique el nombre de red virtual (por ejemplo, WestUSVNet).</p><p>Cuenta de almacenamiento: Seleccione <b>usar una cuenta de almacenamiento generado automáticamente</b> para la primera VM y luego seleccione ese mismo nombre de cuenta de almacenamiento al crear más máquinas virtuales que va a hospedar el rol de controlador de dominio.</p><p>Conjunto de disponibilidad: Elija <b>crear un conjunto de disponibilidad</b>.</p><p>Nombre del conjunto de disponibilidad: escriba un nombre para la disponibilidad establece al crear la primera VM y, a continuación, seleccione mismo nombre al crear máquinas virtuales más.</p>
    **Configuración de máquina virtual**  | <p>Seleccione <b>instalar el agente de VM</b> y cualquier otra extensión que necesita.</p>
2. Adjuntar un disco a cada VM que se ejecutará el rol de servidor de controlador de dominio. Se necesita el disco adicional para almacenar la base de datos, los registros y SYSVOL de AD. Especifique el tamaño del disco (por ejemplo, 10 GB) y deje la **Preferencia de caché de Host** establecida en **ninguna**. Para ver los pasos, consulte [cómo adjuntar un disco de datos a una máquina Virtual de Windows](../virtual-machines/virtual-machines-windows-classic-attach-disk.md).
3. Una vez que inicie sesión primero la máquina virtual, abra el **Administrador del servidor** > **y servicios de almacenamiento de archivos** para crear un volumen en el disco con NTFS.
4. Reservar una dirección IP estática para máquinas virtuales que se ejecutarán la función de controlador de dominio. Para reservar una dirección IP estática, descargue el instalador de plataforma Web de Microsoft e [instale PowerShell de Azure](../powershell-install-configure.md) y ejecute el cmdlet Set-AzureStaticVNetIP. Por ejemplo:

    ' AzureVM get - ServiceName AzureDC1-nombre AzureDC1 | Establecer AzureStaticVNetIP - dirección IP 10.0.0.4 | Actualización AzureVM

Para obtener más información sobre cómo configurar una dirección IP estática, vea [Configurar una dirección IP estática interna para una máquina virtual](../virtual-network/virtual-networks-reserved-private-ip.md).

## <a name="install-ad-ds-on-azure-vms"></a>Instalar AD DS en máquinas virtuales de Azure

Inicie sesión en una máquina virtual y compruebe que tiene conectividad a través de la conexión de sitio a sitio VPN o ExpressRoute a los recursos en su red local. A continuación, instalar AD DS en las máquinas virtuales de Azure. Puede usar el mismo proceso que utiliza para instalar un controlador de dominio adicional en su red local (interfaz de usuario, Windows PowerShell o un archivo de respuesta). Cuando se instala AD DS, asegúrese de que especificar el volumen nuevo la ubicación de la base de datos de AD, registros y SYSVOL. Si necesita un repaso de la instalación de AD DS, vea [Instalar Active Directory Domain Services (nivel 100)](https://technet.microsoft.com/library/hh472162.aspx) o [instalar un controlador de dominio de réplica Windows Server 2012 en un dominio existente (nivel 200)](https://technet.microsoft.com/library/jj574134.aspx).

## <a name="reconfigure-dns-server-for-the-virtual-network"></a>Volver a configurar el servidor DNS para la red virtual

1. En el [portal de clásica Azure](https://manage.windowsazure.com), haga clic en el nombre de la red virtual y, a continuación, haga clic en la ficha **Configurar** para [volver a configurar las direcciones IP del servidor DNS para su red virtual](../virtual-network/virtual-networks-manage-dns-in-vnet.md) para usar las direcciones IP estáticas asignadas a la réplica controladores de dominio en lugar de las direcciones IP de los servidores DNS local.

2. Para asegurarse de que todos los la réplica máquinas virtuales de controlador de dominio en la red virtual están configurados con usar los servidores DNS en la red virtual, haga clic en **máquinas virtuales de Windows**, haga clic en la columna Estado para cada VM y, a continuación, haga clic en **reiniciar**. Espere a que la máquina virtual muestra el estado **Running** antes de intentar iniciar sesión en él.

## <a name="create-vms-for-application-servers"></a>Crear máquinas virtuales para servidores de aplicaciones

1. Repita los pasos siguientes para crear las VM se ejecuten como servidores de aplicaciones. Acepte el valor predeterminado para una configuración a menos que otro valor se sugiere o requerido.

    En esta página del asistente...  | Especificar estos valores
    ------------- | -------------
    **Elegir una imagen**  | Centro de datos de Windows Server 2012 R2
    **Configuración de máquina virtual**  | <p>Nombre de la máquina virtual: Escriba un nombre de etiqueta única (por ejemplo, AppServer1).</p><p>Nuevo nombre de usuario: Escriba el nombre de un usuario. Este usuario será un miembro del grupo de administradores locales en la máquina virtual. Necesitará este nombre para iniciar sesión en la máquina virtual por primera vez. La cuenta denominada administrador no funcionará.</p><p>Contraseña nueva y confirmar: Escriba una contraseña</p>
    **Configuración de máquina virtual**  | <p>Servicio de nube: Elija **crear un nuevo servicio de nube** para la primera VM y seleccione ese mismo nombre de servicio de nube al crear más máquinas virtuales que hospedará la aplicación.</p><p>Nombre DNS del servicio de nube: Especifique un nombre único global</p><p>Región/grupo afinidad/red Virtual: especifique el nombre de red virtual (por ejemplo, WestUSVNet).</p><p>Cuenta de almacenamiento: Elija **usar una cuenta de almacenamiento generado automáticamente** para la primera VM y luego seleccione ese mismo nombre de cuenta de almacenamiento al crear más máquinas virtuales que hospedará la aplicación.</p><p>Conjunto de disponibilidad: Elija **crear un conjunto de disponibilidad**.</p><p>Nombre del conjunto de disponibilidad: escriba un nombre para la disponibilidad establece al crear la primera VM y, a continuación, seleccione mismo nombre al crear máquinas virtuales más.</p>
    **Configuración de máquina virtual**  | <p>Seleccione <b>instalar el agente de VM</b> y cualquier otra extensión que necesita.</p>

2. Después de cada VM se aprovisiona, inicie sesión y unirse al dominio. En el **Administrador del servidor**, haga clic en el **Servidor Local** > **grupo de trabajo** > **cambiar...** y, a continuación, seleccione el **dominio** y escriba el nombre del dominio local. Proporcionar credenciales de usuario del dominio y, a continuación, reinicie la máquina virtual para completar la combinación de dominio.

Para crear las máquinas virtuales con Windows PowerShell en lugar de la interfaz de usuario, vea [Usar PowerShell de Azure para crear y configurar previamente máquinas virtuales basadas en Windows](../virtual-machines/virtual-machines-windows-classic-create-powershell.md).

Para obtener más información sobre el uso de Windows PowerShell, vea [Introducción a los Cmdlets de Azure](https://msdn.microsoft.com/library/azure/jj554332.aspx) y [Azure Cmdlet referencia](https://msdn.microsoft.com/library/azure/jj554330.aspx).

## <a name="additional-resources"></a>Recursos adicionales

-  [Directrices para la implementación de Active Directory Windows Server en máquinas virtuales de Windows Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx)
-  [Cómo cargar los controladores de dominio de Hyper-V local existentes en Azure con PowerShell de Azure](http://support.microsoft.com/kb/2904015)
-  [Instalar un nuevo bosque de Active Directory en una red virtual de Azure](../active-directory/active-directory-new-forest-virtual-machine.md)
-  [Red Virtual Azure](../virtual-network/virtual-networks-overview.md)
-  [Microsoft Azure IT Pro IaaS: aspectos básicos de la máquina (01)](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
-  [Microsoft Azure IT Pro IaaS: (05) crear redes virtuales y conectividad entre local](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)
-  [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx)
-  [Cmdlets de administración de Azure](https://msdn.microsoft.com/library/azure/jj152841)

<!--Image references-->
[1]: ./media/active-directory-install-replica-active-directory-domain-controller/ReplicaDCsOnAzureVNet.png
