<properties
    pageTitle="Instalar un bosque de Active Directory en una red virtual Azure | Microsoft Azure"
    description="Tutorial que explica cómo crear un nuevo bosque de Active Directory en una máquina virtual (VM) en una red Virtual de Azure."
    services="active-directory, virtual-network"
    keywords="máquina virtual de Active directory, bosque de active directory de instalación, vídeos de azure directorio activo "
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    tags=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="10/10/2016"
    ms.author="markusvi"/>


# <a name="install-a-new-active-directory-forest-on-an-azure-virtual-network"></a>Instalar un nuevo bosque de Active Directory en una red virtual de Azure

Este tema muestra cómo crear un entorno de Active Directory de Windows Server en una red virtual Azure en una máquina virtual (VM) en una [red virtual Azure](../virtual-network/virtual-networks-overview.md). En este caso, la red virtual Azure no está conectada a una red local.

Es posible que también que le interese en los siguientes temas relacionados:

- Para ver un vídeo que muestra estos pasos, consulte [cómo instalar un nuevo bosque de Active Directory en una red virtual de Azure](http://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/How-to-install-a-new-Active-Directory-forest-on-an-Azure-virtual-network)
- Opcionalmente, puede [Configurar una VPN de sitio a sitio](../vpn-gateway/vpn-gateway-site-to-site-create.md) y, a continuación, instale un nuevo bosque o extender un bosque local a una red virtual Azure. Para estos pasos, consulte [instalar un controlador de dominio de réplica de Active Directory en una red Virtual de Azure](../active-directory/active-directory-install-replica-active-directory-domain-controller.md).
-  Para obtener información conceptual sobre la instalación de los servicios de dominio de Active Directory (AD DS) en una red virtual Azure, vea [las instrucciones para la implementación de Active Directory de Windows Server en Azure máquinas virtuales de Windows](https://msdn.microsoft.com/library/azure/jj156090.aspx).

## <a name="scenario-diagram"></a>Diagrama de escenario

En este escenario, los usuarios externos necesitan tener acceso a aplicaciones que se ejecutan en servidores de dominio. Las máquinas virtuales que se ejecutan los servidores de aplicaciones y las máquinas virtuales que se ejecutan controladores de dominio se instalan instalado en su propio servicio de nube en una red virtual Azure. También se incluyen en una disponibilidad para tolerancia a errores mejorada.

![Bosque de Active Directory en un máquinas virtuales de red Virtual de Azure ][1] 7
## <a name="how-does-this-differ-from-on-premises"></a>¿En qué esto difiere local?

No hay mucha diferencia entre instalar un controlador de dominio en Azure frente a local. Las diferencias principales se muestran en la tabla siguiente.

Para configurar...  | Local  | Red virtual Azure
------------- | -------------  | ------------
**Dirección IP del controlador de dominio**  | Asignar dirección IP estática en las propiedades del adaptador de red   | Ejecute el cmdlet Set-AzureStaticVNetIP para asignar una dirección IP estática
**Resolución de cliente DNS**  | Establecer dirección del servidor DNS alternativo y preferido en la red de propiedades del adaptador de miembros de dominio   | Establecer la dirección del servidor DNS en las propiedades de una red virtual
**Almacenamiento de base de datos de Active Directory**  | Si desea cambiar la ubicación de almacenamiento predeterminada de C:\  | Debe cambiar la ubicación de almacenamiento predeterminada de C:\



## <a name="create-an-azure-virtual-network"></a>Crear una red virtual de Azure

1. Inicie sesión en el portal de clásico de Azure.
2. Crear una red virtual. Haga clic en **redes** > **crear una red virtual**. Use los valores de la tabla siguiente para completar al asistente.

    En esta página del asistente...  | Especificar estos valores
    ------------- | -------------
    **Detalles de la red virtual**  | <p>Nombre: Escriba un nombre para su red virtual</p><p>Región: Seleccione la región más cercana</p>
    **DNS y VPN**  | <p>Deje en blanco del servidor DNS</p><p>No seleccione cualquier opción de VPN</p>
    **Espacios de direcciones de red virtual**  | <p>Nombre de subred: escriba un nombre para la subred</p><p>Dirección IP inicial: <b>10.0.0.0</b></p><p>CIDR: <b>/24 (256)</b></p>



## <a name="create-vms-to-run-the-domain-controller-and-dns-server-roles"></a>Crear máquinas virtuales para ejecutar el controlador de dominio y los roles del servidor DNS

Repita los pasos siguientes para crear máquinas virtuales para hospedar el rol de DC según sea necesario. Debe implementar al menos dos DC virtual para proporcionar redundancia y tolerancia a errores. Si la red virtual Azure incluye al menos dos controladores de dominio que estén configurados de forma similar (es decir, son ambos catálogos globales, ejecutar DNS server, y no contiene cualquier función FSMO etc.), a continuación, coloque las máquinas virtuales que se ejecutan los controladores de dominio en una disponibilidad para tolerancia a errores mejorada.

Para crear las máquinas virtuales con Windows PowerShell en lugar de la interfaz de usuario, vea [Usar PowerShell de Azure para crear y configurar previamente máquinas virtuales basadas en Windows](../virtual-machines/virtual-machines-windows-classic-create-powershell.md).

1. En el portal clásico, haga clic en **nuevo** > **calcular** > **Máquina Virtual** > **Desde la Galería**. Use los valores siguientes para completar al asistente. Acepte el valor predeterminado para una configuración a menos que otro valor se sugiere o requerido.

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

## <a name="install-windows-server-active-directory"></a>Instalar Windows Server Active Directory

Usar la misma rutina [instalar AD DS](https://technet.microsoft.com/library/jj574166.aspx) que use local (es decir, puede usar la interfaz de usuario, un archivo de respuesta o Windows PowerShell). Debe proporcionar las credenciales de administrador para instalar un nuevo bosque. Para especificar la ubicación de la base de datos de Active Directory, los registros y SYSVOL, cambiar la ubicación de almacenamiento predeterminada de la unidad de sistema operativo en el disco de datos adicionales que ha adjuntado a la máquina virtual.

Cuando termine la instalación de controlador de dominio, vuelva a conectar a la máquina virtual e inicie sesión el controlador de dominio. Recuerde que debe especificar credenciales de dominio.

## <a name="reset-the-dns-server-for-the-azure-virtual-network"></a>Restablecer el servidor DNS de la red virtual de Azure

1. Restablecer la configuración del reenviador DNS en el nuevo servidor DC/DNS.
  1. En el administrador del servidor, haga clic en **Herramientas** > **DNS**.
  2. En el **Administrador de DNS**, haga clic en el nombre del servidor DNS y haga clic en **Propiedades**.
  3. En la ficha **reenviadores** , haga clic en la dirección IP del reenviador y haga clic en **Editar**.  Seleccione la dirección IP y haga clic en **Eliminar**.
  4. Haga clic en **Aceptar** para cerrar el editor y **Aceptar** para cerrar las propiedades del servidor DNS.
2. Actualizar la configuración del servidor DNS de la red virtual.
  1. Haga clic en **Redes virtuales** > haga doble clic en la red virtual que creó > **Configurar** > **servidores DNS**, escriba el nombre y la DIP de una de las VM que ejecuta el rol de servidor DNS o controlador de dominio y haga clic en **Guardar**.
  2. Seleccione la máquina virtual y haga clic en **reiniciar** para desencadenar la máquina virtual para configurar las opciones de resolución DNS con la dirección IP del servidor DNS nuevo.


## <a name="create-vms-for-domain-members"></a>Crear máquinas virtuales de los miembros del dominio

1. Repita los pasos siguientes para crear las VM se ejecuten como servidores de aplicaciones. Acepte el valor predeterminado para una configuración a menos que otro valor se sugiere o requerido.

    En esta página del asistente...  | Especificar estos valores
    ------------- | -------------
    **Elegir una imagen**  | Centro de datos de Windows Server 2012 R2
    **Configuración de máquina virtual**  | <p>Nombre de la máquina virtual: Escriba un nombre de etiqueta única (por ejemplo, AppServer1).</p><p>Nuevo nombre de usuario: Escriba el nombre de un usuario. Este usuario será un miembro del grupo de administradores locales en la máquina virtual. Necesitará este nombre para iniciar sesión en la máquina virtual por primera vez. La cuenta denominada administrador no funcionará.</p><p>Contraseña nueva y confirmar: Escriba una contraseña</p>
    **Configuración de máquina virtual**  | <p>Servicio de nube: Elija **crear un nuevo servicio de nube** para la primera VM y seleccione ese mismo nombre de servicio de nube al crear más máquinas virtuales que hospedará la aplicación.</p><p>Nombre DNS del servicio de nube: Especifique un nombre único global</p><p>Región/grupo afinidad/red Virtual: especifique el nombre de red virtual (por ejemplo, WestUSVNet).</p><p>Cuenta de almacenamiento: Elija **usar una cuenta de almacenamiento generado automáticamente** para la primera VM y luego seleccione ese mismo nombre de cuenta de almacenamiento al crear más máquinas virtuales que hospedará la aplicación.</p><p>Conjunto de disponibilidad: Elija **crear un conjunto de disponibilidad**.</p><p>Nombre del conjunto de disponibilidad: escriba un nombre para la disponibilidad establece al crear la primera VM y, a continuación, seleccione mismo nombre cuando se crea VM más.</p>
    **Configuración de máquina virtual**  | <p>Seleccione <b>instalar el agente de VM</b> y cualquier otra extensión que necesita.</p>
2. Después de cada VM se aprovisiona, inicie sesión y unirse al dominio. En el **Administrador del servidor**, haga clic en el **Servidor Local** > **grupo de trabajo** > **cambiar...** y, a continuación, seleccione el **dominio** y escriba el nombre del dominio local. Proporcionar credenciales de usuario del dominio y, a continuación, reinicie la máquina virtual para completar la combinación de dominio.

Para crear las máquinas virtuales con Windows PowerShell en lugar de la interfaz de usuario, vea [Usar PowerShell de Azure para crear y configurar previamente máquinas virtuales basadas en Windows](../virtual-machines/virtual-machines-windows-classic-create-powershell.md).

Para obtener más información sobre el uso de Windows PowerShell, vea [Introducción a los Cmdlets de Azure](https://msdn.microsoft.com/library/azure/jj554332.aspx) y [Azure Cmdlet referencia](https://msdn.microsoft.com/library/azure/jj554330.aspx).


## <a name="see-also"></a>Vea también

-  [Cómo instalar un nuevo bosque de Active Directory en una red virtual de Azure](http://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/How-to-install-a-new-Active-Directory-forest-on-an-Azure-virtual-network)
-  [Directrices para la implementación de Active Directory Windows Server en máquinas virtuales de Windows Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx)

-  [Configurar una VPN de sitio a otro](../vpn-gateway/vpn-gateway-site-to-site-create.md)
-  [Instalar un controlador de dominio de Active Directory réplica en una red virtual de Azure](../active-directory/active-directory-install-replica-active-directory-domain-controller.md)
-  [Microsoft Azure IT Pro IaaS: aspectos básicos de la máquina (01)](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
-  [Microsoft Azure IT Pro IaaS: (05) crear redes virtuales y conectividad entre local](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)
-  [Información general de una red virtual](../virtual-network/virtual-networks-overview.md)
-  [Cómo instalar y configurar PowerShell de Azure](../powershell-install-configure.md)
-  [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx)
-  [Referencia de Cmdlet de Azure](https://msdn.microsoft.com/library/azure/jj554330.aspx)
-  [Establecer la dirección IP estática de Azure VM](http://windowsitpro.com/windows-azure/set-azure-vm-static-ip-address)
-  [Cómo asignar la dirección IP estática a Azure VM](http://www.bhargavs.com/index.php/2014/03/13/how-to-assign-static-ip-to-azure-vm/)
-  [Instalar un nuevo bosque de Active Directory](https://technet.microsoft.com/library/jj574166.aspx)
-  [Introducción a Active Directory Domain Services (AD DS) virtualización (nivel 100)](https://technet.microsoft.com/library/hh831734.aspx)

<!--Image references-->
[1]: ./media/active-directory-new-forest-virtual-machine/AD_Forest.png
